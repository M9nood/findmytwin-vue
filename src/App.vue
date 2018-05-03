<template>
  <div id="app">
    <div style="padding-top:10%">
      <div v-if="!index" div class="box playbox container"  >
        <button style="margin-top:30%" class="button is-link" v-on:click="Gstart()">START</button>
      </div>
      <div v-if="!play"  class="box playbox container" >
        <div class="columns">
          <div class="column ">
          <p class="title is-5 text-center">เลือกตัว</p>
          </div>
        </div>
        <div class="columns is-multiline">
          <div v-for="(actor,key) in testData" :key="key" class="column is-3 " >
            <div class="card" v-bind:class="{ 'active-actor': chooseActor==key}" v-on:click="selectActor(key)">
              <div class="card-image">
                <figure class="image is-4by3">
                  <img :src="actor.img" alt="Placeholder image">
                </figure>
              </div>
              <div class="card-content">
                {{actor.name}}
              </div>
            </div>
          </div>
        </div>
        <div class="columns">
          <div class="column ">
            <button :disabled="!showPlayBtn" class="button is-link" v-on:click="gameplay()">เริ่ม</button>
          </div>
        </div>
      </div>
      <div v-else  class="box playbox container"  >
        <div class="tile is-ancestor">
          <div class="tile is-parent">
            <article class="tile is-child box">
              <div style="display:inline-block;float:left">
                <img :src="player.img" style="display:inline-block" alt="" width="30" height="30">
                <p class="" style="display:inline-block">สวัสดี {{player.name}} คุณเหลือ {{heart}} ชีวิต<br>เริ่มหาคู่แฝดคุณได้เลย..</p>
              </div>
            </article>
          </div>
        </div>
        <div class="columns">
          <div class="column ">
            <table width="60%" height="240px" class="table is-bordered" align="center" >
              <tr v-for="n in 3" :key="n">
                <td v-for="m in 3" :key="m" v-bind:class="{ 'not-open': tb[((n-1)*3+m)-1].open==1}" class="text-center" v-on:click="find(((n-1)*3+m)-1)">
                  <img v-if="founded && twinAt == ((n-1)*3+m)-1 " :src="player.img" width="30" height="30" alt="">
                  <p v-else>{{(n-1)*3+m}}</p>
                </td>
              </tr>
            </table>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<script>

import axios from 'axios'
import 'bulma/css/bulma.css'
export default {
  name: 'App',
  data () {
    return {
      testData: '',
      errors: [],
      index: true,
      play: false,
      showPlayBtn: false,
      chooseActor: '',
      player: {
        name: '',
        img: ''
      },
      tb: [],
      twinAt: 0,
      founded: false,
      heart: 3
    }
  },
  components: {
  },
  methods: {
    selectActor (id) {
      this.chooseActor = id
      this.showPlayBtn = true
      this.player.name = this.testData[id].name
      this.player.img = this.testData[id].img
    },
    gameplay () {
      this.play = true
      var data = []
      for (var i = 0; i < 9; i++) {
        data[i] = { 'open': 0 }
      }
      this.tb = data
      this.twinAt = Math.floor((Math.random() * 9) + 1)
    },
    find (position) {
      if (position === this.twinAt) {
        this.founded = true
        setTimeout(function () {
          alert('You win the Game.')
          window.location.reload()
        }, 500)
      } else {
        this.heart--
        this.tb[position].open = 1
        if (this.heart < 1) {
          setTimeout(function () {
            alert('You Lose the Game.')
            window.location.reload()
          }, 500)
        }
      }
    }
  },
  created () {
    let vm = this
    axios.get('https://find-twin.azurewebsites.net/users/actor')
      .then(response => {
        // JSON responses are automatically parsed.
        vm.testData = response.data
        // console.log(response)
      })
      .catch(e => {
        vm.errors.push(e)
      })
  }
}
</script>

<style>
#app {
  font-family: 'Avenir', Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
}
.playbox {
  width: 600px!important;
  height: 400px!important;
}
.text-center{
  text-align: center!important
}
.active-actor{
  background-color: cornflowerblue;
}
.find-box{
  min-height: 150px;
}
td.not-open{
  background-color: brown;
}
</style>
