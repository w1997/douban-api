                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                       <template>
  <div id="app">
    <router-view></router-view>
    <div class="index-wrap" v-if="showTab">
      <mt-tab-container v-model="active" :swipeable="true">
        <mt-tab-container-item id="tab-container1">
          <router-link to="/movie" class="rank-wrap">
            <img class="maximg" :src="hot" alt="" v-if="hot">
            <p class="title">正在热映</p>
          </router-link>
          <div class="rank-wrap">
            <img class="maximg" :src="soon" alt="" v-if="soon">
            <p class="title">即将上映</p>
          </div>
          <div class="rank-wrap">
            <img class="maximg" :src="top" alt="" v-if="top">
            <p class="title">TOP250</p>
          </div>
        </mt-tab-container-item>
        <mt-tab-container-item id="tab-container2">
          <mt-search v-model="searchValue" @keyup.13="search" @keyup.enter="search"></mt-search>
          <div class="card-wrap">
            <div @click.navtive="tag(songs[index])" v-for="(n, index) in songs" class="card-box" :style="{ backgroundColor: bgColors[index] }">{{ songs[index] }}</div>
          </div>
        </mt-tab-container-item>
      </mt-tab-container>
      <keep-alive>
        <mt-tabbar :fixed="true" v-model="active">
          <mt-tab-item id="tab-container1">
            <i class="material-icons" slot="icon">movie</i>
            电影
          </mt-tab-item>
          <mt-tab-item id="tab-container2">
            <i class="material-icons" slot="icon">library_music</i>
            音乐
          </mt-tab-item>
        </mt-tabbar>
      </keep-alive>
    </div>
    <mt-spinner v-show="wait" type="double-bounce" color="#26a2ff" :size="40"></mt-spinner>
  </div>
</template>

<script>
  export default {
    name: 'app',
    data () {
      return {
        active: 'tab-container1',
        hot: null,
        soon: null,
        top: null,
        searchValue: null,
        bgColors: [],
        songs: [
          '国语', '英语', '韩语', '粤语', '日语',
          '流行', '轻音乐', '摇滚', '民谣', 'R&B', '嘻哈', '电子', '古典',
          'ACG', '经典', '网络歌曲', '影视', 'KTV热歌', '儿歌', '中国风', '古风',
          '伤感', '安静', '快乐', '治愈', '励志', '甜蜜', '寂寞', '宣泄', '思念',
          '睡前', '夜店', '学习', '运动', '开车', '约会', '工作', '旅行'
        ]
      }
    },
    computed: {
      showTab () {
        return this.$store.state.show;
      },
      wait () {
        return this.$store.state.wait;
      }
    },
    created () {
      this.rand();
      this.$http.get('movie/in_theaters').then( response => {
        this.hot = response.data.subjects[0].images.large;
        this.$store.commit('setHots', response.data.subjects);
      } );
      this.$http.get('movie/coming_soon').then( response => {
        this.soon = response.data.subjects[0].images.large;
        this.$store.commit('setSoons', response.data.subjects);
      } );
      this.$http.get('movie/top250').then( response => {
        this.top = response.data.subjects[0].images.large;
        this.$store.commit('setTops', response.data.subjects);
      } );
    },
    watch: {
      '$route' () {
        //  离开首页事件
        if (this.$route.path !== '/' && this.$store.state.show) {
          this.$store.commit('show');
        }
      }
    },
    methods: {
      rand () {
        for (let i = 0; i < this.songs.length; i++) {
          this.bgColors.push(this.getRandomColor())
        }
      },
      getRandomColor () {
        return "hsl(" + Math.floor(Math.random() * 360)  + ", 44%, 62%)";
      },
      tag (t) {
        this.$http.get(`music/search?tag=${t}`).then( response => {
          console.log(response)
        } );
      },
      search () {
        alert('1')
      }
    }
  }
</script>
<style lang="scss">
  @import "assets/common.scss";

  .rank-wrap {
    position: relative;
    display: block;
    width: 100%;
    height: calc((100vh - 56px) / 3);
    overflow: hidden;

    .title {
      @extend %box-center;
      position: absolute;
      left: 0;
      top: 0;
      width: 100%;
      height: 100%;
      letter-spacing: 14px;
      font-size: 20px;
      color: #fff;
      background-color: rgba(0, 0, 0, .6);
    }
  }
  .mint-searchbar {
    background-color: #e9e9e9 !important;
  }
  .mint-search {
    height: 52px!important;
  }
  .card-box {
    margin: 10px 8px;
    padding: 0 12px;
    @extend %box-center;
    display: inline-flex;
    width: auto;
    height: 5vh;
    border-radius: 4px;
    color: #fff;
  }
</style>

