<template>
<!-- 等比缩放函数地址https://www.cnblogs.com/hdwang/p/8120590.html -->
<el-container style="height: 937px; border: 1px solid #eee">
  <el-container>
    <el-header style="height: 60px; font-size: 12px">
        <span>头部选择器</span>
        <!-- <el-select placeholder="请选择活动区域">
        </el-select> -->
    </el-header>
    <el-main>
      <el-row style="height: 100%">
        <draggable
          style="height: 100%"
          :options="Contentoptions"
          v-model="Content"
        >
        <el-col v-for="(item, index) in Content"
            style="height: 99%"
            :key="index"
            :span="item.type === 1 ? 12 : 6">
          <draggable
            v-model="Content.children"
            class="content"
            @start="start"
            @end="end"
            :move="move">
            <div v-for="(item, index) in item.children"
              class="content-item"
              :data-name="item.name"
              :data-index="index"
              :class="[{ highLight:ActivedType==item.type }, item.type]"
              :type="item.type"
              :key="index"
              @click="itemClick(item)">
              {{ item.name }}
            </div>
          </draggable>
        </el-col>
        <!-- <el-col :span="6" class="content">
          <div class="content-item typeA">类型a</div>
          <div class="content-item typeA">类型a</div>
          <div class="content-item typeB">类型b</div>
        </el-col>
        <el-col :span="12" class="content">
          <div class="content-item map">地图</div>
          <div class="content-item typeC">类型c</div>
        </el-col>
        <el-col :span="6" class="content">
          <div class="content-item typeA">类型a</div>
          <div class="content-item typeA">类型a</div>
          <div class="content-item typeB">类型b</div>
        </el-col> -->
      </draggable>
      </el-row>
    </el-main>
  </el-container>
  <el-aside width="200px" style="background-color: rgb(238, 241, 246)">
      <el-menu>
        <draggable
          style="height: 100%"
          :options="Contentoptions"
          v-model="Menuoptions"
          @change="change"
          @start="start"
          @end="end"
          :move="move"
        >
          <el-menu-item
            v-for="(item, index) in Menuoptions"
            :data-name="item.name"
            :data-index="index"
            :key="index">
            <i class="el-icon-menu"></i>
            <span>{{ item.name }}</span>
          </el-menu-item>
        </draggable>
    </el-menu>
  </el-aside>
</el-container>
</template>

<script>
export default {
  name: 'indexEditor',
  data () {
    return {
      Content: [
        {
          name: '左侧',
          type: 0,
          children: [
            {
              name: '类型a11',
              type: 'typeA'
            },
            {
              name: '类型a1',
              type: 'typeA'
            },
            {
              name: '类型b2',
              type: 'typeB'
            }
          ]
        },
        {
          name: '中',
          type: 1,
          children: [
            {
              name: '地图',
              type: 'map'
            },
            {
              name: '类型C',
              type: 'typeC'
            },
            {
              name: 'xx',
              type: 'empty'
            }
          ]
        },
        {
          name: '右',
          type: 2,
          children: [
            {
              name: '类型a12',
              type: 'typeA'
            },
            {
              name: '类型a15',
              type: 'typeA'
            },
            {
              name: '类型b',
              type: 'typeB'
            }
          ]
        }
      ],
      Contentoptions: {
        // group: 'content',
        animation: 150,
        ghostClass: 'sortable-ghost',
        chosenClass: 'chosenClass',
        scroll: true,
        scrollSensitivity: 200
      },
      Menuoptions: [],
      Menu: [
        {
          name: '组件类型A1',
          type: 'typeA'
        },
        {
          type: 'typeA',
          name: '组件类型A2'
        },
        {
          type: 'typeA',
          name: '组件类型A3'
        },
        {
          type: 'typeA',
          name: '组件类型A4'
        },
        {
          type: 'typeB',
          name: '组件类型B1'
        },
        {
          type: 'typeB',
          name: '组件类型B2'
        },
        {
          type: 'typeB',
          name: '组件类型B3'
        },
        {
          type: 'typeC',
          name: '组件类型C1'
        },
        {
          type: 'map',
          name: '地图组件'
        }
      ],
      Actived: {},
      ActivedType: '',
      Pos: {}
    }
  },
  watch: {
    Actived (val) {
    //   alert('sss')
    }
  },
  methods: {
    // 获取type相同的div元素位置
    getDivList (type) {
      let a = '.' + type
      let divlist = document.querySelectorAll(a)
      divlist.forEach((item) => {
        // 获取div坐标
        let rect = item.getBoundingClientRect()
        let px1 = rect.x
        let py1 = rect.y
        let px2 = rect.x + rect.width
        let py2 = rect.y + rect.height
        // 计算鼠标下落点是否进入
        if (this.Pos.x > px1 && this.Pos.x < px2 && this.Pos.y > py1 && this.Pos.y < py2) {
          let dataName = item.dataset.name
          let dataIndex = item.dataset.index
          this.Content.forEach((item, index) => {
            if (item.children[dataIndex].name === dataName) {
              this.Content[index].children[dataIndex].name = this.Actived.name
              this.Actived.name = dataName
            }
          })
        }
      })
    },
    itemClick (item) {
      // 在vuex添加选中状态，根据类型反馈控制高亮和列表
      let type = item.type
      this.Menuoptions = this.Menu.filter(item => {
        return item.type === type
      })
    },
    start: function (evt) {
      if (evt.target.className === 'content') {
        this.Content.forEach((item, index) => {
          if (item.children[evt.oldIndex].name === evt.item.dataset.name) {
            this.Actived = this.Content[index].children[evt.oldIndex]
          }
        })
      } else {
        // 拖动开始在vuex添加选中状态，同itemClick作用一样
        this.Actived = this.Menuoptions[evt.oldIndex]
      }
      this.ActivedType = this.Actived.type
    },
    change: function (evt) {
    },
    getMousePos (event) {
      let e = event || window.event
      // let scrollX = document.documentElement.scrollLeft || document.body.scrollLeft
      // let scrollY = document.documentElement.scrollTop || document.body.scrollTop
      this.Pos = {
        x: e.clientX || e.pageX,
        y: e.clientY || e.pageY
      }
    },
    end: function (evt) {
      console.log(evt)
      this.getMousePos()
      this.getDivList(this.Actived.type)
      this.ActivedType = ''
    },
    move: function (evt, originalEvent) {
    }
  },
  mounted () {
    this.Menuoptions = this.Menu
  }
}
</script>

<style rel="stylesheet/scss" lang="scss" scoped>
  .el-header {
    background-color: #B3C0D1;
    color: #333;
    line-height: 60px;
  }
  .el-main {
    padding: 0;
    .el-row {
      .content {
        height: 100%;
        display: flex;
        flex-direction: column;
        border: 1px solid red;
        .content-item {
          margin: 10px;
          flex-grow: 1;
          border: 1px solid blueviolet;
        }
        .map {
          flex-grow: 2;
        }
        .empty {
          display: none;
        }
        .highLight {
            background-color: red;
        }
      }
    }
  }
  .el-aside {
    color: #333;
  }
</style>