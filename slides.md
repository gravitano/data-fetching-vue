---
# try also 'default' to start simple
theme: seriph
# random image from a curated Unsplash collection by Anthony
# like them? see https://unsplash.com/collections/94734566/slidev
background: https://source.unsplash.com/collection/94734566/1920x1080
# apply any windi css classes to the current slide
class: 'text-center'
# https://sli.dev/custom/highlighters.html
highlighter: shiki
# show line numbers in code blocks
lineNumbers: false
# some information about the slides, markdown enabled
info: |
  ## Data Fetching in Vue.js
---

# Data Fetching in Vue.js

GITS Internal Sharing Session

---

# Frontend Updates

UI Component Docs (32%)

- Collapsible ✅
- DataTable ✅
- DataTablePagination ✅
- Dropdown ✅
- Icon ✅
- List ✅
- Logo ✅

<!--
You can have `style` tag in markdown to override the style for the current page.
Learn more: https://sli.dev/guide/syntax#embedded-styles
-->

---

# OKR

| OKR   | Current Value | Target | Percentage |
| ----- | :-----------: | :----: | ---------- |
| CI/CD |      13       |   16   | 81.25%     |

---

# Data Fetching

- The Basic: [fetch](https://developer.mozilla.org/en-US/docs/Web/API/Fetch_API/Using_Fetch) & [axios](https://github.com/axios/axios)
- VueUse: [useFetch](https://vueuse.org/core/usefetch/) & [useAxios](https://vueuse.org/integrations/useaxios/#useaxios)
- [VueQuery](https://vue-query.vercel.app/#/)
- [Tanstack’s VueQuery](https://tanstack.com/query/v4/docs/adapters/vue-query)
- Nuxt 3: [useFetch](https://v3.nuxtjs.org/api/composables/use-fetch/) & [useAyncData](https://v3.nuxtjs.org/api/composables/use-async-data)

---

# Two Ways of Data Fetching

- Fetching After Navigation
  - perform the navigation first,
  - and fetch data in the incoming component's lifecycle hook.
  - Display a loading state while data is being fetched.
- Fetching Before Navigation
  - Fetch data before navigation in the route enter guard,
  - and perform the navigation after data has been fetched.

Source: https://router.vuejs.org/guide/advanced/data-fetching.html

---

# The Basic - `fetch`

```vue {all|11-12}
<script setup lang="ts">
import { Todo } from './types';

const loading = ref(false);
const error = ref();
const todos = ref<Todo[]>([]);

const fetchData = async () => {
  loading.value = true;
  try {
    const response = await fetch('https://jsonplaceholder.typicode.com/todos');
    todos.value = await response.json();
  } catch (err: any) {
    error.value = err.message;
  } finally {
    loading.value = false;
  }
};

fetchData();
</script>
```

---

## Displaying Data

```vue {all|3|5|7|8-10}
<template>
  <div class="container mx-auto p-6">
    <div v-if="loading">Loading...</div>

    <div v-else-if="error">{{ error }}</div>

    <ul v-else class="list-disc">
      <li v-for="todo in todos" :key="todo.title">
        {{ todo.title }}
      </li>
    </ul>
  </div>
</template>
```

---

# Using `axios`

```vue {all|2|12-15}
<script setup lang="ts">
import axios from 'axios';
import { Todo } from './types';

const loading = ref(false);
const error = ref();
const todos = ref<Todo[]>([]);

const fetchData = async () => {
  loading.value = true;
  try {
    const response = await axios.get<Todo[]>(
      'https://jsonplaceholder.typicode.com/todos'
    );
    todos.value = response.data;
  } catch (err: any) {
    error.value = err.message;
  } finally {
    loading.value = false;
  }
};

fetchData();
</script>
```

---

# VueUse

- `useFetch`
- `useAxios`

---

# `useFetch`

```vue
<script setup lang="ts">
import { Todo } from './types';

const { data, isFetching, error } = useFetch<Todo[]>(
  'https://jsonplaceholder.typicode.com/todos'
).json();
</script>

<template>
  <div class="container mx-auto p-6">
    <div v-if="isFetching">Loading...</div>
    <div v-else-if="error">{{ error }}</div>
    <ul class="list-disc">
      <li v-for="todo in data" :key="todo.title">
        {{ todo.title }}
      </li>
    </ul>
  </div>
</template>
```

---

# `useAxios`

```vue
<script setup lang="ts">
import { useAxios } from '@vueuse/integrations/useAxios';
import { Todo } from './types';

const {
  data: todos,
  isLoading,
  error,
} = useAxios<Todo[]>('https://jsonplaceholder.typicode.com/todos');
</script>

<template>
  <div class="container mx-auto p-6">
    <div v-if="isLoading">Loading...</div>
    <div v-else-if="error">{{ error }}</div>
    <ul class="list-disc">
      <li v-for="todo in todos" :key="todo.title">
        {{ todo.title }}
      </li>
    </ul>
  </div>
</template>
```

---

# VueQuery

- Vue Query package provides hooks for **fetching**, **caching** and **updating asynchronous data** in Vue.
- Support for Vue 2.x and 3.x is provided via vue-demi package
- Based on react-query. All main concepts are inherited from the main package.

- Help you remove many lines of complicated and misunderstood code from your application and replace with just a handful of lines of Vue Query logic.
- Make your application more maintainable and easier to build new features without worrying about wiring up new server state data sources
- Have a direct impact on your end-users by making your application feel faster and more responsive than ever before.
- Potentially help you save on bandwidth and increase memory performance

Source: https://vue-query.vercel.app/#/

---

# VueQuery - Example

```vue
<script setup lang="ts">
import { useQuery } from 'vue-query';
function fetchTodoList() {
  return fetch('https://jsonplaceholder.typicode.com/todos').then((res) =>
    res.json()
  );
}
function useTodosQuery() {
  return useQuery('todos', fetchTodoList);
}
const { isLoading, data, error } = useTodosQuery();
</script>

<template>
  <div class="container mx-auto p-6">
    <div v-if="isLoading">Loading...</div>
    <div v-else-if="error">{{ error }}</div>
    <ul class="list-disc">
      <li v-for="todo in data" :key="todo.title">
        {{ todo.title }}
      </li>
    </ul>
  </div>
</template>
```

---

# Tanstack's VueQuery

- The `vue-query` package offers a 1st-class API for using TanStack Query via Vue.
- However, all of the primitives you receive from these hooks are core APIs that are shared across all of the TanStack Adapters including the Query Client, query results, query subscriptions, etc.

---

# Tanstack's VueQuery - Example

```vue
<script setup>
import { useQueryClient, useQuery, useMutation } from '@tanstack/vue-query';
// Access QueryClient instance
const queryClient = useQueryClient();
// Query
const { isLoading, isError, data, error } = useQuery(['todos'], getTodos);
// Mutation
const mutation = useMutation(postTodo, {
  onSuccess: () => {
    // Invalidate and refetch
    queryClient.invalidateQueries(['todos']);
  },
});
function onButtonClick() {
  mutation.mutate({
    id: Date.now(),
    title: 'Do Laundry',
  });
}
</script>
```

---

# Tanstack's VueQuery - Displaying Data

```vue
<template>
  <span v-if="isLoading">Loading...</span>
  <span v-else-if="isError">Error: {{ error.message }}</span>
  <!-- We can assume by this point that `isSuccess === true` -->
  <ul v-else>
    <li v-for="todo in data" :key="todo.id">{{ todo.title }}</li>
  </ul>
  <button @click="onButtonClick">Add Todo</button>
</template>
```

---

# Nuxt 3

- `useFetch`
- `useLazyFetch`
- `useAsyncData`
- `useAsyncData`

https://v3.nuxtjs.org/getting-started/data-fetching

---

# Nuxt 3 - `useFetch`

- Within your `pages`, `components` and `plugins` you can use useFetch to universally fetch from any URL.
- This composable provides a convenient wrapper around `useAsyncData` and `$fetch`.
- It automatically generates a key based on URL and fetch options,
- provides type hints for request url based on server routes,
- and infers API response type.

```vue
<script setup>
const { data: count } = await useFetch('/api/count');
</script>

<template>Page visits: {{ count }}</template>
```

---

# Nuxt 3 - `useLazyFetch`

- identically to `useFetch` with the `lazy: true` option set.
- In other words, the async function does not block navigation.
- you will need to handle the situation where the data is null (or whatever value you have provided in a custom default factory function).

```vue
<script setup>
const { pending, data: posts } = useLazyFetch('/api/posts');
watch(posts, (newPosts) => {
  // Because posts starts out null, you will not have access
  // to its contents immediately, but you can watch it.
});
</script>

<template>
  <!-- you will need to handle a loading state -->
  <div v-if="pending">Loading ...</div>
  <div v-else>
    <div v-for="post in posts">
      <!-- do something -->
    </div>
  </div>
</template>
```

---

# Nuxt 3 - `useAsyncData`

- Within your `pages`, `components` and `plugins` you can use `useAsyncData` to get access to data that resolves asynchronously.

```ts
// server/api/count.ts
let counter = 0;
export default () => {
  counter++;
  return JSON.stringify(counter);
};
```

```vue
<script setup>
const { data } = await useAsyncData('count', () => $fetch('/api/count'));
</script>

<template>Page visits: {{ data }}</template>
```

---

# What's the difference between `useFetch` and `useAsyncData`?

- In brief, `useFetch` receives a URL and gets that data, whereas `useAsyncData` might have more complex logic.
- `useFetch(url)` is nearly equivalent to `useAsyncData(url, () => $fetch(url))` - it's developer experience sugar for the most common use case.

---

# Grouping

- Advantages
  - Reusability
  - Three-shaking
  - Easier to test
- Folder Structure
  - (Old) Starter: in `src/services` folder
  - (New) Starter: in `src/api` folder

```
-- public
-- src
  -- api
    -- index.ts
    -- users.ts
  -- components
  -- pages
```

---

# Grouping - Example

```ts
// src/api/users.ts
export interface User {
  id: number;
  name: string;
  username: string;
  email: string;
}
const api = axios.create({
  baseURL: 'https://jsonplaceholder.typicode.com',
});
export const getUsers = () => {
  return useAxios('/users', api);
};
export const createUser = (user: User) => {
  return useAxios(
    '/users',
    {
      method: 'POST',
      data: user,
    },
    api
  );
};
```

---

# Grouping - Entry Point

```ts
// src/api/index.ts

export * from './users';
```

---

# Grouping - Usage Example

```vue
<script setup lang="ts">
import { getUsers } from '@/api';

const { data, isLoading, error } = getUsers();
</script>

<template>
  <div class="container mx-auto m-6">
    <div v-if="isLoading">Loading...</div>
    <div v-if="error">Loading...</div>
    <div v-else>
      <pre>{{ data }}</pre>
    </div>
  </div>
</template>
```

---

# Recommendation

- Vue
  - VueUse: `useFetch` or `useAxios`
  - VueQuery (non Tanstack)
  - Tanstack's VueQuery: React dev friendly
- Nuxt 3
  - use built-in composable like `useFetch` or `useAsyncData`
