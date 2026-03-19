<Slide>
          <div>
            <p id="given">
              {{ trial.person }} {{ trial.context.action }}.
              {{ trial.person }} sent this message to you:
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

          <br />

          <p>
            To communicate
            <strong>only</strong> about the emotion felt about the experience.
          </p>
          <RatingInput
            :count="9"
            left="very unlikely"
            right="very likely"
            :response.sync="trial.goalValenceArousal"
          />

          <br />

          <p>
            To communicate about
            <strong>both</strong> the rating of the experience, and the emotion
            felt about it.
          </p>
          <RatingInput
            :count="9"
            left="very unlikely"
            right="very likely"
            :response.sync="trial.goalAll"
          />

          <!-- <SliderRangesInput
            :min="0"
            :max="100"
            :initial="0"
            :interval="10"
            :tooltip="true"
            :ranges="['state', 'emotion', 'state_emotion']"
            :range-values.sync="$magpie.measurements.goals"
          />
          <p v-if="$magpie.measurements.goals">
            {{ $magpie.measurements.goals[0] }} Rating only,
            {{ $magpie.measurements.goals[1] }} Emotion only,
            {{ $magpie.measurements.goals[2] }} Both rating and emotion
          </p> -->

          <!-- <MultipleChoiceInput
            :response.sync= "$magpie.measurements.breakfast"
            :options="['Rating only', 'Emotion only', 'Both rating and emotion']" /> -->

          <button
            v-if="
              trial.goalState != null &&
              trial.goalState !== 0 &&
              trial.goalValenceArousal != null &&
              trial.goalValenceArousal !== 0 &&
              trial.goalAll != null &&
              trial.goalAll !== 0
            "
            style="margin-top: 18px"
            @click="
              $magpie.measurements.context = trial.context.item;
              $magpie.measurements.emoji = trial.emoji;
              $magpie.measurements.adj = trial.adj;
              $magpie.measurements.inferredState = trial.inferredState;
              $magpie.measurements.goalState = trial.goalState;
              $magpie.measurements.goalValenceArousal =
                trial.goalValenceArousal;
              $magpie.measurements.goalAll = trial.goalAll;
              $magpie.measurements.trialType = trial.trialType;
              $magpie.measurements.attentionCheck = trial.attentionCheck;
              $magpie.measurements.isPassedAttention =
                trial.trialType === 'trial' ||
                trial.attentionCheck === trial.inferredState;
              $magpie.saveAndNextScreen();
            "
          >
            Next
          </button>
        </Slide>