<template>
    <div class="flex flex-col text-gray-100 justify-center">
        <nuxt-link class="self-center" :to=to :class="{'activeLink': currentPath===to}" >{{displayTitle}}</nuxt-link>
        <div class="flex flex-row gap-5" v-if="currentPath.startsWith(to) && subLinks.length > 0">
            <nuxt-link v-for="subLink in subLinks" :key="subLink.to" :to="subLink.to" :class="{'activeLink': currentPath === subLink.to}">{{subLink.displayTitle}}</nuxt-link> 
        </div>
    </div>
</template>
<script>
export default {
    data() {
        return {
            currentPath: "/"
        }
    },
    props: {
        displayTitle: {type:String, default: "Navigation link"},
        to: {type: String, default: "/does/not/exist"},
        subLinks: {default: () => []}
    },
    mounted() {
        this.currentPath = this.$route.path;
    },
    watch: {
        $route(to, from) {
            this.currentPath = to.path;
        }
    }
}
</script>
<style scoped>
.activeLink {
    color: burlywood;
}
</style>