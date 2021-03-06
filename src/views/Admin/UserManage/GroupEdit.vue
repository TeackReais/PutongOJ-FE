<template>
  <div>
    <h1>管理用户组</h1>
    <Row type="flex" justify="start">
      <Col :span="2" class="label">{{ $t("message.Group") }}</Col>
      <Col :span="4">
        <Select v-model="ind" filterable>
          <Option v-for="(item, index) in groupList" :value="index" :key="item.gid">{{ item.title }}</Option>
        </Select>
      </Col>
      <Col :offset="1" :span="2">
        <Dropdown @on-click="manageGroup">
          <Button type="primary">
            {{ $t("message.Manage") }}
            <Icon type="arrow-down-b"></Icon>
          </Button>
          <DropdownMenu slot="list">
            <DropdownItem name="search">{{
              $t("message.Search")
            }}</DropdownItem>
            <DropdownItem name="create">{{
              $t("message.Create")
            }}</DropdownItem>
            <DropdownItem name="delete">{{
              $t("message.Delete")
            }}</DropdownItem>
          </DropdownMenu>
        </Dropdown>
      </Col>
      <Col span="2">
        <Tag>
          {{ operation }}
        </Tag>
      </Col>
    </Row>
    <Row type="flex" justify="start">
      <Col :span="2" class="label">{{ $t("message.Title") }}</Col>
      <Col :span="4">
        <Input v-model="group.title"></Input>
      </Col>
    </Row>
    <Transfer
      :data="transData"
      :target-keys="targetKeys"
      :render-format="format"
      :list-style="listStyle"
      :operations="['To left','To right']"
      filterable
      :filter-method="filterMethod"
      @on-change="handleChange"
      class="tranfer"
    >
    </Transfer>
    <Button type="primary" @click="saveGroup" class="submit">{{
      $t("message.Submit")
    }}</Button>
  </div>
</template>

<script>
import { mapGetters } from 'vuex'
import only from 'only'

export default {
  i18n: {
    messages: {
      zh_CN: {
        message: {
          Group: '用户组',
          Title: '标题',
          Submit: '提交',
          Search: '查找',
          Create: '创建',
          Delete: '删除',
          Manage: '管理'
        }
      },
      en_US: {
        message: {
          Group: 'Group',
          Title: 'Title',
          Submit: 'Submit',
          Search: 'Search',
          Create: 'Create',
          Delete: 'Delete',
          Manage: 'Manage'
        }
      }
    }
  },
  data: () => ({
    ind: 0,
    targetKeys: [],
    listStyle: {
      width: '350px',
      height: '400px'
    },
    userList: [],
    operation: 'search'
  }),
  computed: {
    ...mapGetters({
      user: 'user/user',
      userSum: 'user/list',
      groupList: 'group/list',
      group: 'group/group'
    }),
    transData () {
      return this.userSum.map((item, index) => ({
        key: index + '',
        label: item.uid + ' | ' + item.nick
      }))
    }
  },
  created () {
    this.fetchGroup()
  },
  methods: {
    fetchGroup () {
      this.$Spin.showLoading()
      this.$store.dispatch('user/find')
        .then(() => {
          this.$store.dispatch('group/find')
        })
        .then(() => {
          this.userSum.forEach((item) => {
            this.userList.push(item.uid)
          })
          this.$Spin.hide()
        })
    },
    format (item) {
      return item.label
    },
    filterMethod (data, query) {
      return data.label.indexOf(query) > -1
    },
    handleChange (newTargetKeys) {
      this.targetKeys = newTargetKeys
    },
    manageGroup (name) {
      if (this.groupList.length > 0) {
        this.group.gid = this.groupList[this.ind].gid
        this.group.title = this.groupList[this.ind].title
      }
      this.operation = name
      if (name === 'search') {
        this.$Spin.showLoading()
        this.targetKeys = []
        this.$store.dispatch('group/findOne', { gid: this.group.gid }).then(() => {
          this.group.list.forEach((item) => {
            this.targetKeys.push(this.userList.indexOf(item) + '')
          })
          this.$Spin.hide()
        }).catch(() => {
          this.$Spin.hide()
        })
      } else if (name === 'create') {
        this.group.gid = ''
        this.group.title = ''
        this.group.list = []
        this.targetKeys = []
      } else if (name === 'delete') {
        if (!this.group || !this.group.gid) {
          this.$Message.info('未选择要删除的Group!')
        } else {
          this.$Modal.confirm({
            title: '提示',
            content: `<p>此操作将永久删除Group--${this.group.title}, 是否继续?</p>`,
            onOk: () => {
              this.$Spin.showLoading()
              this.$store.dispatch('group/delete', { gid: this.group.gid }).then(() => {
                this.$Spin.hide()
                this.$Message.success(`成功删除 ${this.group.title}！`)
              }).catch(() => {
                this.$Spin.hide()
              })
            },
            onCancel: () => {
              this.$Message.info('已取消删除！')
            }
          })
        }
      }
    },
    saveGroup () {
      const user = this.targetKeys.map((item) => this.userList[+item])
      const group = Object.assign(
        only(this.group, 'gid title'),
        { list: user }
      )
      if (this.group.gid !== '') {
        this.$Spin.showLoading()
        this.$store.dispatch('group/update', group).then(() => {
          this.$Spin.hide()
          this.$Message.success('更新当前用户组成功！')
        }).catch(() => {
          this.$Spin.hide()
        })
      } else {
        this.$Spin.showLoading()
        this.$store.dispatch('group/create', group).then(() => {
          this.$store.dispatch('group/find')
          this.$Spin.hide()
          this.$Message.success('新建当前用户组成功！')
        }).catch(() => {
          this.$Spin.hide()
        })
      }
    }
  }
}
</script>

<style lang="stylus" scoped>
.ivu-col-offset-1
  margin-left: 1%
.tranfer
  margin-top: 20px
  margin-bottom: 20px
.ivu-tag
  height: 28px
  line-height: 26px
</style>
