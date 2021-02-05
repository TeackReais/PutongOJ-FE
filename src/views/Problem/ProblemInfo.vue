<template lang="html">
  <div class="proinfo-wrap">
    <problem :problem="problem" />
    <Button type="ghost" shape="circle" icon="ios-paperplane" @click="submit">{{$t("message.Submit")}}</Button>
  </div>
</template>

<script>
import Problem from '@/components/Problem.vue'
import { mapGetters, mapActions } from 'vuex'

export default {
  i18n: {
    messages: {
      zh_CN: { message: { Submit: '提交' } },
      en_US: { message: { Submit: 'Submit' } }
    }
  },
  components: {
    Problem
  },
  computed: {
    ...mapGetters('problem', [ 'problem' ])
  },
  created () {
    this.$store.dispatch('problem/findOne', this.$route.params).then(() => {
      this.changeDomTitle({ title: `Problem ${this.problem.pid}` })
    })
  },
  methods: {
    ...mapActions(['changeDomTitle']),
    submit () {
      this.$router.push({
        name: 'problemSubmit',
        params: this.$router.params
      })
    }
  }
}
</script>

<style>
</style>
