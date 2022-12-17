<script lang="ts" setup>
  import { getPost, Post } from '@/api/api';
  import { computed, onBeforeMount, ref } from 'vue';
  import { useRoute } from 'vue-router';

  const route = useRoute();
  const postId = computed(() => +route.params.postId as Post['id']);
  const post = ref<Post>();

  onBeforeMount(() => loadPost());

  async function loadPost() {
    post.value = await getPost(postId.value);
  }
</script>

<template>
  <h1>Post {{ postId }}</h1>
  <RouterLink class="back" to="/">Back</RouterLink>
  <template v-if="post">
    <h3>{{ post.title }}</h3>
    <div>{{ post.body }}</div>
  </template>
</template>

<style scoped>
  .back {
    color: #46f;
  }
</style>
