<template>
  <div class="mt-20 sm:mt-10">
    <div
      class=" px-8 py-8 lg:bg-none flex lg:items-left justify-end"
    >
      <div class="max-w-sm mx-0 w-full md:mx-0 flex-row">
        <ul class="bg-transparent">
          <li
            v-for="link in navigationLinks"
            :key="link.title"
            class="text-xs flex items-left text-gray-200 "
          >
            <!-- Heroicon name: check 
              <svg class="h-6 w-6 text-cyan-200" xmlns="http://www.w3.org/2000/svg" fill="none" viewBox="0 0 24 24" stroke="currentColor" aria-hidden="true">
                <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M5 13l4 4L19 7" />
              </svg>-->
            <nuxt-link
              :to="link.address"
              class=" text-gray-50 text-left text-xs mx-8 track w-full rounded"
              :class="{
                'customCol py-2 text-gray-900': currentPath === link.address,
              }"
              >{{ link.title }}</nuxt-link
            >
          </li>
        </ul>
      </div>
    </div>
  </div>
</template>
<script>
export default {
  data() {
    return {
      currentPath: "/",
    };
  },
  mounted() {
    this.currentPath = this.$route.path;
  },
  props: {
    // iconSrc: {
    //   default: "development-01.svg"
    // },
    logoNavigationLink: undefined,
    navigationLinks: {
      default: () => [
        { title: "Sub-link 1", address: "/does/not/exist" },
        { title: "Sub-link 2", address: "/does/not/exist" },
        { title: "Sub-link 3", address: "/does/not/exist" },
      ],
    },
    quote: {
      type: String,
      default: "In the midst of chaos, there is also opportunity.",
    },
    name: { type: String, default: "Sun Tzu" },
  },
  watch: {
    $route(to, from) {
      this.currentPath = to.path;
    },
  },
};
</script>
<style scoped>
.track {
  font-family: "Track";
}

.customCol {
  background-color: #eae0c4;
}
</style>
