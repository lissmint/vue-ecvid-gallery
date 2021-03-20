<template lang="pug">
  .dnd(
    @dragenter.prevent="active = true"
    @dragover.prevent="active = true"
    @dragleave.prevent="active = false"
    @drop.prevent="handleDrop"
    :class="{ active }"
  )
    h5 Отпустите, чтобы загрузить изображение
</template>

<script>
export default {
  name: "DragNDrop",
  data: () => ({
    active: false,
    toAdd: [],
  }),
  methods: {
    handleDrop(e) {
      e.stopPropagation();
      let dt = e.dataTransfer;
      let files = dt.files;
      this.handleFiles(files);
    },
    handleFiles(files) {
      const toUpload = [];
      files.forEach((file) => {
        if (file.type.includes("image")) toUpload.push(file);
      });
      [...toUpload].forEach(this.uploadFile);
      // eslint-disable-next-line no-unused-vars
      let interval = setInterval(() => {
        if (this.toAdd.length === toUpload.length) {
          this.$emit("filesAdded", this.toAdd);
          clearInterval(interval);
        }
      }, 100);
    },
    uploadFile(file) {
      const fr = new FileReader();

      fr.onloadend = () => {
        const img = new Image();
        img.onload = (e) => {
          const width = e.target.width;
          const height = e.target.height;
          this.toAdd.push({
            url: fr.result,
            width,
            height,
          });
        };
        img.src = fr.result;
      };
      fr.readAsDataURL(file);
    },
  },
};
</script>

<style lang="stylus" scoped>
@import '../assets/variables.styl'
.dnd
  max-width 100%
  min-heigh 20rem
  padding 2rem
  border 1px dashed inactive
  &.active
    border-color accent
</style>
