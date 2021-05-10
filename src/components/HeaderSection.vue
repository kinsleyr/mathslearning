<template>
    <div id="header" class="row">        
        <div class="col-4 m-3" id="logo">
            <img src="/SM Logo.png" alt="Maths Game" >
        </div>
        <div v-if="game_state == 2" class="col-7 m-3" id="score">
            <h2>Score</h2>
            <p>
                Your current score is: <span>{{score}}</span>
            </p>
        </div>
    </div>
</template>

<script>
export default {
    name: "MathGameHeaderSection",
    data() {
        return {
            score: 0,
            game_state: 2
        }
    },
    props: {
        //do we send in a the score to the header or use the vuex state
    }
}
</script>

<style scoped>
#logo {
    background-color: aquamarine;
    padding: 3em;
    border: 1px solid #ddd;
}
#score {

}
</style>