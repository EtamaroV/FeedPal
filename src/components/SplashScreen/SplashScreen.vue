<script setup>
import IconSplashLemon from '@/components/icons/IconSplashLemon.vue';
import TextLogo from '@/components/icons/TextLogo.vue';

var isLoading = true
var finishLoad = false
var dropped = false

</script>


<template>
    <v-snackbar
      v-model="show_Nointernet"
      :timeout="-1"
      color="#222222"
      location="top"
      style="padding-top: env(safe-area-inset-top);"
    >
    Oops! No Internet Connection.
    </v-snackbar>
    <div class="Splash-Bg" ref="SplashBGANDALL">
        <IconSplashLemon class="Splash-Icon" ref="SplashIcon"/>
        <TextLogo class="Splash-Text" ref="SplashText"/>

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
  data() {
    return {
      show_Nointernet: false,
    }
  },
  mounted() { 

    var nowOnline = false
    if (navigator.onLine) {
      console.log("online");
      nowOnline = true
      this.show_Nointernet = false
    } else {
      console.log("offline");
      nowOnline = false
      this.show_Nointernet = true
    }

    var SplashFadeBallSize = "vw"
    if(window.innerHeight > window.innerWidth){
      SplashFadeBallSize = "vh"
    }

    var splashAnimEnd = gsap.timeline({repeat: 0, repeatDelay: 1, paused: true });
    splashAnimEnd.to(this.$refs.AllSplashDrop, { duration: 0, delay:0, display: 'none'})
    splashAnimEnd.to(this.$refs.SplashFadeBall, { duration: 0.3, delay:0, ease: "power1.in", width: 'calc(200'+ SplashFadeBallSize +' + 50px)', height: 'calc(200'+ SplashFadeBallSize +' + 50px)', bottom: '-100'+SplashFadeBallSize})
    splashAnimEnd.to(this.$refs.SplashFadeBall, { duration: 0.1, delay:0, ease: "none", width: '110%', height: '110%', bottom: '0', borderRadius: '0'})
    splashAnimEnd.to(this.$refs.SplashBGANDALL, { duration: 0.5, delay:0, opacity: 0})
    splashAnimEnd.to(this.$refs.SplashBGANDALL, { duration: 0, delay:0, display: 'none'})

    var splashAnim = gsap.timeline({repeat: -1, repeatDelay: 0.2});
    splashAnim.to(this.$refs.SplashDrop, { duration: 1.2, delay:1.0, ease: "expo.in", bottom: 'calc( -50vh + 40px )',
      onComplete: ()=>{
        if (nowOnline) {
          if(window.innerHeight > window.innerWidth){
            SplashFadeBallSize = "vh"
          } else {
            SplashFadeBallSize = "vw"
          }
          //console.log(SplashFadeBallSize)

          splashAnim.pause()
          splashAnimEnd.play()
          
        } else {

          if (navigator.onLine) {
            console.log("online");
            nowOnline = true
            this.show_Nointernet = false
          }
          
        }
      }})

    
  }
}

</script>

<style lang="scss" scoped>

.Splash-Bg {
    position: fixed;
    width: 100vw;
    height: 100vh;
    background: #FFD84F;


    display: flex;
    justify-content: center;
    text-align: center;
    align-items: center;
    z-index: 1000;
}
.Splash-Icon {
    align-self: center;
    z-index: 1003;
}
.Splash-Text {
    position: fixed;
    bottom: 62px;
    z-index: 1001;
    fill: #222222;
}

.Splash-Loader {
    position: fixed;
    z-index: 1002;
}

.Splash-drops {
    -webkit-filter: url('#liquid');
    filter: url('#liquid');
    position:absolute;
    top:0;
    left:0;
    bottom:0;
    right:0;
    z-index: 1005;
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
    z-index: 1000;
}

.Splash-drop2 {
  bottom: 6px;
  z-index: 1000;
}

.Splash-drop1 {
    width: 50px;
    height: 50px;
    bottom: 9px;
    border-radius: 0;
    z-index: 1000;
}

.Splash-dropping {
    animation: drop 1.5s cubic-bezier(.77,.04,.67,0);
    z-index: 1000;
}

.Splash-hide {
    display: none;
    z-index: 1000;
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
  z-index: 1004;
  position: fixed;
  background-color: var(--color-background);

  border-radius: 100%;

  bottom: -40px;
}
</style>
