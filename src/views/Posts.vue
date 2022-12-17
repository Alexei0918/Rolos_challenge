<script lang="ts" setup>
  import { getPosts, Post } from '@/api/api';
  import { onBeforeMount, ref } from 'vue';
  import Paginate from '@/views/Paginate.vue';

  const posts = ref<Post[]>();
  const pageCount = ref<number>();

  onBeforeMount(() => loadPosts(1));

  async function loadPosts(pageNum:number) {
    pageCount.value = (await getPosts(pageNum)).total / 5;
    posts.value = (await getPosts(pageNum)).posts;
  }
</script>

<template>
  <h1>Posts list</h1>
  <paginate
    :page-count="pageCount"
    :page-range="5"
    :margin-pages="2"
    :click-handler="loadPosts"
    :container-class="'pagination'"
    :page-class="'page-item'">
  </paginate>
  <RouterLink
    class="post"
    v-for="(post, index) of posts"
    :to="{ name: 'post', params: { postId: post.id } }"
    :key="index"
  >
    <div class="post__id">#{{ post.id }}</div>
    <div class="post__title">{{ post.title }}</div>
    <div>{{ post.body }}</div>
  </RouterLink>
</template>

<style scoped>
  .post {
    display: grid;
    grid-template-columns: min-content 1fr;
    grid-template-rows: repeat(2, auto);
    gap: 8px;

    & + & {
      margin-top: 12px;
      padding-top: 12px;
      border-top: 1px solid #ccc;
    }

    &__id {
      grid-row: -1/1;
    }

    &__title {
      font-weight: 700;
    }
  }
</style>
