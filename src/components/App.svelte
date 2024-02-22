<script>
  import { onMount } from 'svelte';
  import * as d3 from 'd3';

  let totalFilteredPlayers = 0;

  let data = [];
  let filteredPlayers = [];
  let scoreFilter = 0;
  let reboundFilter = 0;
  let assistFilter = 0;
  let ageFilter = 19;
  let fgPercentFilter = 0;
  let threePointPercentFilter = 0;
  let twoPointPercentFilter = 0;
  let freeThrowPercentFilter = 0;

  let selectedPosition = 'ALL';
  let selectedTeam = 'ALL';
  let teams = ['ATL', 'BOS', 'BRK', 'CHI', 'CHO', 'CLE', 'DAL', 'DEN', 'DET', 'GSW', 'HOU', 'IND', 'LAC', 'LAL', 'MEM', 'MIA', 'MIL', 'MIN', 'NOP', 'NYK', 'OKC', 'ORL', 'PHI', 'PHO', 'POR', 'SAC', 'SAS', 'TOR', 'UTA', 'WAS'];

  onMount(async () => {
    const response = await fetch('/nba_player_per_game_clean.csv');
    const csvText = await response.text();
    data = d3.csvParse(csvText, d3.autoType); // Parse the CSV data and assign it to data
    filterPlayers(); // You might want to call this function to filter players immediately after fetching data
    console.log(tempData);
  });

  function filterPlayers() {
    filteredPlayers = data.filter(player => {
      const positionMatch = selectedPosition === 'ALL' || player.Pos.includes(selectedPosition);
      const teamMatch = selectedTeam === 'ALL' || player.Tm.includes(selectedTeam);
      return positionMatch &&
             teamMatch &&
             player.PTS >= scoreFilter &&
             player.TRB >= reboundFilter &&
             player.AST >= assistFilter &&
             player['Age'] >= ageFilter &&
             parseFloat(player['FG%']) >= fgPercentFilter && 
             parseFloat(player['3P%']) >= threePointPercentFilter &&
             parseFloat(player['2P%']) >= twoPointPercentFilter &&
             parseFloat(player['FT%']) >= freeThrowPercentFilter;
    });
    totalFilteredPlayers = filteredPlayers.length;
  }

  const getImagePath = (player) => `/${player['Player-additional']}.jpg`;
  
  function showTooltip(player, event) {
    const tooltip = event.currentTarget.querySelector('.player-info');
    const tooltipRect = tooltip.getBoundingClientRect();
    const playerRect = event.currentTarget.getBoundingClientRect();
    const listRect = document.querySelector('.player-list').getBoundingClientRect();
    const overflowRight = tooltipRect.right > window.innerWidth;
    const overflowBottom = playerRect.bottom + tooltipRect.height > listRect.bottom;

    const offsetX = overflowRight ? -tooltipRect.width : 65;
    tooltip.style.left = `${offsetX}px`;

    const offsetY = overflowBottom ? -tooltipRect.height : 65;
    tooltip.style.top = `${offsetY}px`;
  }

  function resetFilters() {
  scoreFilter = 0;
  reboundFilter = 0;
  assistFilter = 0;
  ageFilter = 19;
  fgPercentFilter = 0;
  threePointPercentFilter = 0;
  twoPointPercentFilter = 0;
  freeThrowPercentFilter = 0;
  selectedPosition = 'ALL';
  selectedTeam = 'ALL';
  filterPlayers(); 
  }

  let selectedPlayers = [];

  function selectPlayer(player) {
  if (selectedPlayers.length < 2) {
    selectedPlayers = [...selectedPlayers, player];
  }
}

function removePlayer(index) {
  selectedPlayers = selectedPlayers.filter((_, i) => i !== index);
}

function getColor(player1, player2, stat) {
  if (selectedPlayers.length < 2) {
    return 'green';
  }
  let value1 = parseFloat(player1[stat]);
  let value2 = parseFloat(player2[stat]);

  if (value1 > value2) {
    return 'green';
  } else if (value1 < value2) {
    return 'red';
  } else {
    return 'orange';
  }
}

function toPercentage(value) {
  return (parseFloat(value) * 100).toFixed(1) + '%';
}
</script>

