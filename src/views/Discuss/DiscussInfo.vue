<template>
  <div class="discuss-wrap">
    <h1>{{ discuss.title }}</h1>
    <Card dis-hover v-for="comment in discuss.comments" :key="comment.content">
      <p slot="title">
        {{ comment.uid }}
      </p>
      <span slot="extra">
        {{ comment.create | timeagoPretty }}
      </span>
      <pre><code>{{ comment.content }}</code></pre>
    </Card>
    <br>
    <Form :model="form" label-position="top" class="form">
        <FormItem>
            <Input v-model="form.content" type="textarea" :autosize="{minRows: 2,maxRows: 20}"></Input>
        </FormItem>
        <FormItem>
          <Button
            type="primary"
            @click="createNew"
            :loading="loading"
            :disabled="!isLogined"
          >{{ $t("message.Addareply") }}</Button>
          <span v-if="!isLogined">Login to reply</span>
        </FormItem>
    </Form>
    <span>{{
      $t("message.Youwillreceivenotificationsthroughyouremailifanyonereplies")
    }}</span>
  </div>
</template>

<script>
  import { mapGetters, mapActions } from 'vuex'

  export default {
    i18n: {
      messages: {
        zh_CN: {
          message: {
            Addareply: '回复',
            Youwillreceivenotificationsthroughyouremailifanyonereplies:
              '如果有人回复，你将收到邮件通知。'
          }
        },
        en_US: {
          message: {
            Addareply: 'Add a reply',
            Youwillreceivenotificationsthroughyouremailifanyonereplies:
              'You will receive notifications through your email, if anyone replies'
          }
        }
      }
    },
    props: {
      did: {
        required: true
      }
    },
    data () {
      return {
        loading: false,
        form: {
          content: ''
        }
      }
    },
    computed: {
      ...mapGetters('discuss', [ 'discuss' ]),
      ...mapGetters('session', [ 'isLogined' ])
    },
    created () {
      this.fetch()
    },
    methods: {
      ...mapActions('discuss', [ 'findOne', 'update' ]),
      fetch () {
        this.findOne({ did: this.did })
      },
      createNew () {
        this.loading = true
        this.update({
          did: this.did,
          content: this.form.content
        }).then(() => {
          this.loading = false
          this.form.content = ''
          this.fetch()
        }).catch(() => {
          this.loading = false
        })
      }
    }
  }
</script>

<style lang="stylus">
.discuss-wrap
  .ivu-card-body
    padding: 0 16px
  .ivu-card
    margin-top: 1em
    &:first-child
      margin-top: 0.1em
  .ivu-card-extra
    top: 4px
  .ivu-card-head
    padding: 4px 16px
</style>
