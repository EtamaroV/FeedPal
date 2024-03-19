<script setup>
import IconSplashLemon from '@/components/icons/IconSplashLemon.vue';
import TextLogo from '@/components/icons/TextLogo.vue';



var isLoading = true
var finishLoad = false
var dropped = false




</script>


<template>
    <div class="Splash-Bg" ref="SplashBGANDALL">
        <IconSplashLemon class="Splash-Icon"/>
        <TextLogo class="Splash-Text"/>

        <div class="Splash-Loader">
            <div class="Splash-drops" ref="AllSplashDrop" :class="{ 'Splash-hide': !isLoading }">
                <div class="Splash-drop1"></div>
                <div class="Splash-drop2" ref="SplashDrop"></div>  
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

        <div class="Splash-FadeBall" ref="SplashFadeBall"></div>
    </div>

    
</template>

<script>
import { gsap } from 'gsap';
export default { 
  mounted() { 
    var SplashFadeBallSize = "vw"
    if(window.innerHeight > window.innerWidth){
      SplashFadeBallSize = "vh"
    }
    var splashAnim = gsap.timeline({repeat: 0, repeatDelay: 1});
    splashAnim.to(this.$refs.SplashDrop, { duration: 1.2, delay:0.5, ease: "expo.in", bottom: 'calc( -50vh + 40px )',
      onComplete: ()=>{
        if(window.innerHeight > window.innerWidth){
          SplashFadeBallSize = "vh"
        } else {
          SplashFadeBallSize = "vw"
        }
        console.log(SplashFadeBallSize)
      }})
    
    splashAnim.to(this.$refs.AllSplashDrop, { duration: 0, delay:0, display: 'none'})
    splashAnim.to(this.$refs.SplashFadeBall, { duration: 0.5, delay:0, ease: "power1.in", width: 'calc(200'+ SplashFadeBallSize +' + 50px)', height: 'calc(200'+ SplashFadeBallSize +' + 50px)', bottom: '-100'+SplashFadeBallSize})
    splashAnim.to(this.$refs.SplashFadeBall, { duration: 0.1, delay:0, ease: "none", width: '100%', height: '100%', bottom: '0', borderRadius: '0'})
    splashAnim.to(this.$refs.SplashBGANDALL, { duration: 0.5, delay:0, opacity: 0})
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
    z-index: 3;
}
.Splash-Text {
    position: fixed;
    bottom: 62px;
    z-index: 1;
}

.Splash-Loader {
    position: fixed;
    z-index: 2;
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
    background-color: var(--color-background);

}

.Splash-drop2 {
  bottom: 6px;
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

.Splash-FadeBall {
  width: 40px;
  height: 40px;
  z-index: 4;
  position: fixed;
  background-color: var(--color-background);

  border-radius: 100%;

  bottom: -40px;
}
</style>
