<!-- experiment 1a: literal semantics -->

<template>
  <Experiment title="rsa emoji experiment">
    <InstructionScreen :title="'Welcome, nice to see you!'">
      <p>Thank you for participating in our experiment!</p>
      <p>
        In this experiment, you will be asked to judge ratings of everyday
        experiences based on short descriptions
      </p>
      <p>Click “Next” to read the instructions.</p>
    </InstructionScreen>

    <InstructionScreen :title="'Instruction'">
      <p>
        On each trial, you will see a short sentence, with which a person
        described an experience.
      </p>
      <p>
        Your task is to judge how the person truly experienced it by rating with
        a 5-star scale, where 1 denotes the worst experience, 5 denotes the best
        experience.
      </p>
      <p>Click “Next” to begin.</p>
    </InstructionScreen>

    <!-- We iterate over our experiment trials -->

    <!-- and display a screen with a slider rating task
             using the built-in SliderScreen component -->

    <Screen v-for="(trial, i) in trials" :key="i">
      <Slide>
        <p v-if="trial.trialType === 'attention'" id="trial-sentence">
          put the attention check condition here.
        </p>
        <p v-else id="given">
          {{ trial.person }} {{ trial.context.action }}.
          {{ trial.person }} posted this comment on (a social media platform / messaging app):
        </p>
        <p id="trial-sentence">
          <strong
            >"The {{ trial.context.item }} was
            <span id="trial-state"
              >{{ trial.adj }}<span id="trial-emoji">{{ trial.emoji }}</span
              >."
            </span></strong
          >
        </p>
        <p>
          Out of five stars, how do you think {{ trial.person }} would have
          actually rated the experience?
        </p>

        <RatingInput
          :count="5"
          left="1 star"
          right="5 stars"
          :response.sync="trial.inferredState"
        />

        <p>
          Based on this comment, how likely do you think
          {{ trial.person }} would have actually intended to communicate?
        </p>

        <p>
          {{ trial.person }} intended to communicate <strong>only</strong> about the rating of the
          experience.
        </p>
        <RatingInput
          :count="9"
          left="very unlikely"
          right="very likely"
          :response.sync="trial.goalState"
        />

        <p>
          {{ trial.person }} intended to communicate <strong>only</strong> about the emotion felt
          about the experience.
        </p>
        <RatingInput
          :count="9"
          left="very unlikely"
          right="very likely"
          :response.sync="trial.goalValenceArousal"
        />

        <p>
          {{ trial.person }} intended to communicate about <strong>both</strong> the rating
          of the experience, and the emotion felt about it.
        </p>
        <RatingInput
          :count="9"
          left="very unlikely"
          right="very likely"
          :response.sync="trial.goalAll"
        />

        <button
          v-if="trial.inferredState != null && trial.inferredState !== 0"
          style="margin-top: 18px"
          @click="
            $magpie.measurements.context = trial.context.item;
            $magpie.measurements.state = trial.state;
            $magpie.measurements.adj = trial.adj;
            $magpie.measurements.inferredState = trial.inferredState;
            $magpie.measurements.trialType = trial.trialType;
            $magpie.measurements.attentionCheck = trial.attentionCheck;
            $magpie.measurements.isPassedAttention =
              trial.trialType === 'trial' ||
              trial.attentionCheck === trial.applicability;
            $magpie.saveAndNextScreen();
          "
        >
          Next
        </button>
      </Slide>
    </Screen>

    <!-- This screen will ask some optional questions about the
           participant's background, like age, gender etc. -->
    <PostTestScreen />
    <!-- <template #default="{ measurements }">

    <p>Which best describes you?</p>
    <select v-model="measurements.gender">
      <option value="">Prefer not to say</option>
      <option value="male">Man</option>
      <option value="female">Woman</option>
      <option value="other">Another identity</option>
    </select>
  </template> -->

    <!-- While setting your experiment mode to 'debug' in the magpie config
       this screen will show the results of the current experiment directly. Once you switch to directLink or prolific
       this screen will submit the results to your magpie backend -->
    <SubmitResultsScreen />
  </Experiment>
</template>

<script>
import _ from 'lodash';
import { COUNTRIES_LIST } from '@/data/countryList';

const CONTEXTS = [
  { action: 'attended a concert', item: 'concert' },
  { action: 'tried a pizza', item: 'pizza' },
  { action: 'watched a movie', item: 'movie' },
  { action: 'tried a cookie', item: 'cookie' },
  { action: 'reviewed a restaurant meal', item: 'restaurant meal' },
  { action: 'tried a coffee', item: 'coffee' },
  { action: 'attended a party', item: 'party' }
];

