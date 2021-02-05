<template lang="html">
  <div class="nav-wrap">
    <Layout>
      <Header :style="{position: 'fixed', width: '100%', 'z-index': 100}">
        <Menu mode="horizontal" theme="light" @on-select="routerTo" :active-name="active">
          <div class="left">
            <MenuItem name="home">
              <Icon type="ios-home"></Icon>{{$t("message.Home")}}
            </MenuItem>
            <MenuItem name="problemList">
                <Icon type="ios-keypad"></Icon>{{$t("message.Problem")}}
            </MenuItem>
            <MenuItem name="discuss">
              <Icon type="chatbubble-working"></Icon>{{$t("message.Discuss")}}
            </MenuItem>
            <MenuItem name="status">
              <Icon type="refresh"></Icon>{{$t("message.Status")}}
            </MenuItem>
            <MenuItem name="ranklist">
              <Icon type="stats-bars"></Icon>{{$t("message.Ranklist")}}
            </MenuItem>
            <MenuItem name="contestList">
              <Icon type="android-bar"></Icon>{{$t("message.Contest")}}
            </MenuItem>
            <MenuItem name="faq">
              <Icon type="help-circled"></Icon>{{$t("message.FAQ")}}
            </MenuItem>
            <Submenu v-if="isAdmin" name="admin">
              <template slot="title">
                <Icon type="paper-airplane"></Icon>{{$t("message.Admin")}}
              </template>
              <MenuItem name="problemCreate">{{$t("message.CreateProblems")}}</MenuItem>
              <MenuItem name="contestCreate">{{$t("message.CreateContests")}}</MenuItem>
              <MenuItem name="newsCreate">{{$t("message.CreateNews")}}</MenuItem>
              <MenuItem name="userEdit">{{$t("message.UserManagement")}}</MenuItem>
            </Submenu>
          </div>
        </Menu>
        <div class="right">
          <Dropdown v-if="isLogined" @on-click="profileAction">
            <a href="javascript:void(0)">
              {{ profile.uid }}
              <Icon type="arrow-down-b"></Icon>
            </a>
            <DropdownMenu slot="list">
              <DropdownItem name="profile">{{$t("message.Profile")}}</DropdownItem>
              <DropdownItem name="logout">{{$t("message.Logout")}}</DropdownItem>
              <DropdownItem name="changeLanguage">{{$t("message.changeLanguage")}}</DropdownItem>
            </DropdownMenu>
          </Dropdown>
          <Button type="text" @click="login" v-else>{{$t("message.Login")}} / {{$t("message.Register")}}</Button>
        </div>
      </Header>
      <Content :style="{margin: '88px 20px 0', background: '#fff', minHeight: '500px', padding: '20px 40px'}">
        <router-view></router-view>
      </Content>
     <Footer class="layout-footer-center">
       <p>{{$t("message.ServerTime")}}: {{ currentTime | timePretty }}</p>
       <strong>Putong OJ</strong> by <a href="https://github.com/acm309" target="_blank">acm309 <Icon type="social-github"></Icon>.</a>
       The source code is licensed <a href="http://opensource.org/licenses/mit-license.php" target="_blank">MIT</a>.
     </Footer>
    </Layout>
    <Dialog></Dialog>
  </div>
</template>

<script>
import { mapMutations, mapGetters, mapState } from 'vuex'
import { TRIGGER_LOGIN } from '@/store/types'
import Dialog from './LoginAndRegister'

export default {
  components: {
    Dialog
  },
  i18n: {
    messages: {
      zh_CN: {
        message: {
          Home: '首页',
          Problem: '问题',
          Discuss: '讨论',
          Status: '提交',
          Ranklist: '排名',
          Contest: '比赛',
          FAQ: '常见问题',
          ServerTime: '服务器时间',
          Profile: '个人信息',
          Register: '注册',
          Login: '登录',
          Logout: '注销',
          changeLanguage: 'English',
          Admin: '管理员',
          CreateProblems: '创建问题',
          CreateContests: '创建比赛',
          CreateNews: '创建新闻',
          UserManagement: '用户管理'
        }
      },
      en_US: {
        message: {
          Home: 'Home',
          Problem: 'Problem',
          Discuss: 'Discuss',
          Status: 'Status',
          Ranklist: 'Ranklist',
          Contest: 'Contest',
          FAQ: 'FAQ',
          ServerTime: 'Server Time',
          Profile: 'Profile',
          Register: 'Register',
          Login: 'Login',
          Logout: 'Logout',
          changeLanguage: '中文',
          Admin: 'Admin',
          CreateProblems: 'Create Problems',
          CreateContests: 'Create Contests',
          CreateNews: 'Create News',
          UserManagement: 'User Management'
        }
      }
    }
  },
  computed: {
    ...mapGetters({
      isLogined: 'session/isLogined',
      profile: 'session/profile',
      isAdmin: 'session/isAdmin',
      currentTime: 'currentTime'
    }),
    ...mapState({
      active: state => state.route.name
    })
  },
  methods: {
    ...mapMutations('session', {
      login: TRIGGER_LOGIN
    }),
    routerTo (name) {
      this.$router.push({ name })
    },
    profileAction (name) {
      if (name === 'logout') {
        this.$store.dispatch('session/logout').then(() => this.$Message.info('bye bye!'))
      } else if (name === 'profile') {
        this.$router.push({ name: 'userInfo', params: { uid: this.profile.uid } })
      } else if ( name === 'changeLanguage') {
        if (this.$root.$i18n.locale === 'zh_CN') {
          this.$root.$i18n.locale = 'en_US'
        } else if (this.$root.$i18n.locale === 'en_US') {
          this.$root.$i18n.locale = 'zh_CN'
        }
      }
    }
  }
}
</script>

<style lang="stylus">
.nav-wrap
  border: 1px solid #d7dde4
  background: #f5f7f9
  position: relative
  border-radius: 4px
  overflow: hidden
  .ivu-layout-header
    display: flex
    justify-content: space-between
    background: #fff
    padding: 2px 0px 0px 0px
    height: 62px
    line-height: 62px
    box-shadow: 0 2px 3px hsla(0,0%,4%,.1)
  .ivu-menu-horizontal
    height: 62px
    line-height: 60px
  .left
    width: 900px
    margin: 0 auto
    margin-left: 5%
  .right
    margin-right: 5%
    .ivu-btn
      font-size: 14px
      margin-bottom: 6px
  .layout-footer-center
    text-align: center
    p
      margin-bottom: 8px
</style>
