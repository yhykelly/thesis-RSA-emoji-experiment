<!-- experiment 2: emoji semantics -->

<template>
  <Experiment title="rsa emoji experiment">
    <InstructionScreen :title="'Welcome!'">
      <p>Thank you for participating in our experiment!</p>
      <p>
        In this experiment, you will be asked to judge emotions expressed by
        emojis.
      </p>
      <p>Click “Next” to read the instructions.</p>
    </InstructionScreen>

    <InstructionScreen :title="'Instruction'">
      <p>On each trial, you will see a common emoji.</p>
      <p>
        Your task is to judge the emotion you associate with the emoji face
        shown in terms of two dimensions:
      </p>
      <ul>
        <li>
          <strong>Valence</strong>: how negative/unpleasant or positive/pleasant
          the emotion was, and
        </li>
        <li>
          <strong>Arousal</strong>: how calm/passive or arousing/exciting the
          emotion was.
        </li>
      </ul>
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
        <p>Please consider this emoji:</p>
        <p v-if="trial.trialType === 'attention'">
          For this trial, please set {{ trial.attentionCheckV }} for the first
          scale and {{ trial.attentionCheckA }} for the second
        </p>
        <img :src="`img/${trial.emoji}.png`" style="width: 100px" />
        <p>
          To what extent do you think the emoji denotes something negative or
          something positive?
        </p>

        <RatingInput
          :count="9"
          left="Very negative/unpleasant"
          right="Very positive/pleasant"
          :response.sync="trial.valence"
        />

        <p>
          To what extent do you think the emoji denotes something passive or
          something arousing?
        </p>

        <RatingInput
          :count="9"
          left="Very passive/calm"
          right="Very arousing/exciting"
          :response.sync="trial.arousal"
        />

        <button
          v-if="trial.arousal != 0 && trial.valence != 0"
          style="margin-top: 18px"
          @click="
            $magpie.measurements.emoji = trial.emoji;
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

    <Screen>
      <Slide>
        <h3>Additional information</h3>
        <strong>1. What is your age?</strong>
        <p>
          <label>
            <input v-model="demographic.age" type="number" max="110" min="18" />
          </label>
        </p>
        <p>
          <label>
            <strong>2. What is your gender?</strong>
            <DropdownInput
              :options="['', 'female', 'male', 'other', 'prefer not to say']"
              :response.sync="demographic.gender"
            />
          </label>
        </p>
        <p>
          <label>
            <strong>3. How do you rate your English proficiency?</strong>
            <RatingInput
              left="Complete Beginner"
              right="Native Speaker"
              :response.sync="demographic.proficiency"
            />
          </label>
        </p>
        <p>
          <label>
            <strong>
              4. How frequently do you use or encounter emojis in your daily
              life?
            </strong>
            <RatingInput
              left="Never"
              right="Almost constantly"
              :response.sync="demographic.emojiUsage"
            />
          </label>
        </p>
        <p>
          <label>
            <strong> 5. Where do you currently live? </strong>
            <DropdownInput
              :options="COUNTRIES"
              :response.sync="demographic.country"
            />
          </label>
        </p>
        <p><strong> Further comments (optional) </strong></p>
        <TextareaInput :response.sync="demographic.comments" />

        <button
          v-if="
            demographic.country !== null &&
            demographic.country !== 'Select country' &&
            demographic.age >= 18 &&
            demographic.gender !== null &&
            demographic.emojiUsage !== null &&
            demographic.proficiency !== null
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
    <!-- <PostTestScreen /> -->

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

const STATES = [1, 2, 3, 4, 5];

export default {
  name: 'AppExperiment1',
  data() {
    return {
      trials: this.makeTrials(),
      COUNTRIES: COUNTRIES_LIST.map((c) => c.name),
      demographic: {
        age: null,
        gender: null,
        proficiency: null,
        emojiUsage: null,
        country: null
      }
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

      const attentionPositions = [Math.floor(n / 3), Math.floor((2 * n) / 3)];

      const trials = [];

      for (let i = 0; i < n; i++) {
        if (attentionPositions.includes(i)) {
          trials.push({
            trialType: 'attention',
            attentionCheckV: _.sample(STATES), // convenient sampling from 1-5
            attentionCheckA: _.sample(STATES),
            emoji: _.sample(EMOJIS), // resample from the pool
            arousal: 0,
            valence: 0
          });
        }

        trials.push({
          trialType: 'trial',
          attentionCheckV: 0,
          attentionCheckA: 0,
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
