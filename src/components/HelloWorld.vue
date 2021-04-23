<template>
  <div class="hello">
    <h2>{{ msg }}</h2>
    <div class="add-children-action-area">
      <el-button type="text" @click="dialog = true">输入配置</el-button>
      <el-input
        v-if="!ifSquare"
        v-model.number="inputWidth"
        placeholder="宽"
        clearable
        min="1"
      ></el-input>
      <el-input
        v-if="!ifSquare"
        v-model.number="inputHeight"
        placeholder="高"
        clearable
        min="1"
      ></el-input>
      <el-input
        v-if="ifSquare"
        v-model.number="inputRadius"
        placeholder="边长"
        clearable
        min="1"
      ></el-input>
      <el-input
        v-model="inputText"
        placeholder="Text"
        type="text"
        clearable
      ></el-input>

      <el-color-picker v-model="inputColor" show-alpha></el-color-picker>
      <el-badge :value="childrenDiv.length" :max="99" class="item">
        <el-button type="success" plain @click="addChildren">添加</el-button>
      </el-badge>
      <el-button
        type="danger"
        plain
        @click="delChildren(childrenDiv.length - 1)"
        :disabled="childrenDiv.length == 0"
        >删除</el-button
      >
    </div>

    <template v-for="item in setParams">
      <el-popover
        placement="top-start"
        width="400"
        trigger="hover"
        :key="item.popoverKey"
      >
        <h4>{{ item.popover.title }}</h4>
        <el-table :data="item.popover.tableData">
          <el-table-column
            width="150"
            property="name"
            label="属性"
          ></el-table-column>
          <el-table-column
            width="250"
            property="text"
            label="说明"
          ></el-table-column>
        </el-table>
        <el-tag slot="reference" class="cursor">{{ item.name }}</el-tag>
      </el-popover>

      <el-select
        v-model="item.value"
        filterable
        style="width: 150px"
        size="small"
        :key="item.selectKey"
      >
        <el-option
          v-for="item2 in item.options"
          :key="item2.label"
          :label="item2.label"
          :value="item2.label"
        >
        </el-option>
      </el-select>
    </template>

    <div style="margin-top: 20px">父布局宽度(%)</div>
    <el-slider v-model="parentWidth"></el-slider>
    <div style="margin-top: 20px">border-radius(%)</div>
    <el-slider v-model="borderRadius" max="50"></el-slider>
    <div
      class="parent"
      :style="{
        width: parentWidth + '%',
        'flex-direction': setParams[0].value,
        'flex-wrap': setParams[1].value,
        'justify-content': setParams[2].value,
        'align-items': setParams[3].value,
        'align-content': setParams[4].value,
      }"
    >
      <el-popover
        placement="top"
        width="230"
        trigger="click"
        v-for="(item, index) in childrenDiv"
        :key="index"
      >
        <div>
          宽: <el-input-number v-model="item.width"></el-input-number> px
        </div>
        <br />
        <div>
          高: <el-input-number v-model="item.height"></el-input-number> px
        </div>
        <div
          :style="{
            width: item.width + 'px',
            height: item.height + 'px',
            'background-color': item.color,
            'line-height': item.height / 2 + 'px',
            'border-radius':borderRadius+'%'
          }"
          class="childrenBlock fade-enter-active"
          @dblclick="delChildren(index)"
          slot="reference"
        >
          {{ item.text }}
        </div>
      </el-popover>
    </div>

    <!-- 抽屉弹窗 -->
    <el-drawer
      title="参数配置"
      :visible.sync="dialog"
      direction="ltr"
      custom-class="demo-drawer"
      ref="drawer"
    >
      <div class="demo-drawer__content">
        <el-form>
          <el-form-item>
            <el-button
              type="text"
              @click="
                () => {
                  ifSquare = !ifSquare;
                }
              "
              >正方形</el-button
            >
            <el-switch v-model="ifSquare" active-color="#13ce66"> </el-switch>
          </el-form-item>
          <el-form-item>
            <el-button
              type="text"
              @click="
                () => {
                  clearInput = !clearInput;
                }
              "
              >输入后清除</el-button
            >
            <el-switch v-model="clearInput" active-color="#13ce66"> </el-switch>
          </el-form-item>
        </el-form>
      </div>
    </el-drawer>
  </div>
