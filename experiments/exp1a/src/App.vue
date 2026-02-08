<!-- experiment 1a: literal semantics -->

<template>
  <Experiment title="rsa emoji experiment">
    <InstructionScreen :title="'Welcome, nice to see you!'">
      <p>Thank you for participating in our experiment!</p>
      <p>
        In this experiment, you will be asked to judge how well short
        descriptions apply to ratings of everyday experiences.
      </p>
      <p>The experiment takes around 5 minutes to complete.</p>
      <p>Click “Next” to read the instructions.</p>
    </InstructionScreen>

    <InstructionScreen :title="'Instruction'">
      <p>
        On each trial, you will see a short description of a situation in which
        a person rated an experience.
      </p>
      <p>You will then see a sentence describing the experience.</p>
      <p>
        Your task is to judge how applicable this description is given the
        rating.
      </p>
      <p>Click “Next” to begin.</p>
    </InstructionScreen>

    <!-- We iterate over our experiment trials -->

    <!-- and display a screen with a slider rating task
             using the built-in SliderScreen component -->

    <Screen v-for="(trial, i) in trials" :key="i">
      <Slide>
        <p id="given">
          {{ trial.person }} {{ trial.context.action }} and rated it
          <strong> {{ trial.state }} out of 5 stars</strong>
        </p>

        <p id="given">How applicable is the description?</p>

        <p v-if="trial.isTrial" id="trial-sentence">
          "{{ trial.person }} thought that the {{ trial.context.item }} was
          <strong id="trial-state">{{ trial.adj }}.</strong>"
        </p>

        <p v-else id="trial-sentence">
          "{{ trial.person }} thought that the {{ trial.context.item }} was
          <strong id="trial-state">{{ trial.adj }}.</strong>
          For this trial, please select {{ trial.trialState }} on the scale."
        </p>

        <p id="debugging">DEBUG applicability: {{ trial.applicability }}</p>

        <RatingInput
          :count="9"
          left="not applicable at all"
          right="very applicable"
          :response.sync="trial.applicability"
        />

        <button
          :disabled="trial.applicability == null || trial.applicability === 0"
          style="margin-top: 18px"
          @click="
            $magpie.measurements.context = trial.context.item;
            $magpie.measurements.state = trial.state;
            $magpie.measurements.adj = trial.adj;
            $magpie.measurements.applicability = trial.applicability;
            $magpie.measurements.isTrial = trial.isTrial;
            $magpie.measurements.trialState = trial.trialState;
            $magpie.measurements.isPassedAttention =
              trial.isTrial || trial.trialState === trial.state;
            $magpie.saveAndNextScreen();
          "
        >
          Next
        </button>
      </Slide>
    </Screen>

    <Screen>
      <Slide>
        <p>1. What is your age?</p>
        <p>
          <label>
            <input v-model="demographic.age" type="number" max="110" min="18" />
          </label>
        </p>
        <p>
          <label
            >2. What is your gender?
            <DropdownInput
              :options="['', 'female', 'male', 'other', 'prefer not to say']"
              :response.sync="demographic.gender"
            />
          </label>
        </p>
        <p>
          <label
            >3. On a scale from 1 to 7, how do you rate your English
            proficiency?
            <RatingInput
              left="Complete Beginner"
              right="Native Speaker"
              :response.sync="demographic.proficiency"
            />
          </label>
        </p>
        <p>
          <label
            >4. Where do you currently live?
            <DropdownInput
              :options="COUNTRIES"
              :response.sync="demographic.country"
            />
          </label>
        </p>

        <p id="debugging">""""debug use only"""" age = {{ demographic.age }}</p>
        <p id="debugging">
          """"debug use only"""" gender = {{ demographic.gender }}
        </p>
        <p id="debugging">
          """"debug use only"""" prof = {{ demographic.proficiency }}
        </p>
        <p id="debugging">
          """"debug use only"""" country = {{ demographic.country }}
        </p>

        <button
          :disabled="
            demographic.country === null ||
            demographic.country === 'Select country' ||
            demographic.age < 18 ||
            demographic.gender === null ||
            demographic.proficiency === null
          "
          style="margin-top: 18px"
          @click="
            $magpie.addExpData({
              age: demographic.age,
              gender: demographic.gender,
              proficiency: demographic.proficiency,
              country: demographic.country
            });
            $magpie.saveAndNextScreen();
          "
        >
          Next
        </button>
      </Slide>
    </Screen>

    <!-- This screen will ask some optional questions about the
           participant's background, like age, gender etc. -->
    <PostTestScreen
      :age="false"
      :education="false"
      :gender="false"
      :languages="false"
    >
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
  { action: 'attened a party', item: 'party' }
];

const STATES = [1, 2, 3, 4, 5];

const ADJECTIVES = ['terrible', 'bad', 'okay', 'good', 'amazing'];

const PERSONS = ['Alice', 'Bob', 'Chris', 'Dani'];

export default {
  name: 'AppExperiment1',
  data() {
    return {
      trials: this.makeTrials(6),
      COUNTRIES: COUNTRIES_LIST.map((c) => c.name),
      demographic: { age: null, gender: null, proficiency: null, country: null }
    };
  },

  methods: {
    makeTrials(n) {
      const attentionPositions = [Math.floor(n / 3), Math.floor((2 * n) / 3)];

      const trials = [];

      for (let i = 0; i < n; i++) {
        if (attentionPositions.includes(i)) {
          trials.push({
            isTrial: false,
            trialState: _.sample(STATES),
            person: _.sample(PERSONS),
            context: _.sample(CONTEXTS),
            state: _.sample(STATES),
            adj: _.sample(ADJECTIVES),
            applicability: 0
          });
        }

        trials.push({
          isTrial: true,
          trialState: 0,
          person: _.sample(PERSONS),
          context: _.sample(CONTEXTS),
          state: _.sample(STATES),
          adj: _.sample(ADJECTIVES),
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
  font-style: italic;
  /* font-size: 20px; */
}

#debugging {
  font-size: 10px;
}

/* #given {
  font-size: 16px;
} */
</style>
