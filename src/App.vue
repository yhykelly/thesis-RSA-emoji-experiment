<!-- experiment 1a: literal semantics -->

<template>
  <Experiment title="rsa emoji experiment">
    <InstructionScreen :title="'Welcome, nice to see you!'">
      <p>Thank you for participating in our experiment!</p>
      <p>
        In this study, we are calibrating a scale for feedback words to
        understand how people use different adjectives to describe ratings.
      </p>
      <p>The experiment takes around 5 minutes to complte.</p>
      <p>Click “Next” to learn more about the situation.</p>
    </InstructionScreen>

    <InstructionScreen :title="'Situation'">
      <p>
        In the following trials, you will see a series of feedback on different
        experiences.
      </p>
      <p>Your task is to provide your intuitive judgment on these feedbacks.</p>
      <p>Click “Next” to begin.</p>
    </InstructionScreen>

    <!-- We iterate over our experiment trials -->

    <!-- and display a screen with a slider rating task
             using the built-in SliderScreen component -->

    <Screen v-for="(trial, i) in trials" :key="i">
      <Slide>
        <p id="trial-context">
          <strong>{{ trial.person }} {{ trial.context.action }} </strong>
        </p>
        <!-- <p id="trial-state">
          
        </p> -->
        <p>
          Given that {{ trial.person }} rated the {{ trial.context.item }} with
          <strong> {{ trial.state }} out of 5 stars</strong>,
        </p>
        <p>
          how applicable is the description
          <span id="trial-sentence">
            "{{ trial.person }} thinks that the {{ trial.context.item }} was
            <strong id="trial-state">{{ trial.adj }}</strong
            >" ?
          </span>
        </p>

        <!-- <p>
          
        </p> -->

        <p>DEBUG applicability: {{ trial.applicability }}</p>

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

        <p>""""debug use only"""" age = {{ demographic.age }}</p>
        <p>""""debug use only"""" gender = {{ demographic.gender }}</p>
        <p>""""debug use only"""" prof = {{ demographic.proficiency }}</p>
        <p>""""debug use only"""" country = {{ demographic.country }}</p>

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
  { action: 'attended a concert.', item: 'concert' },
  { action: 'tried a pizza.', item: 'pizza' },
  { action: 'watched a movie.', item: 'movie' },
  { action: 'tried a cookie.', item: 'cookie' },
  { action: 'reviewed a restaurant meal.', item: 'restaurant meal' },
  { action: 'tried a coffee.', item: 'coffee' },
  { action: 'attened a party.', item: 'party' }
];

const STATES = [1, 2, 3, 4, 5];

const ADJECTIVES = ['terrible', 'bad', 'okay', 'good', 'amazing'];

const PERSONS = ['Alice', 'Bob', 'Chris', 'Dani'];

export default {
  name: 'AppExperiment1',
  data() {
    return {
      trials: this.makeTrials(2),
      COUNTRIES: COUNTRIES_LIST.map((c) => c.name),
      demographic: { age: null, gender: null, proficiency: null, country: null }
    };
  },
  methods: {
    makeTrials(n) {
      return _.times(n, () => ({
        person: _.sample(PERSONS),
        context: _.sample(CONTEXTS),
        state: _.sample(STATES),
        adj: _.sample(ADJECTIVES),
        applicability: 0
      }));
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
}
</style>