</template>

<script>
export default {
  name: "HelloWorld",
  props: {
    msg: String,
  },
  data() {
    return {
      ifSquare: false,
      clearInput: false,
      parentWidth: 50,
      borderRadius:'0',
      setParams: [
        {
          popover: {
            title: "flex-direction：决定主轴的方向，即项目的排列方向",
            tableData: [
              {
                name: "row（默认）",
                text: "主轴为水平方向，起点在左端",
              },
              {
                name: "row-reverse",
                text: "主轴为水平方向，起点在右端",
              },
              {
                name: "column",
                text: "主轴为垂直方向，起点在上沿",
              },
              {
                name: "column-reverse",
                text: "主轴为垂直方向，起点在下沿",
              },
            ],
          },
          options: [
            { label: "row" },
            { label: "row-reverse" },
            { label: "column" },
            { label: "column-reverse" },
          ],
          value: "row",
          name: "flex-direction",
          popoverKey: "popover-1",
          selectKey: "select-1",
        },
        {
          popover: {
            title: "flex-wrap：如果一条轴线排不下，如何换行",
            tableData: [
              {
                name: "nowrap（默认）",
                text: "不换行",
              },
              {
                name: "wrap",
                text: "换行，第一行在上方",
              },
              {
                name: "wrap-reverse",
                text: "换行，第一行在下方",
              },
            ],
          },
          options: [
            { label: "nowrap" },
            { label: "wrap" },
            { label: "wrap-reverse" },
          ],
          value: "nowrap",
          name: "flex-wrap",
          popoverKey: "popover-2",
          selectKey: "select-2",
        },
        {
          popover: {
            title: "justify-content：定义了项目在主轴上的对齐方式",
            tableData: [
              {
                name: "flex-start（默认）",
                text: "左对齐",
              },
              {
                name: "flex-end",
                text: "右对齐",
              },
              {
                name: "center",
                text: "居中",
              },
              {
                name: "space-between",
                text: "两端对齐，项目之间的间隔都相等",
              },
              {
                name: "space-around",
                text: "每个项目两侧的间隔相等",
              },
            ],
          },
          options: [
            { label: "flex-start" },
            { label: "flex-end" },
            { label: "center" },
            { label: "space-between" },
            { label: "space-around" },
          ],
          value: "flex-start",
          name: "justify-content",
          popoverKey: "popover-3",
          selectKey: "select-3",
        },
        {
          popover: {
            title: "align-items：定义项目在交叉轴上如何对齐",
            tableData: [
              {
                name: "flex-start",
                text: "交叉轴的起点对齐",
              },
              {
                name: "flex-end",
                text: "交叉轴的终点对齐",
              },
              {
                name: "center",
                text: "交叉轴的中点对齐",
              },
              {
                name: "baseline",
                text: "项目的第一行文字的基线对齐",
              },
              {
                name: "stretch（默认）",
                text: "如果项目未设置高度或设为auto，将占满整个容器的高度",
              },
            ],
          },
          options: [
            { label: "flex-start" },
            { label: "flex-end" },
            { label: "center" },
            { label: "baseline" },
            { label: "stretch" },
          ],
          value: "stretch",
          name: "align-items",
          popoverKey: "popover-4",
          selectKey: "select-4",
        },
        {
          popover: {
            title:
              "align-content：定义了多根轴线的对齐方式。如果项目只有一根轴线，该属性不起作用",
            tableData: [
              {
                name: "flex-start",
                text: "与交叉轴的起点对齐",
              },
              {
                name: "flex-end",
                text: "与交叉轴的终点对齐",
              },
              {
                name: "center",
                text: "与交叉轴的中点对齐",
              },
              {
                name: "space-between",
                text: "与交叉轴两端对齐，轴线之间的间隔平均分布",
              },
              {
                name: "space-around",
                text: "每根轴线两侧的间隔都相等",
              },
              {
                name: "stretch（默认）",
                text: "轴线占满整个交叉轴",
              },
            ],
          },
          options: [
            { label: "flex-start" },
            { label: "flex-end" },
            { label: "center" },
            { label: "space-between" },
            { label: "space-around" },
            { label: "stretch" },
          ],
          value: "stretch",
          name: "align-content",
          popoverKey: "popover-5",
          selectKey: "select-5",
        },
      ],
      dialog: false,
      formLabelWidth: "150px",
      childrenDiv: [],
      inputColor: null,
      inputWidth: "",
      inputHeight: "",
      inputRadius: "",
      inputText: "",
      count: 0,
    };
  },
  methods: {
    //添加子元素
    addChildren() {
      const newChildren = {
        color: this.inputColor ? this.inputColor : this.getRandomColor(),
        text: this.inputText || this.count,
      };
      if (this.ifSquare) {
        //如果选择正方形
        (newChildren.width = this.inputRadius
          ? this.inputRadius
          : this.getRandomNum()),
          (newChildren.height = newChildren.width);
      } else {
        newChildren.width = this.inputWidth
          ? this.inputWidth
          : this.getRandomNum();
        newChildren.height = this.inputHeight
          ? this.inputHeight
          : this.getRandomNum();
      }
      this.childrenDiv.push(newChildren);
      this.count++;

      //清除输入
      if (this.clearInput) {
        this.inputWidth = "";
        this.inputHeight = "";
        this.inputColor = null;
        this.inputText = "";
      }

      //弹出message框
      this.$message({
        dangerouslyUseHTMLString: true,
        showClose: true,
        type: "success",
        duration: 1200,
        message: "成功添加子元素",
      });
    },

    //删除子元素
    delChildren(index = this.childrenDiv.length - 1) {
      this.childrenDiv.splice(index, 1);
      this.$message({
        showClose: true,
        type: "error",
        duration: 1200,
        message: "成功删除子元素",
      });
    },

    //获取指定范围随机数
    getRandomNum(min = 20, max = 400) {
      return parseInt(Math.random() * (max - min + 1) + min, 10);
    },

    //获取指定颜色
    getRandomColor() {
      var r = Math.floor(Math.random() * 256);
      var g = Math.floor(Math.random() * 256);
      var b = Math.floor(Math.random() * 256);
      var a = Math.floor(Math.random() * 256);
      if (a - 50 < 0) {
        a += 50;
      }
      var color =
        "#" +
        this.formatNum(r.toString(16)) +
        this.formatNum(g.toString(16)) +
        this.formatNum(b.toString(16)) +
        this.formatNum(a.toString(16));
      return color;
    },

    //0~9数字前补0
    formatNum(value) {
      return value.length == 1 ? "0" + value : value;
    },
  },
};
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
.parent {
  margin-top: 12px;
  display: flex;
  height: auto;
  background-color: rgba(194, 191, 191, 0.753);
}
.add-children-action-area {
  display: flex;
  align-items: center;
  padding: 10px 200px 20px 200px;
}
.childrenBlock {
  transition: all 0.5s ease-in-out;
  cursor: pointer;
}
.cursor {
  cursor: default;
}
div[class*="childrenBlock"]:hover {
  box-shadow: 4px 4px 8px #aaaaaa88;
  -webkit-transform: scale(1.1);
  transform: scale(1.1);
}

@keyframes fade-in {
  0% {
    transform: scale(0);
  }
  50% {
    transform: scale(1.1);
  }
  100% {
    transform: scale(1);
  }
}
@-webkit-keyframes fade-in {
  /*针对webkit内核*/
  0% {
    transform: scale(0);
  }
  50% {
    transform: scale(1.1);
  }
  100% {
    transform: scale(1);
  }
}
.fade-enter-active {
  animation: fade-in; /*动画名称*/
  -webkit-animation: fade-in 0.5s; /*针对webkit内核*/
  animation-duration: 0.5s; /*动画持续时间*/
}

.item {
  margin-right: 10px;
}

*[class~="el-input"] {
  margin-left: 5px;
  margin-right: 5px;
}
*[class*="el-color"] {
  margin-left: 5px;
  margin-right: 5px;
}
*[class*="el-switch"] {
  margin-left: 5px;
  margin-right: 5px;
}
</style>
