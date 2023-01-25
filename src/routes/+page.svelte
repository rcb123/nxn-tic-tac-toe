<script lang="ts">
	import { onMount } from 'svelte';

	let grid: string[] = new Array(9).fill('?');
	let dim = 3;
	let turn: string = 'X';

	let inputEl: HTMLInputElement;

	// Gets value at the row and column provided
	function getEntryAtRC(r: number, c: number): string {
		return grid[r * dim + c];
	}

	// Gets an row number from an index
	function idxToRow(idx: number): number {
		return Math.floor(idx / dim);
	}

	// Gets a column number form an index
	function idxToCol(idx: number): number {
		return idx % dim;
	}

	function checkForWinner(): boolean {
		let xchar = 'X'.charCodeAt(0);
		let ochar = 'O'.charCodeAt(0);

		// Check across row from left to right
		for (let row = 0; row < dim; row++) {
			let sum = 0;
			for (let column = 0; column < dim; column++) {
				sum += getEntryAtRC(row, column).charCodeAt(0);
			}

			if (sum == xchar * dim) {
				return true;
			} else if (sum == ochar * dim) {
				return true;
			}
		}

		// Check across column from top to bottom
		for (let column = 0; column < dim; column++) {
			let sum = 0;
			for (let row = 0; row < dim; row++) {
				sum += getEntryAtRC(row, column).charCodeAt(0);
			}

			if (sum == xchar * dim) {
				return true;
			} else if (sum == ochar * dim) {
				return true;
			}
		}

		// Check TL to BR diagonal
		let sum = 0;
		for (let i = 0; i < dim; i++) {
			sum += getEntryAtRC(i, i).charCodeAt(0);
		}
		if (sum == xchar * dim) {
			return true;
		} else if (sum == ochar * dim) {
			return true;
		}

		// Check TR to BL diagonal

		sum = 0;
		for (let i = 0; i < dim; i++) {
			sum += getEntryAtRC(i, dim - i - 1).charCodeAt(0);
		}
		if (sum == xchar * dim) {
			return true;
		} else if (sum == ochar * dim) {
			return true;
		}

		return false;
	}

	function checkForDraw(): boolean {
		for (let i = 0; i < dim * dim; i++) {
			if (grid[i] === '?') {
				return false;
			}
		}
		return true;
	}

	function printTTT(): void {
		let board: HTMLElement | null = document.getElementById('board');
		if (!board) {
			return;
		}
		board.innerHTML = '';
		for (let i = 0; i < dim; i++) {
			let row = board.insertRow();
			for (let j = 0; j < dim; j++) {
				let cell = row.insertCell();
				let idx = i * dim + j;
				cell.innerHTML = grid[idx];
				cell.addEventListener('click', () => {
					if (grid[idx] === '?') {
						grid[idx] = turn;
						cell.innerHTML = turn;
						if (checkForWinner()) {
							alert(turn + ' wins!');
							turn = 'X';
							printTTT();
						} else if (checkForDraw()) {
							alert("It's a draw!");
							turn = 'X';
							printTTT();
						} else {
							turn = turn === 'X' ? 'O' : 'X';
						}
					}
				});
			}
		}
	}

	function restart(): void {
		grid = new Array(dim * dim).fill('?');
		turn = 'X';
		printTTT();
	}

	function changeDimension(): void {
		let size: number = Number(inputEl.value);
		console.log(size);
		grid = new Array(size * size).fill('?');
		dim = size;
		printTTT();
	}

	onMount(() => {
		printTTT();
	});
</script>

<head>
	<title>Tic Tac Toe</title>
</head>
<div class="my-48 flex flex-col items-center justify-center align-middle">
	<h1 class="mb-4 text-3xl text-rose-700">NxN Tic Tac Toe Game</h1>

	<table id="board" class="table text-3xl" />

	<div class="form-control mt-5">
		<label class="input-group">
			<span>Size:</span>
			<input
				bind:this={inputEl}
				type="number"
				min="3"
				max="10"
				value="3"
				class="input-bordered input"
			/>
			<button on:click={changeDimension} class="btn-square btn w-fit px-3">Generate Board</button>
		</label>
	</div>

	<button on:click={restart} class="btn mt-5">Restart</button>
</div>
