<template>
  <div>
    <h1>修改用户信息</h1>
    <Row type="flex" justify="start">
      <Col :span="2" class="label">{{ $t("message.Username") }}</Col>
      <Col :span="4">
        <Input v-model="uid"></Input>
      </Col>
      <Col :offset="1" :span="2">
        <Button type="primary" @click="findUser">{{
          $t("message.Search")
        }}</Button>
      </Col>
    </Row>
    <Row type="flex" justify="start">
      <Col :span="2" class="label">{{ $t("message.Nick") }}</Col>
      <Col :span="21">
        <Input v-model="user.nick"></Input>
      </Col>
    </Row>
    <Row type="flex" justify="start">
      <Col :span="2" class="label">{{ $t("message.Motto") }}</Col>
      <Col :span="21">
        <Input v-model="user.motto"></Input>
      </Col>
    </Row>
    <Row type="flex" justify="start">
      <Col :span="2" class="label">{{ $t("message.School") }}</Col>
      <Col :span="21">
        <Input v-model="user.school"></Input>
      </Col>
    </Row>
    <Row type="flex" justify="start">
      <Col :span="2" class="label">{{ $t("message.Password") }}</Col>
      <Col :span="21">
        <Input v-model="newPwd" type="password" placeholder="Leave it blank if it is not changed"></Input>
      </Col>
    </Row>
    <Row type="flex" justify="start">
      <Col :span="2" class="label">{{ $t("message.CheckPwd") }}</Col>
      <Col :span="21">
        <Input v-model="checkPwd" type="password" placeholder="Leave it blank if it is not changed"></Input>
      </Col>
    </Row>
    <Button type="primary" @click="saveUser" class="submit">Submit</Button>
  </div>
</template>

<script>
import { mapGetters } from 'vuex'
import { purify } from '@/util/helper'

export default {
  i18n: {
    messages: {
      zh_CN: {
        message: {
          Username: '用户名',
          Nick: '昵称',
          Motto: '座右铭',
          School: '学校',
          Password: '密码',
          CheckPwd: '确认密码',
          Submit: '提交',
          Search: '查找'
        }
      },
      en_US: {
        message: {
          Username: 'Username',
          Nick: 'Nick',
          Motto: 'Motto',
          School: 'School',
          Password: 'Password',
          CheckPwd: 'CheckPwd',
          Submit: 'Submit',
          Search: 'Search'
        }
      }
    }
  },
  data: () => ({
    uid: '',
    newPwd: '',
    checkPwd: ''
  }),
  computed: {
    ...mapGetters({
      user: 'user/user'
    })
  },
  created () {
  },
  methods: {
    findUser () {
      this.$store.dispatch('user/findOne', { uid: this.uid })
    },
    saveUser () {
      if (this.newPwd === this.checkPwd) {
        const user = purify(Object.assign(
          this.user,
          { newPwd: this.newPwd }
        ))
        this.$store.dispatch('user/update', user).then(() => {
          this.$Message.success('修改成功！')
        })
      } else {
        this.$Message.info('两次密码不一致，请重新输入！')
      }
    }
  }
}
</script>

<style lang="stylus" scoped>
.ivu-col-offset-1
  margin-left: 1%
</style>
