<template>
  <div class="main localmain">
    <div class="item tool">标题：
      <Input v-model="q.title" placeholder="标题" class="qinput"/>描述：
      <Input v-model="q.description" placeholder="描述" class="qinput"/>链接：
      <Input v-model="q.url" placeholder="链接" class="qinput"/>
      <Button type="primary" shape="circle" icon="ios-search" @click="query"></Button>
      <ButtonGroup style="display:block-inline; float:right;">
        <Button type="primary" @click="mode = 'add', drawshow = true">添加</Button>
        <Drawer
          :title="drawTitle"
          scrollable
          :closable="true"
          width="300"
          :styles="styles"
          v-model="drawshow"
        >
          <Form ref="maplayer" :model="maplayer" label-position="left" :label-width="60">
            <FormItem label="标题" prop="title">
              <Input type="text" size="small" v-model="maplayer.title" placeholder="title"/>
            </FormItem>
            <FormItem label="描述" prop="description">
              <Input
                type="text"
                size="small"
                v-model="maplayer.description"
                placeholder="description"
              />
            </FormItem>
            <FormItem label="链接" prop="url">
              <Input type="url" size="small" v-model="maplayer.url" placeholder="url"/>
            </FormItem>
            <FormItem>
              <Button v-show="mode == 'add'" type="primary" size="small" @click="handleSave">保存</Button>
              <Button v-show="mode == 'add'" size="small" type="primary" @click="handleReset">重置</Button>

              <Button v-show="mode == 'ru'" type="primary" size="small" @click="handleUpdate">保存</Button>
            </FormItem>
            <Divider>不经常改变的参数</Divider>
            <FormItem label="版本" prop="version">
              <Input size="small" v-model="maplayer.version"/>
            </FormItem>
            <FormItem label="服务分类" prop="category">
              <Input size="small" v-model="maplayer.category"/>
            </FormItem>
            <FormItem label="服务类型" prop="serviceType">
              <Input size="small" v-model="maplayer.serviceType"/>
            </FormItem>
            <FormItem label="空间参考代码" prop="wkid">
              <Input size="small" v-model="maplayer.wkid"/>
            </FormItem>
            <FormItem label="最小显示级别" prop="minZoom">
              <Input size="small" v-model="maplayer.minZoom"/>
            </FormItem>
            <FormItem label="最大显示级别" prop="maxZoom">
              <Input size="small" v-model="maplayer.maxZoom"/>
            </FormItem>
            <FormItem label="能力" prop="capabilities">
              <array-input v-model="maplayer.capabilities"/>
            </FormItem>范围：
            <FormItem label="最小X" prop="extent.xMin">
              <Input size="small" v-model="maplayer.extent.xMin"/>
            </FormItem>
            <FormItem label="最小Y" prop="extent.yMin">
              <Input size="small" v-model="maplayer.extent.yMin"/>
            </FormItem>
            <FormItem label="最大X" prop="extent.xMax">
              <Input size="small" v-model="maplayer.extent.xMax"/>
            </FormItem>
            <FormItem label="最大Y" prop="extent.yMax">
              <Input size="small" v-model="maplayer.extent.yMax"/>
            </FormItem>发布信息：
            <FormItem label="发布者" prop="publishInfo.author">
              <Input size="small" v-model="maplayer.publishInfo.author"/>
            </FormItem>
            <FormItem label="发布日期" prop="publishInfo.date">
              <Input size="small" v-model="maplayer.publishInfo.date"/>
            </FormItem>
            <FormItem label="发布平台" prop="publishInfo.server">
              <Input size="small" v-model="maplayer.publishInfo.server"/>
            </FormItem>
          </Form>
        </Drawer>
        <!-- <Tooltip content="设置常用且不经常变的图层资源参数">
          <Button type="primary">设置</Button>
        </Tooltip>-->
      </ButtonGroup>
    </div>
    <div ref="tableDiv" class="item content">
      <Table
        class="table"
        :height="tableSize.h-10"
        :width="tableSize.w"
        :columns="columns1"
        :data="tableList"
      >
        <template slot-scope="{ row }" slot="url">
          <a :href="row.url +'?f=jsapi'" target="_blank">{{row.url}}</a>
        </template>
        <template slot-scope="{ row, index }" slot="action">
          <Button
            type="primary"
            size="small"
            style="margin-right: 5px"
            @click="handleRU(index,row)"
          >查改</Button>
          <Button type="error" size="small" @click="handleDelete(index,row)">删除</Button>
        </template>
      </Table>
      <div>状态栏</div> 
    </div>
  </div>
