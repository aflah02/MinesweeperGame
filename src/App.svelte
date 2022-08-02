<script>
	let gameOver=false;
	let won=false;
	let lost=false;
	let grid = [];
	let row = 10;
	let col = 10;
	let bombs = 10;
	let totalChooseable = row*col-bombs;
	let revealedSoFar = 0;
	for (let i=0;i<row;i++){
		grid[i] = new Array(col);
		for (let j=0;j<col;j++){
			grid[i][j] = {
				bomb: false,
				revealed: false,
				distance:0
			}
		}
	}
	for (let i=0;i<bombs;i++){
		let x = Math.floor(Math.random()*row);
		let y = Math.floor(Math.random()*col);
		if (grid[x][y].bomb){
			i--;
			continue;
		}
		grid[x][y].bomb = true;
	}
	console.log(grid);
	function countDistanct(i,j){
		if (grid[i][j].bomb){
			grid[i][j].distance=-1;
			return;
		}
		let count = 0;
		for (let x=-1; x<=1; x++){
			for (let y=-1; y<=1; y++){
				try{
					if (grid[i+x][j+y].bomb){
						count++;
					}
				} catch(msg){

				}
			}
		}
		grid[i][j].distance = count;
	}
	for (let i=0;i<row;i++){
		for (let j=0;j<col;j++){
			countDistanct(i,j);
		}
	}
	function revealBox(i,j){
		grid[i][j].revealed = true;
		revealedSoFar++;
		if (revealedSoFar==totalChooseable){
			gameOver = true;
			won = true;
			var audio = new Audio('assets/youWin.m4a');
			audio.play();
		}
		if (grid[i][j].bomb){
			var audio = new Audio('assets/gameOver.m4a');
			audio.play();
			gameOver = true;
			lost = true;
			for (let i=0;i<row;i++){
				for (let j=0;j<col;j++){
					if (grid[i][j].bomb){
						grid[i][j].revealed = true;
					}
				}
			}
		}
		if (grid[i][j].distance == 0){
			for (let x=-1; x<=1; x++){
				for (let y=-1; y<=1; y++){
					try{
						if (!grid[i+x][j+y].revealed && !grid[i+x][j+y].revealed){
							revealBox(i+x,j+y);
						}
					} catch(msg){

					}
				}
			}
		}
	}
</script>

<style>
	.game {
		position: fixed;
		top: 50%;
		left: 50%;
		transform: translate(-50%, -50%);
		width: 400px;
		height: 400px;
		display: flex;
		flex-wrap: wrap;
	}
	.game > div {
		width:10%;
		height:10%;
		text-align: center;
		line-height: 40px;
		border: 1px solid #111;
		box-sizing: border-box;
		cursor: pointer;
		position: relative;
	}
	.game > div.bomb:before {
		content: "";
		position: absolute;
		top: 50%;
		left: 50%;
		transform: translate(-50%, -50%);
		width: 20px;
		height: 20px;
		background: #111;
		border-radius: 50%;
	}
	.game > div.revealed{
		background: #ddd;
	}
</style>

{#if gameOver && lost}
	<h1 style="text-align: center; font-size: 40px; font-weight: bold; margin-top: 0px">
		Game Over
	</h1>
{:else if gameOver && won}
	<h1 style="text-align: center; font-size: 40px; font-weight: bold; margin-top: 0px">
		You Won
	</h1>
{:else}
	<h1 style="text-align: center; font-size: 40px; font-weight: bold; margin-top: 0px">
		Minesweeper Game
	</h1>
{/if}
<div class="game">
	{#each grid as row,i}
		{#each row as box,j}
			{#if box.revealed}
			<div class:bomb={box.bomb} class:revealed={box.revealed}>
				{#if box.distance > 0}
					{box.distance}
				{/if}
			</div>
			{:else}
				<div on:click={()=>{revealBox(i,j)}}>
				</div>
			{/if}
		{/each}
	{/each}
</div>