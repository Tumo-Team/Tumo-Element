<template>
  <div class="navbar">
    <hamburger id="hamburger-container" :is-active="sidebar.opened" class="hamburger-container" @toggleClick="toggleSideBar" />

    <breadcrumb id="breadcrumb-container" class="breadcrumb-container" />

    <div class="right-menu">
      <template v-if="device!=='mobile'">
        <el-tooltip content="全屏" effect="dark" placement="bottom">
          <screenfull id="screenfull" class="right-menu-item hover-effect" />
        </el-tooltip>
      </template>

      <el-tooltip content="用户头像" effect="dark" placement="bottom">
        <div class="user-avatar-container">
          <img :src="avatar+'?imageView2/1/w/80/h/80'" class="user-avatar">
        </div>
      </el-tooltip>
      <el-dropdown class="avatar-container right-menu-item hover-effect" trigger="click">
        <span class="el-dropdown-link">
          {{ name }}<i class="el-icon-arrow-down el-icon--right"></i>
        </span>
        <el-dropdown-menu slot="dropdown">
          <router-link to="/profile/index">
            <el-dropdown-item>Profile</el-dropdown-item>
          </router-link>
          <router-link to="/">
            <el-dropdown-item>Dashboard</el-dropdown-item>
          </router-link>
          <a target="_blank" href="https://github.com/TyCoding/vue-element-admin/">
            <el-dropdown-item>Github</el-dropdown-item>
          </a>
          <a target="_blank" href="https://panjiachen.github.io/vue-element-admin-site/#/">
            <el-dropdown-item>Docs</el-dropdown-item>
          </a>
          <el-dropdown-item divided @click.native="logout">
            <span style="display:block;">Log Out</span>
          </el-dropdown-item>
        </el-dropdown-menu>
      </el-dropdown>
    </div>
  </div>
</template>

<script>
import { mapGetters } from 'vuex'
import Breadcrumb from '@/components/Breadcrumb'
import Hamburger from '@/components/Hamburger'
import Screenfull from '@/components/Screenfull'

export default {
  components: {
    Breadcrumb,
    Hamburger,
    Screenfull
  },
  computed: {
    ...mapGetters([
      'sidebar',
      'avatar',
      'name',
      'device'
    ])
  },
  methods: {
    toggleSideBar() {
      this.$store.dispatch('app/toggleSideBar')
    },
    async logout() {
      await this.$store.dispatch('user/logout')
      this.$router.push(`/login?redirect=${this.$route.fullPath}`)
    }
  }
}
</script>

<style lang="scss" scoped>
.navbar {
  height: 64px;
  overflow: hidden;
  position: relative;
  background: #fff;
  box-shadow: 0 1px 4px rgba(0,21,41,.08);

  .hamburger-container {
    line-height: 64px;
    height: 100%;
    float: left;
    cursor: pointer;
    transition: background .3s;
    -webkit-tap-highlight-color:transparent;

    &:hover {
      background: #fff
    }
  }

  .breadcrumb-container {
    float: left;
  }

  .errLog-container {
    display: inline-block;
    vertical-align: top;
  }

  .right-menu {
    float: right;
    height: 100%;
    line-height: 64px;
    margin-right: 20px;;

    &:focus {
      outline: none;
    }

    .right-menu-item {
      display: inline-block;
      padding: 0 8px;
      height: 100%;
      font-size: 12px;
      color: #5a5e66;
      vertical-align: text-bottom;

      &.hover-effect {
        cursor: pointer;
        transition: background .3s;

        &:hover {
          background: #fff
        }
      }
    }

    .user-avatar-container{
      display: inline-block;
      padding: 0;
      height: 100%;
      font-size: 14px;
      color: #5a5e66;
      vertical-align: text-bottom;
    }

    .user-avatar {
      margin: 0 8px 0 5px;
      padding: 2px;
      width: 30px;
      height: 30px;
      border-radius: 100%;
      -webkit-box-sizing: border-box;
      box-sizing: border-box;
      border: 1px solid #eee;
      vertical-align: middle;
    }

    .avatar-container{
      padding: 0px;
    }
  }
}
</style>
