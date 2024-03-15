<script setup>
import IconSplashLemon from '@/components/icons/IconSplashLemon.vue';
import TextLogo from '@/components/icons/TextLogo.vue';



var isLoading = true
var finishLoad = false
var dropped = false




</script>


<template>
    <div class="Splash-Bg">
        <IconSplashLemon class="Splash-Icon"/>
        <TextLogo class="Splash-Text"/>

        <div class="Splash-Loader">
            <div class="Splash-drops"  :class="{ 'Splash-hide': !isLoading }">
                <div class="Splash-drop1"></div>
                <div class="Splash-drop2" ref="XD"></div>  
            </div>

            <svg xmlns="http://www.w3.org/2000/svg" version="1.1">
              <defs>
                <filter id="liquid">
                  <feGaussianBlur in="SourceGraphic" stdDeviation="10" result="blur" />
                  <feColorMatrix in="blur" mode="matrix" values="1 0 0 0 0  0 1 0 0 0  0 0 1 0 0  0 0 0 18 -7" result="liquid" />
                </filter>
              </defs>
            </svg>
            
        </div>
    </div>
</template>

<script>
import { gsap } from 'gsap';

export default { 
  mounted() { 
    gsap.to(this.$refs.XD, { duration: 1.2, delay:0.5, ease: "power4.in", bottom: 'calc( -50vh + 40px )', repeat: -1})
  }
}

</script>

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
    z-index: 10;
}
.Splash-Text {
    position: fixed;
    bottom: 62px;
}

body {
    background: #FFD84F;
}

.Splash-Loader {
    position: fixed;
    z-index: 5;
}

.Splash-drops {
    -webkit-filter: url('#liquid');
    filter: url('#liquid');
    position:absolute;
    top:0;
    left:0;
    bottom:0;
    right:0;
    z-index: 5;
    opacity: 0;
    animation: fade-in .1s linear .4s forwards;
}

.Splash-drop1 , .Splash-drop2 {
    width: 30px;
    height: 40px;
    border-radius: 50%;
    position: absolute;
    left: 0;
    right: 0;
    bottom: 0;
    margin: auto;
    background-color: #ffffff;

}

.Splash-drop1 {
    width: 50px;
    height: 50px;
    bottom: 9px;
    border-radius: 0;
}

.Splash-dropping {
    animation: drop 1.5s cubic-bezier(.77,.04,.67,0);
}

.Splash-hide {
    display: none;
}

@keyframes fade-in {
  0% {
    opacity: 0;
  }
  100% {
    opacity: 1;
  }
}

@keyframes drop {
  0% {
    bottom: 0px;
    opacity: 1;
  }
  
  80% {
    opacity: 1;
  }
  
  100% {
    opacity: 1;
    bottom: calc((100vh / -2) + 40px);
  }
}

</style>
