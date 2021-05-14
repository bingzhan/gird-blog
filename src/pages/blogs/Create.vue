<template>
  <Layout>
    <el-form :model="form" label-width="80px">
      <el-form-item label="文章标题">
        <el-input v-model="form.title" />
      </el-form-item>
      <el-form-item label="文章内容">
        <el-input type="textarea" v-model="form.content" />
      </el-form-item>
      <el-form-item>
        <el-button type="primary" @click.prevent="onSubmit">提 交</el-button>
      </el-form-item>
    </el-form>
  </Layout>
</template>
<page-query>
</page-query>
<script>
import axios from 'axios'
export default {
  name: 'BlogCreate',
  data() {
    return {
      form: {
        title: '',
        content: ''
      },
      token: sessionStorage.getItem('jwt')
    }
  },
  methods: {
    onSubmit() {
      const { id } = this.$route.query;
      if (id) {
        this.updateBlog(id);
        return false;
      }
      axios({
        method: 'POST',
        headers: {
          Authorization: `Bearer ${this.token}`,
        },
        url: `${process.env.GRIDSOME_API_URL}/posts`,
        data: this.form
      }).then(res => {
        this.$message({
            message: '创建文章成功！',
            type: 'success'
        })
        location.href = `${location.origin}/blogs`
      }).catch(err => {
        this.$message({
            message: '创建文章失败',
            type: 'error'
        })
      })
    },
    updateBlog(id) {
      axios({
        method: 'PUT',
        headers: {
          Authorization: `Bearer ${this.token}`,
        },
        url: `${process.env.GRIDSOME_API_URL}/posts/${id}`,
        data: this.form
      }).then(res => {
        this.$message({
            message: '编辑文章成功！',
            type: 'success'
        })
      }).catch(err => {
        this.$message({
            message: '编辑文章失败',
            type: 'error'
        })
      })
    },
    initData(id) {
      axios.get(`${process.env.GRIDSOME_API_URL}/posts/${id}`).then(res => {
        const { title, content } = res.data;
        this.form.title = title;
        this.form.content = content;
      }).catch(err => {
        this.$message({
            message: '获取文章失败',
            type: 'error'
        })
      });
    }
  },
  mounted() {
    const { id } = this.$route.query;
    if (id) {
      this.initData(id);
    }
  }
}
</script>

<style>
</style>
