<template>
  <div class="formWrapper">
    <h1>Speak</h1>
    <br />
    <br />
    <form @submit="speakText" id="speech-form" v-show="speech.hasBrowserSupport() && !error">
      <textarea v-model="tts" placeholder="Add Text..." />
      <input type="submit" value="Speak" />
      <label for="voice">Voice:&nbsp;</label>
      <select id="voice" v-model="voice" @change="changeVoice">
        <option value selected disabled>Select a Voice...</option>
        <option v-bind:key="voice" v-for="voice in voices">{{voice}}</option>
      </select>
      <label v-if="!voice" for="lang">Language:&nbsp;</label>
      <select v-if="!voice" id="lang" v-model="lang" @change="changeLang">
        <option value selected disabled>Selecte a Language...</option>
        <option v-bind:key="lang" v-for="lang in langs">{{lang}}</option>
      </select>
      <label for="vol">Volume:&nbsp;</label>
      <input
        type="range"
        max="1"
        min="0.1"
        step=".1"
        value="1"
        v-model="vol"
        @change="changeVol"
        id="vol"
      />
      <label for="pitch">Pitch:&nbsp;</label>
      <input
        type="range"
        max="2"
        min="0.1"
        step=".1"
        value="1"
        v-model="pitch"
        @change="changePitch"
        id="pitch"
      />
      <label for="rate">Rate:&nbsp;</label>
      <input
        type="range"
        max="10"
        min="0.1"
        step=".1"
        value="1"
        v-model="rate"
        @change="changeRate"
        id="rate"
      />
      <br />
      <br />
      <input v-show="speaking" type="button" v-model="pausePlay" @click="togglePause" />&nbsp;
      <input v-show="speaking" type="button" value="Cancel" @click="cancelSpeech" />
    </form>
    <p v-show="!speech.hasBrowserSupport()">Your browser does not support Text-To-Speech</p>
    <p v-show="error">{{error}}</p>
  </div>
</template>

<script>
import Speech from "speak-tts";

export default {
  name: "Speak",
  data() {
    return {
      tts: "",
      speech: new Speech(),
      error: false,
      voices: [],
      langs: [],
      voice: "",
      lang: "",
      vol: 1,
      pitch: 1,
      rate: 1,
      pausePlay: "Pause",
      speaking: false
    };
  },
  methods: {
    speakText(e) {
      e.preventDefault();
      this.speaking = true;
      this.pausePlay = "Pause";

      this.speech
        .speak({
          text: this.tts,
          queue: false,
          listeners: {
            onend: () => {
              this.speaking = false;
            }
          }
        })
        .catch(e => {
          this.error = e;
          console.log(e);
        });

      this.tts = "";
    },
    changeLang() {
      console.log(this.lang);
      this.speech.setLanguage(this.lang);
    },
    changeVoice() {
      console.log(this.voice);
      this.speech.setVoice(this.voice);
    },
    changeVol() {
      console.log(this.vol);
      this.speech.setVolume(this.vol);
    },
    changePitch() {
      console.log(this.pitch);
      this.speech.setPitch(this.pitch);
    },
    changeRate() {
      console.log(this.rate);
      this.speech.setRate(this.rate);
    },
    togglePause() {
      console.log(this.pausePlay);
      if (this.pausePlay == "Pause") {
        this.pausePlay = "Play";
        this.speech.pause();
      } else {
        this.pausePlay = "Pause";
        this.speech.resume();
      }
    },
    cancelSpeech() {
      this.speaking = false;
      this.speech.cancel();
    }
  },
  created() {
    this.speech
      .init()
      .then(data => {
        this.voices = data.voices.map(x => x.name);
        this.langs = data.voices
          .map(x => x.lang)
          .filter((value, index, self) => {
            return self.indexOf(value) === index;
          });
        console.log(data);
      })
      .catch(e => {
        this.error = e;
        console.log(e);
      });
  }
};
</script>

<style scoped>
.formWrapper {
  margin: auto;
  width: 80%;
}
form {
  width: 100%;
  margin: -1%;
  display: flex;
  flex-wrap: wrap;
  justify-content: center;
}

form > * {
  flex: 1 1 40%;
  margin: 1%;
}

select {
  width: 40%;
}
</style>