<!--
 * @Description: 
 * @Author: Benny
 * @Date: 2019-08-25 11:12:55
 * @LastEditTime: 2019-09-01 14:11:42
 -->
<template>
  <div>
    <!-- 列标题-->
    <div 
      class="widget-view"
      v-if="element.type == 'title'"
      :class="{active: selectWidget.key == element.key, 'is_req': element.options.required}"
      @click.stop="handleSelectWidget(index)"
    >
      <h4 class="title-style">{{element.name}}</h4>
      <div class="btn-group">
            <el-button
              title="删除"
              @click.stop="handleWidgetDelete(index)"
              class="widget-action-delete"
              v-if="selectWidget.key == element.key"
              circle
              plain
              type="danger"
            >
              <i class="iconfont icon-trash"></i>
            </el-button>
            <el-button
              title="复制"
              @click.stop="handleWidgetClone(index)"
              class="widget-action-clone"
              v-if="selectWidget.key == element.key"
              circle
              plain
              type="primary"
            >
              <i class="iconfont icon-icon_clone"></i>
            </el-button>
      </div>
  </div>
  <el-form-item
    class="widget-view"
    v-else
    :class="{active: selectWidget.key == element.key, 'is_req': element.options.required}"
     @click.native.stop="handleSelectWidget(index)"
  >
    <!-- 标签模版 -->
    <template v-if="element.type == 'label'">
      <div style="flex:1;">{{element.name}}</div>
    </template>

    <template v-if="element.type == 'input'">
      <div>{{element.name}}</div>
    </template>

    <template v-if="element.type == 'textarea'">
      <el-input
        type="textarea"
        :rows="5"
        v-model="element.options.defaultValue"
        :style="{width: element.options.width}"
        :disabled="element.options.disabled"
        :placeholder="element.options.placeholder"
      ></el-input>
    </template>

    <div class="btn-group">
      <el-button
        title="删除"
        @click.stop="handleWidgetDelete(index)"
        class="widget-action-delete"
        v-if="selectWidget.key == element.key"
        circle
        plain
        type="danger"
      >
        <i class="iconfont icon-trash"></i>
      </el-button>
      <el-button
        title="复制"
        @click.stop="handleWidgetClone(index)"
        class="widget-action-clone"
        v-if="selectWidget.key == element.key"
        circle
        plain
        type="primary"
      >
        <i class="iconfont icon-icon_clone"></i>
      </el-button>
    </div>
  </el-form-item>
  </div>
</template>

<script>
import FmUpload from "./Upload";
// import FmEditor from "./Editor/tinymce";
export default {
  props: ["element", "select", "index", "data"],
  components: {
    FmUpload,
    // FmEditor
  },
  data() {
    return {
      selectWidget: this.select
    };
  },
  mounted() {
    // console.log(this.element);
  },
  methods: {
    handleSelectWidget(index) {
      console.log(this.data.list)
      this.selectWidget = this.data.list[index];
    },
    handleWidgetDelete(index) {
      if (this.data.list.length - 1 === index) {
        if (index === 0) {
          this.selectWidget = {};
        } else {
          this.selectWidget = this.data.list[index - 1];
        }
      } else {
        this.selectWidget = this.data.list[index + 1];
      }

      this.$nextTick(() => {
        this.data.list.splice(index, 1);
      });
    },
    handleWidgetClone(index) {
      let cloneData = {
        ...this.data.list[index],
        options: { ...this.data.list[index].options },
        key: Date.parse(new Date()) + "_" + Math.ceil(Math.random() * 99999)
      };

      if (
        this.data.list[index].type === "radio" ||
        this.data.list[index].type === "checkbox"
      ) {
        cloneData = {
          ...cloneData,
          options: {
            ...cloneData.options,
            options: cloneData.options.options.map(item => ({ ...item }))
          }
        };
      }

      this.data.list.splice(index, 0, cloneData);

      this.$nextTick(() => {
        this.selectWidget = this.data.list[index + 1];
      });
    }
  },
  watch: {
    select(val) {
      this.selectWidget = val;
    },
    selectWidget: {
      handler(val) {
        this.$emit("update:select", val);
      },
      deep: true
    }
  }
};
</script>

