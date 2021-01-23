<template>
  <div id="app">
      <AmazingPlayer>
      </AmazingPlayer>
  </div>
</template>

<script>
import AmazingPlayer from "@/components/AmazingPlayer";

export default {
  name: 'App',
  components: {
      AmazingPlayer
  }
}
</script>

<style>
#app {
    display: flex;
    align-items: flex-end;
    height: 100vh;
    padding: 24px;
    box-sizing: border-box;
}
*{
    box-sizing: border-box;
}
</style>
