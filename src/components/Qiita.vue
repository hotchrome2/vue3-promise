<script>
import axios from 'axios';
import { debounce } from "lodash";
import { resolveDirective } from 'vue';

export default {
  data: () => ({
    items: null,
    hits: null,
    keyword: '',
    message: ''
  }),
  watch: {
    keyword: function (newKeyword, oldKeyword) {
      console.log(newKeyword)
      this.message = 'Waiting for you to stop typing...'
      this.debouncedGet()
    }

  },
  mounted: function() {
    this.debouncedGet = debounce(this.getLinks, 1000)

  },
  methods: {
    async getLinks() {
      await this.getAnswer()
      this.getWait()
    },
    getWait: () => {
      console.log('--wait a minitues.')
    },
    getAnswer(){
      return new Promise(resolve => {
        if (this.keyword === '') {
          console.log('karamoji')
          this.items = null
          return
        }
        this.message = 'Laoding...'
        const vm = this
        const params = { page: 1, per_page: 10, query: this.keyword }
        axios.get('https://qiita.com/api/v2/items', { params })
          .then(function (response) {
            // console.log(response)
            console.log('--We got items')
            vm.items = response.data
            resolve('resolved')
          })
          .catch(function (error) {
            vm.message = 'Error!' + error
            resolve('rejected')
          })
          .finally(function () {
            vm.message = ''
          })

      })
    },
    getHit: function () {
      if (this.keyword === '') {
        console.log('karamoji')
        this.hits = null
        return
      }
      this.message = 'Hit...'
      const vm = this
      const params = { page: 1, per_page: 10, query: this.keyword + ' FastAPI' }
      axios.get('https://qiita.com/api/v2/items', { params })
        .then(function (response) {
          console.log('--Got Hits')
          vm.hits = response.data
        })
        .catch(function (error) {
          vm.message = 'Error!' + error
        })
        .finally(function () {
          vm.message = ''
        })
    }

  }
}
</script>

<template>
  <div>
    <p>
      <input type="text" v-model="keyword">

    </p>
    <p>{{ message }}</p>
    <ul>
      <li v-for="item in items">
        <a v-bind:href="item.url" target="_blank">
          {{ item.title }}
        </a>
        likes: {{ item.likes_count }}
      </li>
    </ul>
    <ul>
      <li v-for="hit in hits">
        hit: <a v-bind:href="hit.url" target="_blank">
          {{ hit.title }}
        </a>
        likes count: {{ hit.likes_count }}
      </li>
    </ul>
  </div>
</template>

<style scoped>
h1 {
  font-weight: 500;
  font-size: 2.6rem;
  top: -10px;
}

h3 {
  font-size: 1.2rem;
}
</style>
