<Screen>
      <Slide>
        <p><strong>1. What is your age?</strong></p>
        <p>
          <label>
            <input v-model="demographic.age" type="number" max="110" min="18" />
          </label>
        </p>
        <p><strong>2. What is your gender?</strong></p>
        <DropdownInput
          :options="['', 'female', 'male', 'other', 'prefer not to say']"
          :response.sync="demographic.gender"
        />
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
            <strong> 4. Where do you currently live? </strong>
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
            demographic.proficiency !== null
          "
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
