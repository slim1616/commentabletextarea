<template>
  <div>
    <textarea v-model="text" id="txt" ref="txt" @keyup="handle()"></textarea>
    <div class="freinds" v-if="isArobase">
      <input type="text" v-model="search">
      <ul>
        <li v-for="fr in freindsList" @click="addToTextArea(fr)">{{fr.name}}</li>
      </ul>
    </div>
    <span v-for="f in commentUsers">
      <a href="#">{{f.name}}</a>,
    </span>
    <p>insertedFreinds</p>
    {{insertedFreinds}}
    <p>freindsList</p>
    <br>
    <br>
    <br>
    {{freindsList}}
  </div>
</template>


<script>
//import { emojiIndex } from 'emoji-mart-vue'
import data from "emoji-mart-vue/data/messenger";
import { NimbleEmojiIndex } from "emoji-mart-vue";
import { type } from "os";
export default {
  data() {
    return {
      emojis: [],
      search: "",
      text: "",
      isArobase: false,
      freinds: [
        { id: 1, name: "fr1" },
        { id: 2, name: "fr2" },
        { id: 3, name: "fr3" },
        { id: 4, name: "fr4" },
        { id: 5, name: "fr5" },
        { id: 6, name: "fr6" }
      ],
      commentUsers: []
    };
  },
  methods: {
    handle() {
      var input = this.$refs.txt;
      let lastIndexOf = input.value.lastIndexOf(" ");
      //console.log(input.selectionStart);
      //console.log(lastIndexOf);
      if (lastIndexOf === -1) {
        lastIndexOf = 0;
      } else {
        lastIndexOf += 1;
      }
      let word = this.text.substr(lastIndexOf, input.selectionStart);
      if (this.getEmojis(word) !== false) {
        //console.log("word |" + word + "|");
        //console.log(this.getEmojis(word));
        //console.log("input " + input.value);
        var ch = this.text.replace(word, this.getEmojis(word));
        this.text = ch;
      }
    },
    getEmojis(word) {
      let res = this.emojis.find(e => e.word == word);
      if (res) {
        return res.native;
      } else {
        return false;
      }
    },
    addToTextArea(fr) {
      var input = this.$refs.txt;
      let selectionStart = input.selectionStart;

      let start = input.selectionStart;
      //console.log("start " + start);

      var ch =
        this.text.substr(0, selectionStart) +
        fr.name +
        " " +
        this.text.substr(selectionStart);
      let end = input.selectionStart + fr.name.length;
      //console.log("end " + end);
      this.text = ch;
      this.isArobase = true;
      input.focus();
      this.search = "";
      this.commentUsers.push({
        id: fr.id,
        name: fr.name,
        start: start,
        end: end - 1
      });
    }
  },
  watch: {
    text(nv, old) {
      var input = this.$refs.txt;
   
      if (old.substr(input.selectionStart - 1, 1) == "@") {
        this.isArobase = false;
      } else {
        let u = this.commentUsers.find(
          user => input.value.indexOf("@" + user.name) == -1
        );

        if (u) {
          this.commentUsers = this.commentUsers.filter(user => user.id != u.id);
        }

        if (input.value.substr(input.selectionStart - 1, 1) == "@") {
          this.isArobase = true;
          // this.currentPos = input.selectionStart
        } else {
          this.isArobase = false;
        }
      }
    }
  },
  computed: {
    freindsList() {
      if (this.search != "") {
        return this.insertedFreinds.filter(
          item => item.indexOf(this.search) != -1
        );
      } else {
        return this.freinds;
      }
    },
    commentUsersIds() {
      let frs = this.commentUsers.map(fr => fr.id);
      return frs;
    },
    insertedFreinds() {
      let frs = this.freinds.filter(
        fr => !this.commentUsersIds.includes(fr.id)
      );
      return frs;
    }
  },
  mounted() {
    let emojiIndex = new NimbleEmojiIndex(data);
    //emojiIndex.search('christmas')
    var t = [];
    for (let [key, value] of Object.entries(emojiIndex.emojis)) {
      //console.log(`${key}: ${value}`);
      if (value.emoticons.length > 0) {
        value.emoticons.forEach(el =>
          t.push({ word: el, native: value.native })
        );
      }
    }
    this.emojis = t;
    //console.log(t);
    //console.log(emojiIndex.emojis)
  }
};
</script>


<style scoped>
.freinds {
  background: #eee;
  position: absolute;
  top: 35px;
}

ul {
  list-style: none;
  background: #fff;
  padding: 0;
  margin: 0;
}

li {
  background: #eee;
  padding: 5px 4px;
  cursor: pointer;
  margin: 1px 0;
  transition: background 0.5s ease;
}

li:hover {
  background: #fff;
}
</style>
