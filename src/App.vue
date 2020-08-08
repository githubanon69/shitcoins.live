<template>
  <div id="app">
    <modal :width=175 :height=215 :adaptive="true" name="settings">
      <div class="modalcontent">
        <ul class="nobull">
          <li>
            <input type="checkbox" id="twitter" v-model="twitter">
            <label for="twitter">Twitter</label>
          </li>
          <li>
            <input type="checkbox" id="reddit" v-model="reddit">
            <label for="reddit">Plebbit</label>
          </li>
          <li>
            <input type="checkbox" id="bitmex" v-model="bitmex">
            <label for="bitmex">Bitmex</label>
          </li>
          <li>
            <input type="checkbox" id="chan" v-model="chan">
            <label for="chan">Chan</label>
          </li>
          <li>
            <input type="checkbox" id="youtube" v-model="youtube">
            <label for="youtube">YouTube</label>
          </li>
          <li>
            <input type="checkbox" id="bitcointalk" v-model="bitcointalk">
            <label for="bitcointalk">BitCoinTalk</label>
          </li>
          <li>
            <input type="checkbox" id="medium" v-model="medium">
            <label for="medium">Medium</label>
          </li>
        </ul>
      </div>
    </modal>
    <div class="settings" @click="showsettings()">&#9881;</div>
    <table class="feed" cellspacing="8px">
      <template v-for="item in feed">
        <tr :key="item.id" class="link" style="cursor:pointer;" @click="chanlink(item.no)" v-if="item.type == '4chan' && chan == true"><th><img class="thumb" v-if="item.thumb" :src="'https://shitcoins.sfo2.digitaloceanspaces.com/chanimages/' + item.thumb + 's.jpg'"><h2 v-html="item.title"></h2><span v-html="item.message"></span></th></tr>
        <tr :key="item.id" class="bitmex" v-if="item.type == 'bitmex' && bitmex == true"><th>{{ item.user }}: <span v-html="item.message"></span></th></tr>
        <tr :key="item.id" class="link" style="cursor:pointer;" @click="redditlink(item.link)" v-if="item.type == 'reddit' && reddit == true"><th><img class="thumb" v-if="item.thumb" :src="item.thumb"><h3 v-html="item.title"></h3><h4>Plebbit/{{ item.sub }}</h4></th></tr>
        <tr :key="item.id" class="twitter link" style="cursor:pointer;" @click="twitterlink(item.link)" v-if="item.type == 'twitter' && twitter == true"><th><span v-html="decode(item.content)"></span></th></tr>
        <tr :key="item.id" class="link" style="cursor:pointer;" @click="youtubelink(item.vidid)" v-if="item.type == 'youtube' && youtube == true"><th><img class="thumb" v-if="item.thumb" :src="item.thumb"><h2 v-html="item.title"></h2></th></tr>
        <tr :key="item.id" class="link" style="cursor:pointer;" @click="bitcointalklink(item.topicid)" v-if="item.type == 'bitcointalk' && bitcointalk == true"><th><h3 v-html="item.title"></h3><h4>BitCoinTalk - {{ item.board }}</h4></th></tr>
        <tr :key="item.id" class="link" style="cursor:pointer;" @click="mediumlink(item.medlink)" v-if="item.type == 'medium' && medium == true"><th><img class="thumb" v-if="item.thumb" :src="'https://cdn-images-1.medium.com/fit/t/150/150/' + item.thumb"><h3 v-html="item.title"></h3><h4 v-html="item.subtitle"></h4></th></tr>
      </template>
    </table>
  </div>
</template>

<script>
export default {
  name: 'App',
  data () {
    return {
      polling: null,
      feed: null,
      chan: true,
      bitmex: true,
      reddit: true,
      twitter: true,
      youtube: true,
      bitcointalk: true,
      medium: true
    }
  },
  methods: {
    async pollData () {
      this.polling = setInterval(async () => {
        const response = await fetch('https://shitcoins.sfo2.digitaloceanspaces.com/feed.json')
        if (response.ok) {
          let feed = await response.json()
          this.feed = feed.reverse()
        }
      }, 3000)
    },
    chanlink(no) {
      window.open('https://boards.4channel.org/biz/thread/' + no)
    },
    redditlink(link) {
      window.open(link)
    },
    youtubelink(vidid) {
      window.open('https://www.youtube.com/watch?v=' + vidid)
    },
    bitcointalklink(topicid) {
      window.open('https://bitcointalk.org/index.php?topic=' + topicid)
    },
    mediumlink(articleid) {
      window.open('https://medium.com/post/' + articleid)
    },
    twitterlink(tstatus) {
      window.open('https://twitter.com' + tstatus)
    },
    decode(base64) {
      // eslint-disable-next-line
      return atob(base64).replace(/[\x00-\x08\x0E-\x1F\x7F-\uFFFF]/g, '')
    },
    showsettings () {
      this.$modal.show('settings')
    }
  },
  beforeDestroy () {
    clearInterval(this.polling)
  },
  created () {
    this.pollData()
  }
}
</script>

<style>
html, body {
  background: #4d5879;
  word-break : break-word;
}
.thumb {
  padding: 5px;
  float: left;
}
.bitmex {
  text-align: left;
}
code {
  width: 100px;
}
pre {
  width: 100px;
}
.twitter {
  text-align: left;
}
th {
  transition: all .01s ease-in-out;
  width:100%;
  padding: 6px;
  float: left;
  background: #C9D6FF;
  -moz-box-shadow: 0 0 2px 2px #666;
  -webkit-box-shadow: 0 0 2px 2px #666;
  box-shadow: 0 1px 1px rgba(0,0,0,0.1),
    0 2px 2px rgba(0,0,0,0.1), 
    0 4px 4px rgba(0,0,0,0.1), 
    0 8px 8px rgba(0,0,0,0.1),
    0 16px 16px rgba(0,0,0,0.1);
}
.link:hover th {
  transform: scale(1.004);
}
.feed {
  overflow: hidden;
  padding-right: 10px;
}
#app {
  padding-top: 25px; 
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
}
#ticker {
  position: fixed;
  top: 0;
  left: 0;
  z-index: 999;
  width: 100%;
  height: 36px;
  border:0;
  margin:0;
  padding:0;
}
.settings {
  position: fixed;
  right: 0;
  z-index: 20;
  font-size: 40px;
  cursor: pointer;
  color: white; 
  color: rgba(255, 255, 255, 0.8);
}
.nobull {
  list-style-type: none;
}
@media only screen and (max-width: 600px) {
  .thumb {
    display: none;
  }
}
</style>
