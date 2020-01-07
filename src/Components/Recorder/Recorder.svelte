<script>
  import { createEventDispatcher } from "svelte";
  const dispatch = createEventDispatcher();

  let isActive = false;
  function playHandler() {
    isActive = !isActive;
  }

  function iconHandler(type) {
    if (type === "PLAY" || type === "PAUSE") {
      isActive = !isActive;
    }
    let actionType =
      type === "PLAY" && isActive
        ? "PLAY"
        : type === "PLAY" && !isActive
        ? "PAUSE"
        : type;
    console.log(actionType);
    dispatch("recorderHandler", {
      actionType: actionType
    });
  }
</script>

<style>
  .player {
    position: relative;
  }
  .player .info {
    position: absolute;
    height: 60px;
    top: 0;
    opacity: 0;
    left: 10px;
    right: 10px;
    background-color: rgba(255, 255, 255, 0.5);
    padding: 5px 15px 5px 110px;
    border-radius: 15px;
    transition: all 0.5s ease;
  }
  .player .info .artist,
  .player .info .name {
    display: block;
  }
  .player .info .artist {
    color: #222;
    font-size: 16px;
    margin-bottom: 5px;
  }
  .player .info .name {
    color: #999;
    font-size: 12px;
    margin-bottom: 8px;
  }
  .player .info .progress-bar {
    background-color: #ddd;
    height: 2px;
    width: 100%;
    position: relative;
  }
  .player .info .progress-bar .bar {
    position: absolute;
    left: 0;
    top: 0;
    bottom: 0;
    background-color: red;
    width: 10%;
    transition: all 0.2s ease;
  }
  .player .info.active {
    top: -60px;
    opacity: 1;
    transition: all 0.5s ease;
  }
  .player .control-panel {
    position: relative;
    background-color: #fff;
    border-top-left-radius: 15px;
    border-top-right-radius: 15px;
    height: 80px;
    z-index: 5;
    box-shadow: 0px 20px 20px 5px rgba(132, 132, 132, 0.3);
  }
  .player .control-panel .album-art {
    position: absolute;
    left: 20px;
    top: -15px;
    height: 80px;
    width: 80px;
    border-radius: 50%;
    box-shadow: 0px 0px 20px 5px rgba(0, 0, 0, 0);
    transform: scale(1);
    transition: all 0.5s ease;
  }
  .player .control-panel .album-art::after {
    content: "";
    position: absolute;
    top: 50%;
    left: 50%;
    width: 15px;
    height: 15px;
    background-color: #fff;
    border-radius: 50%;
    z-index: 5;
    transform: translate(-50%, -50%);
    -webkit-transform: translate(-50%, -50%);
  }
  .player .control-panel .album-art::before {
    content: "";
    position: absolute;
    top: 0;
    left: 0;
    right: 0;
    bottom: 0;
    border-radius: 50%;
    background-position: center;
    background-repeat: no-repeat;
    background-size: 80px;
    background-image: url("https://i.scdn.co/image/9dcbd30dbe0c621cbaeae427cf80eff9877b4fcd");
  }
  .player .control-panel.active .album-art {
    box-shadow: 0px 0px 20px 5px rgba(0, 0, 0, 0.2);
    transform: scale(1.2);
    transition: all 0.5s ease;
  }
  .player .control-panel.active .album-art::before {
    animation: rotation 3s infinite linear;
    -webkit-animation: rotation 3s infinite linear;
    animation-fill-mode: forwards;
  }
  @keyframes rotation {
    0% {
      transform: rotate(0deg);
    }
    100% {
      transform: rotate(360deg);
    }
  }
  .player .control-panel .controls {
    display: flex;
    justify-content: flex-end;
    height: 80px;
    padding: 0 15px;
  }

  .fontIcons {
    font-size: 24px;
    z-index: 999999;
    color: #c2c6cf;
  }
  .icon__wrapper:hover {
    background-color: #eee;
    transition: background-color 0.3s ease;
    -webkit-transition: background-color 0.3s ease;
  }
  .icon__wrapper {
    display: flex;
    flex-direction: column;
    justify-content: center;
    color: black;
    cursor: pointer;
    width: 55px;
    height: auto;
    border-radius: 10px;
    background-position: center center;
    background-repeat: no-repeat;
    background-size: 20px;
    margin: 5px 0;
    background-color: #fff;
    transition: background-color 0.3s ease;
    -webkit-transition: background-color 0.3s ease;
  }
  .none {
    visibility: hidden;
  }
  
</style>

<div class="player">
  <div id="info" class="info {isActive ? 'active' : ''}">
    <span class="artist">Recording</span>
    <span class="name">Say it</span>
    <div class="progress-bar">
      <div class="bar" />
    </div>
  </div>
  <div id="control-panel" class="control-panel {isActive ? 'active' : ''}">
    <div class="album-art" />

    <div class="controls">
      <div
        class="icon__wrapper {isActive ? 'none' : ''}"
        on:click={() => iconHandler('RESET')}>
        <i class="fas fa-undo fontIcons" />
        <small>Reset</small>
      </div>
      <div class="icon__wrapper" on:click={() => iconHandler('PLAY')}>
        <i class="fas fontIcons {isActive ? 'fa-pause' : 'fa-play'}" />
        <small>{isActive ? 'Pause' : 'Play'}</small>
      </div>
      <div
        class="icon__wrapper {isActive ? 'none' : ''}"
        on:click={() => iconHandler('SAVE')}>
        <i class="fas fa-save fontIcons" />
        <small>Save</small>
      </div>
    </div>
  </div>
</div>
