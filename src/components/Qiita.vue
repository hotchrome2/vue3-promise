<script>
import axios from 'axios';
import { debounce } from "lodash";

export default {
  // data() {
  //   return {
  //     count: 0
  //   }
  // },
  data: () => ({
    items: null,
    keyword: '',
    message: ''
  }),
  watch: {
    keyword: function(newKeyword, oldKeyword){
      console.log(newKeyword)
      this.message = 'Waiting for you to stop typing...'
      this.debouncedGetAnswer()
    }

  },
  mounted: function() {
    // this.keyword = 'JavaScript'
    // this.getAnswer()
    this.debouncedGetAnswer = debounce(this.getAnswer, 1000)

  },
  methods: {
    getAnswer: function() {
      if(this.keyword === ''){
        console.log('karamoji')
        this.items = null
        return
      }
      this.message = 'Laoding...'
      const vm = this
      const params = { page: 1, per_page: 20, query: this.keyword }
      axios.get('https://qiita.com/api/v2/items', { params })
        .then(function(response){
          // console.log(response)
          vm.items = response.data
        })
        .catch(function(error){
          vm.message = 'Error!' + error
        })
        .finally(function(){
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
