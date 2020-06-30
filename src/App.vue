<template>
  <div id="app">
    <div class="main-section columns">
      <main class="column">
        <Parse/>
      </main>
    </div>
  </div>
</template>

<script>
import Parse from './components/Parse';

export default {
  name: 'App',

  components: {
    Parse,
  },

  data: () => ({
    //
  }),
};
</script>
