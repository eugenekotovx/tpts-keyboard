<template>
  <section>
    <a href="https://www.twitch.tv/twitchplaysthesynth" target="_blank" class="logoContainer">
      <img src="./assets/logo.png" class="logo" />
      keyboard
    </a>

    <h2>Select a note duration</h2>

    <NoteSelector
      @durationSelected="durationSelected"
    />

    <h2>and play a note on the keyboard</h2>

    <PianoKeyboard
      :octaves="octaves"
      :octaveShift="octaveShift"
      :showHints="showHints"
      @keyPressed="keyPressed"
    />

    <input type="checkbox" id="showHints" v-model="showHints">
    <label for="showHints">Show note hints</label>

    <div class="buttons">
      <RippleButton
        title="Clear sequence"
        @click="clearSequence"
      />
    </div>

    <h2>Last note: <strong>{{ selectedNote + selectedOctave }}</strong></h2>
    <h1>
      Result: <span class="result">{{ formattedSequence }}</span>
    </h1>

    <p>
      Made with ❤️ by 
      <a href="https://github.com/paolozanchi" target="_blank" class="link">
        mmmmmeh1
      </a>
      /
      <a href="https://www.linkedin.com/in/paolo-zanchi/" target="_blank" class="link">
        Paolo Zanchi
      </a>
    </p>
  </section>
</template>

<script>
import NoteSelector from '@/components/NoteSelector.vue'
import PianoKeyboard from '@/components/PianoKeyboard.vue'
import RippleButton from '@/components/RippleButton.vue'

export default {
  name: 'TptsKeyboard',
  metaInfo: {
    title: 'tpts! keyboard',
    charset: 'utf-8',
  },
  components: {
    NoteSelector,
    PianoKeyboard,
    RippleButton
  },
  data() {
    return {
      selectedDuration: '1',
      selectedNote: null,
      selectedOctave: null,
      previousNoteDistance: null,

      octaves: 5,
      octaveShift: 1,
      sequence: [],
      showHints: true
    }
  },
  computed: {
    formattedSequence() {
      return this.sequence.length > 0 ? '!m1 ' + this.sequence.join(' ') : ' ';
    }
  },
  methods: {
    durationSelected(duration) {
      this.selectedDuration = duration;
    },
    keyPressed(note, octave) {
      // Convert the note
      this.selectedNote = note;
      this.selectedOctave = octave;
      
      if(this.sequence.length == 0) {
        // FIRST NOTE
        // We need to find the first note's distance from C4 to start the sequence
        this.previousNoteDistance = this.getDistanceFromC4(note, octave);
        this.sequence.push(this.previousNoteDistance + "[" + this.selectedDuration + "]");
      } else {
        const currentNoteDistance = this.getDistanceFromC4(note, octave);
        const difference = currentNoteDistance - this.previousNoteDistance;

        this.previousNoteDistance += difference;

        this.sequence.push(difference + "[" + this.selectedDuration + "]");
      }
    },
    getDistanceFromC4(note, octave) {
      let distance = 0;

      const pureNote = note[0];
      const pitch = note[1];

      switch (pureNote) {
        case "D":
          distance += 2;
          break;

        case "E":
          distance += 4;
          break;

        case "F":
          distance += 5;
          break;

        case "G":
          distance += 7;
          break;

        case "A":
          distance += 9;
          break;

        case "B":
          distance += 11;
          break;
      }

      if (pitch) {
        switch (pitch) {
          case "b":
            distance -= 1;
            break;

          case "#":
            distance += 1;
            break;
        }
      }

      if (octave != 4) {
        let octaveDistance = -12 * (4 - octave);
        distance += octaveDistance;
      }

      return distance;
    },
    clearSequence() {
      this.sequence = [];
      this.previousNoteDistance = null;
      this.selectedNote = null;
      this.selectedOctave = null;
    }
  }
}
</script>

<style>
  @font-face {
    font-family: "Metropolis";
    src: url('./assets/metropolis.regular.otf');
  }

  :root {
    --dark: #111;
    --light: #eee;
    --accent: #f57f17;
    --secondary: #f9a825;
  }

  html {
    background: var(--dark);
    color: var(--light);
    font-family: 'Metropolis', 'Helvetica Neue', Helvetica, Arial, sans-serif;
    -webkit-font-smoothing: antialiased;
    text-align: center;
  }

  .logoContainer {
    color: var(--light);
    font-size: 24pt;
    text-decoration: none;
    outline: none;
  }

  .logo {
    width: 100px;
    vertical-align:middle;
  }

  .buttons {
    margin-top: 2em;
  }

  .result {
    color: var(--accent);
  }

  .link {
    color: var(--light);
    outline: none;
  }
</style>
