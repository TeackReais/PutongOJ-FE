<template lang="html">
  <div class="con-wrap">
    <table>
      <tr>
        <th>CID</th>
        <th>Title</th>
        <th>Status</th>
        <th>Start Time</th>
        <th>Type</th>
        <th>Delete</th>
      </tr>
      <tr v-for="(item, index) in list" :key="item.pid">
        <td>{{ item.cid }}</td>
        <td>
          <router-link :to="{ name: 'contestOverview', params: { cid: item.cid } }">
            <Button type="text">{{ item.title }}</Button>
          </router-link>
        <td>
          <span>{{ status[item.status] }}</span>
        </td>
        <td>
          <span>{{ item.create | timePretty }}</span>
        </td>
        <td>
          <span>{{ type[item.encrypt] }}</span>
        </td>
        <td>
          <Button type="text" @click="del(item.cid)">Delete</Button>
        </td>
      </tr>
    </table>
    <Page :total="sum"
      @on-change="pageChange"
      :page-size="pageSize"
      :current.sync="page"
      show-elevator>
    </Page>
  </div>
</template>

<script>
import { mapGetters } from 'vuex'
import constant from '../util/constant'
import only from 'only'
import pickBy from 'lodash.pickby'

export default {
  data () {
    return {
      currentPage: parseInt(this.$route.query.page) || 1,
      pageSize: parseInt(this.$route.query.pageSize) || 20,
      status: constant.contestStatus,
      type: constant.contestType
    }
  },
  computed: {
    ...mapGetters('contest', [
      'list',
      'sum'
    ]),
    query () {
      const opt = only(this.$route.query, 'page pageSize type content')
      return pickBy(
        opt,
        x => x != null && x !== ''
      )
    }
  },
  created () {
    this.fetch()
  },
  methods: {
    fetch () {
      this.$store.dispatch('contest/find', this.query)
      const query = this.$route.query
      this.page = parseInt(query.page) || 1
      this.pageSize = parseInt(query.pageSize) || 20
    },
    reload (payload = {}) {
      const query = Object.assign(this.query, payload)
      this.$router.push({
        name: 'contestList',
        query
      })
    },
    sizeChange (val) {
      this.reload({ pageSize: val })
    },
    pageChange (val) {
      this.reload({ page: val })
    },
    del (cid) {
      this.$Modal.confirm({
        title: '提示',
        content: '<p>此操作将永久删除该文件, 是否继续?</p>',
        onOk: () => {
          this.$store.dispatch('contest/delete', { cid }).then(() => {
            this.$Message.success(`成功删除 ${cid}！`)
          })
        },
        onCancel: () => {
          this.$Message.info('已取消删除！')
        }
      })
    }
  },
  watch: {
    '$route' (to, from) {
      if (to !== from) this.fetch()
    }
  }
}
</script>

<style lang="stylus">
.con-wrap
  margin-bottom: 20px
  table
    width: 100%
    margin-bottom: 20px
    border-collapse: collapse
    border-spacing: 0
    th:nth-child(1)
      padding-left: 10px
    //   width: 5%
    // th:nth-child(2)
    //   width: 10%
    // th:nth-child(3)
    //   width: 20%
    // th:nth-child(4)
    //   width: 20%
    // th:nth-child(5)
    //   width: 10%
    // th:nth-child(6)
    //   width: 10%
    tr
      border-bottom: 1px solid #ebeef5
      height: 40px
      line-height: 40px
      font-size: 14px
      td:nth-child(1)
        padding-left: 10px
    th
      text-align:left
    .ivu-btn
      vertical-align: baseline
      color: #e040fb
      padding: 0 1px
      font-size: 14px
</style>