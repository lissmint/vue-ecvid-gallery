<template lang="pug">
  .lightbox
    .lightbox__header
      .lightbox__counter {{ `${toShow + 1}/${imgs.length}` }}
      .lightbox__close(@click="$emit('hide')")
    .lightbox__current-img
      .lightbox__controls
        .lightbox__controls--left(@click="show(toShow - 1)")
          img(src="/images/arrow.svg")
        .lightbox__controls--right(@click="show(toShow + 1)")
          img(src="/images/arrow.svg")
      transition(name="fade")
        img(
          v-for="(img, i) in imgs"
          :key="i"
          v-if="toShow === i"
          :src="img.url"
          :alt="`img${i}`"
        )
    .lightbox__all-imgs(ref="previews")
      .lightbox__all-imgs--container(ref="previewContainer")
        .lightbox__all-imgs--img(
          v-for="(img, i) in imgs"
          :key="i"
        )
          img(
            :class="{'active': i === toShow}"
            :src="img.url"
            :alt="`img${i}`"
            @click="show(i)"
          )
</template>

<script>
export default {
  name: "GalleryLightbox",
  props: {
    imgs: {
      type: Array,
      default: () => [],
    },
    currentIdx: {
      type: Number,
      default: 0,
    },
  },
  data: () => ({
    toShow: 0,
    maxPreviews: 6,
  }),
  watch: {
    toShow(newVal) {
      this.movePreviews(newVal);
    },
  },
  mounted() {
    document.querySelector("body").classList.add("lock");
    if (window.innerWidth < 769) this.maxPreviews = 3
    this.toShow = this.currentIdx;
    const previewsWidth = this.$refs.previews.offsetWidth;
    this.$refs.previewContainer.children.forEach(
      (elem) => (elem.style.width = previewsWidth / this.maxPreviews + "px")
    );
    this.movePreviews(this.toShow);
  },
  beforeDestroy() {
    document.querySelector("body").classList.remove("lock");
  },
  methods: {
    show(i) {
      if (i < 0) {
        this.toShow = this.imgs.length - 1;
      } else if (i > this.imgs.length - 1) {
        this.toShow = 0;
      } else {
        this.toShow = i;
      }
    },
    movePreviews(next) {
      const previewContainer = this.$refs.previewContainer;
      const imgWidth = previewContainer.firstChild.offsetWidth;
      let left = previewContainer.style.left;
      left = Number(left.replace(/px$/, ""));
      if (next === 0) {
        left = 0;
      } else if (next > this.imgs.length - 1 - this.maxPreviews) {
        left = -(this.imgs.length - this.maxPreviews) * imgWidth;
      } else {
        left = -imgWidth * next;
      }
      if (window.innerWidth > 768) {
        previewContainer.style.left = left + 'px';
      } else {
        this.$refs.previews.scroll(Math.abs(left), 0)
      }
    },
  },
};
</script>

<style lang="stylus" scoped>
@import "../assets/variables.styl"
.lightbox
  position fixed
  left 0
  top 0
  width 100%
  height 100vh
  background rgba(bg, .5)
  &__header
    user-select none
    position absolute
    top 0
    left 0
    display flex
    width 100%
    z-index 1
    justify-content space-between
    & > *
      margin 1rem
  &__close
    width 1.5rem
    height 1.5rem
    cursor pointer
    position relative
    &::before
    &::after
      content ''
      width 100%
      height .15rem
      position absolute
      background white
      top 50%
    &::before
      transform rotate(45deg)
    &::after
      transform rotate(135deg)
  &__controls
    user-select none
    position absolute
    top 50%
    transform translateY(-50%)
    display flex
    width 100%
    align-items center
    z-index 5
    &--left
    &--right
      cursor pointer
      filter invert(1)
      padding 1rem
      width 1.5rem
    &--left
      transform rotate(180deg)
    &--right
      margin-left auto
  &__current-img
    position fixed
    top 0
    width 100%
    height 80%
    max-height 80%
    display flex
    align-items center
    justify-content center
    & > img
      position absolute
      max-width 100%
      margin 1rem
      max-height calc(100% - 2rem)
      object-fit contain
  &__all-imgs
    position fixed
    bottom 0
    left 50%
    transform translateX(-50%)
    height 20%
    min-height 20%
    display flex
    width 70%
    overflow hidden
    margin 0 auto
    @media screen and (max-width: 768px)
      overflow-x scroll
    &--container
      position absolute
      top 0
      left 0
      display flex
      height 100%
      transition .3s ease-out
    &--img
      box-sizing border-box
      flex 1
      padding 0 .5rem 1rem 0
      img
        border-radius .2rem
        cursor pointer
        object-fit cover
        width 100%
        height 100%
        border .1rem solid transparent
        transition .3s ease-in
        filter grayscale(.6)
        &.active
        &:hover
          border-color accent
          filter grayscale(0)
    @media screen and (max-width: 768px)
      //height 10%
      //min-height 10%
</style>