</template>
<script>
import { createNamespacedHelpers } from "vuex";
import { isNull, log } from "util";

import ArrayInput from "../components/gismanage/ArrayInput.vue";
import Resizeable from './Resizeable.vue'
const {
  mapState,
  mapMutations,
  mapActions,
  mapGetters
} = createNamespacedHelpers("gis");
export default {
  name: "MaplayerManage",
  mixins:[Resizeable],
  components: {
    ArrayInput
  },
  data() {
    return {
      styles: {
        height: "calc(100% - 55px)",
        overflow: "auto",
        paddingBottom: "53px",
        position: "static"
      },
      mode: "add",
      drawshow: false,
      q: {
        title: null,
        description: null,
        url: null
      },
      defaultAttrs: {},
      columns1: [
        {
          title: "标题",
          key: "title",
          width: 200
        },
        {
          title: "描述",
          key: "description",
          width: 200
        },
        {
          title: "链接",
          slot: "url",
          width: 430
        },
        {
          title: "操作",
          slot: "action",
           width: 200,
          align: "center"
        }
      ],
      tableSize: { h: 600, w: 1100 },
      tableList: [],
      maplayerupdate: {},
      maplayeradd: {
        title: "YRGP_HHLY_L06L12_DX",
        version: "1.0.0",
        category: "地图服务",
        description: "黄河流域1:100万地形服务",
        url:
          "http://10.4.148.10/arcgis/rest/services/yrgpv2/YRGP_HHLY_L06L12_DX/MapServer",
        serviceType: "MapService",
        capabilities: ["WMS", "WTMS"],
        wkid: 4490,
        extent: { xMin: 95, yMin: 32, xMax: 120, yMax: 42, wkid: 4490 },
        minZoom: 6,
        maxZoom: 12,
        publishInfo: {
          author: "HHCH",
          date: "2018-12-20 09:00:00",
          server: "ArcGIS Server"
        }
      }
    };
  },
  computed: {
    ...mapState({
      a: state => state.m1.m1test,
      layerList: state => state.layerList
    }),
    ...mapGetters(["testgetter"]),
    maplayer() {
      return this.mode == "add" ? this.maplayeradd : this.maplayerupdate;
    },
    drawTitle() {
      if (this.mode == "add") {
        return "添加图层资源";
      } else {
        return "查改图层资源";
      }
    }
  },
  methods: {
    ...mapMutations(["testmutation"]),
    ...mapActions(["testaction"]),
    handleRU(index, row) {
      // alert(index);
      this.maplayerupdate = row;
      this.mode = "ru";
      this.drawshow = true;
    },
    handleDelete(index, row) {
      this.data6.splice(index, 1);
    },
    query() {
      const self = this;
      this.tableList = this.layerList.filter(item => {
        return (
          (!self.q.title || item.title.includes(self.q.title)) &&
          (!self.q.description ||
            item.description.includes(self.q.description)) &&
          (!self.q.url || item.url.includes(self.q.url))
        );
      });
    },
    handleSave() {},
    handleReset() {
      this.maplayeradd.title = "";
      this.maplayeradd.url = "";
      this.maplayeradd.description = "";
    },
    handleUpdate() {},
    resize(){
      console.log('maplayer resize')
      this.tableSize.h = this.$refs.tableDiv.clientHeight
      this.tableSize.w = this.$refs.tableDiv.clientWidth
    }
  },
  beforeMount() {
    this.tableList = this.layerList;
  },
  mounted() {

  }
};
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
