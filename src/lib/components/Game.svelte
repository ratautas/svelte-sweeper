<script>
  const SIZE = 8;
  const DIFFICULTY = 10;

  // Populate board with random mine locations:
  let rows = new Array(SIZE)
    .fill(new Array(SIZE).fill(null))
      .map(row => row.map(col => ({
        isMine: Math.random() < DIFFICULTY / 100,
      })
    ));

  const handleRightClick = (x, y) => rows[x][y].isMarked = !rows[x][y].isMarked;

  const reveal = (x,y) => {
    const tile = rows[x][y];
    if (tile.isMine) return alert('mine!');
    if (tile.isChecked) return;

    rows[x][y].isChecked = true;

    if (tile.adjacentMines !== 0) return;

    getAdjacentTiles(x, y).forEach(([key, val]) => {
      const [x, y] = key.split(':').map(Number);
      const tile = rows[x][y];
      if (tile.isMine) return;
      reveal(x,y);
    });
  }

  const getAdjacentMines =(x, y) => getAdjacentTiles(x, y).filter(([key, val]) => val.isMine).length;

  function getAdjacentTiles(x,y) {
    return [
      [`${x}:${y-1}`, rows[x]?.[y-1]],
      [`${x}:${y+1}`, rows[x]?.[y+1]],
      [`${x-1}:${y}`, rows[x-1]?.[y]],
      [`${x+1}:${y}`, rows[x+1]?.[y]],
      [`${x-1}:${y-1}`, rows[x-1]?.[y-1]],
      [`${x-1}:${y+1}`, rows[x-1]?.[y+1]],
      [`${x+1}:${y-1}`, rows[x+1]?.[y-1]],
      [`${x+1}:${y+1}`, rows[x+1]?.[y+1]]
    ].filter(([key, val]) => !!val);
  };

  rows = rows.map((row, x) => row.map((col, y) => ({
    ...col,
    adjacentMines: getAdjacentTiles(x, y).filter(([key, val]) => val.isMine).length
  })));

</script>

<div class="board" style="--size:{SIZE}">
	{#each rows as row, x}
    {#each row as tile, y}
      <button
        class="tile"
	      class:checked="{tile?.isChecked}"
	      class:mine="{tile?.isMine}"
	      class:marked="{tile?.isMarked}"
        on:contextmenu|preventDefault={e=>handleRightClick(x,y)}
        on:click={e=>reveal(x,y)}
      >
      {#if tile.isMarked}
        🏴󠁧󠁢󠁳󠁣󠁴󠁿
      {:else if tile.isChecked && tile.adjacentMines > 0}
        {tile.adjacentMines}
      {/if}
      </button>
    {/each}
	{/each}
</div>

<style>
.board {
  display: grid;
  grid-template-columns: repeat(var(--size), 1fr);
  grid-template-rows: repeat(var(--size), 1fr);
  gap: 2px;
}
.tile {
  width: 48px;
  text-align: center;
  aspect-ratio: 1 / 1;
  background-color: grey;
}
.tile.checked {
  background-color: #fff;
}
</style>