<template>
  <div class="main-box">
    <Header />
    <transition name="fade" mode="out-in">
      <router-view />
    </transition>
    <!-- <Shell v-if="isShowShell" /> -->
  </div>
</template>

<script>
// import Shell from "./shell/index";
import Header from "./header/index.vue";
export default {
  name: "mains",
  components: {
    // Shell,
    Header
  },
  // data() {
  //   // return {
  //   //   isShowShell: false
  //   // };
  // },
  // created() {
  //   // this.$event.on("isShell", state => {
  //   //   this.isShowShell = state;
  //   // });
  // }
};
</script>

