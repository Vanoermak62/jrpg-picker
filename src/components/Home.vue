<script setup lang="ts">

import Quiz from './Quiz.vue';
import digitalRain from './digitalRain.vue';
import { Button } from '@/components/ui/button'
import {
  Card,
  CardContent,
  CardHeader,
  CardTitle,
} from '@/components/ui/card'

import { ref } from 'vue';

const isQuizOpen = ref(false)

function OpenQuiz() {
  isQuizOpen.value = true
}


</script>

<template>
  <div class="min-h-screen w-full flex items-center justify-center bg-background">
    <div
      class="w-[min(90%,600px)] flex flex-col items-center p-10 border-2 border-border rounded-2xl bg-card text-foreground">
      <digitalRain />
      <Card class="w-full border-0 bg-transparent shadow-none">
        <CardHeader>
          <CardTitle class="text-center text-foregound font-bold text-2xl caret-transparent">
            Не знаешь какую jrpg выбрать?
          </CardTitle>
        </CardHeader>

        <CardContent class="flex justify-center">
          <Button variant="outline" size="lg"
            class="border-border bg-transparent text-foreground hover:bg-primary-hover mt-6 caret-transparent cursor-pointer transition-colors duration-200"
            @click="OpenQuiz">
            Подобрать игру
          </Button>
        </CardContent>
      </Card>

      <Quiz v-if="isQuizOpen" @close="isQuizOpen = false" />
    </div>
  </div>

</template>
