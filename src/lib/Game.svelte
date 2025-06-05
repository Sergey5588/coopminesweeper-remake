<style>
    .cell {
        min-width: 2em;
        min-height: 2em;
        margin: 0;
        padding: 0;
        font-family: monospace;
        font-size: 16px;
        border-radius: 0px;
        user-select: none;
        font-weight: bold;
        
    }

    .closed-cell {
        background-color: grey;
    }
</style>


<script>
    function createRandomBinaryArray(rows, cols, numOnes) {
        // Проверка на корректность входных данных
        if (numOnes > rows * cols) {
            throw new Error("Количество единиц не может превышать общее количество элементов массива");
        }

        // Создаем одномерный массив с нужным количеством единиц и нулей
        const totalElements = rows * cols;
        const arr = new Array(totalElements).fill(0);
        for (let i = 0; i < numOnes; i++) {
            arr[i] = 1;
        }

        // Перемешиваем массив (Fisher-Yates shuffle)
        for (let i = arr.length - 1; i > 0; i--) {
            const j = Math.floor(Math.random() * (i + 1));
            [arr[i], arr[j]] = [arr[j], arr[i]];
        }

        // Преобразуем одномерный массив в двумерный
        const result = [];
        for (let i = 0; i < rows; i++) {
            result.push(arr.slice(i * cols, (i + 1) * cols));
        }

        return result;
    }
    const create2DArray = (rows, cols, value) => 
    Array(rows).fill().map(() => Array(cols).fill(value));

    

    let width = 16;
    let heigth = 16;

    const CellType = {
        FLAG : 10,
        CLOSED: 9,
        OPEN_BLANK: 0,
        
    };

    const Colors = {
        1:"#0000FF",
        2:"#008000",
        3:"#FF0000",
        4:"#000080",
        5:"#800000",
        6:"#008080",
        7:"#000000",
        8:"#808080"
    }
    //40
    let mines = createRandomBinaryArray(heigth,width,40)
    let state = $state(create2DArray(heigth,width,9))
    function count_n(x,y) {
        let c = 0
        for(let i = -1; i < 2; i++) {
            for(let j = -1; j < 2; j++){
                if(i== 0 && j == 0) {continue;}
                const yy = i+y
                const xx = j+x
                try{
                    if(mines[yy][xx] != undefined){
                        c+=mines[yy][xx];
                    }
                    
                }catch{}
                
                
                
            }
        }
        return c;
    }

    function updateMap() {
        for(let y = 0; y < heigth; y++) {
            for(let x = 0; x < width; x++){
                if(state[y][x] == 0){

                    for(let i = -1; i < 2; i++) {
                        for(let j = -1; j < 2; j++){
                            if(i== 0 && j == 0) {continue;}
                            const yy = i+y
                            const xx = j+x
                            try{
                                if(state[yy][xx] != undefined){
                                    open_cell(xx,yy);
                                }
                                
                            }catch{}
                            
                            
                            
                        }
                    }
                }
            }
        }
        
    }

    function open_cell(x,y) {
        if(mines[y][x] == 1) {
            loose();
        } else {
            if(state[y][x] == CellType.CLOSED){
                state[y][x] = count_n(x,y)
                if(state[y][x] == CellType.OPEN_BLANK) {updateMap();}
            
            }
        }
    }

    function toggle_flag(event, x,y) {
        if(event.button == 2){

            if(state[y][x] == 9){
                state[y][x] = 10
            } else if (state[y][x] == 10) {
                state[y][x] = 9
            }
        }
    }

    function loose() {
        alert("you lost!");
    }

    
    
</script>



<h1>Game here:</h1>
<svelte:window on:contextmenu|preventDefault={()=>{}} />

{#each state as row, y}
    {#each row as col, x}
        {#if col == 0}
            <button tabindex='-1' aria-label="Close" class="cell">&nbsp</button>
        {:else if col ==9}
            <button onmousedown={(e)=>{toggle_flag(e,x,y)}} tabindex='-1' aria-label="Close" class = "cell closed-cell" onclick={()=> {open_cell(x,y)}}>&nbsp</button>
        
        {:else if col==10}
            <button style="background-color: brown;" onmousedown={(e)=>{toggle_flag(e,x,y)}} tabindex='-1' aria-label="Close" class = "cell closed-cell" onclick={()=> {open_cell(x,y)}}>&nbsp</button>
        {:else}
            <button style="color: {Colors[col]};" onmousedown={(e)=>{toggle_flag(e,x,y)}} tabindex='-1' aria-label="Close" class = "cell" onclick={()=> {open_cell(x,y)}}>{col}</button>
        {/if}
    {/each}
    <br>
{/each}


