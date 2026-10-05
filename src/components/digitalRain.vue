<script setup>
const columns = [
    {
        x: 8,
        speed: 8,
        opacity: 0.25,
        symbols: [
            { value: '7' },
            { value: 'A' },
            { value: '3' },
            { value: 'K', color: 'red' },
            { value: '9' }
        ]
    },
    {
        x: 22,
        speed: 11,
        opacity: 0.4,
        symbols: [
            { value: '1' },
            { value: 'F' },
            { value: '2' },
            { value: 'B' },
            { value: '0' }
        ]
    },
    {
        x: 37,
        speed: 7,
        opacity: 0.3,
        symbols: [
            { value: 'X' },
            { value: '4' },
            { value: 'C', color: 'red' },
            { value: '8' },
            { value: '1' }
        ]
    },
    {
        x: 51,
        speed: 13,
        opacity: 0.5,
        symbols: [
            { value: '9' },
            { value: 'D' },
            { value: '7' },
            { value: '2' },
            { value: 'M', color: 'red' }
        ]
    },
    {
        x: 66,
        speed: 9,
        opacity: 0.35,
        symbols: [
            { value: '3' },
            { value: 'K' },
            { value: '0' },
            { value: 'F' },
            { value: '6' }
        ]
    },
    {
        x: 79,
        speed: 6,
        opacity: 0.25,
        symbols: [
            { value: 'A' },
            { value: '5', color: 'red' },
            { value: 'X' },
            { value: '1' },
            { value: 'C' }
        ]
    },
    {
        x: 93,
        speed: 10,
        opacity: 0.4,
        symbols: [
            { value: '8' },
            { value: 'B' },
            { value: '4' },
            { value: '7' },
            { value: 'N', color: 'red' }
        ]
    }
]
</script>




<template>

    <div class="fixed inset-0 pointer-events-none">
        <div class="absolute top-0 animate-rain" v-for="column in columns" :key="column.x"
            :style="{ left: `${column.x}%`, opacity: column.opacity, animationDuration: `${column.speed}s` }">

            <div :class="symbol.color === 'red' ? 'text-[#8F111F]' : 'text-muted-foreground'"
                v-for="symbol in column.symbols" :key="symbol.value">
                {{ symbol.value }}
            </div>
        </div>

    </div>

</template>