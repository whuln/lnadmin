<template>
  <div ref="maindiv" class="main localmain">
    <div class="left item">      
      <Collapse v-model="leftside">
        <Panel name="entity">
          实体类
          <div slot="content">
             <Button size="small" type="primary" ghost long>添加实体类</Button>
            <Table :height="mainsize - 50" :width="180" :columns="entitycolumn" :data="data1"></Table>
          </div>
        </Panel>
        <Panel name="attribute">
          实体属性表
          <div slot="content">
             <Button size="small" type="primary" ghost long>添加实体属性表</Button>
            <Table :height="mainsize - 50" :width="180" :columns="attributecolumn" :data="data1"></Table>
          </div>
        </Panel>
      </Collapse>
    </div>
    <div class="right item">
      <div class="toolbar">
        <Input size="small" placeholder="实体代码" style="width:200px; margin-left:10px;"/>
        <Icon class="icontool" size="25" type="ios-search"/>
        <Icon class="icontool" size="25" type="ios-cog-outline"/>
        <Icon class="icontool" size="25" type="md-add"/>
        <Icon class="icontool" size="25" @click="batch = true" type="ios-paper-outline"/>
        <Modal v-model="modal12" draggable scrollable title="批量录入">
          <div>This is the first modal</div>
        </Modal>
      </div>
      <div>
        <Table :height="dataTableSize.h" :width="dataTableSize.w" :columns="columns1" :data="data1"></Table>
        <div>状态栏</div>
      </div>
    </div>
  </div>
</template>
<script>
import Resizeable from "./Resizeable.vue";
export default {
  name: "GeoentityManage",
  mixins: [Resizeable],
  data() {
    return {
      batch: false, //批量录入对话框
      leftside: "entity",
      mainsize: {
        h: 600,
        w: 1100
      },
      entitycolumn: [
        {
          title: "实体类",
          key: "name"
        }
      ],
      attributecolumn: [
        {
          title: "实体属性表",
          key: "name"
        }
      ],
      columns1: [
        {
          title: "Name",
          key: "name"
        },
        {
          title: "Age",
          key: "age"
        },
        {
          title: "Address",
          key: "address"
        }
      ],
      data1: [
        {
          name: "John Brown",
          age: 18,
          address: "New York No. 1 Lake Park",
          date: "2016-10-03"
        },
        {
          name: "Jim Green",
          age: 24,
          address: "London No. 1 Lake Park",
          date: "2016-10-01"
        },
        {
          name: "Joe Black",
          age: 30,
          address: "Sydney No. 1 Lake Park",
          date: "2016-10-02"
        },
        {
          name: "Jon Snow",
          age: 26,
          address: "Ottawa No. 2 Lake Park",
          date: "2016-10-04"
        }
      ]
    };
  },
  methods: {
    resize() {
      this.mainsize.h = this.$refs.maindiv.clientHeight;
      this.mainsize.w = this.$refs.maindiv.clientWidth;
      //   console.log(this.mainsize);
    }
  },
  computed: {
    dataTableSize() {
      return {
        h: this.mainsize.h - 40,
        w: this.mainsize.w - 210
      };
    }
  },
  mounted() {
    this.resize();
  }
};
</script>

<style lang="less" scoped>
@import "./gismanage.less";

.localmain {
  display: flex;

  .item {
    // border: 1px solid green;

    .fullextent {
      width: 100%;
      height: 100%;
    }

    .borderblue {
      border: springgreen 1px solid;
    }

    .toolbar {
      border-bottom: 1px solid silver;
      height: 25px;
      .icontool {
        cursor: pointer;
        margin-left: 2px;
      }
    }
    .content {
    }
  }

  .left {
    flex: 0 0 200px;
    border-right: 1px solid silver;
    overflow: auto;
    // display: flex;
  }

  .right {
    flex: 1 1 auto;
  }
}
</style>
