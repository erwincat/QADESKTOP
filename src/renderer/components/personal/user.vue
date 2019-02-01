<template>
  <div id='acc'>
      <h1>> {{this.user['username']}}</h1>

        <h2>策略树概览</h2>

        <div id='usernode'></div>
        <h2>策略组</h2>
        <mu-button>创建新的组合</mu-button>
        <div id='table'>
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
        <h2>策略订阅</h2>
        
          <div id='strategy'>
            <mu-table :height="height"  :showCheckbox=false >
            <mu-thead  slot="header">
                <mu-tr>
                <mu-th>strategy_id</mu-th>
                <mu-th>start</mu-th>
                <mu-th>end</mu-th>
                <mu-th>status</mu-th>
                </mu-tr>
            </mu-thead>
            <template v-for="strategy in user['subuscribed_strategy']">
                <mu-tbody>
                    <mu-tr >
                        <mu-td>{{strategy['strategy_id']}}</mu-td>
                        <mu-td>{{strategy['start']}}</mu-td>
                        <mu-td>{{strategy['end']}}</mu-td>
                        <mu-td>{{strategy['status']}}</mu-td>
                    </mu-tr>
                </mu-tbody>
            </template>

            </mu-table>
          </div>
        </div>



  </div>

  
</template>
<script>
import axios from 'axios'
import echarts from 'echarts'
export default {
  data () {
    return {
      username: sessionStorage.user,
      password: sessionStorage.password,
      user: {},
      height: '80',
      chart: null
    }
  },
  methods: {
    get_user () {
      axios.get('http://localhost:8010/user?username=' + this.username + '&password=' + this.password + '&model=password')
        .then(response => {
          this.user = response.data['result']
          console.log(this.user)
          sessionStorage.user_cookie = this.user['user_cookie']
        })
        .catch(function (error) {
          console.log(error)
        })
    },
    jump (index, tr) {
      var portfoliocookie = tr.$children[0].$el.innerText
      console.log(portfoliocookie)
      sessionStorage.portfolio_cookie = portfoliocookie
      this.$router.push({ name: 'portfolio', params: { id: portfoliocookie } })
    },
    drawline (id) {
      this.chart = echarts.init(document.getElementById(id))
      axios.get('http://localhost:8010/user?username=' + this.username + '&password=' + this.password + '&model=password&action=get_nodeview')
        .then(response => {
          var res = response.data['result']
          console.log(res)

          this.chart.setOption({
            title: {
              text: 'NODE VIEW'
            },
            animationDuration: 1500,
            animationEasingUpdate: 'quinticInOut',
            legend: {
              data: [
                {
                  name: 'USER'
                },
                {
                  name: 'PORTFOLIO'
                },
                {
                  name: 'ACCOUNTS'
                }
              ],
              // data:['k_line'],
              x: 'left',
              top: '5%'
            },
            series: [
              {
                name: 'assets',
                type: 'graph',
                layout: 'force',
                data: res['data'],
                links: res['link'],
                force: {
                  edgeLength: 50,
                  repulsion: 200,
                  gravity: 0.1,
                  layoutAnimation: true
                },
                roam: true,
                focusNodeAdjacency: true,
                draggable: true,
                itemStyle: {
                  normal: {
                    borderColor: '#999',
                    borderWidth: 1,
                    shadowBlur: 10,
                    shadowColor: 'rgba(0, 0, 0, 0.3)'
                  }
                },
                label: {
                  normal: {
                    show: true,
                    position: 'top', // inside
                    formatter: '{b}',
                    fontSize: 16,
                    color: '#999',
                    fontStyle: '100'
                  }
                },
                height: 3,
                lineStyle: {
                  normal: {
                    width: 3,
                    color: {
                      type: 'linear',
                      x: 0,
                      y: 0,
                      x2: 0,
                      y2: 1,
                      colorStops: [{
                        offset: 0,
                        color: 'white'
                      }, {
                        offset: 1,
                        color: 'green'
                      }]
                    },
                    curveness: 0,
                    type: 'solid'
                  }
                },
                emphasis: {
                  lineStyle: {
                    width: 10
                  }
                }
              }
            ]
          })
        })
        .catch(function (error) {
          console.log(error)
        })
    }
  },
  mounted () {
    this.$nextTick(function () {
      this.get_user()
      this.drawline('usernode')
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

#usernode {
  position: relative;
  margin-left: 0px;
  margin-bottom: 0px;
  width: 400px;
  height: 300px;
  border-radius: 10px;
}


#table {
  position: relative;
  margin-left: 0px;
  margin-bottom: 0px;
  width: 400px;
  height: 300px;
  border-radius: 10px;
}



#strategy {
  position: relative;
  margin-left: 0px;
  margin-bottom: 0px;
  width: 600px;
  height: 300px;
  border-radius: 10px;
}


</style>