## Markdown Content


<div class="canvas" v-for="row in canvas">
    <div :style="{backgroundColor: !item ? 'white' : (item == 1 ? 'black' : 'red')}" v-for="item in row"></div>
</div>


<script setup>
import { reactive, onMounted } from 'vue'

const size = 15
let temp = []
for (let i = 0; i < size; i++) {
    temp.push([])
    for (let j = 0; j < size; j++) {
        temp[i].push(0)
    }
}
const canvas = reactive(temp)

function setAddr(addr, bit) {
    canvas[addr[0]][addr[1]] = bit
}

const bodyList = []

function addrInList(addr) {
    return JSON.stringify(bodyList).indexOf(JSON.stringify(addr)) != -1
}

function newApple() {
    let newAddr = undefined
    do {
        newAddr = [Math.floor(Math.random() * canvas.length), Math.floor(Math.random() * canvas.length)]
    } while (addrInList(newAddr))

    return newAddr
}

const initHead = newApple()
setAddr(initHead, 1)
bodyList.push(initHead)


let direction = undefined
const up_space = initHead[0]
const down_space = canvas.length - 1 - initHead[0]
const left_space = initHead[1]
const right_space = canvas.length - 1 - initHead[1]

const max_space = Math.max(up_space, down_space, left_space, right_space)
if (max_space == up_space) {
    direction = 'ArrowUp'
}
else if (max_space == down_space) {
    direction = 'ArrowDown'
}
else if (max_space == left_space) {
    direction = 'ArrowLeft'
}
else {
    direction = 'ArrowRight'
}

setAddr(newApple(), 2)

let acceptSet = false

document.addEventListener('keydown', (event) => {
    if (!acceptSet) {
        return
    }
    if (direction == 'ArrowUp' && event.key == 'ArrowDown') {
        return
    }
    if (direction == 'ArrowDown' && event.key == 'ArrowUp') {
        return
    }
    if (direction == 'ArrowLeft' && event.key == 'ArrowRight') {
        return
    }
    if (direction == 'ArrowRight' && event.key == 'ArrowLeft') {
        return
    }

    direction = event.key
    acceptSet = false
})

function autoMove() {
    let head = [...bodyList[bodyList.length - 1]]

    if (direction == 'ArrowRight') {
        head[1] += 1
    }
    if (direction == 'ArrowLeft') {
        head[1] -= 1
    }
    if (direction == 'ArrowUp') {
        head[0] -= 1
    }
    if (direction == 'ArrowDown') {
        head[0] += 1
    }

    if (head.indexOf(-1) != -1 || head.indexOf(canvas.length) != -1 || addrInList(head)) { // 在这里写失败的逻辑，head的x和y小于0或者等于canvas.length，或者head已经在bodyList里了（JSON.strinfy比较两个字符串一个是否包含另一个）
        alert('游戏失败')
        clearInterval(movement)
        return
    }

    bodyList.push(head)

    if (!canvas[head[0]][head[1]]) {
        setAddr(bodyList.shift(), 0)
    }
    else {
        setAddr(newApple(), 2)
    }

    setAddr(head, 1)
    acceptSet = true
}

const movement = setInterval(autoMove, 500)
      
</script>

<style>
    .canvas {
       display: flex;
       
        > * {
            width: 20px;
            height: 20px;
            border: solid 1px;
        }
    }
</style>




