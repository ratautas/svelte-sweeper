<script>
  const SIZE = 8; 
  const DIFFICULTY = 10;

  const getSurrounding = (x,y) => [
    [`${x}:${y-1}`, rows[x]?.[y-1]],
    [`${x}:${y+1}`, rows[x]?.[y+1]],
    [`${x-1}:${y}`, rows[x-1]?.[y]],
    [`${x+1}:${y}`, rows[x+1]?.[y]],
    [`${x-1}:${y-1}`, rows[x-1]?.[y-1]],
    [`${x-1}:${y+1}`, rows[x-1]?.[y+1]],
    [`${x+1}:${y-1}`, rows[x+1]?.[y-1]],
    [`${x+1}:${y+1}`, rows[x+1]?.[y+1]]
  ].filter(([key, val]) => typeof val !== 'undefined');

  const checkForDanger = (x, y, tile) => {
    if (tile?.isChecked) return; // escape
    if (tile?.isMine) return; // EXPLODE!

    setTileProp(x, y, 'isChecked', true);

    if (countDanger(x,y)) return; // If is dangerous, just uncover it

    getSurrounding(x, y).forEach(([key, val])=> {
      if (val?.isMine) return;
      
      const [x, y] = key.split(':');
      checkForDanger(x, y, val);
    })
  };

  const countDanger = (x,y) => getSurrounding(x,y).filter(([key,val])=>val?.isMine).length;

  const onRightClick = (x, y) => {
    setTileProp(x, y,'isMarked', !rows[x][y].isMarked);
  };

  const setTileProp = (x,y,prop,val) => {
    const r = [...rows];
    r[x] = [...r[x]];
    r[x][y] = {
      ...r[x][y],
      [prop]: val,
    };
    rows = r;
  };

  let rows = new Array(SIZE)
    .fill(new Array(SIZE).fill(null))
      .map(row => row.map(col => ({
        isChecked: false,
        isRevealed: false,
        isMarked: false,
        isEmpty: false,
        isMine: Math.random() < DIFFICULTY /100,
      })
    ));

</script>

<div class="board" style="--size:{SIZE}">
	{#each rows as row, x}
    {#each row as tile, y}
      <button
        class="tile"
	      class:checked="{tile?.isChecked}"
	      class:mine="{tile?.isMine}"
	      class:marked="{tile?.isMarked}"
	      class:empty="{tile?.isEmpty}"
	      class:revealed="{tile?.isRevealed}"
        on:contextmenu|preventDefault={e=>onRightClick(x,y)}
        on:click={e=>checkForDanger(x, y, tile)}
      >
      {#if tile.isChecked}
        {countDanger(x,y)}
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
  background-color: red;
}
.tile.mine {
  /* background-color: green; */
}
.tile.checked {
  background-color: blue;
}
.tile.empty {
  background-color: purple;
}
.tile.revealed {
  background-color: pink;
}
.tile.marked {
  background-color: black;
}
</style>