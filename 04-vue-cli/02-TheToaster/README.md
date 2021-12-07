# TheToaster

## Компонент UiToast

Создадим для тоста отдельный компонент с параметрами `type` с типом тоста и `message` - с сообщением. Для сообщения
можно предоставить как слот, так и входной параметр. Этот компонент будет выводить один тост. В него также нужно
перенести часть стилей.

Такой компонент можно будет использовать для вывода тоста независимо от тостера.

```html
<template>
  <div class="toast" :class="markup.class">
    <ui-icon class="toast__icon" :icon="markup.icon" />
    <span>
      <slot>{{ message }}</slot>
    </span>
  </div>
</template>

<script>
  import UiIcon from './UiIcon';

  export default {
    name: 'UiToast',

    components: { UiIcon },

    props: {
      type: {
        type: String,
        default: 'success',
        validator: (value) => ['success', 'error'].includes(value),
      },

      message: {
        type: String,
      },
    },

    computed: {
      markup() {
        // Будем хранить класс и иконку для каждого типа тоста
        // Легко добавить новый тип при необходимости
        const toastTypesMarkup = {
          success: {
            class: 'toast_success',
            icon: 'check-circle',
          },

          error: {
            class: 'toast_error',
            icon: 'alert-circle',
          },
        };

        return toastTypesMarkup[this.type];
      },
    },
  };
</script>

<style scoped>
  .toast {
    display: flex;
    flex: 0 0 auto;
    flex-direction: row;
    align-items: center;
    padding: 16px;
    background: #ffffff;
    box-shadow: 0 2px 6px rgba(0, 0, 0, 0.15);
    border-radius: 4px;
    font-size: 18px;
    line-height: 28px;
    width: auto;
  }

  .toast + .toast {
    margin-top: 20px;
  }

  .toast__icon {
    margin-right: 12px;
  }

  .toast.toast_success {
    color: var(--green);
  }

  .toast.toast_error {
    color: var(--red);
  }
</style>
```

## UiToaster

Добавим ещё один глупый UI компонент - для вывода списка тостов. Фактически он содержит только стили для всего списка
тостов.

Его можно делать либо со слотом, в который пользователь компонента будет передавать `UiToast`, либо передавать массив с
данными тостов, чтобы он выводил их самостоятельно. Допустимы оба варианта, но первый проще в использовании.

```html
<template>
  <div class="toasts">
    <slot />
  </div>
</template>

<script>
  export default {
    name: 'UiToaster',
  };
</script>

<style scoped>
  .toasts {
    position: fixed;
    bottom: 8px;
    right: 8px;
    display: flex;
    flex-direction: column;
    justify-content: flex-end;
    white-space: pre-wrap;
    z-index: 999;
  }

  @media all and (min-width: 992px) {
    .toasts {
      bottom: 72px;
      right: 112px;
    }
  }
</style>
```

## Компонент TheToaster

Теперь можно переходить к главному компоненту - тостеру. Он будет отвечать за управление тостами, их хранение и вывод.

### Хранение тостов

Требуется добавить компоненту свойство в состоянии (`data`), например, `toasts`, в котором будет храниться список
текущих тостов. Именно этот список будет определять состояние компонента и будет выводиться в шаблоне.

Хранить в списке понадобится не только текст сообщения, но и его тип.

### Методы success, error

Осталось только реализовать собственно методы добавления тостов. Эти методы довольно простые, добавляющие новый тост в
список тостов.

Тут же можно используя `setTimeout` установить таймер на удаление этого тоста через 5 секунд.

Идеально будет иметь универсальный метод добавления тоста любого типа.

### key списка тостов

Мы пока плохо знаем, что делает `key`. Но, если вы читали Style Guide, или используете линтер (возможно, встроенные в
вашу среду разработки), то могли видеть ошибки об отсутствии `key` на элементе с директивой `v-for`.

Нет какого-либо естественного идентификатора у тоста. Можно создать случайный, счётчик, либо использовать, например,
идентификатор его таймера.

### Решение

```html
<template>
  <ui-toaster>
    <ui-toast v-for="toast in toasts" :key="toast.id" :type="toast.type"> {{ toast.message }} </ui-toast>
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
```