const STATES = [1, 2, 3, 4, 5];
const GOOD_ADJECTIVES = ['good', 'amazing'];
const NEUTRAL_ADJECTIVES = ['okay'];
const BAD_ADJECTIVES = ['terrible', 'bad'];
const ALL_ADJECTIVES = ['good', 'amazing', 'okay', 'terrible', 'bad'];
const PERSONS = [
  'Alice',
  'Bob',
  'Chris',
  'Dani',
  'Emma',
  'Liam',
  'Noah',
  'Olivia',
  'Mia',
  'Lucas',
  'Sophia',
  'James',
  'Emily',
  'Daniel',
  'Sarah',
  'Michael',
  'Anna',
  'David',
  'Laura',
  'John',
  'Lisa',
  'Mark',
  'Julia',
  'Paul',
  'Tom',
  'Sam',
  'Peter'
];
// possible combo = 2 * 6 = 12
const GOOD_EMOJIS = [
  'grinning',
  'slightly_smiling_face',
  'smile',
  'relaxed',
  'laughing',
  'null'
];
// possible combo = 1 * 3 = 3
const NEUTRAL_EMOJIS = ['neutral_face', 'kissing', 'null'];

// possible combo = 2 * 6 = 12
const BAD_EMOJIS = [
  'pensive',
  'slightly_frowning_face',
  'weary',
  'white_frowning_face',
  'angry',
  'null'
];

const ALL_EMOJIS = [
  'angry',
  'grinning',
  'kissing',
  'laughing',
  'neutral_face',
  'pensive',
  'relaxed',
  'slightly_frowning_face',
  'slightly_smiling_face',
  'smile',
  'weary',
  'white_frowning_face'
];

const EMOJIS_MAPPING = {
  angry: '😀',
  grinning: '😃',
  kissing: '😃',
  laughing: '😃',
  neutral_face: '😃',
  pensive: '😃',
  relaxed: '🤐',
  slightly_frowning_face: '🥲',
  slightly_smiling_face: '🥲',
  smile: '🥲',
  weary: '🤕',
  white_frowning_face: '🤕',
  null: ''
};

export default {
  name: 'AppExperiment1',
  data() {
    return {
      trials: this.makeTrials(),
      COUNTRIES: COUNTRIES_LIST.map((c) => c.name),
      demographic: { age: null, gender: null, proficiency: null, country: null }
    };
  },

  methods: {
    makeTrials() {
      const combos = [];
      for (const adj of GOOD_ADJECTIVES) {
        for (const emoji of GOOD_EMOJIS) {
          combos.push({ adj, emoji });
        }
      }

      for (const adj of BAD_ADJECTIVES) {
        for (const emoji of BAD_EMOJIS) {
          combos.push({ adj, emoji });
        }
      }

      for (const emoji of NEUTRAL_EMOJIS) {
        for (const adj of NEUTRAL_ADJECTIVES) {
          combos.push({ adj, emoji });
        }
      }

      const shuffledCombos = _.shuffle(combos);
      const shuffledPersons = _.shuffle(PERSONS);

      const nMain = shuffledCombos.length;

      const attentionPositions = [
        Math.floor(nMain / 3),
        Math.floor((2 * nMain) / 3)
      ];

      const trials = [];

      for (let i = 0; i < nMain; i++) {
        // insert attention BEFORE this main trial
        if (attentionPositions.includes(i)) {
          trials.push({
            trialType: 'attention',
            attentionCheck: _.sample(STATES),
            person: _.sample(PERSONS), // can repeat
            context: _.sample(CONTEXTS),
            emoji: EMOJIS_MAPPING[_.sample(ALL_EMOJIS)],
            adj: _.sample(ALL_ADJECTIVES),
            inferredState: 0,
            inferredGoalState: 0,
            inferredGoalVelnceArousal: 0,
            inferredGoalAll: 0
          });
        }

        // main trial
        trials.push({
          trialType: 'trial',
          attentionCheck: null,
          person: shuffledPersons[i], // unique per main trial
          context: _.sample(CONTEXTS),
          adj: shuffledCombos[i].adj,
          emoji: EMOJIS_MAPPING[shuffledCombos[i].emoji],
          inferredState: 0,
          inferredGoalState: 0,
          inferredGoalVelnceArousal: 0,
          inferredGoalAll: 0
        });
      }

      return trials;
    }
  }
};
</script>

<style>
/* p {
  font-weight: bold;
} */

#trial-context {
  font-size: 20px;
}

#trial-sentence {
  font-size: 20px;
}

/* #trial-state {
  font-style: italic;
} */

#trial-emoji {
  font-size: 22px;
}

#debugging {
  font-size: 10px;
}

#question {
  font-style: bold;
}

/* #given {
  font-size: 16px;
} */
</style>
