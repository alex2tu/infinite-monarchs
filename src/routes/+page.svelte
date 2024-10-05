<script lang="ts">
  export let n = 8; // Default grid size

  type CellContent = [string, string]; // [content, colorCode]

  // Generate grid data
  let grid = Array(n).fill(null).map(() => 
    Array(n).fill(null).map(() => [" ", "white"] as CellContent)
    );

    // console.log(grid);

// If you still want reactivity when n changes:

  function findValidConfigurations(n) {
    //create empty chess board
    //String in javascript is immutable, so here I use 2D array for chess board
    var chessBoard = new Array(n);
    for(var i = 0; i < n; i++) {
        chessBoard[i] = new Array(n).fill(" ");
    }
    
    var result = [];
    
    var isValidQueen = function(row, col) {
        //check for spots above on this column
        for(let i = 0; i < row; i++) {
            if(chessBoard[i][col] === "Q") return false;
        }
        //check for up left
        if (row > 0 && col > 0) {
            if (chessBoard[row - 1][col - 1] === "Q") return false;
        }
        //check for up right
        if (row > 0 && col < n - 1) {
            if (chessBoard[row - 1][col + 1] === "Q") return false;
        }
        return true;
    }
    
    var backtrack = function(row) {
        if(row === n) {
            result.push(chessBoard.map(row => [...row]));
            return;
        }
        for(var col = 0; col < n; col++) {
            if(isValidQueen(row, col)) {
                chessBoard[row][col] = "Q";
                backtrack(row + 1);
                chessBoard[row][col] = " ";
            }
        }
    }
    backtrack(0);
    return result;
  }

  let validConfigurations = findValidConfigurations(n);

