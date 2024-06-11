<template>
    <div class="score">
        Player <span>{{ playerPoints }}</span> x <span>{{ computerPoints }}</span> Computer
    </div>
</template>

<script>
    export default {
        name: "ScoreBoard",
        props: ["playerPoints", "computerPoints" ]
    };
</script>

<style lang="scss">
    .score {
        font-size: 24px;
        padding: 20px;
        border-bottom: 1px solid #000;
    
        span {
            padding: 10px;
            border: 1px solid #000;
            font-weight: bold;
        }
    }
</style>