<template>
    <div>
      <!-- 海报宣传栏 -->
    <div class="poster-carousel">
      <el-carousel indicator-position="outside" :interval="5000" height="450px" width="600px">
        <el-carousel-item v-for="(item, index) in posterList" :key="index">
          <img class="poster-image" :src="require(`@/assets/${item}`)" alt="poster" 
                style="max-width: 75%; max-height: 100%;"/>
        </el-carousel-item>
      </el-carousel>
    </div>
        <div class="whole">
            <div class="left">
                <div class="panel">
                    <div class="panel-header">
                        <div style="" id="hot">
                            <b> 热映中</b>
                            <span>Ongoing</span>
                        </div>
                        <a href="/movie/movieOngoing">全部</a>
                    </div>
                    <div class="panel-content">
                        <movie-item
                            :movieItem="item"
                            v-for="(item, index) in ongoingMovieList"
                            :key="index"
                        ></movie-item>
                    </div>
                </div>
                <div class="panel">
                    <div class="panel-header">
                        <div id="will">
                            <b> 即将上映</b>
                            <span>Comming Soon</span>
                        </div>
                        <a href="/movie/movieUpcoming">全部</a>
                    </div>
                    <div class="panel-content">
                        <div class="panel-content">
                            <movie-item
                                :movieItem="item"
                                v-for="(item, index) in upcomingMovieList"
                                :key="index"
                            ></movie-item>
                        </div>
                    </div>
                </div>
            </div>
            <div class="right">
                <div class="panel">
                    <div class="panel-header">
                        <div id="account">
                            <b> 票房榜</b>  <br>
                            <b>Box office chart</b>
                        </div>
                        <a href="/rankingList/totalBoxOfficeList"
                            >查看完整榜单</a
                        >
                    </div>
                    <div class="panel-content">
                        <div
                            class="board"
                            v-for="(item, index) in totalBoxOfficeList"
                            :key="index"
                        >
                            <div class="board-left">
                                <i class="board-index">{{ index + 1 }}</i>
                            </div>
                            <div class="board-middle">
                                <a :href="'/movieInfo/' + item.movieId">
                                    <p class="name">{{ item.movieName }}</p>
                                </a>
                            </div>
                        </div>
                    </div>
                </div>
            </div>
        </div>
        <div style="text-align: center; margin-top: 50px;">
            <el-text>
                对本网站使用进行评分：
            </el-text>
            <el-rate  allow-half
                v-model="value" 
                :colors="['#99A9BF', '#F7BA2A', '#FF9900']">
            </el-rate>
        </div>
        <div class="footerBox">
            <p>关于南国: <span>关于我们</span> | <span>开发团队</span> |  友情链接: <a href="https://cn.vuejs.org/guide/introduction.html#api-styles"> Vue </a> | <a href="https://element.eleme.cn/#/zh-CN/guide/design">ElementUI</a> | <a href="https://www.axios-http.cn/">Axios</a> | </p>
            <p>商务合作邮箱: v@163.com  客服电话:10105335   违法和不良信息举报电话:1234567890</p>
            <p>用户投诉邮箱: tousujubao@163.com   舞弊线索举报邮箱: wubijubao@163.com</p>
            <p>用户服务协议 | 平台交易规则总则 | 隐私政策</p>
            <p>c2024南国影城</p>
            <p></p>
            <p>
                <img src="https://p0.meituan.net/moviemachine/e54374ccf134d1f7b2c5b075a74fca525326.png" alt="">
                <img src="https://p1.meituan.net/moviemachine/805f605d5cf1b1a02a4e3a5e29df003b8376.png" alt="">
                <img src="https://p0.meituan.net/scarlett/3cd2a9b7dc179531d20d27a5fd686e783787.png" alt="">
            </p>
        </div>
    </div>
</template>

