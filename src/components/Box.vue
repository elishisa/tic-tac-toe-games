<template>
    <div :style="{ width: 'calc('+(100/fieldSize)+'% - 4px)', height: 'calc('+(100/fieldSize)+'% - 4px)' }" class="box">
        <div :style="{fontSize:'calc((var(--h) / '+fieldSize+'))', color: playerInfo.color}" class="symbol" v-html="playerInfo.player"></div>
    </div>
</template>

<script>
    export default {
        props: ["playerInfo", "fieldSize"]
    }
</script>

<style scoped>
    .box {
        display: flex;
        flex-direction: column;
        align-items: center;
        justify-content: center;

        --h: 80vh;
        height: var(--h);
        width: var(--h);

        float: left;
        border: 2px solid black;
    }

    .box:hover {
        cursor: pointer;
    }

    .symbol {
        font-family: Roboto, sans-serif;
        font-size: calc((var(--h) / 20));

        flex-basis: auto;
        flex-grow: 1;
        flex-shrink: 0;
    }
</style>