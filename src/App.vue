<template>
  <Experiment title="rsa emoji experiment">
    <InstructionScreen :title="'Welcome!'">
      <p>Thank you for participating in our experiment!</p>
      <p>
        In this study, we are calibrating a scale for emojis to understand how
        people perceive the emotions expressed by emojis.
      </p>
      <p>The experiment takes around 5 minutes to complete.</p>
      <p>Click “Next” to learn more about the instruction.</p>
    </InstructionScreen>

    <InstructionScreen :title="'Instruction'">
      <p>
        In the following section, you will see a series of emojis from Apple iOS
        system.
      </p>
      <p>
        We are interested in the feelings you associate with each emoji,
        therefore, please report your
        <strong> direct perception of the emoji </strong> itself.
      </p>
      <p>
        Do not worry about whether you use the emojis in your daily life or how
        they might be used in different subcultures — We are interested in your
        intuitive interpretation of face shown.
      </p>
      <p>Click “Next” to begin.</p>
    </InstructionScreen>

    <!-- We iterate over our experiment trials -->

    <!-- and display a screen with a slider rating task
             using the built-in SliderScreen component -->

    <Screen v-for="(trial, i) in trials" :key="i">
      <Slide>
        <p>A person sent the following emoji to you:</p>
        <img :src="`img/${trial.emoji}.png`" style="width: 100px" />
        <p>
          You know nothing about the context, but that they wanted to express
          their emotions with this emoji.
        </p>
        <p>
          On a scale from 1 to 9, rate how you think the person would feel in
          his/her emotion:
        </p>
        <!-- <p style="margin-top: 18px"><strong>Arousal</strong></p> -->
        <RatingInput
          :count="9"
          left="The person felt very calm/relaxed"
          right="The person felt very aroused/excited"
          :response.sync="trial.arousal"
        />
        <p>
          On a scale from 1 to 9, rate how intense you think the person's
          emotion was:
        </p>
        <!-- <p style="margin-top: 18px"><strong>Valence</strong></p> -->
        <RatingInput
          :count="9"
          left="The person felt very unhappy"
          right="The peson felt very happy"
          :response.sync="trial.valence"
        />

        <p>""""debug use only"""" {{ trial.arousal }}</p>
        <p>""""debug use only"""" {{ trial.valence }}</p>

        <button
          v-if="trial.arousal != 0 && trial.valence != 0"
          style="margin-top: 18px"
          :disabled="trial.arousal === 0 || trial.valence === 0"
          @click="
            $magpie.measurements.emoji = trial.emoji;
            $magpie.measurements.arousal = trial.arousal;
            $magpie.measurements.valence = trial.valence;
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
    </PostTestScreen>

    <!-- While setting your experiment mode to 'debug' in the magpie config
       this screen will show the results of the current experiment directly. 
       Once you switch to directLink or prolific
       this screen will submit the results to your magpie backend -->
    <DebugResultsScreen />
    <SubmitResultsScreen />
  </Experiment>
</template>

<script>
import _ from 'lodash';
import { COUNTRIES_LIST } from '@/data/countryList';

const EMOJIS = [
  'angry',
  'grinning',
  'kissing'
  // 'laughing',
  // 'neutral_face',
  // 'pensive',
  // 'relaxed',
  // 'slightly_frowning_face',
  // 'slightly_smiling_face',
  // 'smile',
  // 'weary',
  // 'white_frowning_face'
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
      return _.shuffle(EMOJIS).map((e) => ({
        emoji: e,
        arousal: 0,
        valence: 0
      }));
    }
  }
};
</script>
