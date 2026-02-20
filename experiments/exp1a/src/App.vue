<!-- experiment 1a: literal semantics -->

<template>
  <Experiment title="rsa emoji experiment">
    <InstructionScreen :title="'Welcome, nice to see you!'">
      <p>Thank you for participating in our experiment!</p>
      <p>
        In this experiment, you will be asked to judge how well short verbal
        descriptions apply to star ratings of everyday experiences (like going
        to a restaurant).
      </p>
      <p>Click “Next” to read the instructions.</p>
    </InstructionScreen>

    <InstructionScreen :title="'Instruction'">
      <p>
        On each trial, you will see a short description of a situation in which
        a person rated an experience (for example, a movie or a restaurant
        meal).
      </p>
      <p>
        The rating is presented as <strong>“X out of 5 stars”</strong>, where 1
        star represents the lowest possible rating and 5 stars represents the
        highest possible rating.
      </p>

      <p>You will then see a sentence describing the experience.</p>
      <p>
        Your task is to judge how applicable this sentence is given
        the star rating.
      </p>
      <p>Click “Next” to begin.</p>
    </InstructionScreen>

    <!-- We iterate over our experiment trials -->

    <!-- and display a screen with a slider rating task
             using the built-in SliderScreen component -->

    <Screen v-for="(trial, i) in trials" :key="i">
      <Slide>
        <p id="given">
          {{ trial.person }} {{ trial.context.action }} and experienced it as
          <strong> {{ trial.state }} out of 5 stars.</strong>
        </p>
        <p id="ratingReminder">
          Reminder: 1 star is the lowest possible rating and 5 stars is the
          highest possible rating.
        </p>

        <p id="given">How applicable is the description?</p>

        <p v-if="trial.trialType === 'trial'" id="trial-sentence">
          <strong
            >"{{ trial.person }} thought that the {{ trial.context.item }} was
            <span id="trial-state">{{ trial.adj }}.</span>"</strong
          >
        </p>

        <p v-else id="trial-sentence">
          <strong
            >"{{ trial.person }} thought that the {{ trial.context.item }} was
            <span id="trial-state">{{ trial.adj }}.</span>
            For this trial, please select {{ trial.attentionCheck }} on the
            scale."</strong
          >
        </p>

        <RatingInput
          :count="9"
          left="not applicable at all"
          right="very applicable"
          :response.sync="trial.applicability"
        />

        <button
          v-if="trial.applicability != null && trial.applicability !== 0"
          style="margin-top: 18px"
          @click="
            $magpie.measurements.context = trial.context.item;
            $magpie.measurements.state = trial.state;
            $magpie.measurements.adj = trial.adj;
            $magpie.measurements.applicability = trial.applicability;
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
const ADJECTIVES = ['terrible', 'bad', 'okay', 'good', 'amazing'];
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
  'Tom'
];

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
      // 1) Build 5 × 5 combo list (25 trials)
      const combos = [];
      for (const state of STATES) {
        for (const adj of ADJECTIVES) {
          combos.push({ state, adj });
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
            state: _.sample(STATES),
            adj: _.sample(ADJECTIVES),
            applicability: 0
          });
        }

        // main trial
        trials.push({
          trialType: 'trial',
          attentionCheck: null,
          person: shuffledPersons[i], // unique per main trial
          context: _.sample(CONTEXTS),
          state: shuffledCombos[i].state,
          adj: shuffledCombos[i].adj,
          applicability: 0
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
  font-size: 19px;
}

#trial-state {
  font-style: italic;
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
