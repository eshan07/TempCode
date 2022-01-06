vuexy select

        <b-form-group
          label="Ad type"
          label-for="type"
        >
          <v-select
            id="vue-select"
            v-model="form.type"
            :options="option"
          />
        </b-form-group>
        
        import vSelect from 'vue-select' 
          components: {
    vSelect,
  },
