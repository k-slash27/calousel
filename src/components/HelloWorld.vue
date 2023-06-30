<template>
  <div class="vue-center-detail-image-carousel">
    <!-- メインカルーセル -->
    <div>
      <div class="carousel" @mousedown="onMouseDown" @click.stop ref="calousel">
        <div class="list" :style="_listStyle" @transitionend="onTransitionEnd">
          <!-- 後ろの要素をコピー -->
          <template v-for="(item, index) in items.slice(-$data.COPY_COUNT)">
            <div class="list__item">
              <div
                class="item"
                :class="{ 'item--copy': $props.isCopyElementColoring }"
                ref="item"
              >
                {{ items[$data.COUNT - ($data.COPY_COUNT - index)].id }}
              </div>
            </div>
          </template>
          <!-- 本体 -->
          <template v-for="(item, index) in items">
            <div class="list__item">
              <div class="item">{{ item.id }}</div>
            </div>
          </template>
          <!-- 最初の要素をコピー -->
          <template v-for="(item, index) in items.slice(0, $data.COPY_COUNT)">
            <div class="list__item">
              <div
                class="item"
                :class="{ 'item--copy': $props.isCopyElementColoring }"
              >
                {{ item.id }}
              </div>
            </div>
          </template>
        </div>
      </div>
      <button @click="prev" @mousedown.stop>prev</button>
      <button @click="next" @mousedown.stop>next</button>
    </div>
    <!-- ナビゲーションカルーセル -->
    <div class="carousel__wrapper --nav">
      <button class="prev" @click="prev" @mousedown.stop>
        <img
          src="https://works.litalico.jp/assets/img/center/index/icon_slider_prev.png"
        />
      </button>
      <div
        class="carousel --nav"
        @mousedown="onMouseDown"
        @click.stop
        ref="calouselNav"
      >
        <div
          class="list"
          :style="_listStyleNav"
          @transitionend="onTransitionEnd"
        >
          <!-- 後ろの要素をコピー -->
          <template v-if="visible">
            <div
              class="list__item isCopy"
              v-for="(item, index) in items.slice(-$data.COPY_COUNT)"
            >
              <div
                class="item"
                :class="{
                  'item--copy': $props.isCopyElementColoring,
                  isCurrent: currentClassSliderNav(
                    items[$data.COUNT - ($data.COPY_COUNT - index)].id
                  ),
                }"
                ref="item"
                @click="
                  jump(items[$data.COUNT - ($data.COPY_COUNT - index)].id - 1)
                "
              >
                {{ items[$data.COUNT - ($data.COPY_COUNT - index)].id }}
              </div>
            </div>
          </template>
          <!-- 本体 -->
          <template v-for="(item, index) in items">
            <div class="list__item">
              <div
                class="item"
                :class="{ isCurrent: currentClassSliderNav(item.id) }"
                @click="jump(item.id - 1)"
              >
                {{ item.id }}
              </div>
            </div>
          </template>
          <!-- 最初の要素をコピー -->
          <template v-if="visible">
            <div
              class="list__item isCopy"
              v-for="(item, index) in items.slice(0, $data.COPY_COUNT)"
            >
              <div
                class="item"
                :class="{
                  'item--copy': $props.isCopyElementColoring,
                  isCurrent: currentClassSliderNav(item.id),
                }"
                @click="jump(item.id - 1)"
              >
                {{ item.id }}
              </div>
            </div>
          </template>
        </div>
      </div>
      <button class="next" @click="next" @mousedown.stop>
        <img
          src="https://works.litalico.jp/assets/img/center/index/icon_slider_next.png"
        />
      </button>
    </div>
  </div>
</template>

