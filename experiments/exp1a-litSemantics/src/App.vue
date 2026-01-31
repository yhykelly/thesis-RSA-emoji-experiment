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
        You have a friend Alice, who likes share with you her feedback on
        different experiences. You will be shown her ratings on a series of
        items or events.
      </p>
      <p>Your task is to provide your intuitive judgment on these feedbacks.</p>
      <p>Click “Next” to begin.</p>
    </InstructionScreen>

    

    <!-- We iterate over our experiment trials -->
    
      <!-- and display a screen with a slider rating task
             using the built-in SliderScreen component -->
      <template v-for="(trial, i) in makeTrials(10)">
             <RatingScreen
        :key="i"
        :count = "9"
        option-left="not applicable at all"
        option-right="very applicable"
      >
        
        <!-- <template #stimulus>
          <img src="img/angry.png" />
        </template> -->
        <template #stimulus>
          <p>
            <strong>{{ trial.context }}</strong>
          </p>
          <p>
            She gave a rating of <strong>{{ trial.stars }}</strong> out of 5
            stars.
          </p>
          <p>
            On a scale from 1 to 9, how applicable do you think the word <strong>"{{ trial.adj}}"</strong> is to describe the rating?
          </p>
        </template>
        
      </RatingScreen>
    </template>
   

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

const CONTEXTS = [
  'Alice attended a concert. ',
  'Alice tried a pizza.',
  'Alice watched a movie.',
  'Alice tried a cookie.',
  'Alice reviewed a restaurant meal.',
  'Alice tried a coffee.',
  'Alice attened a party.'
];

const STARS = [1, 2, 3, 4, 5];

const ADJECTIVES = ['terrible', 'bad', 'okay', 'good', 'amazing'];

export default {
  name: 'AppExperiment1',
  data() {
    return {};
  },
  methods: {
    makeTrials(n) {
      return _.times(n, () => ({
        context: _.sample(CONTEXTS),
        stars: _.sample(STARS),
        adj: _.sample(ADJECTIVES)
      }));
    }
  }
};
</script>
