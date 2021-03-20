<template lang="pug">
  #app(
    @dragenter.prevent="dragActive = true"
    @drop.prevent="dragActive = false"
    @dragover.prevent
    @dragleave.prevent
  )
    #container
      .app__header
        h1 Vue.js Image Gallery
        button.btn--red(@click="clear" v-if="imgs.length > 1") Очистить
      img-upload(
        :drag-active="dragActive"
        @newImage="addImage"
        @addImages="addImages"
        @drop.prevent
        @dragover.prevent
        @dragleave.prevent
      )
      #divider
      gallery(
        :imgs="imgs"
        @removeImage="removeImage"
        @drop.prevent
        @dragover.prevent
        @dragleave.prevent
      )
</template>

<script>
import ImgUpload from "@/components/ImgUpload";
import Gallery from "@/components/Gallery";
import DragNDrop from "@/components/DragNDrop";

export default {
  name: "App",
  components: { DragNDrop, Gallery, ImgUpload },
  data: () => ({
    imgs: [],
    dragActive: false,
  }),
  beforeMount() {
    const ls = localStorage.getItem("imgs");
    if (ls) {
      this.imgs = JSON.parse(ls);
    }
  },
  methods: {
    addImage(image) {
      this.imgs.unshift(image);
      this.saveImages();
    },
    addImages(list) {
      this.imgs = list.concat(this.imgs);
      this.dragActive = false;
      this.saveImages();
    },
    removeImage(index) {
      this.imgs.splice(index, 1);
      this.saveImages();
    },
    saveImages() {
      localStorage.setItem("imgs", JSON.stringify(this.imgs));
    },
    clear() {
      this.imgs = [];
      localStorage.removeItem("imgs");
    },
  },
};
</script>

<style lang="stylus">
@import './assets/main.styl'
</style>