<script>
export default {
  name: "HelloWorld",
  props: {
    isCopyElementColoring: Boolean,
  },
  data() {
    return {
      items: [
        { id: 1, title: "apple" },
        { id: 2, title: "banana" },
        { id: 3, title: "orange" },
        { id: 4, title: "peach" },
        { id: 5, title: "plum" },
        { id: 6, title: "grape" },
        { id: 7, title: "pineapple" },
        { id: 8, title: "apricot" },
      ],
      COUNT: 5, // アイテムの数
      COPY_COUNT: 2, // コピーする数
      currentNum: 0,
      diffX: 0,
      startX: null,
      isAnimating: false,
      diffAmount: 0,
      breakPoint: 768,
      isCurrent: 0,
      visible: true,
    };
  },
  computed: {
    _listStyle() {
      return {
        transition: this.$data.isAnimating ? "" : "none",
        transform: `translate3d(${
          -100 * (this.$data.currentNum + this.$data.COPY_COUNT)
        }%, 0, 0) translate3d(${this.$data.diffX}px, 0, 0)`,
      };
    },
    _listStyleNav() {
      let style;
      if (window.innerWidth <= this.breakPoint) {
        style = {
          transition: this.$data.isAnimating ? "" : "none",
          transform: `translate3d(${
            -100 * (this.$data.currentNum + this.$data.COPY_COUNT + 1)
          }%, 0, 0) translate3d(${this.$data.diffX}px, 0, 0)`,
        };
      } else {
        style = {
          transition: "none",
          transform: `translate3d(${
            -100 * (this.$data.currentNum + this.$data.COPY_COUNT + 1) * 0
          }%, 0, 0) translate3d(${this.$data.diffX * 0}px, 0, 0)`,
        };
      }
      return style;
    },
  },
  created() {
    this.$data.COUNT = this.$data.items.length;
    this.$data.visible = window.innerWidth <= this.$data.breakPoint;
  },
  mounted() {
    this.$refs.calousel.addEventListener("mousemove", this.onMouseMove);
    this.$refs.calousel.addEventListener("mouseup", this.onMouseUp);
    this.$refs.calouselNav.addEventListener("mousemove", this.onMouseMove);
    this.$refs.calouselNav.addEventListener("mouseup", this.onMouseUp);
    window.addEventListener(
      "resize",
      ((e) => {
        e.preventDefault();
        e.stopPropagation();
        this.$data.visible = window.innerWidth <= this.$data.breakPoint;
        this.jump(this.$data.currentNum > 0 ? this.$data.currentNum : 0);
      }).bind(this)
    );
  },
  beforeDestroy() {
    this.$refs.calousel.removeEventListener("mousemove", this.onMouseMove);
    this.$refs.calousel.removeEventListener("mouseup", this.onMouseUp);
    this.$refs.calouselNav.removeEventListener("mousemove", this.onMouseMove);
    this.$refs.calouselNav.removeEventListener("mouseup", this.onMouseUp);
    window.removeEventListener(
      "resize",
      ((e) => {
        e.preventDefault();
        e.stopPropagation();
        this.jump(this.$data.currentNum > 0 ? this.$data.currentNum : 0);
      }).bind(this)
    );
  },
  methods: {
    onMouseDown(e) {
      e.stopPropagation();
      e.preventDefault();
      if (window.innerWidth > this.$data.breakPoint) {
        return false;
      }
      this.step1(e.clientX);
    },
    onMouseMove(e) {
      e.stopPropagation();
      e.preventDefault();
      if (window.innerWidth > this.$data.breakPoint) {
        return false;
      }
      this.step2(e.clientX);
    },
    onMouseUp(e) {
      e.stopPropagation();
      e.preventDefault();
      if (window.innerWidth > this.$data.breakPoint) {
        return false;
      }
      if (this.$data.diffX > 20) {
        this.$data.diffAmount = -1;
      }
      if (this.$data.diffX < -20) {
        this.$data.diffAmount = 1;
      }
      this.step3();
    },
    step1(startX) {
      this.adjustPosition();
      this.$data.startX = startX;

      let amt = Math.abs(this.$data.diffAmount);
      this.$data.COPY_COUNT = amt + 1 < 2 ? 2 : amt + 1;
    },
    step2(endX) {
      if (this.$data.startX == null) {
        return;
      }
      this.$data.diffX = endX - this.$data.startX;
    },
    step3() {
      this.$data.startX = null;
      let amt = Math.abs(this.$data.diffAmount);
      if (this.$data.diffAmount < 0) {
        this.$data.currentNum -= amt;
        this.$data.isAnimating = true;
      }
      if (this.$data.diffAmount > 0) {
        this.$data.currentNum += amt;
        this.$data.isAnimating = true;
      }
      // 差分算出用変数を初期化
      this.$data.diffX = 0;
      this.$data.diffAmount = 0;
    },
    onTransitionEnd() {
      this.adjustPosition();
    },
    adjustPosition() {
      this.$data.isAnimating = false;
      this.$data.currentNum =
        (this.$data.currentNum + this.$data.COUNT) % this.$data.COUNT;
      // コピー数を初期化
      this.$data.COPY_COUNT = 2;
    },
    prev(e) {
      e.stopPropagation();
      e.preventDefault();
      this.jump(this.$data.currentNum - 1);
    },
    next(e) {
      e.stopPropagation();
      e.preventDefault();
      this.jump(this.$data.currentNum + 1);
    },
    jump(jumpto) {
      let directionAmt = -(this.$data.currentNum - jumpto);
      let startX =
        this.$refs.calousel.getBoundingClientRect().left +
        this.$refs.calousel.clientWidth / 2;
      let endX =
        startX -
        (this.$refs.calousel.clientWidth / 2 +
          this.$refs.calousel.clientWidth *
            (Math.abs(directionAmt) - this.$data.COPY_COUNT));

      if (Math.sign(directionAmt) < 0) {
        endX =
          startX +
          (this.$refs.calousel.clientWidth / 2 +
            this.$refs.calousel.clientWidth *
              (Math.abs(directionAmt) - this.$data.COPY_COUNT));
      }

      console.log(directionAmt, endX, startX);

      this.$data.diffAmount = directionAmt;
      this.step1(startX);
      setTimeout(this.step2(endX), 1);
      setTimeout(this.step3, 1);
    },
    // // カルーセルで表示する画像の活性化
    // currentClassSliderMain(id) {
    //   const isCurrent = this.currentNum + 1 === id;
    //   return isCurrent;
    // },
    // サムネイルリストで選択画像の活性化
    currentClassSliderNav(id) {
      const isCurrent = this.$data.currentNum + 1 === id;
      return isCurrent;
    },
  },
};
</script>
