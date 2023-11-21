<template>
  <div className="links">
    <a href="https://www.linkedin.com/in/ryangormican/">
      <Icon icon="mdi:linkedin" color="#0e76a8" width="60" />
    </a>
    <a href="https://github.com/RyanGormican/PaintersParadise">
      <Icon icon="mdi:github" color="#e8eaea" width="60" />
    </a>
    <a href="https://ryangormicanportfoliohub.vercel.app/">
      <Icon icon="teenyicons:computer-outline" color="#199c35" width="60" />
    </a>
  </div>
  <div class="title">
    <h1> PaintersParadise</h1>
  </div>
  <Board/>
</template>

<script lang="ts">
  import { defineComponent } from 'vue';
  import Board from './components/Board.vue';
  import { Icon } from '@iconify/vue';
  export default defineComponent({
  name: 'App',
  components: {
  Board,
  Icon,
  }
  });
</script>

<style>
  @import '@/assets/styles.css';

</style>
