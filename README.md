# nuxt-microcms-module

[microCMS](https://microcms.io) integration for [Nuxt](https://nuxt.com/).

## Getting Started

### Install

```bash
$ npm install nuxt-microcms-module
```

### Setup

```typescript
// nuxt.config.js

export default defineNuxtConfig({
  modules: [
    [
      'nuxt-microcms-module',
      {
        serviceDomain: 'YOUR_DOMAIN', // YOUR_DOMAIN is the XXXX part of XXXX.microcms.io
        apiKey: 'YOUR_API_KEY',
        // target: 'server',
      },
    ],
  ],
  // or
  modules: ['nuxt-microcms-module'],
  microCMS: {
    serviceDomain: 'YOUR_DOMAIN',
    apiKey: 'YOUR_API_KEY',
    // target: 'server',
  },
});
```

#### serviceDomain

`String`
Required.
Your service id, which is a subdomain of microCMS.

#### apiKey

`String`  
Required.
Your api-key.  
It can be obtained from the service settings.

#### target

`String` (can be `server` or `all`)
Default: `server`
By setting this value to `all`, the api-key you set will be included in the client-side code.
This will allow the client side to send requests to microCMS.
If you only want to send requests to microCMS at build time or from the server side, set this value to `server` or leave it blank.

### Hot to use

We provide several custom hooks that can be used globally.
These are functions that internally wrap useFetch.

```typescript
type useMicroCMSGetList = <T>(
  args: {
    endpoint: string;
    queries?: MicroCMSQueries;
  },
  fetchOptions?: FetchOptions
) => Promise<AsyncData<MicroCMSListResponse<T>>>;
type useMicroCMSGetListDetail = <T>(
  args: {
    endpoint: string;
    contentId: string;
    queries?: MicroCMSQueries;
  },
  fetchOptions?: FetchOptions
) => Promise<AsyncData<T & MicroCMSListContent>>;
type useMicroCMSGetObject = <T>(
  args: {
    endpoint: string;
    queries?: MicroCMSQueries;
  },
  fetchOptions?: FetchOptions
) => Promise<AsyncData<T & MicroCMSObjectContent>>;

// FetchOptions is the same as the second argument option of useFetch provided by Nuxt3.
// AsyncData is the return value of useFetch.
// https://nuxt.com/docs/api/composables/use-fetch
```

```vue
<template>
  <ul>
    <li v-for="blog in blogs?.contents" :key="blog.id">
      <NuxtLink :to="`/${blog.id}`">
        <img
          :src="blog.eyecatch.url"
          :width="blog.eyecatch.width"
          :height="blog.eyecatch.height"
          alt=""
        />
        <span>
          {{ blog.title }}
        </span>
      </NuxtLink>
    </li>
  </ul>
</template>

<script setup lang="ts">
type Blog = {
  title: string;
  eyecatch: MicroCMSImage;
};

const { data: blogs } = await useMicroCMSGetList<Blog>({
  endpoint: 'blogs',
  queries: { fields: ['id', 'title', 'eyecatch'] },
});
</script>
```

# LICENSE

Apache-2.0
