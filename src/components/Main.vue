<script setup>
    import { onMounted, ref } from 'vue';
    import { SceneNode, CompoundObject, TransformedObject } from './main_modules/scene_graph_classes.vue';
    import { rectangle, fillRectangle, triangle, fillTriangle, circle, fillCircle, line } from './main_modules/primitive_shapes.vue';
    //global variables declaration
    let graphics;
    let gameEnv;
    let width;
    let height;
    let game;
    let tile;
    let world;
    let snakeDirection;
    let snakeDirectionString = ref('⬇️');
    let snakeColor = 2;

    //event listeners for controls
    window.addEventListener("keydown",(event)=>{
        if (event.key == "ArrowUp" || event.key.toUpperCase() == "W"){
            snakeDirection = 0
            snakeDirectionString.value = '⬆️'
            game.run()
        }
        else if (event.key == "ArrowDown" || event.key.toUpperCase() == "S"){
            snakeDirection = 1
            snakeDirectionString.value = '⬇️'
            game.run()
        }
        else if (event.key == "ArrowLeft" || event.key.toUpperCase() == "A"){
            snakeDirection = 2
            snakeDirectionString.value = '⬅️'
            game.run()
        }
        else if (event.key == "ArrowRight" || event.key.toUpperCase() == "D"){
            snakeDirection = 3
            snakeDirectionString.value = '➡️'
            game.run()
        }
    })

    function newDirection(direction){
        if (direction == 0){
            snakeDirection = 0
            snakeDirectionString.value = '⬆️'
            game.run()
        }
        else if (direction == 1){
            snakeDirection = 1
            snakeDirectionString.value = '⬇️'
            game.run()
        }
        else if (direction == 2){
            snakeDirection = 2
            snakeDirectionString.value = '⬅️'
            game.run()
        }
        else if (direction == 3){
            snakeDirection = 3
            snakeDirectionString.value = '➡️'
            game.run()
        }
    }

    //scene graph objects
    tile = new SceneNode()
    tile.doDraw = (graphics)=>{
        if (false && ['#008000','#00FF00'].includes(`${graphics.fillStyle}`)){
            graphics.save()
            graphics.scale(0.1,-0.2)
            graphics.translate(0.5,0)
            let noTriangles = 7
            for (let i=0; i<noTriangles; i++){
                fillTriangle(graphics)
                graphics.translate(10/noTriangles,0)
            }
            graphics.restore()
        }
        graphics.save()
        fillRectangle(graphics);
        graphics.lineWidth = 0.01;
        graphics.strokeStyle = 'white';
        rectangle(graphics);
        graphics.restore();
    }

    //game class ---------------------------------------------------------------------------
    class Game extends CompoundObject{
        constructor(boardSize=50){
            super();
            //initialize a 50 by 50 game state
            this.colors = {0:"white", 1:"black",2:"red",3:"green",4:"#7185BD",5:"#A3866A"}
            this.collisionColors = [2,3,4,5]
            this.state = []; //2d game state
            this.initState = [];
            this.initHeadCoords = [0,0]
            this.snakeCoords = []; //1d snake list of 2d coords
            this.foodCoords = []
            this.counter = 0;
            this.boardSize = boardSize;
            this.initializeGame(boardSize)
        }
        playable(head =this.initHeadCoords ){
            snakeDirectionString.value = 'checking playability...'
            for (let i=0; i<this.foodCoords.length; i++){
                let count = 0
                if (this.a_star(head,this.foodCoords[i])){
                    for (let i=0; i<4; i++){//check for two points of exit
                        let child = this.move(head,i)
                        if (!child){
                            continue;
                        }
                        if (!(this.state[child[0]][child[1]] == 0 || this.state[child[0]][child[1]] == 1)){ //if not (white or black tiles)
                            continue
                        }
                        count++;
                    }
                    if (count<2){
                        return false;
                    }
                }
                else{
                    return false;
                }
            }
            return true;
        }
        find_food(){
            this.foodCoords = [];
            for (let i=0; i<this.boardSize; i++){
                for (let j=0; j<this.boardSize; j++){
                    if (this.state[i][j] == 0){
                        this.foodCoords.push([i,j])
                    }
                }
            }
        }
        a_star(initialCoords=[0,0], goalCoords=[10,10]){
            //initialize frontier and history
            const history = {};
            const frontier = [];
            const hue = Math.abs(initialCoords[0]-goalCoords[0]) + Math.abs(initialCoords[1]-goalCoords[1])
            frontier.push([hue,0,hue,initialCoords]) //[f,cost,hueristics,coords]
            history[`${initialCoords}`] = [0,null] //key(coords) - value(cost)

            while (frontier.length > 0){
                const [f,cost,hueristics,myCoord] = frontier.shift()
                if (myCoord[0] == goalCoords[0] && myCoord[1] == goalCoords[1]){
                    //return the path...
                    return true;
                }

                for (let i=0; i<4; i++){
                    const child = this.move(myCoord,i);
                    if (!child){
                        continue
                    }
                    if (!(this.state[child[0]][child[1]] == 0 || this.state[child[0]][child[1]] == 1)){ //if not (white or black tiles)
                        continue
                    }
                    if (`${child}` in history){
                        continue
                    }
                    const hue = Math.abs(child[0]-goalCoords[0]) + Math.abs(child[1]-goalCoords[1])
                    frontier.push([hue+cost+1, cost+1, hue, child])
                    history[`${child}`] = [cost+1,myCoord]
                }
                frontier.sort((a,b)=>a[0]-b[0])
            }
            return false;
        }
        goalTest(){
            for (let i=0; i<this.boardSize; i++){
                for (let j=0; j<this.boardSize; j++){
                    if (this.state[i][j] == 0){
                        return false;
                    }
                }
            }
            return true;
        }
        getLevel(i,j){
            if (Math.random()<0.25){ //25% chance of generating an obsticle
                //5% chance to be the same obsticle with its surrounding
                const ticket = Math.random()
  
                if (i>0 && this.state[i-1][j] > 1 && ticket<0.33){ //not black or white
                    return this.state[i-1][j]
                }
                else if (j>0 && this.state[i][j-1] > 1 && ticket<0.66 ){ //not black or white
                    return this.state[i][j-1]
                }
                else if (i>0 && j>0 && this.state[i-1][j-1] > 1 && ticket<1){ //not black or white
                    return this.state[i-1][j-1]
                }
                let obsticle = 3 + Math.round(Math.random()*2)
                if (Math.random()<0.2){
                    obsticle = 3
                }
                return obsticle
            }
            else if (Math.random()<0.025){//5% of the remaining chances to generating food
                return 0
            }
            return 1
        }
        initializeGame(){
            const boardSize = this.boardSize
            let blockWidth = width/boardSize;
            let blockHeight = height/boardSize;
            
            for (let i=0; i<boardSize; i++){
                let row = []
                this.state.push(row)
                for (let j=0; j<boardSize; j++){
                    this.state[i].push(this.getLevel(i,j))
                    //push initial tiles
                    this.objects.push(new TransformedObject(tile).setScale(blockWidth,blockHeight).setTranslation(blockWidth*j,blockHeight*i))
                }
            }
            this.initState = structuredClone(this.state);
            this.find_food()
        }
        nextLevel(){
            this.state = []
            this.objects = []
            this.initializeGame();
            this.start()
        }
        drawState(){
            for (let i=0; i<this.state.length; i++){
                for (let j=0; j<this.state[0].length; j++){
                    this.objects[i*this.state.length + j].fillStyle = this.colors[this.state[i][j]];
                }
            }
            this.draw(graphics)
        }
        reset(){
            //reset the board
            for (let i=0; i<this.state.length; i++){
                for (let j=0; j<this.state[0].length; j++){
                    this.state[i][j] = this.initState[i][j]
                }
            }
        }
        start(){
            //find a playable game
            snakeDirectionString.value = 'checking the level 🖥️'
            if (!this.playable()){
                snakeDirectionString.value = "Failed, retrying... 🔄️"
                return this.nextLevel();
            }
            snakeDirectionString.value = 'Passed ✅'
            snakeDirectionString.value = '⬇️'
            //reset the game
            snakeDirection = 1;
            this.snakeCoords=[[0,0]]
            this.run();
            
        }
        move(head,direction=snakeDirection){
            let newHead;
            
            if (direction == 0 && head[0]>0){
                newHead = [head[0]-1,head[1]]
            }
            else if (direction == 1 && head[0]<this.state.length-1){
                newHead = [head[0]+1,head[1]]
            }
            else if (direction == 2 && head[1]>0){
                newHead = [head[0],head[1]-1]
            }
            else if (direction == 3 && head[1]<this.state[0].length-1){
                newHead = [head[0],head[1]+1]
            }
            else{
                return false
            }
            return newHead

        }
        run(){
            if (this.goalTest()){
                return this.nextLevel()
            }


            //get the snakes next head position
            const head = this.snakeCoords[0]
            let newHead = this.move(head);
            if (!newHead){ //invalid move
                return
            }

            //check head collison
            const collision = this.collisionColors.includes(this.state[newHead[0]][newHead[1]])
            if (collision){ //invalid state
                return
            }
            
            //remove the tail (grows every 1 seconds)
            if (this.state[newHead[0]][newHead[1]] != 0){
                this.snakeCoords.pop()
            }
            else{//set the white to be black
                this.initState[newHead[0]][newHead[1]] = 1
            }

            //clean the board, and add the new head to the front of the list
            this.reset();
            this.snakeCoords.unshift(newHead);

            //draw the snake
            for (const [r,c] of this.snakeCoords){
                this.state[r][c] = 2
            }

            this.drawState()
        }
    }

    //game initialization

    gameEnv = ref(null);

    function initGame(){
        graphics = gameEnv.value.getContext("2d");
        width = gameEnv.value.width;
        height = gameEnv.value.height;
        graphics.fillStyle = "rgb(0,200,130)";
        graphics.fillRect(0,0,width,height);
        drawWorld();
    }

    function drawWorld(){
        game = new Game()
        graphics.clearRect(0, 0, width, height);
        game.start()
    }


    onMounted(initGame);
