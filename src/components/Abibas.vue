<template>
    <div class="container">
        <div class="app d-flex align-items-center justify-content-center" id="app_wrap">
            <div class="w-100">
                <div class="text-center">
                    <img src="@/assets/abibas.svg" class="logo">
                    <p class="mt-3 opacity-25">Про чо застелить епта?</p>
                </div>
                <div class="interface">
                    <transition name="fade" mode="out-in">
                        <!-- Prompt -->
                        <div v-if="abiState === 'prompt'" key="prompt">
                            <form @submit.prevent="generate">
                                <div class="d-flex justify-content-center">
                                    <p class="mb-0 align-self-center">Про: </p>
                                    <input @input="checkprompt" v-model="prompt" type="text" class="cust_input ms-1 w-100"
                                        name="prompt" placeholder="любовь..." required>
                                </div>
                                <div class="text-center mt-3">
                                    <button type="submit" class="but clr w-100"
                                        :class="{ disabled: !pReady }">Трави</button>
                                </div>
                                <!-- Mode -->
                                <div class="form-check form-switch mt-3">
                                    <input @change="abimode" v-model="mode" class="form-check-input" type="checkbox"
                                        role="switch" id="flexSwitchCheckDefault">
                                    <label class="form-check-label" for="flexSwitchCheckDefault">Включить примитивный режим
                                        <small class="opacity-25">(работает быстрее, но тупее)</small></label>
                                </div>
                            </form>
                        </div>
                        <!-- Think -->
                        <div class="thinker" v-else-if="abiState === 'think'" key="think"
                            style="width:100%;height:0;padding-bottom:56%;position:relative;">
                            <iframe src="https://giphy.com/embed/fmb0rfGP5Wumk" width="100%" height="100%"
                                style="position:absolute" frameBorder="0" class="giphy-embed"></iframe>
                            <p class="mb-0 text-center" id="think">Погоди, ща выдам епта...</p>
                        </div>
                        <!-- Result -->
                        <div v-else-if="abiState === 'result'" key="result">
                            <p class="mb-0 text-center" id="result">{{ bazar }}</p>
                            <button @click="abiState = 'prompt'" class="but clr w-100 mt-3" id="real_talk">{{ knopka
                            }}</button>
                        </div>
                    </transition>
                </div>
            </div>
        </div>
    </div>
</template>

<script>
import axios from 'axios'
export default {
    name: 'Abibas',
    data() {
        return {
            abiState: 'prompt',
            pReady: false,
            getgen: 'api/clever.php',
            mode: false,
            prompt: ''
        }
    },
    mounted() {
        document.getElementById("app_wrap").style.height = window.innerHeight + 'px';
    },
    methods: {
        checkprompt: function (e) {
            var inp = e.target.value;
            if (!inp) {
                this.pReady = false
            } else {
                this.pReady = true
            }
        },
        abimode() {
            var debil = this.mode;
            if (debil) {
                this.getgen = 'api/stupid.php';
            } else {
                this.getgen = 'api/clever.php';
            }
        },
        generate() {
            this.abiState = 'think';
            const ask = {
                prompt: this.prompt
            }
            axios.post(this.getgen, ask, {
                headers: {
                    'Content-Type': 'application/x-www-form-urlencoded'
                }
            })
                .then(response => {
                    const choices = response.data.choices;
                    this.bazar = choices[0].text;
                    this.abiState = 'result';
                    this.knopka = 'Красиво сказал брат!';
                })
                .catch(error => {
                    this.abiState = 'result';
                    this.bazar = 'Чото закипел я';
                    this.knopka = 'Отстой бро';
                })
        }
    }
}
</script>

<style lang="less">
.fade-enter-active,
.fade-leave-active {
    transition: opacity 0.5s ease;
}

.fade-enter-from,
.fade-leave-to {
    opacity: 0;
}
</style>