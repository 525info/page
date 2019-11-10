<template>
  <div v-show="total > pageSize || isShow" class="page-wrap">
    <span>总共 {{total}}条</span>
    <ul>
      <li class="item" v-show="current !== 1" @click="changePage(current-1)">上一页</li>
      <li class="item" :class="{'active':  current === 1}" @click="changePage(1)">1</li>
      <li class="item" v-show="showPreMore">···</li>
      <li
        class="item"
        v-for="(item,index) in showPages"
        :key="item+index"
        @click="changePage(item)"
        :class="{'active':  current === item}"
      >{{item}}</li>
      <li class="item" v-show="showNextMore">···</li>
      <li
        class="item"
        :class="{'active':  current === items}"
        v-if="items !== 1"
        @click="changePage(items)"
      >{{items}}</li>
      <li class="item" v-show="current !== items" @click="changePage(current+1)">下一页</li>
    </ul>
  </div>
</template>

<script>
export default {
  props: {
    pageSize: {
      type: Number,
      default: 10
    },
    total: {
      type: Number,
      default: 1
    },
    current: {
      type: Number,
      default: 1
    },
    showNum: {
      tyep: Number,
      default: 6
    },
    isShow: {
      type: Boolean,
      default: false
    }
  },
  data() {
    return {
      showPreMore: false,
      showNextMore: false
    };
  },
  updated() {
    this.isShowMore();
  },
  methods: {
    changePage(item) {
      if (item < 1) {
        return;
      }
      if (item > this.items) {
        return;
      }
      if (item !== this.current) {
        this.$emit("changePage", item);
      }
    },
    isShowMore() {
      const showNum = this.showNum;
      const current = Number(this.current);
      const halfshowNum = Math.floor((showNum - 2) / 2);
      if (this.items > showNum) {
        if (current - halfshowNum > 2) {
          this.showPreMore = true;
        } else {
          this.showPreMore = false;
        }
        if (current + halfshowNum < this.items) {
          this.showNextMore = true;
        } else {
          this.showNextMore = false;
        }
      }
    }
  },
  computed: {
    items() {
      return Math.ceil(this.total / this.pageSize);
    },
    showPages() {
      const showNum = this.showNum;
      const current = Number(this.current);
      const halfshowNum = Math.floor((showNum - 2) / 2);

      let array = [];
      // 如果右边有更多
      if (this.showNextMore && !this.showPreMore) {
        for (let i = 2; i < showNum; i++) {
          array.push(i);
        }
        // 两边都有更多
      } else if (this.showPreMore && this.showNextMore) {
        for (let i = current - halfshowNum; i <= current + halfshowNum; i++) {
          array.push(i);
        }
        // 左边有更多
      } else if (!this.showNextMore && this.showPreMore) {
        // 当folder2显示的时候,页码不能大于总数量,需要往前多显示差额
        let dis = current + halfshowNum - this.items + 1;

        for (let i = current - halfshowNum - dis; i < this.items; i++) {
          array.push(i);
        }
        // 都不显示
      } else {
        for (let i = 2; i < this.items; i++) {
          array.push(i);
        }
      }
      return array;
    }
  },
  watch: {
    route: {
      handler() {
        this.isShowMore();
      }
    },
    current: {
      handler() {
        this.isShowMore();
      },
      immediate: false,
      deep: true
    }
  }
};
</script>

<style lang="stylus" scoped>
.page-wrap
  display flex
  align-items center
.active
  background-color #f00
  color #fff
  border 1px solid #f00 !important
ul
  display flex
  align-items center
  justify-content center
  .item
    display flex
    align-items center
    justify-content center
    min-width 20px
    height 20px
    border 1px solid #ccc
    margin-right 3px
    font-size 12px
    cursor pointer
    box-sizing border-box
    padding 0 5px
    color #666
</style>
