<script lang="ts">
  export let n = 5; // Default grid size

  type CellContent = [string, string]; // [content, colorCode]

  // Generate grid data
  let grid;

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

    function chooseConfiguration() {
        let validConfigurations = findValidConfigurations(n);
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
        let currColorIndex = 0;
        for (let i = 0; i < n; i++) {
            for (let j = 0; j < n; j++) {
                if (currGrid[i][j] === "Q") {
                    currAvailableSquares--;
                    grid[i][j] = ["Q", colors[currColorIndex]];
                    currColorIndex++;
                }
            }
        }
    }

    let directions = [
                [-1, 0],
                [1, 0],
                [0, -1],
                [0, 1]
    ];

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
            setTimeout(() => {
                alert("You win!");
            }, 200);
        }
    }
  }

  function generateNewBoard() {
    grid = Array(n).fill(null).map(() => 
      Array(n).fill(null).map(() => [" ", "white"] as CellContent)
    );
    let configuration = chooseConfiguration();
    setColors();
    numQueens = 0;

    //perform drunkard's walk on all queen squares
    for(let i = 0; i < n; i++) {
        for(let j = 0; j < n; j++) {
            if (grid[i][j][0] === "Q") {
                let stack = [[i, j]];
                let walkLength = Math.floor(Math.random() * (currAvailableSquares / (n-2))) + 1;
                drunkardsWalk(i, j, walkLength, grid[i][j][1], stack);
                grid[i][j][0] = " ";
            }
        }
    }

    //fill in remaining squares
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
  }

  generateNewBoard();
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
          <svg width="16" height="16" viewBox="0 0 16 16" fill="none" xmlns="http://www.w3.org/2000/svg">
            <path d="M10.875 4.25L8 7.125L5.125 4.25L4.25 5.125L7.125 8L4.25 10.875L5.125 11.75L8 8.875L10.875 11.75L11.75 10.875L8.875 8L11.75 5.125L10.875 4.25Z" fill="black" fill-opacity="0.75"></path>
          </svg>
        {:else if content === "Q"}
            <svg class="queens-icon-svg queens-card-queen" width="24" height="24" viewBox="0 0 24 24" xmlns="http://www.w3.org/2000/svg">
            <title>Queen</title>
            <g clip-path="url(#clip0_3812_70403)">
              <path d="M23.25 7C23.25 7.69 22.69 8.25 22 8.25C21.89 8.25 21.78 8.21 21.68 8.18L19 17.99H5L2.32 8.18C2.21 8.21 2.11 8.25 2 8.25C1.31 8.25 0.75 7.69 0.75 7C0.75 6.31 1.31 5.75 2 5.75C2.69 5.75 3.25 6.31 3.25 7C3.25 7.31 3.13 7.59 2.94 7.8L9 13L11.65 4.18C11.14 4.03 10.75 3.57 10.75 3C10.75 2.31 11.31 1.75 12 1.75C12.69 1.75 13.25 2.31 13.25 3C13.25 3.56 12.87 4.02 12.35 4.18L15 13L21.06 7.8C20.87 7.58 20.75 7.31 20.75 7C20.75 6.31 21.31 5.75 22 5.75C22.69 5.75 23.25 6.31 23.25 7ZM19 19H5C4.45 19 4 19.45 4 20C4 20.55 4.45 21 5 21H19C19.55 21 20 20.55 20 20C20 19.45 19.55 19 19 19Z"></path>
            </g>
            <defs>
              <clipPath id="clip0_3812_70403">
                <rect width="24" height="24" fill="white"></rect>
              </clipPath>
            </defs>
          </svg>
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