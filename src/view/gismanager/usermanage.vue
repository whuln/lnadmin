<template>
  <div  class="main localmain">
    <div class="item tool">姓名：
      <Input v-model="q.username" placeholder="姓名" class="qinput"/>角色：
      <Input v-model="q.role" placeholder="角色" class="qinput"/>     
      <Button type="primary" shape="circle" icon="ios-search" @click="query"></Button>
      <ButtonGroup style="display:block-inline; float:right;">
        <Button  type="primary" @click="mode = 'add', drawshow = true">添加</Button>
        <Drawer
          :title="drawTitle"
          scrollable
          :closable="true"
          width="300"
          :styles="styles"
          v-model="drawshow"
        >
          <Form ref="user" :model="user"  label-position="left" :label-width="60">
            <FormItem label="姓名"  prop="username"  >
              <Input type="text" size="small" v-model="user.username" placeholder="姓名"/>
            </FormItem>
            <FormItem label="密码"  prop="psd"  >
              <Input type="text" size="small" v-model="user.psd" placeholder="密码"/>
            </FormItem>
            <FormItem label="角色" prop="role">
              <Input type="text" size="small" v-model="user.role" placeholder="角色"/>
            </FormItem>           
            <FormItem>
              <Button  v-show="mode == 'add'"  type="primary" size="small" @click="handleSave">保存</Button>
              <Button v-show="mode == 'add'" size="small" type="primary" @click="handleReset">重置</Button>

              <Button  v-show="mode == 'ru'"  type="primary" size="small" @click="handleUpdate">保存</Button>
            </FormItem>       
          </Form>
        </Drawer>
        <!-- <Tooltip content="设置常用且不经常变的图层资源参数">
          <Button type="primary">设置</Button>
        </Tooltip>-->
      </ButtonGroup>
    </div>
    <div ref="tableDiv" class="item content">
      <Table class="table" :height="tableSize.h-10" :width="tableSize.w" :columns="columns1" :data="tableList">
        <template slot-scope="{ row }" slot="url">
          <a :href="row.url +'?f=jsapi'" target="_blank">{{row.url}}</a>
        </template>
        <template slot-scope="{ row, index }" slot="action">
          <Button type="primary" size="small" style="margin-right: 5px" @click="handleRU(index,row)">查改</Button>
          <Button type="error" size="small" @click="handleDelete(index,row)">删除</Button>
        </template>
      </Table>
      <div>状态栏</div>
    </div>
  </div>
</template>
<script>
import { createNamespacedHelpers } from 'vuex'
import { isNull, log } from 'util'

import ArrayInput from '../components/gismanage/ArrayInput.vue'
import Resizeable from './Resizeable.vue'
const {
  mapState,
  mapMutations,
  mapActions,
  mapGetters
} = createNamespacedHelpers('gis')
export default {
  name: 'MapuserManage',
  mixins:[Resizeable],
  components: {
    ArrayInput
  },
  data () {
    return {
      styles: {
        height: 'calc(100% - 55px)',
        overflow: 'auto',
        paddingBottom: '53px',
        position: 'static'
      },
      mode: 'add',
      drawshow: false,
      q: {
        username:null,
        role:null
      },
      defaultAttrs: {},
      columns1: [
        {
          title: '姓名',
          key: 'username',
          width: 200,         
        },
        {
          title: '角色',
          key: 'role',
          width: 200
        },
        {
          title: '操作',
          slot: 'action',
          width: 200,
          align: 'center',
          
        }
      ],
      tableSize:{h:600,w:1100},
      tableList: [],
      userupdate: {},
      useradd: {
        username:'',
        psd:'',
        role:''
      }
    }
  },
  computed: {
    ...mapState({
      a: state => state.m1.m1test,
      userList: state => state.userList,
      roleList: state => state.roleList
    }),
    ...mapGetters(['testgetter']),
    user () {
      return this.mode == 'add' ? this.useradd : this.userupdate
    },
    drawTitle () {
      if (this.mode == 'add') {
        return '添加用户'
      } else {
        return '查改用户'
      }
    },
   
  },
  methods: {
    ...mapMutations(['testmutation']),
    ...mapActions(['testaction']),
    handleRU (index,row) {
      // alert(index);
      this.userupdate = row
      this.mode = 'ru'
      this.drawshow = true
    },
    handleDelete (index,row) {
      this.data6.splice(index, 1)
    },
    query () {
      const self = this;
      this.tableList = this.userList.filter(item =>{
        return (!self.q.username || item.username.includes(self.q.username))&&  (!self.q.role || item.role.includes(self.q.role) ) 
      })
    },
    handleSave () {

    },
    handleReset () {
      this.useradd.username = ''
      this.useradd.psd = ''
      this.useradd.role = ''
    },
    handleUpdate(){},
    resize(){
        // console.log('user resize')
        // console.log(this.$refs.tableDiv.clientHeight)
        // console.log(this.$refs.tableDiv.clientWidth)
        this.tableSize.h = this.$refs.tableDiv.clientHeight
        this.tableSize.w = this.$refs.tableDiv.clientWidth
    }
  },
  beforeMount () {
    this.tableList = this.userList


   
  },
  mounted(){
          
  }
}
</script>
<style lang="less" scoped>
@import "./gismanage.less";
.localmain {
  display: flex;
  flex-direction: column;

  .item {
    // border: 1px solid greenyellow;
  }

  .tool {
    flex: 0 0 3em;
  }

  .content {
    margin-top: 1px;
    flex: 1 1 auto;
    display: flex;
    flex-direction: column;

    .table {
      flex: 1 1 auto;
    }
  }

  .qinput {
    width: 13em;
  }
}
</style>
