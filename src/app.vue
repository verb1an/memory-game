<template>
    <div class="app-wrapper">
        <h2 class="count">
            Счёт: <span>{{ count }}</span>
        </h2>
        <div v-if="isRenderer" class="game-pole">
            <div
                v-for="gameItem in gamePole"
                :key="gameItem"
                :data-item="gameItem"
                @click="cardsInterective"
                class="game-item"
            >
                <font-awesome-icon :icon="'fa-' + gameItem" />
                <font-awesome-icon icon="fa-question-circle" />
            </div>
        </div>
        <transition name="win-show">
            <div class="congratulation-wrapper" v-show="win">
                <button v-if="win" @click="restartGame" type="button" class="btn-res">Restart</button>
                <div v-for="n in 150" :key="n" :class="'conf-' + n"></div>
            </div>
        </transition>
    </div>
</template>

<script setup>
import { ref, nextTick } from "vue";

import { library } from "@fortawesome/fontawesome-svg-core";
import {
    faCompass,
    faCloud,
    faStop,
    faPlay,
    faBolt,
    faCogs,
    faAtom,
    faBasketballBall,
    faQuestionCircle,
} from "@fortawesome/free-solid-svg-icons";
import { FontAwesomeIcon } from "@fortawesome/vue-fontawesome";
library.add(faCompass);
library.add(faCloud);
library.add(faStop);
library.add(faPlay);
library.add(faBolt);
library.add(faCogs);
library.add(faAtom);
library.add(faBasketballBall);
library.add(faQuestionCircle);

const gamePole = ref([]);
const count = ref(0);
const win = ref(false);
const isRenderer = ref(true);
let clickPermission = true;
let cardOne = null;
let cardTwo = null;

const cardsInterective = (event) => {
    const clickedItem = event.target.closest(".game-item");
    if (clickPermission && !clickedItem.classList.contains("okay")) {
        clickedItem.classList.add("flip");
        if (cardOne == null) {
            cardOne = clickedItem;
        } else {
            if (cardOne != clickedItem) {
                cardTwo = clickedItem;
                clickPermission = false;
            }
        }

        if (cardOne != null && cardTwo != null && cardOne != cardTwo) {
            if (cardOne.getAttribute("data-item") === cardTwo.getAttribute("data-item")) {
                setTimeout(() => {
                    count.value += 1;
                    cardOne.classList.add("okay");
                    cardTwo.classList.add("okay");
                    cardOne = null;
                    cardTwo = null;
                    clickPermission = true;

                    if(count.value == gamePole.value.length/2) {
                        win.value = true;
                    }
                }, 500);
            } else {
                setTimeout(() => {
                    cardOne.classList.remove("flip");
                    cardTwo.classList.remove("flip");
                    cardOne = null;
                    cardTwo = null;
                    clickPermission = true;
                }, 1000);
            }
        }
    }
};
const restartGame = () => {
    win.value = false;
    count.value = 0;
    cardOne = null;
    cardTwo = null;
    clickPermission = true;
    document.querySelectorAll(".game-item").forEach((el) => {
        el.classList.remove("flip");
        el.classList.remove("okay");
    });
    shuffle();
};
const shuffle = () => {
    gamePole.value = [];
    gamePole.value = [
        "compass",
        "cloud",
        "stop",
        "play",
        "bolt",
        "cogs",
        "atom",
        "basketball-ball",
        "compass",
        "cloud",
        "stop",
        "play",
        "bolt",
        "cogs",
        "atom",
        "basketball-ball",
    ].sort(() => Math.round(Math.random() * 100) - 50);
    forceRender();
};
const forceRender = () => {
    isRenderer.value = false;
    nextTick(() => {
        isRenderer.value = true;
    });
};

shuffle();
</script>

<style lang="scss">
$color-complete: #5dc6ff; // <!-- ? blue -->
$color-flip: #76db82; // <!-- ? green -->
$color-static: #f5f5dc; // <!-- ? beige -->
$color-hover: #d1d1c0; // <!-- ? gray-beige -->
$color-icon: #111111; // <!-- ? dark -->
$color-text: #f2f2f2;
$color-conf: (rgb(255, 0, 191), rgb(228, 46, 46), rgb(14, 132, 241));
* {
    margin: 0;
    padding: 0;
    box-sizing: border-box;
}
html {
    font-family: "Fira Sans", "sans-serif";
    font-size: 18px;
    font-weight: 400;
    color: $color-icon;
}
body {
    width: 100%;
    height: 100vh;
    display: flex;
    align-items: center;
    justify-content: center;
}
.app-wrapper {
    display: flex;
    align-items: center;
    justify-content: center;
    flex-direction: column;

    .count {
        width: 100%;
        text-align: left;
    }

    .game-pole {
        display: flex;
        flex-wrap: wrap;
        width: 500px;
        height: 500px;

        .game-item {
            cursor: pointer;
            flex: 0 0 23%;
            aspect-ratio: 1;
            margin: 1%;
            background-color: $color-static;

            display: flex;
            justify-content: center;
            align-items: center;
            font-size: 32px;
            color: $color-icon;
            transition: background 0.2s ease-in, transform 0.25s linear;

            svg {
                display: none;
                & + svg {
                    display: block;
                }
            }

            &:hover {
                background-color: $color-hover;
            }
            &.flip {
                transform: perspective(100px) rotateY(180deg);
                background-color: $color-flip;

                svg {
                    display: block;
                    & + svg {
                        display: none;
                    }
                }
            }
            &.okay {
                background-color: $color-complete;
            }
        }
    }

    .congratulation-wrapper {
        position: fixed;
        top: 0;
        left: 0;
        width: 100%;
        height: 100%;
        z-index: 100;
        background-color: rgba(0, 0, 0, 0.1);
        backdrop-filter: blur(5px);
        transition: 0.24s;

        .btn-res {
            z-index: 150;
            outline: none;
            display: block;
            position: absolute;
            transform: translate(-50%, -50%);
            top: 50%;
            left: 50%;
            width: 200px;
            height: 56px;
            border: 0;
            background-color: $color-flip;
            color: $color-text;
            font-size: 24px;
            cursor: pointer;
        }

        [class|="conf"] {
            position: absolute;
            display: block;
            top: -10%;
        }

        @for $i from 1 through 150 {
            $width: random(8);
            $left: random(100);
            .conf-#{$i} {
                --left-dir: random(15);
                width: #{$width}px;
                height: #{$width * 0.4}px;
                background-color: nth($color-conf, random(3));
                left: unquote($left + "%");
                opacity: random() + 0.5;
                transform: rotate(#{random() * 360}deg);
                animation: conf-anim-#{$i} unquote(4 + random() + "s") unquote(random() + "s") infinite;

                @keyframes conf-anim-#{$i} {
                    100% {
                        top: 110%;
                        left: unquote($left + random(15) + "%");
                    }
                }
            }
        }

        &.win-show-enter-active,
        &.win-show-leave-active {
            transition: opacity 0.5s ease;

            .btn-res{
                transition: transform 0.3s ease-in-out;
                transition-delay: 0.2s;
            }
        }

        &.win-show-enter-from,
        &.win-show-leave-to {
            opacity: 0;

            .btn-res{
                transform: translate(-50%, -50%) scale(0.5);
            }
        }
    }
}
</style>