//   console.log(validConfigurations);

    function chooseConfiguration() {
        let randomIndex = Math.floor(Math.random() * validConfigurations.length);
        return validConfigurations[randomIndex];
    }

    const colors = [
        "red",
        "blue",
        "green",
        "yellow",
        "purple",
        "orange",
        "pink",
        "teal",
        "indigo",
        "maroon"
    ];

    let currAvailableSquares = n * n;

    function setColors() {
        let currGrid = chooseConfiguration();
        // console.log(currGrid);
        let currColorIndex = 0;
        for (let i = 0; i < n; i++) {
            for (let j = 0; j < n; j++) {
                if (currGrid[i][j] === "Q") {
                    currAvailableSquares--;
                    // console.log("Queen at " + i + " " + j);
                    // console.log("Color: " + colors[currColorIndex]);
                    // console.log(grid);
                    // console.log(grid[i][j]);
                    grid[i][j] = ["Q", colors[currColorIndex]];
                    // console.log(grid[i][j]);
                    currColorIndex++;
                }
            }
        }
    }

    setColors();

    let directions = [
                [-1, 0],
                [1, 0],
                [0, -1],
                [0, 1]
    ];

    for(let i = 0; i < n; i++) {
        for(let j = 0; j < n; j++) {
            if (grid[i][j][0] === "Q") {
                // console.log(grid[i][j]);
                let stack = [[i, j]];
                let walkLength = Math.floor(Math.random() * (currAvailableSquares / (n-1))) + 1;
                // console.log(walkLength);
                drunkardsWalk(i, j, walkLength, grid[i][j][1], stack);
            }
        }
    }

    function drunkardsWalk(row, col, steps, color, stack) {
        if (steps === 0) {
            // console.log("hello")
            return;
        }
        if (grid[row][col][1] === "white") {
            // console.log("test 1");
            grid[row][col][1] = color; 
            // console.log("testing" + grid[row][col]);
            currAvailableSquares--;
            stack.push([row, col]);
        }
        while (checkAdjacency(row, col) === false && stack.length > 0) {
            [row, col] = stack.pop();
        }
        if (stack.length === 0) {
            return;
        }
        let newRow, newCol;
        while (true) {
            let [dx,dy] = directions[Math.floor(Math.random() * 4)];
            newRow = row + dx;
            newCol = col + dy;
            if (newRow < 0 || newRow > n-1 || newCol < 0 || newCol > n-1) {
                continue;
            }
            if (grid[newRow][newCol][1] === "white") {
                break;
            }
        }
        drunkardsWalk(newRow, newCol, steps - 1, color, stack);
    }

    function checkAdjacency(x,y) { //checks if has an available adjacency
        for (let dir of directions) {
            let [dx,dy] = dir;
            let newX = x + dx;
            let newY = y + dy
            if (newX < 0 || newX > n-1 || newY < 0 || newY > n-1) {
                continue
            }
            // console.log(grid[newX][newY]);
            if (grid[newX][newY][1] == "white") {
                return true;
            }
        }
        return false;
    }
    console.log("hello");

    //assign colors to all the remaining squares
    while (currAvailableSquares > 0) {
        for (let i = 0; i < n; i++) {
            for (let j = 0; j < n; j++) {
                if (grid[i][j][1] === "white") {
                    for (let k = 0; k < 4; k++) {
                        let [dx, dy] = directions[Math.floor(Math.random() * 4)];
                        let newX = i + dx;
                        let newY = j + dy;
                        if (newX < 0 || newX > n-1 || newY < 0 || newY > n-1) {
                            continue;
                        }
                        if (grid[newX][newY][1] !== "white") {
                            grid[i][j][1] = grid[newX][newY][1];
                            currAvailableSquares--;
                            break;
                        }
                    }
                }
            }
        }
    }

    //get rid of queens
    for (let i = 0; i < n; i++) {
        for (let j = 0; j < n; j++) {
            if (grid[i][j][0] === "Q") {
                grid[i][j][0] = " ";
            }
        }
    }

  let cellVals = [" ", "X", "Q"];
  let numQueens = 0;

  function checkWin() {
    // Check if there are any queens in the same row
    for (let i = 0; i < n; i++) {
      let queenCount = 0;
      for (let j = 0; j < n; j++) {
        if (grid[i][j][0] == "Q") {
          queenCount++;
        }
      }
      if (queenCount > 1) {
        return false;
      }
    }

    // Check if there are any queens in the same column
    for (let i = 0; i < n; i++) {
      let queenCount = 0;
      for (let j = 0; j < n; j++) {
        if (grid[j][i][0] == "Q") {
          queenCount++;
        }
      }
      if (queenCount > 1) {
        return false;
      }
    }

    // Check if there are any queens touching each other
    let dirs = [
        [-1, -1], [-1, 0], [-1, 1],
        [0, -1], [0, 1],
        [1, -1], [1, 0], [1, 1]
    ];
    for (let i = 0; i < n; i++) {
        for (let j = 0; j < n; j++) {
            if (grid[i][j][0] == "Q") {
                for (let dir of dirs) {
                    let x = i + dir[0];
                    let y = j + dir[1];
                    if (x >= 0 && x < n && y >= 0 && y < n) {
                        if (grid[x][y][0] == "Q") {
                            return false;
                        }
                    }
                }
            }
        }
    }
    return true;
  }

  // @ts-ignore
  function handleClick(row: number, col: number) {
    console.log(`Clicked cell at (${row}, ${col})`);
    console.log(`Current value: ${grid[row][col]}`);
    if (grid[row][col][0] == " ") {
      grid[row][col][0] = cellVals[1];
    } else if (grid[row][col][0] == "X") {
      grid[row][col][0] = cellVals[2];
      numQueens++;
    } else {
      grid[row][col][0] = cellVals[0];
      numQueens--;
    }
    grid = grid; // Trigger reactivity

    if (numQueens >= n) {
        if (checkWin()) {
            console.log("You win!");
        }
        else {
            console.log("You lose!");
        }
    }
  }
//   console.log(grid);
</script>

<div class="grid-container" style="grid-template-columns: repeat({n}, 1fr);">
  {#each grid as row, i}
    {#each row as [content, color], j}
      <div 
        class="cell" 
        on:click={() => handleClick(i, j)}
        style="background-color: {color}">
        {#if content === "X"}
          x
        {:else if content === "Q"}
          <img src="src/img/queen.png" class="queen-image">
        {/if}
      </div>
    {/each}
  {/each}
</div>

<style>
  .grid-container {
    display: grid;
    gap: 1px;
    border: 1px solid #ccc;
    background-color: #ddd;
    max-width: 400px;
    margin: 20px auto;
    
    user-select: none;
    -webkit-user-select: none;
  }

  .cell {
    aspect-ratio: 1;
    display: flex;
    align-items: center;
    justify-content: center;
    font-size: 1.5em;
    cursor: pointer;
    transition: background-color 0.3s;
  }

  .cell:hover {
    background-color: #f0f0f0;
  }

  .queen-image {
    width: 50%;
    height: 50%;
    pointer-events: none;
    -webkit-user-drag: none;
    user-select: none;
    -moz-user-select: none;
    -webkit-user-select: none;
    -ms-user-select: none;
  }
</style>