</script>

<template>
    <section id="game">
        <canvas ref="gameEnv"></canvas>
        <section id="horizontal_controls">
            <button style="height: 50%;" @click='newDirection(2)'>⬅</button>
            <section id="vertical_controls">
                <button @click='newDirection(0)'>⬆</button>
                <button @click='newDirection(1)'>⬇</button>
            </section>
            <button style="height: 50%;" @click='newDirection(3)'>➡</button>
        </section>
    </section>
    <h1>Current direction : {{ snakeDirectionString }}</h1>
</template>

<style scoped>
    #horizontal_controls{
        display: flex;
        justify-content: space-evenly;
        align-items: center;
        user-select: none;
        background-color: rgba(0, 0, 255, 0.1);
        border-radius: 100%;
        height: 50vh;
        width: 50vh;
    }
    #vertical_controls{
        display: flex;
        flex-direction: column;
        justify-content: space-evenly;
        gap: 4vh;
        height: 100%;
    }

    button {
        height: 40%;
        width:10vh;
        font-size: 8vh;
        border-radius: 2vh;
        background: rgba(0, 0, 255, 0.6);
        border: none;
        z-index: 10;
    }

    #game{
            display: flex;
            flex-direction: column;
            align-items: center;
            gap: 20px;
    }

    @media (max-width: 767px) {
    /* Phones */
        canvas {
            width: 90vw;
            height: 90vw;
        }
    }

    @media (min-width: 768px) and (max-width: 1199px) {
    /* Tablets */
         canvas {
            width: 75vh;
            height: 75vh;
        }

        #game{
            flex-direction: row;
            align-items: center;
            gap: 20px;
        }
    }

    @media (min-width: 1200px) {
    /* Laptops / desktops */
        canvas {
            width: 75vh;
            height: 75vh;
        }

        #game{
            flex-direction: row;
            align-items: center;
            gap: 20px;
        }
    }


</style>