<template>
<div>
    <div class="gruz">
    <div class="homeee"><a><RouterLink :to="{name: 'home'}">Home</RouterLink></a></div>
    <div class="abouttt"><a><RouterLink :to="{name: 'products'}">About</RouterLink></a></div>
    </div>
    <RouterView></RouterView>
   </div>
</template>

<script lang="ts">
export default {
  name: "App",
};
</script>

<style scoped>

.homeee {
    text-decoration: none;
    display: inline-block;
    color: #25df0e;
    letter-spacing: 2px;
}
.abouttt {
    margin-left: 7px;
    text-decoration: none;
    display: inline-block;
    color: #25df0e;
    letter-spacing: 2px
}

a {
    text-decoration: none;
}

.gruz {
    text-align: end;
}

</style>
