<script setup lang="ts">
import homePageLogo from '@/components/icons/homePageLogo.vue';
import HashTagLogo from '@/components/icons/hashTagLogo.vue';
import ProfileLogo from '@/components/icons/profileLogo.vue';
</script>

<template>
  <v-layout>
    <v-bottom-navigation grow class="d-flex d-lg-none">
      <v-btn>
        <homePageLogo class="img-icon" />
        <RouterLink to="/" />
      </v-btn>
      <v-btn>
        <RouterLink to="/explore"><HashTagLogo class="img-icon" /></RouterLink>
      </v-btn>
      <v-btn>
        <RouterLink to="/profile"><ProfileLogo class="img-icon" /></RouterLink>
      </v-btn>
    </v-bottom-navigation>
  </v-layout>
</template>

<style scoped>
.img-icon {
  max-width: 16vh;
  max-height: 25vh;
}
</style>
