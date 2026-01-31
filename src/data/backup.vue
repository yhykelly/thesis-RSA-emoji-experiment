 <Screen>
     </Slide>
      <p>1. What is your age?</p>
      <p>
        <label>
          <input
            v-model="$magpie.measurements.age"
            type="number"
            max="110"
            min="18"
          />
        </label>
      </p>
      <p>
        <label
          >2. What is your gender?
          <DropdownInput
            :options="['', 'female', 'male', 'other', 'prefer not to say']"
            :response.sync="$magpie.measurements.gender"
          />
        </label>
      </p>
      <p>
        <label
          >3. On a scale from 1 to 7, how do you rate your English proficiency?
          <RatingInput
            left="Complete Beginner"
            right="Native Speaker"
            :response.sync="$magpie.measurements.proficiency"
          />
        </label>
      </p>
      <p>
        <label
          >4. Where do you currently live?
          <DropdownInput
            :options="COUNTRIES"
            :response.sync="$magpie.measurements.country"
          />
        </label>
      </p>

      <button
        v-if="allAnswered"
        style="margin-top: 18px"
        @click="$magpie.saveAndNextScreen()"
      >
        Next
      </button>
      </Slide>
    </Screen>