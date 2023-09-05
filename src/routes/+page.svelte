<script>
	import Select from 'svelte-select/Select.svelte';

	let selected;

	let simple = ['Fast', 'Medium', 'Slow'];

	function bfs(matrix) {
		const rows = matrix.length;
		const cols = matrix[0].length;
		const directions = [
			[-1, 0],
			[1, 0],
			[0, -1],
			[0, 1]
		];
		let start, end;
		const states = [];

		// find the start and end points
		for (let i = 0; i < rows; i++) {
			for (let j = 0; j < cols; j++) {
				if (matrix[i][j] === 1) {
					if (!start) {
						start = [i, j];
					} else {
						end = [i, j];
						break;
					}
				}
			}
		}

		const queue = [[start, []]];
		const visited = new Set();
		visited.add(start.toString());

		while (queue.length) {
			const [current, path] = queue.shift();
			const [x, y] = current;

			const matrixCopy = matrix.map((row) => row.slice());
			path.forEach(([px, py]) => (matrixCopy[px][py] = 2));
			matrixCopy[x][y] = 3;
			states.push(matrixCopy);

			if (x === end[0] && y === end[1]) {
				return states;
			}

			for (const [dx, dy] of directions) {
				const newX = x + dx;
				const newY = y + dy;

				if (
					newX >= 0 &&
					newX < rows &&
					newY >= 0 &&
					newY < cols &&
					!visited.has(newX + ',' + newY)
				) {
					queue.push([
						[newX, newY],
						[...path, [x, y]]
					]);
					visited.add(newX + ',' + newY);
				}
			}
		}

		return null; // no path found
	}

	function visualizeStep(states) {
		states.forEach((m, index) => {
			setTimeout(() => {
				matrix = m;

				m.forEach((row, i) => {
					row.forEach((cell, j) => {
						if (cell !== 0) {
							matrixVisitedIndexes.push([i, j]);
						}
					});
				});
			}, index * getVisualizeSpeed());
		});
	}

	function getVisualizeSpeed() {
		if (speedSelected.fast) return 10;
		if (speedSelected.medium) return 100;
		if (speedSelected.slow) return 300;
	}

	// ------------------------------------------------------------------------------------------------------
	let matrixVisitedIndexes = [];
	let matrix = Array.from({ length: 20 }, () => Array.from({ length: 20 }, () => 0));
	let nodesSelected = 0;
	let speedSelected = {
		fast: false,
		medium: true,
		slow: false
	};

	const nodeClickedHandler = (i, x) => {
		if (nodesSelected >= 2) return;

		nodesSelected++;

		const newMatrix = matrix.map((row, rowIndex) =>
			row.map((value, colIndex) => (rowIndex === i && colIndex === x ? 1 : value))
		);
		matrix = newMatrix;
	};

	const startClickedHandler = () => {
		bfs(matrix);

		const states = bfs(matrix);

		if (states) {
			// console.log(matrix);
			visualizeStep(states);
		} else {
			console.log('No path found');
		}
	};

	const getNodeColor = (value, i, x) => {
		// if (value == -1) return 'color-yellow';
		// if (matrixVisitedIndexes[i][x] != 0) return 'color-yellow';
		// console.log(`${i}, ${x}`);
		// console.log(matrixVisitedIndexes);

		// console.log(matrix);

		if (value == 1) return 'color-black'; // selected node
		if (value == 2) return 'color-orange'; // part of current path
		if (value == 3) return 'color-red'; // currently being visited
		if (matrixVisitedIndexes.some((el) => el[0] === i && el[1] === x)) return 'color-yellow'; // visited nodes
		if (value == 0) return 'color-gray'; // -_-
	};
</script>

<main class="text-center">
	<h1 class="text-2xl font-bold">Breadth First Search: A Pathfinding Algorithm</h1>
	<p>This application visualizes the patterns created by BFS</p>
	<div class="h-2" />
	<div class="flex flex-col justify-center">
		<p>Speed</p>
		<div class="w-40 ml-auto mr-auto">
			<Select
				items={simple}
				on:select={({ detail }) => {
					selected = detail.value;
					if (selected == 'Fast') {
						speedSelected.fast = true;
						speedSelected.medium = false;
						speedSelected.slow = false;
					} else if (selected == 'Medium') {
						speedSelected.fast = false;
						speedSelected.medium = true;
						speedSelected.slow = false;
					} else if (selected == 'Slow') {
						speedSelected.fast = false;
						speedSelected.medium = false;
						speedSelected.slow = true;
					}
				}}
			/>
		</div>
	</div>
	<div class="h-2" />
	<p>Select 2 nodes</p>
	<div class="container border-2 w-min p-2 flex flex-col gap-2 mr-auto ml-auto">
		{#each matrix as row, i}
			<div class="flex flex-row gap-2">
				{#each row as value, x}
					<button on:click={() => nodeClickedHandler(i, x)}>
						<div class={`${getNodeColor(value, i, x)} rounded-full w-[20px] h-[20px]`} />
					</button>
				{/each}
			</div>
		{/each}
	</div>
	<div class="h-2" />
	<button class="border-[1px] rounded-md border-black p-1" on:click={startClickedHandler}
		>Start</button
	>
</main>

<style>
	.container {
	}
	.color-red {
		background-color: red;
	}
	.color-black {
		background-color: black;
	}
	.color-gray {
		background-color: rgb(220, 220, 220);
	}
	.color-orange {
		background-color: darkorange;
	}
	.color-yellow {
		background-color: yellow;
	}
</style>
