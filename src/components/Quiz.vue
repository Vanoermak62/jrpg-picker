<script setup>
import { ref } from 'vue'
import { questions } from '../data/questions.js'
import { games } from '../data/games.js'
import {
    Card,
    CardContent,
    CardHeader,
    CardTitle,
} from '@/components/ui/card'
import { Progress } from '@/components/ui/progress'

const emit = defineEmits(['close'])

const currentQuestion = ref(0)
const selectedAnswers = ref([])
const isLoading = ref(false)
const resultScreen = ref(false)

const preferenceKeys = [
    "priority",
    "pace",
    "exploration",
    "combat",
    "length",
    "relationships",
    "world",
    "complexity"
]
const scales = {
    pace: {
        slow: 0,
        medium: 1,
        fast: 2
    },

    length: {
        short: 0,
        medium_length: 1,
        long: 2,
        very_long: 3
    },

    relationships: {
        relationships_low: 0,
        relationships_medium: 1,
        relationships_high: 2
    },

    complexity: {
        simple: 0,
        medium_complexity: 1,
        deep: 2
    }
}

function calculateMatch(answer, gameValue, scale) {
    if (answer === "any" || answer.endsWith("_any")) {
        return 100
    }

    const answerValue = scale[answer]
    const gameValueNumber = scale[gameValue]

    const difference = Math.abs(answerValue - gameValueNumber)

    const maxDiference = Math.max(...Object.values(scale)) - Math.min(...Object.values(scale))

    return 100 - (difference / maxDiference) * 100
}

let results = []
const resultIndex = ref(0)

function selectAnswer(option) {
    selectedAnswers.value.push(option.value)
    console.log(selectedAnswers.value)

    currentQuestion.value++

    if (currentQuestion.value >= questions.length) {
        results = resultGame(selectedAnswers.value)

        isLoading.value = true

        setTimeout(() => {
            resultScreen.value = true
            isLoading.value = false
        }, 1000)
    }
}

function resultGame(selectedAnswers) {
    const results = []

    games.forEach(game => {

        let gameScore = 0
        let matches = {}

        selectedAnswers.forEach((answer, index) => {

            const preferenceKey = preferenceKeys[index]
            const gameValue = game.preferences[preferenceKey]

            if (answer === gameValue) {
                gameScore++
            }

            if (answer === "any" || answer.endsWith("_any")) {
                matches[preferenceKey] = 100
            } else if (scales[preferenceKey]) {
                matches[preferenceKey] = calculateMatch(
                    answer,
                    gameValue,
                    scales[preferenceKey]
                )
            } else {
                matches[preferenceKey] =
                    answer === gameValue ? 100 : 0
            }
        })

        results.push({
            game: game,
            score: gameScore,
            matches: matches
        })
    })

    results.sort((a, b) => b.score - a.score)

    return results
}
function openSteam() {
    window.open(results[resultIndex.value].game.steamUrl, '_blank')
}
</script>

<template>
    <div class="fixed inset-0 z-1000 bg-black/70 flex items-center justify-center">

        <Card
            class="z-1001 flex flex-col items-center bg-card border border-border rounded-[20px] p-7.5 w-1/2 min-h-[50%] max-w-200 box-border"
            v-if="!isLoading && !resultScreen">

            <h2 class="text-2xl m-4 text-foreground">
                {{ questions[currentQuestion].question }}
            </h2>

            <button
                class="w-[80%] p-3.75 m-1.25 border border-border rounded-[10px] text-[18px] cursor-pointer bg-secondary hover:bg-button-hover transition-colors duration-200"
                v-for="option in questions[currentQuestion].options" :key="option.value" @click="selectAnswer(option)">
                {{ option.text }}
            </button>

            <button
                class="w-[20%] p-1.75 m-1.25 border border-border rounded-[10px] text-[18px] cursor-pointer bg-secondary"
                @click="emit('close')">
                Закрыть
            </button>

        </Card>

        <div class="z-1001 flex items-center bg-card border border-border rounded-[20px] p-7.5 min-h-[35%] max-w-200 box-border text-center"
            v-else-if="isLoading">

            <h1 class="text-2xl text-foreground">Подбираем игру для вас</h1>

        </div>

        <div class="z-1001 flex flex-col items-center bg-card border border-border rounded-[20px] p-7.5 min-h-[50%] w-1/2 max-w-375 box-border text-center"
            v-else-if="resultScreen">
            <h1 class="text-2xl text-foreground mb-8">
                {{ results[resultIndex].game.title }}
            </h1>

            <div class="flex justify-around">
                <img :src="results[resultIndex].game.image" :alt="results[resultIndex].game.title"
                    class="w-[30%] rounded-2xl m-4 ml-6" />
                <div>
                    <p class="text-xl">Описание:</p>
                    <p class="text-lg text-pretty ">
                        {{ results[resultIndex].game.description }}
                    </p>
                    <div class="pr-2">
                        <h2 class="m-2 p-2 text-2xl text-foreground">Совпадения с вашими предпочтениями:</h2>
                        <p class=" text-foreground text-lg">Темп</p>
                        <Progress :model-value="results[resultIndex].matches.pace" class="m-4 h-6" />
                        <p class=" text-foreground text-lg">Продолжительность</p>
                        <Progress :model-value="results[resultIndex].matches.lenght" class="m-4 h-6" />
                        <p class=" text-foreground text-lg">Отношения между персонажами</p>
                        <Progress :model-value="results[resultIndex].matches.relationships" class="m-4 h-6" />
                        <p class=" text-foreground text-lg">Комплексность</p>
                        <Progress :model-value="results[resultIndex].matches.complexity" class="m-4 h-6" />
                    </div>
                </div>
            </div>

            <div class="flex justify-between w-full mt-5">
                <button @click="resultIndex++"
                    class="rounded-[15px] p-4 text-base border border-border bg-secondary m-4 hover:bg-button-hover cursor-pointer transition-colors duration-200">
                    Подобрать другую игру
                </button>

                <button @click="openSteam"
                    class="rounded-[15px]  text-base border border-border bg-secondary m-4 p-4 hover:bg-button-hover cursor-pointer transition-colors duration-200">
                    Страница в стим
                </button>
            </div>
        </div>

    </div>
</template>



<style>
/* .gameImage {
    display: flex;
    justify-content: space-around;
}

.gameImage img {
    width: 30%;
    height: 30%;
}

.gameImage p {
    font-size: 25px;
}

.resultButtons {
    display: flex;
    justify-content: space-between;
    width: 100%;
    margin-top: 20px;
}

.resultButtons button {
    border-radius: 15px;
    padding: 15px;
    font-size: 15px
}

.resultScreen {
    z-index: 1001;

    display: flex;
    align-items: center;
    flex-direction: column;

    background-color: gray;
    border: 1px solid black;
    border-radius: 20px;

    padding: 30px;


    min-height: 50%;
    width: 50%;
    max-width: 1500px;

    box-sizing: border-box;
    text-align: center;
} */
</style>