<template>
  <Layout>
    <div>
      <h1>{{ $page.post.title }}</h1>
      <div>{{ $page.post.content }}</div>
    </div>
  </Layout>
</template>

<page-query>
query ($id: ID!) {
  post: strapiPost (id: $id) {
    id
    title
    content
  }
}
</page-query>

<script>
export default {
  name: 'PostPage',
  metaInfo () {
    return {
      title: this.$page.post.title
    }
  }
}
</script>

<style>

</style>
