<template lang="html">
  <div>
    <oj-problem-edit :problem="problem" />
    <Button type="primary" @click="submit">{{$t("message.Submit")}}</Button>
  </div>
</template>

<script>
import ProblemEdit from '@/components/ProblemEdit'
import { mapGetters } from 'vuex'

export default {
  i18n: {
    messages: {
      zh_CN: { message: { Submit: '提交' } },
      en_US: { message: { Submit: 'Submit' } }
    }
  },
  computed: {
    ...mapGetters('problem', [ 'problem' ])
  },
  created () {
    this.$store.dispatch('problem/findOne', { pid: this.$route.params.pid })
  },
  methods: {
    submit () {
      this.$store.dispatch('problem/update', this.problem).then((data) => {
        this.$Message.success('提交成功！')
        this.$router.push({name: 'problemInfo', params: { pid: data.pid }})
      })
    }
  },
  components: {
    'oj-problem-edit': ProblemEdit
  }
}
</script>

<style lang="stylus">
</style>