<script>
import movieItem from "../../components/movie/movie-item";
import moment from "moment";
export default {
    name: "Home",
    components: {
        movieItem,
    },
    data() {
        return {
            queryInfo1: {
                total: 0,
                pageSize: 8,
                pageNum: 1,
                startDate: moment().subtract(365, "days").format("YYYY-MM-DD"),
                endDate: moment().format("YYYY-MM-DD"),
            },
            queryInfo2: {
                total: 0,
                pageSize: 8,
                pageNum: 1,
                startDate: moment().format("YYYY-MM-DD"),
            },
            queryInfo3: {
                total: 0,
                pageSize: 8,
                pageNum: 1,
            },
            queryInfo4: {
                pageSize: 10,
                pageNum: 1,
            },
            ongoingMovieList: [],
            upcomingMovieList: [],
            classicMovieList: [],
            totalBoxOfficeList: [],

            posterList:[
              "poster1.jpg",
              "poster2.jpg",
              "poster3.jpg",
              "poster4.jpg",

            ],
            value: null,
        };
        
    },
    created() {
        this.getOngoingMovieList();
        this.getUpcomingMovieList();
        this.getClassicMovieList();
        this.getHeight();
        this.getTotalBoxOfficeList();
    },
    methods: {
        async getOngoingMovieList() {
            const { data: res } = await axios.get("sysMovie/find", {
                params: this.queryInfo1,
            });
            if (res == undefined) {
                return;
            }
            this.ongoingMovieList = res.data;
            this.total = res.total;
        },
        async getUpcomingMovieList() {
            const { data: res } = await axios.get("sysMovie/find", {
                params: this.queryInfo2,
            });
            if (res == undefined) {
                return;
            }
            this.upcomingMovieList = res.data;
            this.total = res.total;
        },
        async getClassicMovieList() {
            const { data: res } = await axios.get("sysMovie/find", {
                params: this.queryInfo3,
            });
            if (res == undefined) {
                return;
            }
            this.classicMovieList = res.data;
            this.total = res.total;
        },
        getHeight() {
            let clientWidth = `${document.documentElement.clientWidth}`;
            clientWidth *= 0.8;
            this.carouselHeight = (clientWidth / 1700) * 520 + "px";
        },
        async getTotalBoxOfficeList() {
            const { data: resp } = await axios.get(
                "sysMovie/find/rankingList/1",
                { params: this.queryInfo4 }
            );
            if (resp == undefined) {
                return;
            }
            console.log(resp);
            if (resp.code !== 200) return this.$message.error(resp.msg);
            this.totalBoxOfficeList = resp.data;

            this.totalBoxOfficeList.sort((a, b) => b.totalBoxOffice - a.totalBoxOffice);
        },
    },
};
</script>

<style scoped>
.el-carousel {
    width: 70%;
    margin: 30px auto;
}

.el-carousel__item > img {
    width: 100%;
    height: auto;
}

.whole {
    width: 1200px;
    margin: 30px auto;
    display: flex;
}

.left {
    width: 70%;
}

.right {
    width: 30%;
    margin-left: 100px;
}

h2 {
    font-size: 26px;
}

.panel-header > a {
    text-align: center;
    text-decoration: none;
    color: #999;
    padding-right: 14px;
    background: url(data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAAAgAAAAOCAYAAAASVl2WAAAABmJLR0QA/wD/AP+gvaeTAAAAv0lEQVQY013RTUpDQRAE4G8eghcR8ScgKCIugpJFjuIjqAvBc7jxj0muEnCjiIQQJOImB3GnbnpkfL1qpqqrunpSzvkDPxjhGdq2VarBF3q4wRHknP8RzvCEQzzguCalaHZwiwHecY6XogCf8TjFHh7Rh9Tx3AylIZa4TgWpSBuY4BSrYlFXKsr4bjrTW5HkJJa9SBW4jbtukmKxG5MDLOKqfzEPcB9LzQN8LSdfwxj7eMMlZvV/NFiPzFddEH4Bt5Y1mf3fnDwAAAAASUVORK5CYII=)
        no-repeat 100%;
}

.panel-header {
    display: flex;
    justify-content: space-between;
    align-items: center;
}

.movie-item {
    margin-left: 0;
    margin-right: 30px;
}

#hot {
    color: #ef4238;
    font-size: 28px;
}
#will {
    color: #2d98f3;
    font-size: 28px;
}
#hot-history {
    color: #ef4238;
    font-size: 28px;
}
.movie-item:nth-child(4n) {
    margin-right: 0;
}

.board {
    display: flex;
    margin: 10px 10px;
}

.board-left {
    display: flex;
    align-items: center;
}

.board-middle {
    display: flex;
    margin-left: 10px;
    width: 150px;
    font-size: 18px;
}

#account {
  color: #ffb400; font-size: 28px
}

.board-middle > a {
    text-decoration: none;
    color: #333;
}

.board-right {
    display: flex;
    font-size: 14px;
    font-weight: 700;
    color: #ffb400;
    margin-left: 40px;
    align-items: center;
}

.board-index {
    color: #ffb400;
    display: inline-block;
    width: 50px;
    height: 50px;
    line-height: 50px;
    text-align: center;
    font-size: 18px;
    align-items: center;
}

.panel-content {
    margin: 0px 0px 50px 0px;
}

.poster-carousel {
    margin: 20px 0;
    display: flex;
    overflow-x: hidden;
    overflow-y: hidden;
  }

  .poster-image {
    max-width: 80%;
    max-height: 120%;
    display: block;
    margin: 0 auto;
    position: absolute;
    top: 50%;
    left: 50%;
    transform: translate(-50%, -50%);
  }

  .footerBox{
            width: 100%;
            margin-top: 82px;
            color: #ccc;
            padding: 56px 0;
            background: #221e22;
            overflow: hidden;
            p  {
                text-align: center;
                font-size: 14px;
                line-height: 22px;
                height: 22px;
            }
            p:nth-child(1){
                span{
                    color: #ef4238;
                    padding:  0 8px;
                }
            }
            p:nth-child(10){
                height: 40px;
                margin-top: 20px;
                img:nth-child(2){
                    margin: 0 20px;
                }

                
            }
        }
</style>
