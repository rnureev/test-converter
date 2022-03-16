<template>
  <v-app>
    <v-app-bar
      app
      color="primary"
      dark
    >
<v-app-bar-title>Converter</v-app-bar-title>

      <v-spacer></v-spacer>

    </v-app-bar>

    <v-main>
      <Converter/>
    </v-main>
  </v-app>
</template>

<script>
import Converter from './components/Converter';

export default {
  name: 'App',

  components: {
    Converter,
  },

  data: () => ({
    //
  }),
};
</script>
