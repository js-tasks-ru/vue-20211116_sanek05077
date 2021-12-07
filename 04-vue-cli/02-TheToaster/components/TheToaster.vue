<template>
  <ui-toaster>
    <ui-toast v-for="toast in toasts" :key="toast.id" :type="toast.type">
      {{ toast.message }}
    </ui-toast>
  </ui-toaster>
</template>

<script>
import UiToaster from './UiToaster';
import UiToast from './UiToast';

const DELAY = 5000;

export default {
  name: 'TheToaster',

  components: { UiToaster, UiToast },

  data() {
    return {
      toasts: [],
    };
  },

  methods: {
    success(message) {
      this.show('success', message);
    },

    error(message) {
      this.show('error', message);
    },

    // Добавим универсальный метод, который может показывать тост любого типа
    show(type, message) {
      const toast = { type, message };

      toast.id = setTimeout(() => {
        // Удалять можно было бы простым unshift,
        // но такой способ работает только, если DELAY одинаковый
        const idToDelete = this.toasts.indexOf(toast);
        // У нас не может быть ситуации, что тост не нашёлся
        // Но можно предусмотреть удаление вне таймера или другие непредвиденные ошибки
        if (idToDelete !== -1) {
          this.toasts.splice(idToDelete, 1);
        }
      }, DELAY);

      this.toasts.push(toast);
    },
  },
};
</script>

<style></style>
