# Experiments procedures
- [Experiment 1a: Literal semantics](#exp-1a-literal-semantics-adjective-applicability-on-rating-states)
- [Experiment 1b: Emotions based on rating states](#exp-1b-emotions-based-on-rating-states)
- [Experiment 2: Emoji semantics](#exp-2-emoji-semantics)
- [Experiment 3a: True state and goal inference](#exp-3a-true-rating-and-goal-inference)
- [Experiment 3b: Emotion inference](#exp-3b-emotion-inference-given-utterance)
- [Demographics](#demographics-asked-in-all-experiments)

## Exp 1a: Literal Semantics (adjective applicability on rating states)
https://yhykelly.github.io/thesis-RSA-emoji-experiment/experiments/exp1a/
### Procedures (order of "screens" participants see)
1. Welcome screen
2. Instructions screen
3. Main task (Main Trials + attention check) screen
4. Demographics screen
5. Final "further comment" (optional) and Submit screen  

Expected time to complete: **5 minutes**

### Trials
#### Main Trials: 25 Trials  
- Stimulus: short event `context` + rating `state` (out of 5 stars) + description sentence with the `adjective` (5 `state`'s * 5 `adjective`'s = 25 combos)
- Response: 9-point Likert Scale (not applicable at all <--> very applicable)
- Per trial, the combination of the event context, person (subject for the context), rating and adjective is randomized. (see below)

#### Attention-Check (AC): 2 Trials
- Stimulus: Highly similar to main the trials, but extended the check instruction after the rating , e.g. “... a rating of 1 out of 5 stars. For this trial, select 3.”  
- Response: 9-point Likert Scale(not applicable at all <--> very applicable)
- ACs are inserted at fixed positions relative to the number of Main Trials: at the 1/3 and 2/3 of the whole trial timeline, that is:  
    - one AC after the 8th (floor (25 / 3)) Main Trial, one after the 16th (floor 25 * 2 / 3) Main Trial.
- The instructed rating per AC per participant are randomised.

#### Randomized stimulus components
- `CONTEXTS`:   { action: 'attended a concert', item: 'concert' },  
                { action: 'tried a pizza', item: 'pizza' },  
                { action: 'watched a movie', item: 'movie' },  
                { action: 'tried a cookie', item: 'cookie' },  
                { action: 'reviewed a restaurant meal', item: 'restaurant meal' },  
                { action: 'tried a coffee', item: 'coffee' },  
                { action: 'attended a party', item: 'party' }  
- `STATES`: {1, 2, 3, 4, 5} out of 5 stars  
- `ADJECTIVES`: {“terrible”, “bad”, “okay”, “good”, “amazing”}  
- `PERSONS`: {'Alice',
  'Bob',
  'Chris',
  'Dani',
  'Emma',
  'Liam',
  'Noah',
  'Olivia',
  'Mia',
  'Lucas',
  'Sophia',
  'James',
  'Emily',
  'Daniel',
  'Sarah',
  'Michael',
  'Anna',
  'David',
  'Laura',
  'John',
  'Lisa',
  'Mark',
  'Julia',
  'Paul',
  'Tom'}  


## Exp 1b: Emotions based on rating states
https://yhykelly.github.io/thesis-RSA-emoji-experiment/experiments/exp1b/
### Procedures (order of "screens" participants see)
1. Welcome screen
2. Instructions screen: here the two emotion dimensions are briefly defined (Valence and Arousal)
3. Main task (Main Trials + attention check) screen
4. Demographics screen
5. Final "further comment" (optional) and Submit screen  

Expected time to complete: **5 minutes**

### Trials
#### Main Trials: 10 Trials  
- Stimulus: short event `context` + rating `state` (out of 5 stars), 2 trials per state
- Response: Two 9-point Likert Scales
    - Valence (negative/unpleasant <--> positive/pleasant)
    - Arousal (calm/passive <--> intense/exciting)
- Per trial, the combination of the event context, person (subject for the context), rating is randomized. (Randomized stimulus see below)

#### Attention-Check (AC): 2 Trials
- Stimulus: Highly similar to the Main Trials, but extended the check instruction after the context , e.g. “... a rating of 1 out of 5 stars. For this trial, select 3 for the first scale and 4 for the second.”    
- Response: Two 9-point Likert Scales
    - Valence (negative/unpleasant <--> positive/pleasant)
    - Arousal (calm/passive <--> intense/exciting)
- ACs are inserted at fixed positions relative to the number of Main Trials: at the 1/3 and 2/3 of the whole trial timeline, that is:  
    - one AC after the 3th (floor (10 / 3)) Main Trial, one after the 6th (floor (10 * 2 / 3)) Main Trial.  
- The instructed ratings per AC per participant are randomised.  

#### Randomized stimulus components 
- `CONTEXTS`: Same as experiment 1a 
- `STATES`: {1, 2, 3, 4, 5} out of 5 stars  
- `PERSONS`: {'Alice',
  'Bob',
  'Chris',
  'Dani',
  'Emma',
  'Liam',
  'Noah',
  'Olivia',
  'Mia',
  'Lucas'}  

## Exp 2: Emoji semantics
https://yhykelly.github.io/thesis-RSA-emoji-experiment/experiments/exp2/  
### Procedures (order of "screens" participants see)
1. Welcome screen
2. Instructions screen: here the two emotion dimensions are briefly defined (Valence and Arousal)
3. Main task (Main Trials + attention check) screen
4. Demographics screen
5. Final "further comment" (optional) and Submit screen  

Expected time to complete: **5 minutes**

### Trials  

#### Main Trials: 12 Trials   

- Stimulus: An `emoji` image (see below)
- Response: Two 9-point Likert Scales
    - Valence (negative/unpleasant <--> positive/pleasant)
    - Arousal (calm/passive <--> intense/exciting)
- The set of emojis are constant among participants, but the order of 12 emoji trials are randomised per participant.

#### Attention-Check (AC): 2 Trial  

- Stimulus: Highly similar to the Main Trials, but the check instruction appeared above the emoji image with wordings e.g. “For this trial, select 1 for the first scale and 2 for the second.”  
- Response: Two 9-point Likert Scales
    - Valence (negative/unpleasant <--> positive/pleasant)
    - Arousal (calm/passive <--> intense/exciting)
- ACs are inserted at fixed positions relative to the number of Main Trials: at the 1/3 and 2/3 of the whole trial timeline, that is:  
    - one AC after the 4th (floor (12 / 3)) Main Trial, one after the 8th (floor (12 * 2 / 3)) Main Trial.   
- The instructed ratings per participant are randomised.  

#### Stimuli  

- `EMOJIS`: { {😆, 😄, 😀, ☺️, 🙂} , {😐, 😗}, {🙁, ☹️, 😔, 😩, 😠} }, from Unicode Emoji version 17 (https://unicode.org/emoji/charts/full-emoji-list.html)
    - 5 positives, 2 “neutrals”, 5 negatives  
- Response format referencing previous works: 
    - Kutsuzawa et al. 2022: 9-point scale (1: displeasure to 9: pleasure for valence, and 1: weak to 9: strong for arousal)
    - Rodrigues et al. 2018: 7-point scale (1: very negative to 7: very positive for valence, and 1: very passive/calm to 7: very arousing/exciting for arousal)
    - Ferré et al. 2022: 9-point scale (1: very negative to 9: very positive for valence, and 1: very passive/calm to 7: very arousing/exciting for arousal)
    - Fischer & Herbert 2021: 9-point scale using SAM (Self-Assessment Manikin) (explained Valence as 1 = “unpleasant/negative" and 9 = “pleasant/positive”, and Arousal as 1 = “calm” to 9 = “arousing")
    - Jaeger et al. 2019: 9-point scale using SAM (Self-Assessment Manikin). (pictorial instead of textual, no textual explanation)

## Exp 3a: True rating and goal inference
https://yhykelly.github.io/thesis-RSA-emoji-experiment/experiments/exp3a/
### Procedures (order of "screens" participants see)
1. Welcome screen
2. Instructions screen
3. Main task (Main Trials + attention check) screen
4. Demographics screen
5. Final "further comment" (optional) and Submit screen  

Expected time to complete: **10 minutes**

### Trials
#### Main Trials: 15 Trials  
- Stimulus: short event `context` + description consisting of `adjective` and `emoji`
- Response: 
    - 1 5-point Likert Scale assessing true state inference  (1 star - 5 stars)
    - 2 9-point Likert Scale assessing the goal inference (very unlikely - very likely)
        - one for goal state,
        - one for goal emotion 
- Per trial, the combination of the event `context`, `person` (subject for the context), `adjective` and `emoji` is randomized. (items see below)
- The variables `adjectives` and `emojis` are pre-classified into three sentiment categories --- good, neutral, bad. Within each trial, `adjective` and `emoji` are sampled from the same sentiment category, rather than being fully crossed across categories. This is to keep the congruency.
- For each emoji category, there is a additional option `null_emoji` (i.e., no emoji), allowing the trial to include a description without an emoji.

#### Attention-Check (AC): 2 Trials
- Stimulus: Highly similar to main the trials, the description sentence is directly replace by the attention check instruction, i.e. “ For this trial, select 3.”  
- Response: 5-point Likert Scale on true state inference (1 star - 5 stars)
- ACs are inserted at fixed positions relative to the number of Main Trials: at the 1/3 and 2/3 of the whole trial timeline
- The instructed rating per AC per participant are randomised.

#### Randomized stimulus components 3a
- `CONTEXTS`:   { action: 'attended a concert', item: 'concert' },  
                { action: 'tried a pizza', item: 'pizza' },  
                { action: 'watched a movie', item: 'movie' },  
                { action: 'tried a cookie', item: 'cookie' },  
                { action: 'reviewed a restaurant meal', item: 'restaurant meal' },  
                { action: 'tried a coffee', item: 'coffee' },  
                { action: 'attended a party', item: 'party' }  
             
- `GOOD_EMOJIS` = [
  'grinning😀',
  'slightly_smiling_face🙂',
  'smile'😊,
  'relaxed☺️',
  'laughing😆',
]

- `GOOD_ADJECTIVES` = ['good', 'amazing']

- `NEUTRAL_EMOJIS` = ['neutral_face😐', 'kissing😗']

- `NEUTRAL_ADJECTIVES` = 'okay'

- `BAD_EMOJIS` = [
  'pensive😔',
  'slightly_frowning_face🙁',
  'weary😩',
  'white_frowning_face☹️',
  'angry' 😠,
]

- `BAD_ADJECTIVES` = ['terrible', 'bad']

- `PERSONS`: {'Alice',
  'Bob',
  'Chris',
  'Dani',
  'Emma',
  'Liam',
  'Noah',
  'Olivia',
  'Mia',
  'Lucas',
  'Sophia',
  'James',
  'Emily',
  'Daniel',
  'Sarah',
  'Michael',
  'Anna',
  'David',
  'Laura',
  'John',
  'Lisa',
  'Mark',
  'Julia',
  'Paul',
  'Tom'}  


## Exp 3b: Emotion inference given utterance 
https://yhykelly.github.io/thesis-RSA-emoji-experiment/experiments/exp3b/
### Procedures (order of "screens" participants see)
1. Welcome screen
2. Instructions screen
3. Main task (Main Trials + attention check) screen
4. Demographics screen
5. Final "further comment" (optional) and Submit screen  

Expected time to complete: **10 minutes**

### Trials
#### Main Trials: 20 Trials  
- Stimulus: short event `context` + description consisting of `adjective` and `emoji`
- Response: Two 9-point Likert Scales
    - Valence (negative/unpleasant <--> positive/pleasant)
    - Arousal (calm/passive <--> intense/exciting)
- Per trial, the combination of the event `context`, `person` (subject for the context), `adjective` and `emoji` is randomized. (items see below)
- The variables `adjectives` and `emojis` are pre-classified into three sentiment categories --- good, neutral, bad. Within each trial, `adjective` and `emoji` are sampled from the same sentiment category, rather than being fully crossed across categories. This is to keep the congruency.
- For each emoji category, there is a additional option `null_emoji` (i.e., no emoji), allowing the trial to include a description without an emoji.

#### Attention-Check (AC): 2 Trials
- Stimulus: Highly similar to main the trials, the description sentence is directly replace by the attention check instruction, i.e. “ For this trial, select 3.”  
- Response: Two 9-point Likert Scales
    - Valence (negative/unpleasant <--> positive/pleasant)
    - Arousal (calm/passive <--> intense/exciting)
- ACs are inserted at fixed positions relative to the number of Main Trials: at the 1/3 and 2/3 of the whole trial timeline
- The instructed rating per AC per participant are randomised.

#### Randomized stimulus components
See [Exp 3a](#randomized-stimulus-components-3a)

## Demographics asked in all experiments  
Answering to demographic questions are optional in experiment 1a and 1b and mandatory in experiment 2. We are interested to see if there is any significant difference in the interpretations of emoji among groups.
1. Age: Numerical input, with minimal = 18
2. Gender: Dropdown list (female, male, other, prefer not to say)
3. English Proficiency: 7-point Likert Scale (complete beignner <--> native speaker)
4. (*in exp2 only*): Emoji usage frequency: 7-point Likert Scale (Never <--> Almost constantly)
5. Current residency: Dropdown list following ISO 3166