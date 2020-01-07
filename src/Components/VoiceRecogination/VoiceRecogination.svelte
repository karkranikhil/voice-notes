<script>
  import StickyNote from "../StickyNote/StickyNote.svelte";
  import Recorder from "../Recorder/Recorder.svelte";
  let support = true;
  let recordingText = `Press the Play button to Start recording.`;
  let noteContent = "";
  try {
    let SpeechRecognition =
      window.SpeechRecognition || window.webkitSpeechRecognition;
    var recognition = new SpeechRecognition();
  } catch (e) {
    console.error(e);
    support = false;
  }

  // Get all notes from previous sessions and display them.
  let notes = getAllNotes();

  /** Voice API Logic Start**/
  recognition.continuous = true;
  recognition.onresult = function(event) {
    let current = event.resultIndex;
    let transcript = event.results[current][0].transcript;
    console.log(transcript);
    noteContent += transcript;
  };

  recognition.onstart = function() {
    recordingText =
      "Voice recognition Started. Try speaking into the microphone.";
  };
  recognition.onspeechend = function() {
    recordingText = "Voice recognition turned off.";
  };

  recognition.onerror = function(event) {
    if (event.error == "no-speech") {
      recordingText = "No Voice was detected. Try again.";
    }
  };
  /** Voice Listen Code **/
  function readOutLoud(message) {
    let speech = new SpeechSynthesisUtterance();
    speech.text = message;
    speech.volume = 1;
    speech.rate = 1;
    speech.pitch = 1;
    window.speechSynthesis.speak(speech);
  }

  /**Local Storage Logic**/
  function saveNote(dateTime, content) {
    localStorage.setItem("note-" + dateTime, content);
  }
  function getAllNotes() {
    let notes = [];
    let key;
    for (var i = 0; i < localStorage.length; i++) {
      key = localStorage.key(i);

      if (key.substring(0, 5) == "note-") {
        notes.push({
          date: key.replace("note-", ""),
          content: localStorage.getItem(localStorage.key(i))
        });
      }
    }
    return notes;
  }
  function deleteNote(event) {
    let dateTime = event.detail.dateTime;
    localStorage.removeItem("note-" + dateTime);
    notes = getAllNotes();
  }
  /** Component Handlers**/
  function recorderHandler(event) {
    let type = event.detail.actionType;
    if (type === "PLAY") {
      startHandler();
    } else if (type === "PAUSE") {
      pauseHandler();
    } else if (type === "RESET") {
      resetHandler();
    } else if (type === "SAVE") {
      saveHandler();
    } else {
    }
  }
  function startHandler() {
    if (noteContent.length) {
      noteContent += " ";
    }
    recognition.start();
  }
  function pauseHandler() {
    recognition.stop();
    recordingText = "Voice recognition paused.";
  }
  function resetHandler() {
    noteContent = "";
    recordingText = "Note reset successfully.";
    window.setTimeout(() => {
      recordingText = `Press the Play button to Start recording.`;
    }, 5000);
  }
  function saveHandler() {
    recognition.stop();

    if (!noteContent.length) {
      recordingText =
        "Could not save empty note. Please add a message to your note.";
    } else {
      saveNote(new Date().toLocaleString(), noteContent);
      noteContent = "";
      notes = getAllNotes();
      recordingText = "Note saved successfully.";
      window.setTimeout(() => {
        recordingText = `Press the Play button to Start recording.`;
    }, 5000);
    }
  }
  function readOutLoudHandler(event) {
    let data = event.detail.content;
    readOutLoud(data);
  }
</script>

<style>
  ul {
    list-style: none;
    padding: 0;
    display: flex;
    flex-wrap: wrap;
    color: #333;
    text-align: left;
  }
  textarea {
    display: block;
    width: 100%;
    padding: 6px 12px;
    font-size: 14px;
    line-height: 1.42857143;
    color: #555;
    background-color: #fff;
    background-image: none;
    border: 1px solid #ccc;
    -webkit-box-shadow: inset 0 1px 1px rgba(0, 0, 0, 0.075);
    box-shadow: inset 0 1px 1px rgba(0, 0, 0, 0.075);
    -webkit-transition: border-color ease-in-out 0.15s,
      box-shadow ease-in-out 0.15s;
    -o-transition: border-color ease-in-out 0.15s, box-shadow ease-in-out 0.15s;
    -webkit-transition: border-color ease-in-out 0.15s,
      -webkit-box-shadow ease-in-out 0.15s;
    transition: border-color ease-in-out 0.15s,
      -webkit-box-shadow ease-in-out 0.15s;
    transition: border-color ease-in-out 0.15s, box-shadow ease-in-out 0.15s;
    transition: border-color ease-in-out 0.15s, box-shadow ease-in-out 0.15s,
      -webkit-box-shadow ease-in-out 0.15s;
    border-bottom-left-radius: 15px;
    border-bottom-right-radius: 15px;
  }
  textarea:focus {
    outline: none;
  }
  .input-single {
    position: relative;
    background-color: #fff;
    border-radius: 15px;
    z-index: 5;
    box-shadow: 0px 20px 20px 5px rgba(132, 132, 132, 0.3);
  }
  .instructions {
    text-align: center;
    padding: 21px 0;
    background-color: #6c5b7b;
    border-radius: 4px;
    top: 0px;
    position: relative;
    box-shadow: 1px 7px 14px -5px rgba(0, 0, 0, 0.2);
    padding: 1rem;
  }
  .no-notes {
    background: #f8b195;
    border-radius: 4px;
    top: 0px;
    position: relative;
    box-shadow: 1px 7px 14px -5px rgba(0, 0, 0, 0.2);
    padding: 1rem;
    width: 100%;
    text-align: center;
  }
</style>

{#if support}
  <!--Recorder Start-->
  <Recorder on:recorderHandler={recorderHandler} />
  <!--Recorder End-->

  <!--Text area for manual entry-->
  <div class="input-single">
    <textarea
      id="note-textarea"
      bind:value={noteContent}
      placeholder="Create a new note by typing or using voice recognition."
      rows="6" />
  </div>

  <!--Instruction Box to the user-->
  <p class="instructions">{recordingText}</p>

  <!--Notes list -->
  <ul id="notes">
    {#each notes as note (note.date)}
      <StickyNote
        {note}
        on:deleteHandler={deleteNote}
        on:readOutLoudHandler={readOutLoudHandler} />
    {/each}
    {#if notes.length === 0}
      <li class="no-notes">
        <p>You don't have any notes.</p>
      </li>
    {/if}
  </ul>
{:else}
  <h3 class="no-browser-support">
    Sorry, Your Browser Doesn't Support the Web Speech API. Try Opening This
    Demo In Google Chrome latest version.
  </h3>
{/if}
