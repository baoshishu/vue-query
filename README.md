# vue-query [![npm package](https://badgen.net/npm/v/@baoshishu/vue-query)](https://www.npmjs.com/package/@baoshishu/vue-query) [![Actions Status](https://github.com/baoshishu/vue-query/workflows/CI/badge.svg)](https://github.com/baoshishu/vue-query/actions)

> Composition API for fetching asynchronous data in Vue.

- 🔥 Simple API
- 🎗 TypeScript Support
- 💪 Hide the Complexity of Reactive

**Depends on [@vue/composition-api](https://github.com/vuejs/composition-api)**

## Installation

```sh
npm install @baoshishu/vue-query
```

## Usage

```vue
<template>
  <div>
    <div v-if="error">error!</div>
    <div v-else-if="loading">...loading</div>
    <pre v-else>{{ JSON.stringify(data) }}</pre>
  </div>
</template>

<script>
import { createComponent } from '@vue/composition-api';
import { useQuery } from '@baoshishu/vue-query';

export default createComponent({
  setup() {
    const fetcher = name =>
      fetch(`https://api.github.com/users/${name}`).then(res => res.json());
    return useQuery(name, fetcher);
  },
});
</script>
```

If first argument of `useQuery` is a function, the result of this function will be passed to `fetcher` function, and this query will be reactive. You can think of this argument as the `getter` passed to `computed`.

```vue
<template>
  <div>
    <div v-if="error">error!</div>
    <div v-else-if="loading">...loading</div>
    <pre v-else>{{ JSON.stringify(data) }}</pre>
  </div>
</template>
<script>
export default {
  setup(props, context) {
    const fetcher = ({ path, params }) => {
      return fetch(
        `https://api.github.com/${path}?page=${params.page}`
      ).then(res => res.json());
    };
    return useQuery(
      () => ({
        path: '/users',
        params: { page: context.root.$route.query.page },
      }),
      fetcher
    );
  },
};

// if we change route, fetcher will be called again
$route.query.page = 2;

// fetcher({'/users', {page: 2}})
</script>
```

When _route_ changes `page`, as _route_ is **reactive**, a new request would be fired to fetch users of this new `page`

### Array Keys

When a query needs more information to uniquely describe its data, you can use an array to describe it, and it will be destructed as argumets to `fetcher` function.

```javascript
const fetcher = (a, b) => Promise.resolve(a, b);
useQuery(['a', 'b'], fetcher);
```

If the first argument is a function which returns an array,
this query will be reactive.

```javascript
const fetcher = (a, b) => Promise.resolve(a, b);

import { reactive } from '@vue/composition-api';
var params = reactive({ page: 1, per_page: 10 });

const fetcher = (page, per_page) => Promise.resolve(page, per_page);

useQuery(() => [params.page, params.per_page], fetcher);
```

## Related

- [@vue/composition-api](https://github.com/vuejs/composition-api)

## License

[MIT](http://opensource.org/licenses/MIT)
