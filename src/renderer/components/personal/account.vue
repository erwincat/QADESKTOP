<template>
  <div id='acc'>
      <h1>> {{this.user['username']}}</h1>

        <h2>当前积分 {{this.user['coins']}}</h2>


        <mu-table :height="height" @rowClick="jump" :showCheckbox=false >
        <mu-thead  slot="header">
            <mu-tr>
            <mu-th>策略组</mu-th>
            </mu-tr>
        </mu-thead>
        <template  v-for="portfolio in user['portfolio_list']">
            <mu-tbody>
                <mu-tr >
                    <mu-td>{{ portfolio}}</mu-td>
                </mu-tr>
            </mu-tbody>
        </template>

        </mu-table>


  </div>

  
</template>
<script>
import axios from 'axios'
export default {
  data () {
    return {
      username: sessionStorage.user,
      password: sessionStorage.password,
      user: {},
      height: 80
    }
  },
  methods: {
    get_user () {
      axios.get('http://localhost:8010/user?username=' + this.username + '&password=' + this.password + '&model=password')
        .then(response => {
          this.user = response.data['result']
          console.log(this.user)
        })
        .catch(function (error) {
          console.log(error)
        })
    },
    jump (index, tr) {
      var portfoliocookie = tr.$children[0].$el.innerText
      console.log(portfoliocookie)
    }
  },
  mounted () {
    this.$nextTick(function () {
      this.get_user()
    })
  }
}
</script>


<style lang="css">

#views{
    width:1100px;
    height: 1000px;
    display: inline-block;
    float: right;
    
}
.mu-item-content {
    font-size: 18px;
    text-align: middle;
    display: inline;
  }
.inside_list{
   width: 200px;
   height: 1000px;
   float: left;
   display: inline-block;
   
}
</style>