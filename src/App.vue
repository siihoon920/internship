<template>
  <Experiment title="magpie demo">


      <Screen title="Welcome">
        Thank you for taking part in this experiment.</br></br>

        Participation in this experiment is voluntary. You may quit at any moment without negative consequences.</br></br>

        The data from this experiment will be used for scientific purposes only. It will be stored anonymously and may be shared with other scientists.</br></br>

        By proceeding you indicate that you have read, understood and that you agree with these terms.</br>

        <button @click="$magpie.nextScreen()">BEGIN THE EXPERIMENT </button>
      </Screen>

      
      <template v-for="(trial,i) in practice_trial_info">
            <Screen>
              <p>{{$magpie.currentScreenIndex}}</p>
              <SelfPacedReadingInputSpecific
                :context="trial.context.split('|')"
                :triggersentence="trial.trigger.split('|')"
                :continuation="trial.continuation.split('|')"
                :underline="'words'"
                @end="$magpie.nextScreen()"
              />
            </Screen>

            <Screen>
              <p>{{$magpie.currentScreenIndex}}</p>

              <p>{{trial.question}}</p>
              <ForcedChoiceInput
                :options="[trial.option1, trial.option2]"
                :question="trial.question"
                :response.sync="practice_continues"
                @update:response="practice_continues==='yes'?$magpie.nextScreen():$magpie.nextScreen('get_ready')"
              />
            </Screen>
      </template>
          

      

      <Screen title="Get ready" label="get_ready">
        The practice trials are now complete. When you are ready to begin the study, click 'Begin'.
        <button @click="$magpie.nextScreen()">BEGIN THE EXPERIMENT </button>
      </Screen>

      <

      <template v-for="(trial,i) in trial_info">


        <Screen>
          <p>{{$magpie.currentScreenIndex}}</p>

          <SelfPacedReadingInputSpecific
            :context="trial.context.split('|')"
            :triggersentence="trial.trigger.split('|')"
            :continuation="trial.continuation.split('|')"
            :underline="'words'"
            @end="$magpie.nextScreen()"
          />
        </Screen>

        <ForcedChoiceScreen
          :options="['Yes', 'No']"
          :question="trial.question"
        />

      </template>
      <DebugResultsScreen/>



  </Experiment>
</template>

<script>
import _ from 'lodash';
import {practice_trial_info, trial_info} from './trials.js';
import SelfPacedReadingInputSpecific from '/src/SelfPacedReadingInputSpecific.vue';
import DebugResultsScreen from 'magpie-base/src/components/screens/DebugResultsScreen.vue';
export default {
  name: 'App',
  components: {
    SelfPacedReadingInputSpecific
  },
  data() {
    return {
      trial_info,
      practice_trial_info,
      practice_continues:'yes'
    };
  },
  computed: {
    // Expose lodash to template code
    _() {
      return _;
    }
    
  },
  methods: {
    test(){
      console.log('Test function called: and answer is', this.practice_continues);
    }
  }
};
</script>
