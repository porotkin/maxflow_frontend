<template>
    <div class="flex">
        {{ answer }}
    </div>
</template>

<script>
    export default {
        name: "UserPanelComponent",
        computed: {
            answer() {
                return this.$store.state.answer
            }
        },
        mounted() {
            this.$store.dispatch("getAnswer")
        }
    }
</script>

<style scoped>
.flex {
    width: 100%;
    height: 5vh;
    display: block;
    text-align: center;
    margin: 0;
    padding-top: 5px;
    -webkit-border-radius: 0 0 100% 100% / 0 0 8px 8px;
    -webkit-box-shadow: rgba(0, 0, 0,.30) 0 2px 3px;
}
</style>