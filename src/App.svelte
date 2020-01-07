<script>
  import VoiceRecogination from "./Components/VoiceRecogination/VoiceRecogination.svelte";
  console.clear();
</script>

<style>
  .container {
    text-align: center;
    padding: 0 50px;
  }
  header {
    background: #355c7d;
    padding: 0 15px;
    color: white;
    height: 50px;
    line-height: 50px;
    font-size: 2rem;
  }
  .recording__box {
    margin-top: 70px;
  }
</style>

<main>
  <header>Voice Notes</header>
  <div class="container">
    <div class="recording__box">
      <VoiceRecogination />
    </div>

  </div>
</main>
