<!-- experiment 3b: full interpretation, inferred emotion -->

<template>
  <Experiment title="rsa emoji experiment">
    <InstructionScreen :title="'Welcome, nice to see you!'">
      <p>Thank you for participating in our experiment!</p>
      <p>
        In this experiment, you will be asked to judge the emotions of a person
        who rated everyday experiences (like going to a restaurant) based on
        short descriptions.
      </p>
      <p>Click “Next” to read the instructions.</p>
    </InstructionScreen>

    <InstructionScreen :title="'Instruction'">
      <p>
        On each trial, you will see a person's short comment describing an
        experience (for example, a movie or a restaurant meal).
      </p>
      <p>
        Your task is to rate the person’s emotion based on this comment, in
        terms of two dimensions:
      </p>
      <ul>
        <li>
          <strong>Valence</strong>: how negative/unpleasant or positive/pleasant
          the emotion was, and
        </li>
        <li>
          <strong>Arousal</strong>: how calm/passive or intense/exciting the
          emotion was.
        </li>
      </ul>
      <p>Click “Next” to begin.</p>
    </InstructionScreen>

    <!-- We iterate over our experiment trials -->

    <!-- and display a screen with a slider rating task
             using the built-in SliderScreen component -->

    <Screen v-for="(trial, i) in trials" :key="i">
      <Slide>
        <p id="trial-context"></p>
        <p>
          {{ trial.person }} {{ trial.context.action }}. {{ trial.person }} sent
          this message to you:
        </p>
        <p v-if="trial.trialType === 'trial'">
          <strong>
            "The {{ trial.context.item }} was
            <span id="trial-state">
              {{ trial.adj
              }}<span v-if="trial.emoji !== ''" id="trial-emoji">
                {{ trial.emoji }}</span
              >.</span
            >"
          </strong>
        </p>
        <p v-else>
          <strong>
            "The {{ trial.context.item }} was
            <span id="trial-state">
              {{ trial.adj
              }}<span v-if="trial.emoji !== ''" id="trial-emoji">
                {{ trial.emoji }}</span
              >.</span
            >
            <p>
              For this trial, please select {{ trial.attentionCheckV }} on the
              scale for the first scale and {{ trial.attentionCheckA }} for the
              second."
            </p>
          </strong>
        </p>

        <p>
          How negative or positive do you think {{ trial.person }} felt about
          the {{ trial.context.item }}?
        </p>

        <!-- <p style="margin-top: 18px"><strong>Valence</strong></p> -->
        <RatingInput
          :count="9"
          :left="`${trial.person} felt very negative/unpleasant`"
          :right="`${trial.person} felt very positive/pleasant`"
          :response.sync="trial.inferredValence"
        />
        <p>How calm or arousing do you think that emotion was ?</p>
        <!-- <p style="margin-top: 18px"><strong>Arousal</strong></p> -->
        <RatingInput
          :count="9"
          left="The emotion was very calm/passive"
          right="The emotion was very arousing/exciting"
          :response.sync="trial.inferredArousal"
        />

        <button
          v-if="trial.inferredValence != 0 && trial.inferredArousal != 0"
          style="margin-top: 18px"
          @click="
            $magpie.measurements.context = trial.context.item;
            $magpie.measurements.adj = trial.adj;
            $magpie.measurements.emoji = trial.emoji;
            $magpie.measurements.inferredValence = trial.inferredValence;
            $magpie.measurements.inferredArousal = trial.inferredArousal;
            $magpie.measurements.trialType = trial.trialType;
            $magpie.measurements.attentionCheckV = trial.attentionCheckV;
            $magpie.measurements.attentionCheckA = trial.attentionCheckA;
            $magpie.measurements.isPassedAttention =
              trial.trialType === 'trial' ||
              (trial.attentionCheckV === trial.inferredValence &&
                trial.attentionCheckA === trial.inferredArousal);
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

    <!-- While setting your experiment mode to 'debug' in the magpie config
       this screen will show the results of the current experiment directly. Once you switch to directLink or prolific
       this screen will submit the results to your magpie backend -->
    <SubmitResultsScreen />
  </Experiment>
</template>

<script>
import _ from 'lodash';
// import { COUNTRIES_LIST } from '@/data/countryList';

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
  angry: '😠',
  grinning: '😀',
  kissing: '😗',
  laughing: '😆',
  neutral_face: '😐',
  pensive: '😔',
  relaxed: '☺️',
  slightly_frowning_face: '🙁',
  slightly_smiling_face: '🙂',
  smile: '😊',
  weary: '😩',
  white_frowning_face: '☹️',
  null: ''
};

export default {
  name: 'AppExperiment1',
  data() {
    return {
      trials: this.makeTrials()
    };
  },

  methods: {
    makeTrials() {
      const combos = [];

      for (const adj of GOOD_ADJECTIVES) {
        for (const emoji of GOOD_EMOJIS) combos.push({ adj, emoji });
      }
      for (const adj of BAD_ADJECTIVES) {
        for (const emoji of BAD_EMOJIS) combos.push({ adj, emoji });
      }
      for (const emoji of NEUTRAL_EMOJIS) {
        for (const adj of NEUTRAL_ADJECTIVES) combos.push({ adj, emoji });
      }

      const shuffledCombos = _.shuffle(combos).slice(0, 4);
      const shuffledPersons = _.shuffle(PERSONS).slice(0, 4);

      const nMain = shuffledCombos.length;

      const attentionPositions = [
        Math.floor(nMain / 3),
        Math.floor((2 * nMain) / 3)
      ];

      const trials = [];

      for (let i = 0; i < nMain; i++) {
        // attention trial
        if (attentionPositions.includes(i)) {
          trials.push({
            trialType: 'attention',
            attentionCheckA: _.sample(STATES),
            attentionCheckV: _.sample(STATES),
            person: _.sample(PERSONS),
            context: _.sample(CONTEXTS),
            emoji: EMOJIS_MAPPING[_.sample(ALL_EMOJIS)],
            adj: _.sample(ALL_ADJECTIVES),
            inferredArousal: 0,
            inferredValence: 0
          });
        }

        // main trial
        trials.push({
          trialType: 'trial',
          attentionCheckA: null,
          attentionCheckV: null,
          person: shuffledPersons[i], // unique per main trial
          context: _.sample(CONTEXTS),
          adj: shuffledCombos[i].adj,
          emoji: EMOJIS_MAPPING[shuffledCombos[i].emoji],
          inferredArousal: 0,
          inferredValence: 0
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

#ratingReminder {
  font-size: 14px;
  font-style: italic;
  color: gray;
}

/* #given {
  font-size: 16px;
} */
</style>
