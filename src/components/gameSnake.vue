<template>
    <div class="game">
        <div class="ball" :style="{left: ballX + 'px', top: ballY + 'px'}" :class="{
                'disable': gameFinish == true
            }"></div>
        <div class="huynaSnizu" :style="{left: huynaSnizuX + 'px'}"></div>

        <div class="board-row" v-for="(row, rowIdx) in Board" :key="rowIdx">
            <div class="board-cell" v-for="(cell, cellIdx) in row" :key="cellIdx"
            :class="{
                'enable': cell == 1,
                'disable': cell == 0
            }"></div>
        </div>
        <div class="popa"></div>
    </div>
</template>

<script setup>
import { onBeforeMount, onMounted, reactive, ref } from 'vue'

const ballX = ref(innerWidth / 2 - 10)
const ballY = ref(innerHeight - 60)

const Board = reactive([])

let ballSpeed = 10

let ballVecX = Math.sin(45) * ballSpeed
let ballVecY = - Math.sin(45) * ballSpeed

let gameStarted = false
let gameFinish = false

const huynaSnizuX = ref(innerWidth / 2 - 100)

onBeforeMount(() => {
    initBlocks()
})

addEventListener('mousedown', gameStart)
addEventListener('mousemove', pos, false)

function initBlocks() {
    for (let y = 0; y < 5; y++)
    {
        let row = []
        for (let x = 0; x < 5; x++)
        {
            row.push(1)
        }
        Board.push(row)
    }
}

function pos(e) {
    if (gameStarted == true) {
        if (e.pageX < 110) {
            huynaSnizuX.value = 10
        }
        else if (e.pageX > innerWidth - 110) {
            huynaSnizuX.value = innerWidth - 210
        }
        else {
            huynaSnizuX.value = e.pageX - 100
        }
    }
}

function gameStart(e) {
    if (gameStarted == false) {
        setInterval(() => RenderBall(), 33)
        gameStarted = true
    }
}

function collisionCheck() {
    for (let y = 0; y < Board.length; y++) {
        for (let x = 0; x < Board[y].length; x++) {
            if (Board[y][x] == 1) {
                let bx = innerWidth * 0.19 * y + innerWidth * 0.05
                let by = 60 * y + 10
                let w = innerWidth * 0.09
                let h = 40
                if (ballX.value + 40 >= bx & ballY.value + 40 >= by & ballX.value <= (bx + w) & ballY.value <= (by + h)) {
                    console.log(x, y, "pensil")
                }
            }
        }
    }
}

function RenderBall() {
    collisionCheck()
    ballX.value += ballVecX
    ballY.value += ballVecY
    if (ballX.value > innerWidth - 20) {
        ballX.value = innerWidth - 20
        ballVecX = -ballVecX
    }
    if (ballX.value < 0) {
        ballX.value = 0
        ballVecX = -ballVecX
    }
    if (ballY.value < 0) {
        ballY.value = 0
        ballVecY = -ballVecY
    }
    if (ballY.value > innerHeight - 60 && ballX.value >= huynaSnizuX.value && ballX.value <= huynaSnizuX.value + 200) {
        // cocent = (ballX.value - huynaSnizuX.value) / 100
        // ballVecY = -Math.floor(Math.cos(90 * cocent) * ballSpeed)
        // ballVecX = Math.abs(Math.floor(Math.sin(90 * cocent) * ballSpeed))
        // console.log(cocent)
        // console.log(ballVecX)
        // console.log(ballVecY)
        ballY.value = innerHeight - 60
        ballVecY = -Math.abs(ballVecY)
    }
    else if (ballY.value > innerHeight - 20) {
        ballY.value  = innerHeight - 20
        ballVecY = 0
        ballVecX = 0
        gameFinish = true
    }

}
</script>

<style>
.popa {
    position: absolute;
    left: 0px;
    top: 0px;
    width: 460.8px;
    height: 10px;
}

.game {
    margin: 0;
    height: 100vh;
    background-color: black;
}

.board-cell {
    margin: 10px 5vw;
    width: 9vw;
    height: 40px;
    display: inline-block;
    background-color: green;
}

.ball {
    display: block;
    position: absolute;
    width: 20px;
    height: 20px;
    border-radius: 50%;
    background-color: rebeccapurple;
}
.huynaSnizu {
    display: block;
    position: absolute;
    width: 200px;
    height: 20px;
    bottom: 20px;
    background-color: aqua;
}
.stat {
    display: block;
    position: absolute;
}

.disable {
    background-color: black;
}

</style>