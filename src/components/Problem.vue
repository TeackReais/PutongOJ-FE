<template lang="html">
  <div class="proinfo-wrap">
    <slot name="title"><h1>{{ problem.pid }}:  {{ problem.title }}</h1></slot>
    <h5>{{$t("message.TimeLimit")}}: {{ problem.time }}MS&nbsp;&nbsp;&nbsp;{{$t("message.MemoryLimit")}}: {{ problem.memory }}KB</h5>
    <h2 class="text-primary">Description</h2>
    <div class="cont" v-html="problem.description"></div>
    <h2>{{$t("message.Input")}}</h2>
    <div class="cont" v-html="problem.input"></div>
    <h2>{{$t("message.Output")}}</h2>
    <div class="cont" v-html="problem.output"></div>
    <h2>{{$t("message.SampleInput")}}
      <Tooltip content="Click to copy" placement="top">
        <Icon type="document" v-clipboard:copy="problem.in" v-clipboard:success="onCopy" style="cursor: pointer"></Icon>
      </Tooltip>
    </h2>
    <pre><code>{{ problem.in }}</code></pre>
    <h2>{{$t("message.SampleOutput")}}
      <Tooltip content="Click to copy" placement="top">
        <Icon type="document" v-clipboard:copy="problem.out" v-clipboard:success="onCopy" style="cursor: pointer"></Icon>
      </Tooltip>
    </h2>
    <pre><code>{{ problem.out }}</code></pre>
    <div v-if="problem.hint">
      <h2>{{$t("message.Hint")}}</h2>
      <div class="cont" v-html="problem.hint"></div>
    </div>
  </div>
</template>

<script>
export default {
  i18n: {
    messages: {
      zh_CN: {
        message: {
          TimeLimit: '时间限制',
          MemoryLimit: '内存限制',
          Input: '输入',
          Output: '输出',
          SampleInput: '输入示例',
          SampleOutput: '输出示例',
          Hint: '提示'
        }
      },
      en_US: {
        message: {
          TimeLimit: 'Time Limit',
          MemoryLimit: 'Memory Limit',
          Input: 'Input',
          Output: 'Output',
          SampleInput: 'Sample Input',
          SampleOutput: 'Sample Output',
          Hint: 'Hint'
        }
      }
    }
  },
  props: ['problem'],
  methods: {
    onCopy () {
      this.$Message.success('Copied!')
    }
  }
}
</script>

<style lang="stylus" scoped>
h1
  color: #9799CA
  margin-top: 10px
  margin-bottom: 8px
  text-align: center
h5
  margin-bottom: 10px
  text-align:center
h2
  border-bottom: 1px solid #e8e8e8
  padding: 10px 0
  color: #9799CA
.cont
  margin-top: 10px
  margin-bottom: 20px
pre
  padding: 10px
  border-radius: 5px
  background-color: #ECEFF1
</style>
