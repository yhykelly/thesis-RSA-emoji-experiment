<!-- experiment 1b: prior emotion given state -->

<template>
  <Experiment title="rsa emoji experiment">
    <InstructionScreen :title="'Welcome, nice to see you!'">
      <p>Thank you for participating in our experiment!</p>
      <p>
        In this experiment, you will be asked to judge emotions based on ratings
        of everyday experiences.
      </p>
      <p>Click “Next” to read the instructions.</p>
    </InstructionScreen>

    <InstructionScreen :title="'Instruction'">
      <p>
        On each trial, you will see how a person genuinely felt about about a
        recent experience (for example, a movie or a meal).
      </p>
      <p>
        The person’s true quality judgement is given as an
        <strong>“X out of 5 stars”</strong> rating.
      </p>
      <p>
        Your task is to judge how the person felt about the experience based on
        that true quality judgement.
      </p>
      <p>
        On each trial, you will rate the person’s emotion in terms of two
        dimensions:
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

      <p></p>
      <p>Click “Next” to begin the task.</p>
    </InstructionScreen>

    <!-- We iterate over our experiment trials -->

    <!-- and display a screen with a slider rating task
             using the built-in SliderScreen component -->
    <Screen v-for="(trial, i) in trials" :key="i">
      <Slide>
        <p id="trial-context">
          <span v-if="trial.trialType === 'trial'">
            {{ trial.person }} {{ trial.context.action }} and experienced it as
            <strong id="trial-state">
              {{ trial.state }} out of 5 stars.
            </strong>
          </span>
          <span v-else
            >{{ trial.person }} {{ trial.context.action }}. For this trial, rate
            <strong>{{ trial.attentionCheckV }}</strong> for the first scale and
            <strong>{{ trial.attentionCheckA }}</strong> for the second.
          </span>
        </p>
        <!-- <p></p>
        <p>On a scale from 1 to 9,</p> -->
        <p>
          How negative or positive do you think {{ trial.person }} felt about
          the {{ trial.context.item }}?
        </p>

        <!-- <p style="margin-top: 18px"><strong>Valence</strong></p> -->
        <RatingInput
          :count="9"
          :left="`${trial.person} felt very negative/unpleasant`"
          :right="`${trial.person} felt very positive/pleasant`"
          :response.sync="trial.valence"
        />
        <p>How calm or arousing do you think that emotion was ?</p>
        <!-- <p style="margin-top: 18px"><strong>Arousal</strong></p> -->
        <RatingInput
          :count="9"
          left="The emotion was very calm/passive"
          right="The emotion was very arousing/exciting"
          :response.sync="trial.arousal"
        />

        <button
          v-if="trial.arousal != 0 && trial.valence != 0"
          style="margin-top: 18px"
          @click="
            $magpie.measurements.context = trial.context.item;
            $magpie.measurements.state = trial.state;
            $magpie.measurements.valence = trial.valence;
            $magpie.measurements.arousal = trial.arousal;
            $magpie.measurements.trialType = trial.trialType;
            $magpie.measurements.attentionCheckV = trial.attentionCheckV;
            $magpie.measurements.attentionCheckA = trial.attentionCheckA;
            $magpie.measurements.isPassedAttention =
              trial.trialType === 'trial' ||
              (trial.attentionCheckV === trial.valence &&
                trial.attentionCheckA === trial.arousal);
            $magpie.saveAndNextScreen();
          "
        >
          Next
        </button>
      </Slide>
    </Screen>

    <!-- This screen will ask some optional questions about the
           participant's background, like age, gender etc. -->
    <PostTestScreen>
      <!-- <template #default="{ measurements }">

    <p>Which best describes you?</p>
    <select v-model="measurements.gender">
      <option value="">Prefer not to say</option>
      <option value="male">Man</option>
      <option value="female">Woman</option>
      <option value="other">Another identity</option>
    </select>
  </template> -->
    </PostTestScreen>

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
  'Lucas'
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
    shufffleStateList(STATES) {
      const first = _.shuffle(STATES);
      let second = _.shuffle(STATES);

      while (first[first.length - 1] === second[0]) {
        second = _.shuffle(STATES);
      }

      return first.concat(second);
    },

    makeTrials() {
      const n = 2 * STATES.length;
      const attentionPositions = [Math.floor(n / 3), Math.floor((2 * n) / 3)];
      const mainStates = this.shufffleStateList(STATES);
      const shuffledPersons = _.shuffle(PERSONS);

      const trials = [];

      for (let i = 0; i < n; i++) {
        if (attentionPositions.includes(i)) {
          trials.push({
            trialType: 'attention',
            attentionCheckV: _.sample(STATES),
            attentionCheckA: _.sample(STATES),
            person: _.sample(PERSONS),
            context: _.sample(CONTEXTS),
            state: _.sample(STATES),
            arousal: 0,
            valence: 0
          });
        }

        trials.push({
          trialType: 'trial',
          attentionCheckV: null,
          attentionCheckA: null,
          person: shuffledPersons[i],
          context: _.sample(CONTEXTS),
          state: mainStates[i],
          arousal: 0,
          valence: 0
        });
      }

      return trials;
    }
  }
};
</script>

<style>
/* #trial-context {
  font-size: 20px;
} */

/* #trial-state {
  color: #db153b;
} */

#debugging {
  font-size: 10px;
}
</style>
