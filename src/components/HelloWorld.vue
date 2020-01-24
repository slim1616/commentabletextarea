<template>
  <div>
    <textarea v-model="text" id="txt" ref="txt" @keyup="handle()"></textarea>
    <div class="freinds" v-if="isArobase">
      <input type="text" v-model="search">
      <ul>
        <li v-for="(fr,i) in freindsList" :key="i" @click="addToTextArea(fr)">{{fr.name}}</li>
      </ul>
    </div>
    <span v-for="(f, i) in commentUsers" :key="i">
      <a href="#">{{f.name}}</a>,
    </span>
    <p>{{text}}</p>

    <p>notInsertedFreinds</p>
    {{notInsertedFreinds}}
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
      console.log("handle");
      var input = this.$refs.txt;
      let lastSpace = this.text.lastIndexOf(" ");
      let lastEnter = this.text.lastIndexOf("\n");
      console.log("selectionStart" + input.selectionStart);
      console.log("lastEnter" + lastEnter);
      console.log("lastSpace" + lastSpace);
      let last = -1;
      if (lastSpace > lastEnter) {
        last = lastSpace;
      } else {
        last = lastEnter;
      }
      // console.log("length" + this.text.length);
      if (last === -1) {
        last = 0;
      } else {
        last += 1;
      }
      console.log("last" + last);
      let word = this.text.substr(last, input.selectionStart);
      if (this.getEmojis(word) !== false) {
        console.log("word |" + word + "|");
        //console.log(this.getEmojis(word));
        //console.log("input " + input.value);
        var ch = this.text.replace(word, this.getEmojis(word));
        this.text = ch;
      }
    },
    getEmojis(word) {
      let res = this.emojis.find(e => e.word === word);
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

      if (old.substr(input.selectionStart - 1, 1) === "@") {
        this.isArobase = false;
      } else {
        let u = this.commentUsers.find(
          user => input.value.indexOf("@" + user.name) === -1
        );

        if (u) {
          this.commentUsers = this.commentUsers.filter(
            user => user.id !== u.id
          );
        }

        if (input.value.substr(input.selectionStart - 1, 1) === "@") {
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
      if (this.search !== "") {
        return this.notInsertedFreinds.filter(
          item => item.indexOf(this.search) !== -1
        );
      } else {
        return this.freinds;
      }
    },
    commentUsersIds() {
      let frs = this.commentUsers.map(fr => fr.id);
      return frs;
    },
    notInsertedFreinds() {
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
