<template>
  <div class="image-uploader">
    <label
      class="image-uploader__preview"
      :class="{ 'image-uploader__preview-loading': loading }"
      :style="{ '--bg-url': bgImage }"
    >
      <span class="image-uploader__text">{{ uploadMessage }}</span>
      <input
        ref="input"
        v-bind="$attrs"
        type="file"
        accept="image/*"
        class="image-uploader__input"
        @change="uploadFile($event.target.files[0])"
        @click="removeFile($event)"
      />
    </label>
  </div>
</template>

<script>
export default {
  name: 'UiImageUploader',
  inheritAttrs: false,
  props: {
    preview: {
      type: String,
    },
    uploader: {
      type: Function,
    },
  },
  emits: ['select', 'remove', 'upload', 'error'],
  data() {
    return {
      loading: false,
      status: this.preview ? 'preview' : 'empty',
    };
  },
  computed: {
    bgImage() {
      return this.preview ? `url("${this.preview}")` : 'var(--default-cover)';
    },
    uploadMessage() {
      if (this.loading) {
        return 'Загрузка...';
      } else if (this.status === 'empty') {
        return 'Загрузить изображение';
      }
      return 'Удалить изображение';
    },
  },
  methods: {
    uploadFile(file) {
      if (this.status === 'empty') {
        this.$emit('select', file);
        if (this.uploader) {
          this.loading = true;
          this.uploader(file)
            .then((result) => {
              this.$emit('upload', result);
              this.loading = false;
              this.status = 'preview';
            })
            .catch((err) => {
              this.$emit('error', err);
              this.loading = false;
              this.status = 'empty';
              this.$refs.input.value = null;
            });
        } else {
          this.loading = false;
          this.status = 'preview';
          this.$refs.input.value = null;
        }
      }
    },
    removeFile(e) {
      if (this.status === 'preview') {
        e.preventDefault();
        this.$emit('remove');
        this.status = 'empty';
        this.$refs.input.value = null;
      }
    },
  },
};
</script>

<style scoped>
.image-uploader {
}

.image-uploader__input {
  opacity: 0;
  height: 0;
}

.image-uploader__preview {
  --bg-url: var(--default-cover);
  background-size: cover;
  background-position: center;
  background-image: linear-gradient(0deg, rgba(0, 0, 0, 0.4), rgba(0, 0, 0, 0.4)), var(--bg-url);
  border: 2px solid var(--blue-light);
  border-radius: 8px;
  transition: 0.2s border-color;
  cursor: pointer;
  display: flex;
  flex-direction: column;
  align-items: center;
  justify-content: center;
  max-width: 512px;
  height: 228px;
}

.image-uploader__text {
  color: var(--white);
  font-family: 'Nunito', sans-serif;
  font-weight: 600;
  font-size: 20px;
  line-height: 28px;
}

.image-uploader__preview:hover {
  border-color: var(--blue);
}

.image-uploader__preview.image-uploader__preview-loading {
  cursor: no-drop;
}
</style>
