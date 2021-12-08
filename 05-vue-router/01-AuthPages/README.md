# AuthPages - основы VueRouter

## Настройка роутера

При создании роутера требуется описать три маршрута:

```javascript
import { createRouter, createWebHistory } from 'vue-router';

export const router = createRouter({
  history: createWebHistory('/05-vue-router/01-AuthPages'),
  routes: [
    {
      path: '/',
      name: 'index',
      component: () => import('../views/PageIndex'),
    },
    {
      path: '/login',
      name: 'login',
      component: () => import('../views/PageLogin'),
    },
    {
      path: '/register',
      name: 'register',
      component: () => import('../views/PageRegister'),
    },
  ],
});
```

К приложению роутер подключается как плагин:

```javascript
import { router } from './router';

createApp(App).use(router).mount('#app');
```

А в главном компоненте требуется вывести компонент маршрута с `RouterView`:

```html
<template>
  <div class="wrapper">
    <meetups-header />
    <main class="main">
      <router-view />
    </main>
    <meetups-footer />
  </div>
</template>
```

## Ссылки

В `MeetupsNav` и `MeetupsLogo` требуется обновить ссылки с `RouterLink`:

```html
<!-- 📁 MeetupsLogo.vue -->
<template>
  <h1 class="logo">
    <router-link :to="{ name: 'index' }" class="logo__link"
      ><img src="@/assets/logo.svg" alt="Meetups" class="logo__image" width="174" height="30"
    /></router-link>
  </h1>
</template>
```

```html
<!-- 📁 MeetupsNav.vue -->
<template>
  <nav class="nav">
    <router-link :to="{ name: 'login' }" class="nav__link">Вход</router-link>
    <router-link :to="{ name: 'register' }" class="nav__link">Регистрация</router-link>
  </nav>
</template>
```

## Формы

На страницах с формами надо аналогично поправить ссылки и добавить программную навигацию.

```javascript
// 📁 PageLogin.vue

methods: {
  handleSubmit() {
    this.$router.push(this.$route.query.from ?? { name: 'index' });
  },
},
```

```javascript
// 📁 PageRegister.vue

methods: {
  handleSubmit() {
    this.$router.push({ name: 'login' });
  },
},
```