<div class="container open-sans-my-text-style">
  <div class="controls-grid">
    <div class="grid-item">
      <!-- Score, Rebound, Assist, FG% -->
      <div class="slider-container">
        <div class="slider-label">Score:</div>
        <input type="range" class="slider" min="0" max="34" bind:value={scoreFilter} on:change="{filterPlayers}" />
        <div class="slider-value">{scoreFilter}</div>
      </div>
      <div class="slider-container">
        <div class="slider-label">Rebound:</div>
        <input type="range" class="slider" min="0" max="13" bind:value={reboundFilter} on:change="{filterPlayers}" />
        <div class="slider-value">{reboundFilter}</div>
      </div>
      <div class="slider-container">
        <div class="slider-label">Assist:</div>
        <input type="range" class="slider" min="0" max="11" bind:value={assistFilter} on:change="{filterPlayers}" />
        <div class="slider-value">{assistFilter}</div>
      </div>
      <div class="slider-container">
        <div class="slider-label">FG%:</div>
        <input type="range" class="slider" min="0" max="1" step="0.01" bind:value={fgPercentFilter} on:change="{filterPlayers}" />
        <div class="slider-value">{(fgPercentFilter * 100).toFixed(1)}%</div>
      </div>
    </div>

    <div class="grid-item">
      <!-- 3P%, 2P%, FT%, Age -->
      <div class="slider-container">
        <div class="slider-label">3P%:</div>
        <input type="range" class="slider" min="0" max="1" step="0.01" bind:value={threePointPercentFilter} on:change="{filterPlayers}" />
        <div class="slider-value">{(threePointPercentFilter * 100).toFixed(1)}%</div>
      </div>
      <div class="slider-container">
        <div class="slider-label">2P%:</div>
        <input type="range" class="slider" min="0" max="1" step="0.01" bind:value={twoPointPercentFilter} on:change="{filterPlayers}" />
        <div class="slider-value">{(twoPointPercentFilter * 100).toFixed(1)}%</div>
      </div>
      <div class="slider-container">
        <div class="slider-label">FT%:</div>
        <input type="range" class="slider" min="0" max="1" step="0.01" bind:value={freeThrowPercentFilter} on:change="{filterPlayers}" />
        <div class="slider-value">{(freeThrowPercentFilter * 100).toFixed(1)}%</div>
      </div>
      <div class="slider-container">
        <div class="slider-label">Age:</div>
        <input type="range" class="slider" min="19" max="42" bind:value={ageFilter} on:change="{filterPlayers}" />
        <div class="slider-value">{ageFilter}</div>
      </div>
    </div>

    <div class="grid-item">
      <!-- Position, Team, Reset -->
      <div class="selector-container">
        <label for="position-selector">Position:</label>
        <select id="position-selector" bind:value={selectedPosition} on:change="{filterPlayers}">
          <option value="ALL">ALL</option>
          <option value="PG">PG</option>
          <option value="SG">SG</option>
          <option value="SF">SF</option>
          <option value="PF">PF</option>
          <option value="C">C</option>
        </select>
      </div>
      <div class="selector-container">
        <label for="team-selector">Team:</label>
        <select id="team-selector" bind:value={selectedTeam} on:change="{filterPlayers}">
          <option value="ALL">ALL</option>
          {#each teams as team}
            <option value="{team}">{team}</option>
          {/each}
        </select>
      </div>
      <button on:click={resetFilters}>Reset</button>
    </div>
  </div>

  <div class="player-list-title-container">
    {#if data.length > 0}
      <h2>{data[0].Player}</h2>
    {/if}
    <h2>Players comparison ({selectedPlayers.length}/{2})</h2>
  </div>
  
  <div class="player-details-container">
    {#each selectedPlayers as player, index}
      <div class="player-details" key={index}>
        <img src={getImagePath(player)} alt={player['Player']} class="player-img" />
        <h3><span>{player['Player']}</span></h3>
        <p>Score: <span style="color: {selectedPlayers.length === 1 ? 'green' : getColor(player, selectedPlayers[(index + 1) % 2], 'PTS')}">{player['PTS']}</span></p>
        <p>Rebound: <span style="color: {selectedPlayers.length === 1 ? 'green' : getColor(player, selectedPlayers[(index + 1) % 2], 'TRB')}">{player['TRB']}</span></p>
        <p>Assist: <span style="color: {selectedPlayers.length === 1 ? 'green' : getColor(player, selectedPlayers[(index + 1) % 2], 'AST')}">{player['AST']}</span></p>
        <p>FG%: <span style="color: {selectedPlayers.length === 1 ? 'green' : getColor(player, selectedPlayers[(index + 1) % 2], 'FG%')}">{toPercentage(player['FG%'])}</span></p>
        <p>3P%: <span style="color: {selectedPlayers.length === 1 ? 'green' : getColor(player, selectedPlayers[(index + 1) % 2], '3P%')}">{toPercentage(player['3P%'])}</span></p>
        <p>2P%: <span style="color: {selectedPlayers.length === 1 ? 'green' : getColor(player, selectedPlayers[(index + 1) % 2], '2P%')}">{toPercentage(player['2P%'])}</span></p>
        <p>FT%: <span style="color: {selectedPlayers.length === 1 ? 'green' : getColor(player, selectedPlayers[(index + 1) % 2], 'FT%')}">{toPercentage(player['FT%'])}</span></p>
        <button on:click={() => removePlayer(index)}>Remove</button>
      </div>
    {/each}
  </div>

  <div class="player-list-title-container">
    <h2>Filtered Players ({totalFilteredPlayers}/{data.length})</h2>
  </div>
  
  <div class="player-list">
    <div class="player-photos">
      {#each filteredPlayers as player (player['Player'])}
        <div class="player-photo-container"
            on:mouseenter={(event) => showTooltip(player, event)}
            on:mouseleave={(event) => {
              const tooltip = event.currentTarget.querySelector('.player-info');
              tooltip.style.left = '';
              tooltip.style.top = '';
            }}>
          <img class="player-photo" alt={player['Player']} src={getImagePath(player)} on:click={() => selectPlayer(player)} />
          <div class="player-info">
            <p>Name: {player['Player']}</p>
            <p>Score: {player['PTS']}</p>
            <p>Rebounds: {player['TRB']}</p>
            <p>Assists: {player['AST']}</p>
            <p>FG%: {toPercentage(player['FG%'])}</p>
            <p>3P%: {toPercentage(player['3P%'])}</p>
            <p>2P%: {toPercentage(player['2P%'])}</p>
            <p>FT%: {toPercentage(player['FT%'])}</p>
          </div>
        </div>
      {/each}
    </div>
  </div>


</div>

<style>
  @import url('https://fonts.googleapis.com/css2?family=Open+Sans:wght@400&display=swap');

  html {
    margin: 0;
    padding: 0;
  }

  .open-sans-my-text-style {
    font-family: "Open Sans", sans-serif;
    font-optical-sizing: auto;
    font-weight: 400; /* Regular weight */
    font-style: normal;
    font-variation-settings: "wdth" 100;
  }



  .filters, .filtered-players {
    width: 100%; 
    max-width: 800px;
    text-align: center; 
    margin-bottom: 20px; 
  }

  /*filter style*/
  .slider-container {
  display: grid;
  grid-template-columns: minmax(0, 100px) auto min-content; 
  align-items: center;
  gap: 10px;
  margin-bottom: 20px;
  background-color: #04549c;
  }

  .slider-label {
    justify-self: right; 
    color: white;
  }

  .sliders {
  min-height: 150px;
  }

  .player-list-title-container {
    width: 100%;
    text-align: left;
    padding: 10px 0; 
    background-color: #f6f7f7;
  }

  .player-list-title-container h2 {
    margin-left: 15px; 
  }

  .player-list {
    background-color: #f6f7f7;
    width: 100%; /* Adjust the width as needed */
    height: 600px; /* Adjust the height as needed */
    overflow-x: auto; /* Allows horizontal scrolling if the content overflows */
  }


  .slider-value {
    display: inline-block;
    width: 3em; 
    text-align: right; 
    color: white;
  }

  .slider::-webkit-slider-thumb {
  -webkit-appearance: none;
  appearance: none;
  width: 25px;
  height: 25px;
  background: #cc1b38;
  cursor: pointer;
  border-radius: 50%;
  }

  .slider::-moz-range-thumb {
  width: 25px;
  height: 25px;
  background: #cc1b38;
  cursor: pointer;
  border-radius: 50%;
  }

  .slider::-moz-range-track {
  width: 100%;
  height: 8px;
  background: #cc1b38;
  cursor: pointer;
  }

  .slider::-moz-range-progress {
  background: #cc1b38;
  }

  .player-photos {
  display: flex;
  flex-wrap: wrap;
  gap: 10px;
  justify-content: center;
  }

  .player-photo {
  width: 100%;
  height: 100%;
  object-fit: cover;
  }

  .player-photo-container {
  position: relative;
  width: 65px;
  height: 65px;
  }

  .player-info {
  display: none;
  position: absolute;
  left: 65px;
  top: 0;
  white-space: nowrap;
  background-color: rgba(0, 0, 0, 0.7);
  color: white;
  padding: 5px;
  box-sizing: border-box;
  z-index: 1;
  
  }
  .player-photo-container:hover .player-info {
  display: block;
  }

  .player-photo-container:hover .player-info {
  display: block;
  width: max-content;
  }

  .container {
  display: flex;
  flex-direction: column;
  align-items: center;
  justify-content: flex-start;
  min-height: 100vh;
}

.controls-grid {
  display: grid;
  grid-template-columns: repeat(3, 1fr); /* 3 columns */
  grid-template-rows: repeat(4, auto); /* 4 rows */
  gap: 10px;
  width: 100%;
  max-width: 2000px;
  background-color: #04549c;
}

.grid-item {
  display: flex;
  flex-direction: column;
  justify-content: center;
  align-items: start;
}

.selector-container {
  color: white;
}

.player-details-container {
  display: flex;
  flex-wrap: wrap;
  justify-content: space-around;
  align-items: flex-start;
}

.player-details {
  white-space: nowrap;
  width: max-content;
  overflow: visible;
  margin-right: 10px;
  margin-left: 10px;
}
</style>
