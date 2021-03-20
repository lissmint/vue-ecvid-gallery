<template lang="pug">
  .img-upload
    drag-n-drop(
      v-if="dragActive"
      @filesAdded="(imgs) => $emit('addImages', imgs)"
    )
    template(v-else)
      .img-upload__header
        .img-upload__header--option(
          @click="fileUpload = false"
          :class="{active: !fileUpload}"
        ) По ссылке
        .img-upload__header--option(
          @click="fileUpload = true"
          :class="{active: fileUpload}"
        ) Из файла
      .img-upload__form--link(v-if="!fileUpload")
        form(@submit.prevent="addImage")
          .img-upload__message(:class="{ error, success }")
            | {{ uploadMsg }}
          .img-upload__form-row
            input(type="text" v-model="url" placeholder="Ссылка")
            button(
              type="submit"
              :disabled="!url"
            ) Загрузить
      .img-upload__form--file(v-else)
        form
          .img-upload__message(:class="{ error, success }")
            | {{ uploadMsg }}
          label
            | Загрузить
            input(type="file" @change="addImagesFromFile")
</template>

<script>
import DragNDrop from "@/components/DragNDrop";
export default {
  name: "ImgUpload",
  components: { DragNDrop },
  props: {
    dragActive: {
      type: Boolean,
      default: false,
    },
  },
  data: () => ({
    fileUpload: false,
    url: "",
    uploadMsg: "Введите ссылку или перетащите файл",
    error: false,
    success: false,
  }),
  watch: {
    url() {
      this.error = false;
      this.success = false;
      this.uploadMsg = "Введите ссылку или перетащите файл";
    },
    fileUpload(val) {
      this.error = false;
      this.success = false;
      this.uploadMsg = val
        ? "Загрузите файл в формате JSON, содержащий список изображений"
        : "Введите ссылку или перетащите файл";
    },
  },
  methods: {
    addImage() {
      if (this.url) {
        const urlRegExp = /[-a-zA-Z0-9@:%._+~#=]{1,256}\.[a-zA-Z0-9()]{1,6}\b([-a-zA-Z0-9()@:%_+.~#?&//=]*)?/gi;
        if (urlRegExp.test(this.url)) {
          const isImg = /(https?:\/\/.*\.(?:png|jpg|jpeg|svg|gif))/.test(
            this.url
          );
          if (isImg) {
            const img = new Image();
            img.onload = (e) => {
              const width = e.target.width;
              const height = e.target.height;
              this.$emit("newImage", {
                url: this.url,
                width,
                height,
              });
              this.error = false;
              this.success = true;
              this.uploadMsg = "Изображение добавлено";
            };
            img.src = this.url;
          } else {
            this.error = true;
            this.uploadMsg = "Необходимо ввести ссылку на изображение";
          }
        } else {
          this.error = true;
          this.uploadMsg = "Ссылка не прошла проверку";
        }
      } else {
        this.error = true;
        this.uploadMsg = "Введите ссылку или перетащите файл";
      }
    },
    addImagesFromFile(e) {
      const fr = new FileReader();
      const files = e.target.files;
      fr.onloadend = () => {
        try {
          const res = fr.result;
          const images = JSON.parse(res);
          if (images) this.$emit("addImages", images.galleryImages);
        } catch (e) {
          this.error = true;
          this.uploadMsg = "Ошибка при загрузке файла";
        }
      };
      fr.readAsText(files[0]);
    },
  },
};
</script>

<style lang="stylus" scoped>
@import '../assets/variables.styl'
.img-upload
  &__header
    user-select none
    display flex
    justify-content flex-start
    font-size 1.2rem
    margin-bottom 1rem
    &--option
      position relative
      cursor pointer
      margin-right 2rem
      transition .3s ease-in
      &:hover
        color accent
      &::before
        content ''
        position absolute
        bottom -.3rem
        left 0
        width 100%
        margin-top 1rem
        height 2px
        background transparent
        transition .3s ease-in
      &.active
        &:hover
          color #ffffff
        &::before
          background accent
  &__message
    transition .1s ease-in
    margin-bottom .5rem
  &__form-row
    display flex
    input
      flex 1
    @media screen and (max-width 400px)
      flex-wrap wrap
      input
        margin 0 0 .5rem 0
      button
        flex 1
        margin-left auto
</style>
