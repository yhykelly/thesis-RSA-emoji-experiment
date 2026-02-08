<!-- experiment 1b: prior emotion given state -->

<template>
  <Experiment title="rsa emoji experiment">
    <InstructionScreen :title="'Welcome, nice to see you!'">
      <p>Thank you for participating in our experiment!</p>
      <p>
        In this experiment, you will be asked to judge emotions based on ratings
        of everyday experiences.
      </p>
      <p>The experiment takes about 5 minutes to complete.</p>
      <p>Click “Next” to read the instructions.</p>
    </InstructionScreen>

    <InstructionScreen :title="'Instruction'">
      <p>
        On each trial, you will see a short description of a situation in which
        a person rated an experience (for example, a movie or a meal).
      </p>
      <p>
        Your task is to judge how the person felt about the experience based on
        the rating they gave.
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
          <span v-if="trial.isTrial">
            {{ trial.person }} {{ trial.context.action }} and rated it
            <strong id="trial-state">
              {{ trial.state }} out of 5 stars.
            </strong>
          </span>
          <span v-else
            >{{ trial.person }} {{ trial.context.action }}. For this trial, rate
            <strong>{{ trial.trialValence }}</strong> for the first scale and
            <strong>{{ trial.trialArousal }}</strong> for the second.
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
        <p>How calm or intense do you think that emotion was ?</p>
        <!-- <p style="margin-top: 18px"><strong>Arousal</strong></p> -->
        <RatingInput
          :count="9"
          left="The emotion was very calm/passive"
          right="The emotion was very intense/exciting"
          :response.sync="trial.arousal"
        />

        <p id="debugging">
          """DEBUG""" arousal is <strong>{{ trial.arousal }}</strong>
        </p>

        <button
          v-if="trial.arousal != 0 && trial.valence != 0"
          style="margin-top: 18px"
          :disabled="trial.arousal === 0 || trial.valence === 0"
          @click="
            $magpie.measurements.context = trial.context.item;
            $magpie.measurements.stars = trial.stars;
            $magpie.measurements.valence = trial.valence;
            $magpie.measurements.arousal = trial.arousal;
            $magpie.measurements.isTrial = trial.isTrial;
            $magpie.measurements.isPassedAttention =
              trial.isTrial ||
              (trial.trialValence === trial.valence &&
                trial.trialArousal === trial.arousal);
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
            >3. How do you rate your English proficiency?
            <RatingInput
              left="Complete Beginner"
              right="Native Speaker"
              :response.sync="demographic.proficiency"
            />
          </label>
        </p>
        <p>
          <label
            >4. How frequently do you use or encounter emojis in your daily
            life?
            <RatingInput
              left="Never"
              right="Almost constantly"
              :response.sync="demographic.emojiUsage"
            />
          </label>
        </p>
        <p>
          <label
            >5. Where do you currently live?
            <DropdownInput
              :options="COUNTRIES"
              :response.sync="demographic.country"
            />
          </label>
        </p>
        <p>Further comments (optional)</p>
        <TextareaInput :response.sync="demographic.comments" />

        <p id="debugging">
          """"debug use only"""" age = {{ demographic.age }} gender =
          {{ demographic.gender }} prof = {{ demographic.proficiency }} country
          = {{ demographic.country }}
        </p>

        <button
          :disabled="
            demographic.country === null ||
            demographic.country === 'Select country' ||
            demographic.age < 18 ||
            demographic.gender === null ||
            demographic.proficiency === null ||
            demographic.emojiUsage === null
          "
          style="margin-top: 18px"
          @click="
            $magpie.addExpData({
              age: demographic.age,
              gender: demographic.gender,
              proficiency: demographic.proficiency,
              country: demographic.country,
              emojiUsage: demographic.emojiUsage,
              comments: demographic.comments
            });
            $magpie.saveAndNextScreen();
          "
        >
          Submit results
        </button>
      </Slide>
    </Screen>

    <!-- This screen will ask some optional questions about the
           participant's background, like age, gender etc. -->
    <!-- <PostTestScreen
      :age="false"
      :education="false"
      :gender="false"
      :languages="false"
    > -->
    <!-- <template #default="{ measurements }">

    <p>Which best describes you?</p>
    <select v-model="measurements.gender">
      <option value="">Prefer not to say</option>
      <option value="male">Man</option>
      <option value="female">Woman</option>
      <option value="other">Another identity</option>
    </select>
  </template> -->
    <!-- </PostTestScreen> -->

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

const PERSONS = ['Alice', 'Bob', 'Chris', 'Dani'];

export default {
  name: 'AppExperiment1',
  data() {
    return {
      trials: this.makeTrials(5),
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
            trialValence: _.sample(STATES), // convenient sampling from 1-5
            trialArousal: _.sample(STATES),
            person: _.sample(PERSONS),
            context: _.sample(CONTEXTS),
            state: _.sample(STATES),
            arousal: 0,
            valence: 0
          });
        }

        trials.push({
          isTrial: true,
          trialValence: 0,
          trialArousal: 0,
          person: _.sample(PERSONS),
          context: _.sample(CONTEXTS),
          state: _.sample(STATES),
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
