<template lang="pug">
  .gallery
    .gallery__img(
      v-for="(img, i) in imgs"
      :key="i"
      :class="{full: i % 3 === 0}"
    )
      .gallery__img--remove
        img(
          src="/images/delete.svg"
          @click="$emit('removeImage', i)"
        )
      gallery-image(
        :img="img"
        :full="i % 3 === 0"
      )
</template>

<script>
import GalleryImage from "@/components/GalleryImage";
export default {
  name: "Gallery",
  components: { GalleryImage },
  props: {
    imgs: {
      type: Array,
      default: () => [],
    },
  },
};
</script>

<style lang="stylus" scoped>
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
    @media screen and (max-width 768px)
      opacity 1
      &--remove
        opacity 1
</style>
