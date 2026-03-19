<!-- experiment 3a: full interpretation, inferred state and inferred goals -->

<template>
  <Experiment title="rsa emoji experiment 3a">
    <InstructionScreen :title="'Welcome, nice to see you!'">
      <p>Thank you for participating in our experiment!</p>
      <p>
        In this experiment, you will be asked to judge ratings of everyday
        experiences based on short descriptions.
      </p>
      <p>Click “Next” to read the instructions.</p>
    </InstructionScreen>

    <InstructionScreen :title="'Instruction'">
      <p>
        On each trial, you will see a person's short comment describing an
        experience (for example, a movie or a restaurant meal).
      </p>
      <p>Your task is to judge:</p>
      <ul>
        <li>
          How the person has truly liked it (on a scale from 1
          to 5 stars); and
        </li>
        <li>What the person has aimed to communicate through the message.</li>
      </ul>
      <p>Click “Next” to begin.</p>
    </InstructionScreen>

    <Screen v-for="(trial, i) in trials" :key="i">
      <!-- ATTENTION CHECK -->
      <Slide>
        <div v-if="trial.trialType === 'attention'">
          <p id="given">
            {{ trial.person.name }} {{ trial.context.action }}.
            {{ trial.person.name }} sent this message to you:
          </p>

          <p id="trial-sentence">
            <strong>
              "The {{ trial.context.item }} was
              <span id="trial-state">
                {{ trial.adj
                }}<span v-if="trial.emoji !== ''" id="trial-emoji">
                  {{ trial.emoji }}</span
                >.</span
              >
              For this trial, please select {{ trial.attentionCheck }} on the
              scale."
            </strong>
          </p>

          <p>
            Out of five stars, how do you think {{ trial.person.name }} would
            have actually rated the experience?
          </p>

          <RatingInput
            :count="5"
            left="1 star"
            right="5 stars"
            :response.sync="trial.inferredState"
          />
          <button
            v-if="
              trial.inferredState != null &&
              trial.inferredState !== 0 
            "
            style="margin-top: 18px"
            @click="
              $magpie.measurements.context = trial.context.item;
              $magpie.measurements.emoji = trial.emoji;
              $magpie.measurements.adj = trial.adj;
              $magpie.measurements.trialType = trial.trialType;
              $magpie.measurements.attentionCheck = trial.attentionCheck;
              $magpie.measurements.inferredState = trial.inferredState;
              $magpie.measurements.goalState = trial.goalState;
              $magpie.measurements.goalEmotion = trial.goalEmotion;
              $magpie.measurements.isPassedAttention =
                trial.trialType === 'trial' ||
                trial.attentionCheck === trial.inferredState;
              $magpie.saveAndNextScreen();
            "
          >
            Next
          </button>
        </div>

        <!-- MAIN TRIAL -->

        <!-- v-if="trial.page === 'experience'" -->
        <div v-else>
          <div>
            <p id="given">
              {{ trial.person.name }} {{ trial.context.action }}.
              {{ trial.person.name }} sent this message to you.
            </p>

            <p id="trial-sentence">
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
          </div>
          <p>What do you think of {{ trial.person.name }}'s utterance?</p>

          <p>
            <strong>
              On a scale from 1 star to 5 stars, how much did
              {{ trial.person.name }} truly like the {{ trial.context.item }}?
            </strong>
          </p>

          <RatingInput
            :count="5"
            left="1 star"
            right="5 stars"
            :response.sync="trial.inferredState"
          />

          <!-- <p id="ratingReminder">
            Reminder: 1 star is the lowest possible rating and 5 stars is the
            highest possible rating.
          </p> -->

          <strong>
            How likely did {{ trial.person.name }} want to communicate how much
            {{ trial.person.pronoun }} liked the {{ trial.context.item }}?
          </strong>
          <RatingInput
            :count="9"
            left="very unlikely"
            right="very likely"
            :response.sync="trial.goalState"
          />

          <strong>
            How likely did {{ trial.person.name }} want to communicate the
            emotion that {{ trial.person.pronoun }} felt about the
            {{ trial.context.item }}?
          </strong>
          <RatingInput
            :count="9"
            left="very unlikely"
            right="very likely"
            :response.sync="trial.goalEmotion"
          />

          <button
            v-if="
              trial.inferredState != null &&
              trial.inferredState !== 0 &&
              trial.goalState != null &&
              trial.goalState !== 0 &&
              trial.goalEmotion != null &&
              trial.goalEmotion !== 0
            "
            style="margin-top: 18px"
            @click="
              $magpie.measurements.context = trial.context.item;
              $magpie.measurements.emoji = trial.emoji;
              $magpie.measurements.adj = trial.adj;
              $magpie.measurements.trialType = trial.trialType;
              $magpie.measurements.attentionCheck = trial.attentionCheck;
              $magpie.measurements.inferredState = trial.inferredState;
              $magpie.measurements.goalState = trial.goalState;
              $magpie.measurements.goalEmotion = trial.goalEmotion;
              $magpie.measurements.isPassedAttention =
                trial.trialType === 'trial' ||
                trial.attentionCheck === trial.inferredState;
              $magpie.saveAndNextScreen();
            "
          >
            Next
          </button>
        </div>
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
  { name: 'Alice', pronoun: 'she' },
  { name: 'Bob', pronoun: 'he' },
  { name: 'Chris', pronoun: 'he' },
  { name: 'Dani', pronoun: 'she' },
  { name: 'Emma', pronoun: 'she' },
  { name: 'Liam', pronoun: 'he' },
  { name: 'Noah', pronoun: 'he' },
  { name: 'Olivia', pronoun: 'she' },
  { name: 'Mia', pronoun: 'she' },
  { name: 'Lucas', pronoun: 'he' },
  { name: 'Sophia', pronoun: 'she' },
  { name: 'James', pronoun: 'he' },
  { name: 'Emily', pronoun: 'she' },
  { name: 'Daniel', pronoun: 'he' },
  { name: 'Sarah', pronoun: 'she' },
  { name: 'Michael', pronoun: 'he' },
  { name: 'Anna', pronoun: 'she' },
  { name: 'David', pronoun: 'he' },
  { name: 'Laura', pronoun: 'she' },
  { name: 'John', pronoun: 'he' },
  { name: 'Lisa', pronoun: 'she' },
  { name: 'Mark', pronoun: 'he' },
  { name: 'Julia', pronoun: 'she' },
  { name: 'Paul', pronoun: 'he' },
  { name: 'Tom', pronoun: 'he' },
  { name: 'Sam', pronoun: 'he' },
  { name: 'Peter', pronoun: 'he' }
]; // 27 names

const GOOD_EMOJIS = [
  'grinning',
  'slightly_smiling_face',
  'smile',
  'relaxed',
  'laughing',
  'null'
];

const NEUTRAL_EMOJIS = ['neutral_face', 'kissing', 'null'];

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
  name: 'AppExperiment3a',
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
      } // 6 * 2 = 12 combos
      for (const adj of BAD_ADJECTIVES) {
        for (const emoji of BAD_EMOJIS) combos.push({ adj, emoji });
      } // 3 * 1 = 3 combos
      for (const emoji of NEUTRAL_EMOJIS) {
        for (const adj of NEUTRAL_ADJECTIVES) combos.push({ adj, emoji });
      } // 6 * 2 12 combos

      const shuffledCombos = _.shuffle(combos).slice(0, 15); // total 27 combos, for checking here sliced until 9
      const shuffledPersons = _.shuffle(PERSONS).slice(0, 15);

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
            attentionCheck: _.sample(STATES),
            person: _.sample(PERSONS),
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

#ratingReminder {
  font-size: 14px;
  font-style: italic;
  color: gray;
}

/* #given {
  font-size: 16px;
} */
</style>
