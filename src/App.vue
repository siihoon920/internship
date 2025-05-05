<template>
  <Experiment title="magpie demo">


      <Screen title="Welcome">
        Thank you for taking part in this experiment.</br></br>

        Participation in this experiment is voluntary. You may quit at any moment without negative consequences.</br></br>

        The data from this experiment will be used for scientific purposes only. It will be stored anonymously and may be shared with other scientists.</br></br>

        By proceeding you indicate that you have read, understood and that you agree with these terms.</br>

        <button @click="$magpie.nextScreen()">BEGIN THE EXPERIMENT </button>
      </Screen>

      
      <Screen v-for="(trial,i) in practice_trial_info">
        <Slide>
          <p>{{$magpie.currentScreenIndex}}</p>
          <SelfPacedReadingInput_SpeakerKnowledge
            :context="trial.context.split('|')"
            :triggersentence="trial.trigger.split('|')"
            :continuation="trial.continuation.split('|')"
            :underline="'words'"
            :response-times.sync="RT"
            @end="endSelfPacedReading(trial)"
          />
        </Slide>

        <Slide>
          <p>{{$magpie.currentScreenIndex}}</p>

          <p>{{trial.question}}</p>
          <ForcedChoiceInput
            :options="[trial.option1, trial.option2]"
            :question="trial.question"
            :response.sync="answer"
            @update:response="endForcedChoice(trial), ($magpie.measurements.answer==='yes'?$magpie.saveAndNextScreen():$magpie.nextScreen('get_ready'))"
          />
        </Slide>
      </Screen>
          

    

      <Screen title="Get ready" label="get_ready">
        The practice trials are now complete. When you are ready to begin the study, click 'Begin'.
        <button @click="$magpie.nextScreen()">BEGIN THE EXPERIMENT </button>
      </Screen>

      

      <Screen v-for="(trial,i) in trial_info">


        <Slide>
          <p>{{$magpie.currentScreenIndex}}</p>
          <SelfPacedReadingInput_SpeakerKnowledge
            :context="trial.context.split('|')"
            :triggersentence="trial.trigger.split('|')"
            :continuation="trial.continuation.split('|')"
            :underline="'words'"
            :response-times.sync="RT"
            @end="endSelfPacedReading(trial)"
          />
        </Slide>
        <Slide>
          <p>{{trial.question}}</p>
          <ForcedChoiceInput
          :options="['Yes', 'No']"
          :response.sync="answer"
          @update:response="endForcedChoice(trial), $magpie.saveAndNextScreen()
"
        />
        </Slide>
        

      </Screen>
      <DebugResultsScreen_edited/>



  </Experiment>
</template>

<script>
import _ from 'lodash';
import {practice_trial_info, trial_info} from './trials.js';
import SelfPacedReadingInput_SpeakerKnowledge from './SelfPacedReadingInput_SpeakerKnowledge.vue';
import DebugResultsScreen_edited from './DebugResultsScreen_edited.vue';
export default {
  name: 'App',
  components: {
    SelfPacedReadingInput_SpeakerKnowledge,
    DebugResultsScreen_edited
  },
  data() {
    return {
      trial_info,
      practice_trial_info,
      RT : [],
      answer : 'unavailable'
    };
  },
  computed: {
    // Expose lodash to template code
    _() {
      return _;
    }
    
  },
  methods: {
    endSelfPacedReading(trial){
      this.$magpie.measurements.context = trial.context;
      this.$magpie.measurements.RT_context = this.RT[0];
      this.$magpie.measurements.trigger = trial.trigger;
      this.$magpie.measurements.RT_trigger = this.RT[1];
      this.$magpie.measurements.continuation = trial.continuation;
      this.$magpie.measurements.RT_continuation = this.RT[2];
      this.$magpie.nextSlide();
    },
    endForcedChoice(trial){
      this.$magpie.measurements.question = trial.question;
      this.$magpie.measurements.correct_answer = trial.correct_answer;
      this.$magpie.measurements.answer = this.answer
    }
  }
};
</script>
