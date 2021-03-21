<template lang="pug">
  .gallery
    .gallery__img(
      v-for="(img, i) in imgs"
      :key="i"
      :class="{full: i % 3 === 0}"
      @click="showLightbox(i)"
    )
      .gallery__img--remove
        img(
          src="/images/delete.svg"
          @click.stop="$emit('removeImage', i)"
        )
      gallery-image(
        :img="img"
        :full="i % 3 === 0"
      )
    transition(name="fade")
      gallery-lightbox(
        :imgs="imgs"
        :currentIdx="lightboxIdx"
        v-if="lightbox"
        @hide="lightbox = false"
      )

</template>

<script>
import GalleryImage from "@/components/GalleryImage";
import GalleryLightbox from "@/components/GalleryLightbox";
export default {
  name: "Gallery",
  components: { GalleryLightbox, GalleryImage },
  props: {
    imgs: {
      type: Array,
      default: () => [],
    },
  },
  data: () => ({
    lightbox: false,
    lightboxIdx: 0,
  }),
  methods: {
    showLightbox(i) {
      this.lightboxIdx = i;
      this.lightbox = true;
    },
  },
};
</script>

<style lang="stylus" scoped>
@import '../assets/variables.styl'
.gallery
  margin 0 -.5rem
  display flex
  flex-wrap wrap
  &__img
    width calc(100%/2 - 1rem)
    margin .5rem
    max-height 30rem
    opacity .7
    position relative
    transition .3s ease-in
    background url("/images/placeholder.png") no-repeat
    border-radius .5rem
    &.loaded
      background none
      & > img
        opacity 1
    &.full
      width calc(100% - 1rem)
    &--remove
      position absolute
      top .5rem
      right.5rem
      width 1.5rem
      opacity 0
      transition .3s ease-in
      cursor pointer
    &:hover
      opacity 1
      .gallery__img--remove
        opacity 1
    & > img
      opacity 0
      width 100%
      height 100%
      object-fit cover
      border-radius .5rem
      transition .5s ease-in
      background bg
    @media screen and (max-width 768px)
      opacity 1
      &--remove
        opacity 1
</style>
