<script>
  import { createEventDispatcher } from "svelte";
  const dispatch = createEventDispatcher();
  export let note = "";
  function closeHandler() {
    console.log(note.date);
    dispatch("deleteHandler", {
      dateTime: note.date
    });
  }
  function listenHandler() {
    console.log(note.content);
    dispatch("readOutLoudHandler", {
      content: note.content
    });
  }
</script>

<style>
  .card {
    box-shadow: 0 4px 8px 0 rgba(0, 0, 0, 0.2);
    max-width: 300px;
    margin: auto;
    font-family: arial;
    margin-bottom: 15px;
    width: 250px;
    height: 200px;
    background-color: #ffffcc;
    position: relative;
    margin: 10px;
  }
  .close {
    position: absolute;
    right: 20px;
    top: 7px;
    width: 15px;
    height: 15px;
    opacity: 0.3;
  }
  .close:hover {
    opacity: 1;
    cursor: pointer;
  }
  .close:before,
  .close:after {
    position: absolute;
    left: 20px;
    content: " ";
    height: 15px;
    width: 2px;
    background-color: #333;
  }
  .close:before {
    transform: rotate(45deg);
  }
  .close:after {
    transform: rotate(-45deg);
  }
  .header-section {
    height: 30px;
    line-height: 30px;
    padding: 0px 5px;
    color: #333;
    box-shadow: 0px 0px 2px #cdcdcd;
  }
  .content-section {
    height: 140px;
    padding: 10px 5px;
    overflow: auto;
  }
  .footer-section {
    height: 30px;
    padding: 0px 5px;
    line-height: 30px;
    text-align: right;
    box-shadow: 0px 0px 2px #cdcdcd;
  }
  .footer-section small {
    opacity: 0.5;
  }
  .footer-section small:hover {
    opacity: 1;
    cursor: pointer;
    text-decoration: underline;
  }
  .header-section small {
    opacity: 0.5;
  }
  .card:hover {
    z-index: 999;
  }
  .card:hover .header-section small,
  .card:hover .header-section .close,
  .card:hover .footer-section small {
    opacity: 1;
  }
</style>

<div class="card">
  <div class="header-section">
    <small>{note.date}</small>
    <div class="close" on:click={closeHandler} />
  </div>
  <div class="content-section">
    <div>{note.content}</div>
  </div>
  <div class="footer-section" on:click={listenHandler}>
    <small>Listen</small>
  </div>

</div>
