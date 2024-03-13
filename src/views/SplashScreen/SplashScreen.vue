<script setup>
import IconSplashLemon from '@/components/icons/IconSplashLemon.vue';
import TextLogo from '@/components/icons/TextLogo.vue';
</script>

<template>
    <div class="Splash-Bg">
        <IconSplashLemon class="Splash-Icon"/>
        <TextLogo class="Splash-Text"/>
    </div>
</template>

<style lang="scss">
.Splash-Bg {
    position: fixed;
    width: 100vw;
    height: 100vh;
    background: #FFD84F;


    display: flex;
    justify-content: center;
    text-align: center;
    align-items: center;
}
.Splash-Icon {
    align-self: center;
}
.Splash-Text {
    position: fixed;
    bottom: 62px;
}

body {
    background: #FFD84F;
}
</style>
