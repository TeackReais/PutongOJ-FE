<template lang="html">
  <div class="proadd-wrap">
    <Row>
      <Col :span="23">
        <Input v-model="problem.title">
          <span slot="prepend">{{$t("message.Title")}}</span>
        </Input>
      </Col>
    </Row>
    <Row>
      <Col :span="11">
        <Input v-model="problem.time">
          <span slot="prepend">{{$t("message.Time")}}</span>
          <span slot="append">MS</span>
        </Input>
      </Col>
      <Col :offset="1" :span="11">
        <Input v-model="problem.memory">
          <span slot="prepend">{{$t("message.Memory")}}</span>
          <span slot="append">KB</span>
        </Input>
      </Col>
    </Row>
    <div class="label">{{$t("message.Description")}}</div>
    <Row>
      <Col :span="23">
        <vue-editor id="editor1"
          useCustomImageHandler
          @imageAdded="handleImageAdded" v-model="problem.description">
        </vue-editor>
      </Col>
    </Row>
    <div class="label">{{$t("message.Input")}}</div>
    <Row>
      <Col :span="23">
        <vue-editor id="editor2" v-model="problem.input"></vue-editor>
      </Col>
    </Row>
    <div class="label">{{$t("message.Output")}}</div>
    <Row>
      <Col :span="23">
        <vue-editor id="editor3" v-model="problem.output"></vue-editor>
      </Col>
    </Row>
    <div class="label">{{$t("message.Hint")}}</div>
    <Row>
      <Col :span="23">
        <vue-editor id="editor4" v-model="problem.hint"></vue-editor>
      </Col>
    </Row>
    <div class="label">{{$t("message.SampleInput")}}</div>
    <Row>
      <Col :span="23">
        <Input type="textarea" :rows="8" v-model="problem.in"></Input>
      </Col>
    </Row>
    <div class="label">{{$t("message.SampleOutput")}}</div>
    <Row>
      <Col :span="23">
        <Input type="textarea" :rows="8" v-model="problem.out"></Input>
      </Col>
    </Row>
  </div>
</template>

<script>
import api from '@/api'
import { VueEditor } from 'vue2-editor'

export default {
  i18n: {
    messages: {
      zh_CN: {
        message: {
          Title: '标题',
          Time: '时间',
          Memory: '内存',
          Description: '描述',
          Input: '输入',
          Output: '输出',
          Hint: '提示',
          SampleInput: '输入示例',
          SampleOutput: '输出示例'
        }
      },
      en_US: {
        message: {
          Title: 'Title',
          Time: 'Time',
          Memory: 'Memory',
          Description: 'Description',
          Input: 'Input',
          Output: 'Output',
          Hint: 'Hint',
          SampleInput: 'Sample Input',
          SampleOutput: 'Sample Output'
        }
      }
    }
  },
  props: ['problem'],
  methods: {
    handleImageAdded (file, Editor, cursorLocation) {
      const formData = new window.FormData()
      formData.append('image', file)
      api.getImage(formData)
        .then(({ data }) => {
          const url = data.url // Get url from response
          Editor.insertEmbed(cursorLocation, 'image', url)
        })
        .catch((err) => console.log(err))
    }
  },
  components: {
    VueEditor
  }
}
</script>

<style lang="stylus">
.proadd-wrap
  margin-bottom: 20px
  .ivu-input-wrapper
    margin-bottom: 14px
  .label
    text-align:left
    margin-bottom: 10px
  #editor1, #editor2, #editor3, #editor4
    text-align: left
    height: 220px
    margin-bottom: 10px
  .el-textarea
    margin-bottom: 20px
</style>
