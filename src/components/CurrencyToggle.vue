<template>
  <v-btn-toggle
      v-model="inputVal"
      mandatory
  >
    <v-btn small :key="c" v-for="(currency,c) in currences">
      <v-icon>{{currency.icon}}</v-icon>{{currency.name}}
    </v-btn>
  </v-btn-toggle>
</template>
<script>
export default {
  props:['value','currences'],
  computed: {
    inputVal: {
      get() {
        for (var i = 0; i < this.currences.length; i++) {
          if (this.currences[i].val==this.value){
            return i;
          }
        }
        return this.value;
      },
      set(val) {
        this.$emit('input', this.currences[val].val);
      }
    }
  }
};
</script>