<script>
  export let player;
</script>

<div class="player-card">
  <div class="player-card-header">
    <img class="player-photo" src={`NBA_Player_Photo/${player['Player-additional']}.jpg`} alt={player['Player']}>
    <h2>{player['Player']}</h2>
  </div>
  <div class="player-card-body">
    <div class="player-stat">
      <label>Team</label>
      <div>{player['Tm']}</div>
    </div>
    <div class="player-stat">
      <label>Score</label>
      <progress value={player['PTS']} max="34"></progress>
      <span>{player['PTS']}</span>
    </div>
    <!-- Repeat for other stats -->
  </div>
</div>

<style>
  .player-card {
    background-color: #333;
    color: white;
    border-radius: 10px;
    padding: 10px;
    width: 200px; /* Adjust as necessary */
  }
  .player-card-header {
    display: flex;
    align-items: center;
    gap: 10px;
  }
  .player-photo {
    width: 50px; /* Adjust as necessary */
    height: 50px; /* Adjust as necessary */
    border-radius: 50%;
  }
  .player-card-body {
    margin-top: 10px;
  }
  .player-stat {
    margin-bottom: 5px;
  }
  progress {
    width: 100%;
    -webkit-appearance: none;
    appearance: none;
  }
  progress::-webkit-progress-bar {
    background-color: #ddd;
  }
  progress::-webkit-progress-value {
    background-color: #04AA6D; /* Adjust color as necessary */
  }
  progress::-moz-progress-bar {
    background-color: #04AA6D; /* Adjust color as necessary */
  }
</style>
