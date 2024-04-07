<template>
  <div>
    <div class="main">
      <div class="intro">
        {{ currentDate }}<el-text > 更新</el-text> <br>
        
        <span >
          榜单规则：将昨日国内热映的影片，按照昨日票房从高到低排列，每天上午10点更新。
        </span>

      </div>
      <div class="board" v-for="(item, index) in totalBoxOfficeList">
        <div class="left">
          <i class="board-index">{{index+1}}</i>
        </div>
        <div class="middle1">
          <a :href="'/movieInfo/' + item.movieId">
            <img :src="global.base + JSON.parse(item.moviePoster)[0]" :alt="item.movieName">
          </a>
        </div>
        <div class="middle2">
          <a :href="'/movieInfo/' + item.movieId"><p class="name">{{ item.movieName }}</p></a>
          <p class="releaseTime">上映时间：{{ item.releaseDate.split(" ")[0] }}</p>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
import { months } from 'moment'

export default {
  name: "TotalBoxOfficeList",
  data(){
    return{
      queryInfo:{
        pageNum: 1,
        pageSize: 10
      },
      totalBoxOfficeList: [],
      currentDate : '',      
    }
  },
  created() {
    this.getTotalBoxOfficeList()
    this.getCurrentDate()
  },
  methods:{
    async getTotalBoxOfficeList(){
      const {data: resp} = await axios.get('sysMovie/find/rankingList/1', {params: this.queryInfo})
      console.log(resp)
      if(resp.code !== 200) return this.$message.error(resp.msg)
      this.totalBoxOfficeList = resp.data
    },
    getCurrentDate() {
      const currentDate = new Date(); 
      const year = currentDate.getFullYear(); 
      const month = String(currentDate.getMonth() + 1).padStart(2, '0'); 
      const day = String(currentDate.getDate()).padStart(2, '0'); 
      // 构建日期字符串，格式为 YYYY-MM-DD
      this.currentDate = `${year}-${month}-${day}`;
    }
  }
}
</script>

<style scoped>

.main{
  width: 950px;
  margin: 0 auto;
  margin-top: 70px;
}

.intro{
  align-items: center;
  text-align: center;
}
.board{
  display: flex;
  margin: 25px 0;
}

.left{
  display: flex;
  align-items: center;
  margin-right: 25px;
}

.middle1{
  display: flex;
  align-items: center;
}

.middle2{
  display: flex;
  /*align-items: center;*/
  flex-direction: column;
  justify-content: center;
  margin-left: 25px;
  width: 300px;
}

.middle2 > a {
  color: #373d41;
  font-size: 26px;
  text-decoration: none;
}

.name, .star, .releaseTime{
  margin-top: 8px;
  margin-bottom: 8px;
}

.releaseTime{
  color: #999999;
}

.right{
  display: flex;
  font-size: 40px;
  font-weight: 700;
  font-style: italic;
  color: #ffb400;
  margin-left: 320px;
  align-items: center;
}

.board-index{
  background: #ffb400;
  color: #fff;
  display: inline-block;
  width: 50px;
  height: 50px;
  line-height: 50px;
  text-align: center;
  font-size: 18px;
  font-weight: 700;
  align-items: center;
}

img{
  width: 160px;
  height: 220px;
}

</style>
