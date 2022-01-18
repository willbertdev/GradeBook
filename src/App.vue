<template>
  <v-app>
    <v-main>
      <GradeBook/>
    </v-main>
  </v-app>
</template>

<script>
// import GradeTable from './components/GradeTable';
import GradeBook from './components/GradeBook';

export default {
  name: 'App',

  components: {
    GradeBook,
  },

  data: () => ({
    //
  }),
};
</script>

<style>
#app {
  background: url('assets/img/background.jpg') no-repeat center center fixed;
  background-size: cover;
}
</style>
