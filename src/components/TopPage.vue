<template>
  <div>
    <input type="text" v-model="keyword">
    <p>
      {{ message }}
    </p>
    <ul>
      <li v-for="item in items" v-bind:key="item.id">
        <a v-bind:href="item.url" target="_blank">{{ item.title }}</a> likes:{{ item.likes_count }}
      </li>
    </ul>
  </div>
</template>

<script>
  import axios from 'axios'
  import _ from 'lodash'
  
  export default {
    data() {
      return{
        items: null,
        keyword: '',
        message: ''
      }
    },
    watch: {
      keyword: function(newKeyword, oldKeyword) {
        console.log(newKeyword)
        console.log(oldKeyword)
        this.message= " Waiting for you to stop typing..."
        this.debouncedGetAnswer()
      }
    },
    created: function() {
      // this.keyword = 'JavaScript'
      // this.getAnswer()
      this.debouncedGetAnswer = _.debounce(this.getAnswer, 1000)
    },
    methods: {
      getAnswer: function() {
        if(this.keyword === ''){
          this.items = null
          this.message =''
          return
        }

      this.message = 'Loading...'
      var vm = this
      var params = { page: 1, per_page:20, query: this.keyword }
      axios.get('http://qiita.com/api/v2/items', { params })
        .then(function(response){
          console.log(response);
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

<style>
</style>
