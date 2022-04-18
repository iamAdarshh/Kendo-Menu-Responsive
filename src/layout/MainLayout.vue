<script lang="ts">
import { defineComponent } from "vue";
import TheMainMenu from "@/components/MainMenu.vue";

export default defineComponent({
  name: "MainLayout",
  components: {
    TheMainMenu,
  },
});
</script>

<template>
  <TheMainMenu />
</template>
