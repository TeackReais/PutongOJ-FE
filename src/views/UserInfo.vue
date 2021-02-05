<template lang="html">
  <div class="user-wrap">
    <Row>
      <Col :span="6">
        <img src="../assets/logo.png" alt="">
        <h1 style="margin-bottom: 20px">{{ user.uid }}</h1>
        <Icon type="person"></Icon>&nbsp;&nbsp;{{ `Nick: ${user.nick}` }}
        <div class="group" v-if="group.length > 0">
          <Icon type="person-stalker"></Icon>&nbsp;&nbsp;{{ `Group: ${group}` }}
        </div>
        <div class="motto" v-if="user.motto">
          <Icon type="edit"></Icon>&nbsp;&nbsp;{{ `Motto: ${user.motto}` }}
        </div>
        <div v-if="user.mail">
          <Icon type="email"></Icon>&nbsp;&nbsp;{{ `Mail: ${user.mail}` }}
        </div>
        <div v-if="user.school">
          <Icon type="university"></Icon>&nbsp;&nbsp;{{ `School: ${user.school}` }}
        </div>
        <Row class="border" type="flex" justify="center">
          <Col :span="12">
            <h1>{{ user.solve }}</h1>
            <h4>{{$t("message.Solved")}}</h4>
          </Col>
          <Col :span="12">
            <h1>{{ user.submit }}</h1>
            <h4>{{$t("message.Submit")}}</h4>
          </Col>
        </Row>
      </Col>
      <Col :offset="1" :span="17">
        <Tabs v-model="display">
          <TabPane label="Overview" name="overview">
            <div class="solved">
              <div class="solved-name">{{$t("message.Solved2")}}</div>
              <Button v-for="(item, index) in solved" :key="item" type="text">
                <router-link :to="{ name: 'problemInfo', params: { pid: item } }">{{ item }}</router-link>
              </Button>
            </div>
            <div class="unsolved">
              <div class="unsolved-name">{{$t("message.Unsolved")}}</div>
              <Button v-for="item in unsolved" :key="item" type="text">
                <router-link :to="{ name: 'problemInfo', params: { pid: item } }">{{ item }}</router-link>
              </Button>
            </div>
          </TabPane>
          <TabPane label="Edit" name="edit" class="edit" v-if="profile && profile.uid === user.uid">
            <Row class="nick">
              <Col :span="2" class="label">{{$t("message.Nick")}}</Col>
              <Col :span="12">
                <Input v-model="user.nick"></Input>
              </Col>
            </Row>
            <Row>
              <Col :span="2" class="label">{{$t("message.Motto")}}</Col>
              <Col :span="12">
                <Input v-model="user.motto"></Input>
              </Col>
            </Row>
            <Row>
              <Col :span="2" class="label">{{$t("message.School")}}</Col>
              <Col :span="12">
                <Input v-model="user.school"></Input>
              </Col>
            </Row>
            <Row>
              <Col :span="2" class="label">{{$t("message.Mail")}}</Col>
              <Col :span="12">
                <Input v-model="user.mail"></Input>
              </Col>
            </Row>
            <Row>
              <Col :span="2" class="label">{{$t("message.Password")}}</Col>
              <Col :span="12">
                <Input v-model="newPwd" type="password" placeholder="Leave it blank if it is not changed"></Input>
              </Col>
            </Row>
            <Row>
              <Col :span="2" class="label">{{$t("message.CheckPwd")}}</Col>
              <Col :span="12">
                <Input v-model="checkPwd" type="password" placeholder="Leave it blank if it is not changed"></Input>
              </Col>
            </Row>
            <Row class="submit">
              <Col :offset="6" :span="6">
                <Button type="primary" size="large" @click="submit">{{$t("message.Submit2")}}</Button>
              </Col>
            </Row>
          </TabPane>
        </Tabs>
      </Col>
    </Row>
  </div>
</template>

<script>
import { mapGetters, mapActions } from 'vuex'
import { purify } from '@/util/helper'

export default {
  i18n: {
    messages: {
      zh_CN: {
        message: {
          Solved: '解决的问题数',
          Submit: '提交的次数',
          Solved2: '解决的问题',
          Unsolved: '未解决的问题',
          Nick: '昵称',
          School: '学校',
          Motto: '座右铭',
          Mail: '邮箱',
          Password: '密码',
          CheckPwd: '确认密码',
          Submit2: '提交'
        }
      },
      en_US: {
        message: {
          Solved: 'Solved',
          Submit: 'Submit',
          Solved2: 'Solved',
          Unsolved: 'Unsolved',
          Nick: 'Nick',
          School: 'School',
          Motto: 'Motto',
          Mail: 'Mail',
          Password: 'Password',
          CheckPwd: 'CheckPwd',
          Submit2: 'Submit'
        }
      }
    }
  },
  data: () => ({
    display: 'overview',
    newPwd: '',
    checkPwd: ''
  }),
  computed: {
    ...mapGetters({
      user: 'user/user',
      solved: 'user/solved',
      unsolved: 'user/unsolved',
      group: 'user/group',
      profile: 'session/profile'
    })
    // Leaveitblankifitisnotchanged: function () {
    //   if (this.$root.$i18n.locale === "zh_CN") {
    //     return "如果不修改请保留空白";
    //   } else if (this.$root.$i18n.locale === "en_US") {
    //     return "Leave it blank if it is not changed";
    //   }
    // },
  },
  created () {
    this.fetch()
  },
  methods: {
    ...mapActions(['changeDomTitle']),
    fetch () {
      this.$store.dispatch('user/findOne', this.$route.params).then(() => {
        this.changeDomTitle({ title: this.user.uid })
      })
    },
    submit () {
      if (this.newPwd === this.checkPwd) {
        const user = purify(Object.assign(
          this.user,
          { newPwd: this.newPwd }
        ))
        this.$store.dispatch('user/update', user).then(() => {
          this.$Message.success('修改成功！')
          this.display = 'overview'
        })
      } else {
        this.$Message.info('两次密码不一致，请重新输入！')
      }
    }
  },
  watch: {
    '$route' (to, from) {
      if (to !== from) {
        this.fetch()
      }
    }
  }
}
</script>

<style lang="stylus">
.user-wrap
  text-align: left
  line-height: 30px
  img
    width: 60%
    margin: 0 20%
  .border
    margin-top: 20px
    padding: 10px 0
    border-top: 1px solid #dfe2e8
    border-bottom: 1px solid #dfe2e8
    h1, h4
      text-align: center
  .solved, .unsolved
    margin-bottom: 30px
  .ivu-btn-text
    margin-left: 10px
    padding: 0
    font-size: 14px
  a
    color: #B12CCC
  .image
    width: 100%
    display: block
  .motto // motto 可能非常长，以至于一页放不下
    word-wrap: break-word
  .edit
    .ivu-row
      margin-bottom: 14px
    .nick
      margin-top: 10px
    .submit
      margin-top: 30px
</style>
