<template>
<div class="recommend-wrapper">
  <scroll ref="scroll" class="scroll-wrapper" :data="discList">
    <div>
      <div class="slider-wrapper" v-if="recommends.length">
        <slider>
          <div v-for="item in recommends" :key="item.id">
            <a :href="item.linkUrl">
            <img :src="item.picUrl" alt="轮播图" @load="loadImg">
          </a>
          </div>
        </slider>
      </div>
      <div class="recommend-list">
        <h1 class="title">热门歌单推荐</h1>
        <ul>
          <li class="recommend-list-item" v-for="item in discList" :key="item.id">
            <div class="icon">
              <img width="60" height="60" v-lazy="item.imgurl" alt="歌单图片">
            </div>
            <div class="text">
              <h2 class="item-title" v-html="item.creator.name"></h2>
              <p class="desc" v-html="item.dissname"></p>
            </div>
          </li>
        </ul>
      </div>
    </div>
    <div class="loading-wrapper" v-if="!discList.length">
      <loading></loading>
    </div>
  </scroll>
</div>
</template>

<script type="text/ecmascript-6">
import Slider from 'base/slider/slider';
import Scroll from 'base/scroll/scroll';
import Loading from 'base/loading/loading';
import { getRecommend, getDiscList } from 'api/recommend';
import { ERR_OK } from 'api/config';

export default {
  name: 'recommend',
  props: [],
  data() {
    return {
      recommends: [],
      discList: [],
      checkImgload: false
    };
  },
  created() {
    this._getRecommendData();
    this._getDiscList();
  },
  methods: {
    /**
     * 获取推荐列表数据
     */
    _getRecommendData() {
      getRecommend().then(res => {
        if (res.code === ERR_OK) {
          this.recommends = res.data.slider;
        }
      });
    },

    /**
     * 获取歌单
     */
    _getDiscList() {
      getDiscList().then(res => {
        if (res.code === ERR_OK) {
          this.discList = res.data.list;
        }
      });
    },

    /**
     * 监听图片加载成功，刷新scroll
     */
    loadImg() {
      if (!this.checkImgload) {
        this.$refs.scroll.refresh();
        this.checkImgload = true;
      }
    }
  },
  components: {
    Slider,
    Scroll,
    Loading
  }
};
</script>

<style type="text/css" lang="less" scoped>
@import '../../common/less/variable.less';

.recommend-wrapper {
  position: fixed;
  top: 88px;
  width: 100%;
  bottom: 0;
  .scroll-wrapper {
    overflow: hidden;
    height: 100%;
    .slider-wrapper {
      position: relative;
      width: 100%;
      overflow: hidden;
    }
    .recommend-list {
      .title {
        height: 65px;
        line-height: 65px;
        text-align: center;
        font-size: @font-size-medium;
        color: @color-theme;
      }
      .recommend-list-item {
        display: flex;
        align-items: center;
        box-sizing: border-box;
        padding: 0 20px 20px;
        .icon {
          flex: 0 0 60px;
          width: 60px;
          padding-right: 20px;
        }
        .text {
          display: flex;
          flex-direction: column;
          justify-content: center;
          flex: 1;
          overflow: hidden;
          line-height: 20px;
          font-size: @font-size-medium;
          .item-title {
            margin-bottom: 10px;
            color: @color-text;
          }
          .desc {
            color: @color-text-l;
          }
        }
      }
    }
    .loading-wrapper {
      position: absolute;
      width: 100%;
      top: 50%;
      transform: translateY(-50%);
    }
  }
}
</style>
