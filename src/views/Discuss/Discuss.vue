<template lang="html">
  <div>
    <table>
      <tr>
        <th>Did</th>
        <th>{{$t("message.Title")}}</th>
        <th>{{$t("message.Author")}}</th>
        <th>{{$t("message.Updated")}}</th>
      </tr>
      <template v-for="item in list">
        <tr>
          <td>
            {{ item.did }}
          </td>
          <td> <router-link :to="{ name: 'discussInfo', params: { did: item.did } }">
            <Button type="text">{{ item.title }}</Button>
          </router-link></td>
          <td>
            <router-link :to="{ name: 'userInfo', params: { uid: item.uid } }">
              <Button type="text"> {{ item.uid }} </Button>
            </router-link>
          <td>
            {{ item.update | timeagoPretty }}
          </td>
        </tr>
      </template>
    </table>
    <h3>{{$t("message.CreateNewThread")}}</h3>
    <Form :model="form" label-position="right" :label-width="100" class="form">
      <FormItem label="Title">
          <Input v-model="form.title"></Input>
      </FormItem>
      <FormItem label="Content">
          <Input v-model="form.content" type="textarea" :autosize="{minRows: 2,maxRows: 20}"></Input>
      </FormItem>
      <FormItem>
        <Button type="primary" @click="createNew" :loading="loading" :disabled="!isLogined">{{$t("message.Submit")}}</Button>
        <span v-if="!isLogined">{{$t("message.Logintoreply")}}</span>
    </FormItem>
  </Form>
  </div>
</template>

<script>
import { mapGetters, mapActions } from 'vuex'

export default {
  i18n: {
    messages: {
      zh_CN: {
        message: {
          Title: '标题',
          Author: '作者',
          Updated: '最后更新',
          CreateNewThread: '创建新的讨论',
          Logintoreply: '登录后回复',
          Submit: '提交'
        }
      },
      en_US: {
        message: {
          Title: 'Title',
          Author: 'Author',
          Updated: 'Updated',
          CreateNewThread: 'Create New Thread',
          Logintoreply: 'Login to reply',
          Submit: 'Submit'
        }
      }
    }
  },
  data () {
    return {
      form: {
        title: '',
        content: ''
      },
      loading: false
    }
  },
  created () {
    this.fetch()
  },
  computed: {
    ...mapGetters('discuss', [ 'list' ]),
    ...mapGetters('session', [ 'isLogined' ])
  },
  methods: {
    ...mapActions('discuss', [ 'find', 'create' ]),
    fetch () {
      this.find()
    },
    createNew () {
      if (!this.form.title || !this.form.content) {
        this.$Message.warning('Title or Content can not be empty')
        return
      }
      this.loading = true
      this.create(this.form).then((did) => {
        this.$router.push({
          name: 'discussInfo',
          params: {
            did
          }
        })
      }).catch(() => {
        this.loading = false
      })
    }
  }
}
</script>

<style lang="stylus" scoped>
@import '../../styles/common'
h3
  margin-top: 20px
.form
  margin-top: 2em
</style>
