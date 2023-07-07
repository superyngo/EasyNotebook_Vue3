<script lang="ts">
import {defineComponent} from "vue";

export default defineComponent({
  data() {
    return {
      currentNoteIndex: 0,
      Notes: [
        {
          content: "",
          backgroundColor: "",
          color: "",
        },
      ],
    };
  },
  methods: {
    Plus1(): void {
      // let newNote: object = { content: "", backgroundColor: "", color: "" };
      const colorPair: string[] = this.getRandomColor();
      this.Notes.push({
        content: "",
        backgroundColor: colorPair[0],
        color: "white", //colorPair[1],
      });
      this.currentNoteIndex = this.Notes.length - 1;
    },
    ThisNote(index: number): void {
      this.currentNoteIndex = index;
    },
    delNote(index: number): void {
      console.log(this.Notes.length);
      if (this.Notes.length !== 1) {
        this.currentNoteIndex =
          this.currentNoteIndex > index
            ? this.currentNoteIndex - 1
            : this.currentNoteIndex;
        this.currentNoteIndex =
          this.currentNoteIndex === this.Notes.length - 1
            ? this.currentNoteIndex - 1
            : this.currentNoteIndex;
        this.Notes.splice(index, 1);
      }
    },
    noteTitles(notecontent: string): string {
      return notecontent === "" ? "empty" : notecontent.substring(0, 5);
    },
    getRandomColor(): string[] {
      let letters: string = "0123456789ABCDEF";
      let color: string = "";
      let colorPair: string[] = [];
      for (let i: number = 0; i < 6; i++) {
        color += letters[Math.floor(Math.random() * 16)];
      }
      function invertHex(hex: string) {
        return (Number(`0x1${hex}`) ^ 0xffffff)
          .toString(16)
          .substr(1)
          .toUpperCase();
      }
      colorPair = ["#" + color, "#" + invertHex(color)];
      return colorPair;
    },
  },
  watch: {
    Notes: {
      deep: true,
      handler(newNotes: string) {
        localStorage.setItem("savedNotes", JSON.stringify(newNotes));
      },
    },
  },
  computed: {},
  mounted() {
    const savedNotes = localStorage.getItem("savedNotes");
    if (savedNotes) {
      this.Notes = JSON.parse(savedNotes);
    }
  },
});
</script>

<template>
  <div class="container">
    <div class="main">
      <div>
        <h1>Easy Notebook</h1>
        <h3>Just a tiny little easy notebook</h3>
      </div>
      <div>
        <textarea
          v-model="Notes[currentNoteIndex].content"
          name="notebook"
          id=""
          cols="30"
          rows="10"
          placeholder="Note here..."
        ></textarea>
      </div>
      <div><button @click="Plus1">+1</button></div>
    </div>
    <footer>
      <div v-for="(note, index) of Notes">
        <button
          id="notes"
          @click="ThisNote(index)"
          :style="{
            backgroundColor: note.backgroundColor,
            color: note.color,
            scale: currentNoteIndex === index ? 1.3 : 1,
          }"
        >
          {{ noteTitles(note.content) }}
        </button>
        <button
          @click="delNote(index)"
          :style="{
            backgroundColor: note.backgroundColor,
            color: note.color,
            scale: currentNoteIndex === index ? 1.3 : 1,
          }"
        >
          X
        </button>
      </div>
    </footer>
  </div>
</template>

<style scoped>
.container {
  height: svh;
  display: grid;
  place-content: center;
}
.main {
  margin: 0 0 10px 0;
}
footer {
  height: 40svh;
  display: flex;
  flex-direction: column;
  flex-wrap: wrap;
  gap: 4px;
}

#notes {
  margin: 0 5px 0 0;
}

button:hover {
  cursor: pointer;
  filter: grayscale(100%);
}
</style>
