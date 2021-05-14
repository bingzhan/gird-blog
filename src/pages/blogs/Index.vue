<template>
  <Layout>
    <section>
      <div style="min-height: 600px">
          <el-card shadow="never" style="margin-bottom: 20px;" body-style="overflow: hidden;">
              <!-- <el-input placeholder="请输入关键字" v-model="searchKey" clearable style="width: 300px"></el-input>
              <el-button @click="search" icon="el-icon-search" style="margin-left: 10px" circle plain></el-button>
              <el-button @click="$share()" style="margin-left: 10px" icon="el-icon-share" type="warning" plain circle></el-button> -->
              <el-button type="primary" v-if="!token" @click="dialogVisible = true" plain style="float: right;">Login</el-button>
              <g-link class="carticle" v-else style="float: right;" to="/blogs/create">
                <i class="el-icon-edit"></i>
                <span>写博文</span>
              </g-link>
          </el-card>

          <div v-if="blogs && blogs.length>0">
              <el-card shadow="hover" v-for="item in blogs" :key="item.node.id" style="margin-bottom: 20px">
                  <div slot="header">
                      <el-row>
                          <el-col :span="16">
                              <span>
                                  <g-link :to="`/post/${item.node.id}`" style="text-decoration:none;cursor:pointer">
                                      <i class="el-icon-edit-outline"></i>&nbsp;&nbsp; {{item.node.title}}
                                  </g-link>
                              </span>
                          </el-col>
                          <el-col :span="8">
                              <div style="text-align: right;">
                                  <el-button @click="$share()" style="padding: 3px 0" type="text" icon="el-icon-share"></el-button>
                                  <g-link :to="`/blogs/create?id=${item.node.id}`" class="el-icon-edit editblog" v-if="token"></g-link>
                                  <el-button @click="deleteBlog(item.node.id)" style="padding: 3px 0" type="text" icon="el-icon-delete" v-if="token"></el-button>
                              </div>
                          </el-col>
                      </el-row>
                  </div>
                  <div style="font-size: 0.9rem;line-height: 1.5;color: #606c71;">
                      最近更新 {{item.node.updated_at | date}}
                  </div>
                  <div style="font-size: 1.1rem;line-height: 1.5;color: #303133;padding: 10px 0px 0px 0px">
                      {{item.node.content}}
                  </div>
              </el-card>
              <Pager class="pager" :info="$page.posts.pageInfo"/>

          </div>

          <el-card shadow="never" style="margin-bottom: 20px;padding: 20px 0px 20px 0px;text-align: center" v-if="!blogs||blogs.length==0">
              <font style="font-size: 30px;color:#dddddd ">
                  <b>还没有博客 (╯°Д°)╯︵ ┻━┻</b>
              </font>
          </el-card>
      </div>
    </section>
    <el-dialog title="登录" :visible.sync="dialogVisible" width="50%">
      <el-form ref="loginForm" :model="loginForm" :rules="rules" label-width="60px">
        <el-form-item label="用户" prop="name" required>
          <el-input v-model="loginForm.name" placeholder="用户" />
        </el-form-item>
        <el-form-item label="密码" prop="password" required>
          <el-input type="password" v-model="loginForm.password" placeholder="密码" />
        </el-form-item>
      </el-form>
      <span slot="footer" class="dialog-footer">
        <el-button @click="resetForm">取 消</el-button>
        <el-button type="primary" @click="onLogin">登 录</el-button>
      </span>
    </el-dialog>
  </Layout>
</template>
<page-query>
  query($page: Int) {
    posts: allStrapiPost(perPage: 2, page: $page) @paginate {
      pageInfo {
        totalPages
        currentPage
      }
      edges{
        node{
          id
          title
          content
          updated_at
        }
      }
    }
  }
</page-query>
<script>
import axios from 'axios'
import { Pager } from 'gridsome'
const copy = (message) => {
    let doc = document.createElement("input")
    doc.value = message
    document.body.appendChild(doc)
    doc.select()
    let status
    try {
        status = document.execCommand('copy')
    } catch (e) { }
    document.body.removeChild(doc)
    return status
}
export default {
  name: "Blogs",
  data() {
    return {
      dialogVisible: false,
      token: window.sessionStorage.getItem('jwt'),
      loginForm: {
        name: '',
        password: ''
      },
      rules: {
        name: [
          { required: true, message: '请输入用户名称', trigger: 'blur' }
        ],
        password: [
          { required: true, message: '请输入用户密码', trigger: 'blur' }
        ],
      }
    }
  },
  methods: {
    $share(message) {
      if (!message) {
          message = window.location
      } else {
          let arr = (window.location + "").split("#")
          message = arr[0] + "#" + message
      }
      if (copy(message)) {
          this.$confirm('链接已复制,去分享给好友吧!!', '分享', {
              showCancelButton: false,
              showClose: false,
              type: 'success'
          })
      } else {
          this.$confirm('链接复制失败,可能因为浏览器不兼容', '分享', {
              showCancelButton: false,
              showClose: false,
              type: 'warning'
          })
      }
    },
    onLogin() {
      this.$refs.loginForm.validate((valid) => {
        if (!valid) {
          console.log('error submit!!');
          return false;
        }
        this.dialogVisible = false
        axios.post(`${process.env.GRIDSOME_API_URL}/auth/local`, {
          identifier: this.loginForm.name,
          password: this.loginForm.password,
        }).then(response => {
          window.sessionStorage.setItem('jwt', response.data.jwt);
          this.token = response.data.jwt;
          this.$refs.loginForm.resetFields();
          this.$message({
              message: '登录成功！',
              type: 'success'
          })
        }).catch(error => {
          this.$message({
              message: '登录失败',
              type: 'error'
          })
        });
      });
    },
    resetForm() {
      this.$refs.loginForm.resetFields();
      this.dialogVisible = false
    },
    deleteBlog(id) {
      axios.delete(`${process.env.GRIDSOME_API_URL}/posts/${id}`, {
        headers: {
          Authorization: `Bearer ${this.token}`,
        },
      }).then(res => {
        console.log(89, res);
        this.$message({
            message: '删除文章成功！',
            type: 'success'
        })
      }).catch(err => {
        this.$message({
            message: '删除文章失败',
            type: 'error'
        })
      });

    },
  },
  computed: {
    blogs() {
      return this.$page.posts.edges;
    }
  },
  components: {
    Pager
  },
  mounted() {
    console.log(112, this.$page);
  }
}
</script>
