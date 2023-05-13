<template>
    <v-container fluid full-height class="main-container">
        <div class="navbar">
            <div style="font-weight: bold; color: white">ADPomodoro</div>
            <div>
                <button class="nav-button" @click="onAuthorClick">Author</button>
            </div>
        </div>
        <br />
        <br />
        <div class="timer-container">
            <div class="timer-box">
                <div class="type-buttons">
                    <template v-for="(type, index) in Object.keys(types)" :key="index">
                        <button @click="() => changeType(type)"
                            :class="selectedType === type ? 'type-button' : 'timer-type-button'">{{
                                type }}</button>
                    </template>
                </div>
                <br>
                <div :class="`timer ${selectedType === 'Pomodoro' ? '' : ' timer-break '}`">
                    {{ minutes }} : {{ seconds < 10 ? '0' : '' }}{{ seconds }} </div>
                        <br>
                        <button class="start-button" @click="onStart">{{ !interval ? 'Start' : 'Reset' }}</button>
                </div>
            </div>
    </v-container>
</template>

<script lang="ts">
import start_work from '../assets/start_work.mp3';
import start_pause from '../assets/start_pause.mp3';

type TimerTypes = {
    Pomodoro: number;
    "Short break": number;
    "Long break": number;
}

export default {
    data(): {
        selectedType: string,
        types: TimerTypes,
        minutes: number,
        seconds: number,
        interval: null | number
    } {
        return {
            selectedType: 'Pomodoro',
            types: { Pomodoro: 25, "Short break": 5, "Long break": 15 },
            minutes: 0,
            seconds: 0,
            interval: null,
        }
    },

    beforeMount() {
        this.onReset();
    },

    computed: {
    },

    methods: {
        changeType(type: string) {
            this.selectedType = type;
            this.onReset();
        },

        onSettingsClick() {
            console.log('clicked')
        },

        onReset() {
            if (this.interval)
                window.clearInterval(this.interval);
            this.interval = null;
            this.seconds = 0;
            // @ts-ignore
            this.minutes = this.types[this.selectedType]
        },

        onStart() {
            if (this.interval) {
                this.onReset();
                return
            }
            if (this.selectedType === 'Pomodoro') {
                this.playSound(start_work);
            } else {
                this.playSound(start_pause);
            }
            this.interval = window.setInterval(() => {
                this.countDown();
            }, 1000)
        },

        countDown() {
            if (this.minutes === 0 && this.seconds === 0) {
                if (this.selectedType === 'Pomodoro') {
                    this.selectedType = "Long break";
                } else {
                    this.selectedType = "Pomodoro";
                }
                this.onReset();
                this.onStart();
            }
            if (this.seconds === 0) {
                this.minutes--;
                this.seconds = 59;
            } else {
                this.seconds--;
            }
        },

        playSound(sound: any) {
            const audio = new Audio(sound);
            audio.play();
        },

        onAuthorClick() {
            alert('Author: Adrian Petran');
        }


    }
}
</script>

<style>
.main-container {
    display: flex;
    flex-direction: column;
}

.navbar {
    display: flex;
    justify-content: space-around;
    padding: 24px;
}

button {
    padding: 12px;
    font-weight: 500;
    margin-left: 4px;
    margin-right: 4px;
    padding-top: 6px;
    padding-bottom: 6px;
    border-radius: 4px;
    border: none;
    cursor: pointer;
}

.nav-button {
    background-color: rgba(255, 255, 255, .5);
    color: white;
}

.timer-type-button {
    background-color: rgba(255, 255, 255, 0);
    color: white;
    font-size: 18px;
}

.type-buttons{
    flex-wrap: nowrap;
    display: flex;
}

.type-button {
    background-color: rgba(255, 255, 255, 0.3);
    color: white;
    font-size: 18px;
    font-weight: bold;
}

.timer-container {
    display: flex;
    justify-content: center;
}

.timer-box {
    display: flex;
    flex-direction: column;
    align-items: center;
    background-color: rgba(255, 255, 255, 0.5);
    width: 500px;
    padding: 24px;
    border-radius: 10px;
    margin: 24px;
}

.timer {
    color: white;
    font-weight: bold;
    font-size: 84px;
}

.timer-break {
    color: lightgreen !important;
}

.start-button {
    color: red;
    background-color: white;
    font-weight: bold;
    font-size: 32px;
    padding: 10px;
    padding-left: 60px;
    padding-right: 60px;
    min-width: 100px;
    border-radius: 10px;
}
</style>