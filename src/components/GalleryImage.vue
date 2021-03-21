<template lang="pug">
  img(
    loading="lazy"
    :src="img.url"
    alt="gallery-image"
    @load="loaded = true; $emit('loaded')"
    :style="`opacity: ${ loaded ? 1 : 0 }`"
    height="500"
  )
</template>

<script>
export default {
  name: "GalleryImage",
  props: {
    img: {
      type: Object,
      default: () => {},
      required: true,
    },
    full: {
      type: Boolean,
      default: true,
    },
  },
  data: () => ({
    loaded: false,
  }),
};
</script>

<style scoped></style>
