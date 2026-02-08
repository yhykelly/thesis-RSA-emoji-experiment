<!-- experiment 2: emoji semantics -->

<template>
  <Experiment title="rsa emoji experiment">
    <InstructionScreen :title="'Welcome!'">
      <p>Thank you for participating in our experiment!</p>
      <p>
        In this experiment, you will be asked to judge emotions expressed by
        emojis.
      </p>
      <p>The experiment takes around 5 minutes to complete.</p>
      <p>Click “Next” to read the instructions.</p>
    </InstructionScreen>

    <InstructionScreen :title="'Instruction'">
      <p>On each trial, you will see a common emoji.</p>
      <p>
        Your task is to judge the emotion you associate with the emoji face
        shown.
      </p>
      <p></p>
      <p>
        Please rely on your intuition and not worry about whether you use the
        emojis in your daily life, or how they might be used in different
        subcultures. There are no right or wrong answers.
      </p>
      <p>Click “Next” to begin.</p>
    </InstructionScreen>

    <!-- We iterate over our experiment trials -->

    <!-- and display a screen with a slider rating task
             using the built-in SliderScreen component -->

    <Screen v-for="(trial, i) in trials" :key="i">
      <Slide>
        <p>A person sent the following emoji to you.</p>
        <p>
          You know nothing about the context, but that they wanted to express
          their emotions with this emoji:
        </p>
        <p v-if="!trial.isTrial">
          For this trial, please set {{ trial.trialValence }} for the first
          scale and {{ trial.trialArousal }} for the second
        </p>
        <img :src="`img/${trial.emoji}.png`" style="width: 100px" />
        <p>How negative or positive do you think this person felt?</p>

        <RatingInput
          :count="9"
          left="The person felt very negative/unpleasant"
          right="The person felt very positive/pleasant"
          :response.sync="trial.valence"
        />

        <p>How calm or intense do you think the emotion was?</p>

        <RatingInput
          :count="9"
          left="The emotion was very calm/passive"
          right="The emotion was very intense/exciting"
          :response.sync="trial.arousal"
        />

        <p id="debugging">
          """"debug use only """" arousal = {{ trial.arousal }} valence =
          {{ trial.valence }}
        </p>

        <button
          v-if="trial.arousal != 0 && trial.valence != 0"
          style="margin-top: 18px"
          :disabled="trial.arousal === 0 || trial.valence === 0"
          @click="
            $magpie.measurements.emoji = trial.emoji;
            $magpie.measurements.arousal = trial.arousal;
            $magpie.measurements.valence = trial.valence;
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
    >
    <Screen> After answering the question, click "Next" to submit your results. </Screen>
    </PostTestScreen> -->

    <!-- While setting your experiment mode to 'debug' in the magpie config
       this screen will show the results of the current experiment directly. 
       Once you switch to directLink or prolific
       this screen will submit the results to your magpie backend -->
    <!-- <DebugResultsScreen /> -->
    <SubmitResultsScreen />
  </Experiment>
</template>

<script>
import _ from 'lodash';
import { COUNTRIES_LIST } from '@/data/countryList';

const EMOJIS = [
  'angry',
  'grinning'
  // 'kissing',
  // 'laughing',
  // 'neutral_face',
  // 'pensive'
  // 'relaxed',
  // 'slightly_frowning_face',
  // 'slightly_smiling_face',
  // 'smile',
  // 'weary',
  // 'white_frowning_face'
];

const STATES = [1, 2, 3, 4, 5];

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
      const emojiTrials = _.shuffle(EMOJIS); // main stimuli (10)
      const n = emojiTrials.length;

      // const attentionPositions = new Set([
      //   Math.floor(n / 3),
      //   Math.floor((2 * n) / 3)
      // ]);

      const attentionPosition = Math.floor((2 * n) / 3);

      const trials = [];

      for (let i = 0; i < n; i++) {
        if (attentionPosition === i) {
          trials.push({
            isTrial: false,
            trialValence: _.sample(STATES), // convenient sampling from 1-5
            trialArousal: _.sample(STATES),
            emoji: _.sample(EMOJIS), // resample from the pool
            arousal: 0,
            valence: 0
          });
        }

        trials.push({
          isTrial: true,
          trialValence: 0, // convenient sampling from 1-5
          trialArousal: 0,
          emoji: emojiTrials[i],
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
#debugging {
  font-size: 10px;
}
</style>
