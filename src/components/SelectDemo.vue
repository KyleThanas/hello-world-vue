<template>
  <div>
    <el-form ref="searchform" label-width="150px">
      <div v-for="(item, i) in formData.list" :key="i">
        <el-form-item label="select多选demo:">
          <el-select
            v-model="item.selectValue"
            @change="changeSelect($event, i)"
            multiple
            clearable
            placeholder="请选择"
          >
            <el-option
              key="selectAll"
              label="全部"
              value="selectAll"
            ></el-option>
            <el-option
              v-for="item in options"
              :key="item.value"
              :label="item.label"
              :value="item.value"
            >
            </el-option>
          </el-select>
        </el-form-item>
      </div>
    </el-form>
  </div>
</template>

<script>
export default {
  data() {
    return {
      formData: {
        list: [
          { selectValue: [], selectAll: false },
          { selectValue: [], selectAll: false },
          { selectValue: [], selectAll: false },
          { selectValue: [], selectAll: false }
        ]
      },
      options: [
        // {
        //   value: 'selectAll',
        //   label: '全部'
        // },
        {
          value: '选项1',
          label: '黄金糕'
        },
        {
          value: '选项2',
          label: '双皮奶'
        },
        {
          value: '选项3',
          label: '蚵仔煎'
        },
        {
          value: '选项4',
          label: '龙须面'
        },
        {
          value: '选项5',
          label: '北京烤鸭'
        }
      ]
      // 用于标识是否全选--默认不全选
    }
  },
  created() {},
  methods: {
    changeSelect(value, i) {
      // selectAll 为true 的时候，就走全选分支，全选后出现的情况就是取消权限
      if (this.formData.list[i].selectAll) {
        this.formData.list[i].selectAll = false
        if (value.indexOf('selectAll') > -1) {
          this.formData.list[i].selectValue = value.filter(
            (p) => p != 'selectAll'
          )
        } else {
          this.formData.list[i].selectValue = []
        }
      } else {
        // 是否点击了‘全选’选项
        if (value.indexOf('selectAll') > -1) {
          // 有‘全选’选项，则将‘全部’和其他值放置一块
          const optionsValue = []
          this.options.forEach((item) => {
            optionsValue.push(item.value)
          })
          this.formData.list[i].selectValue = ['selectAll', ...optionsValue]
          this.formData.list[i].selectAll = true
        } else {
          // 若是勾选选择的长度和提供的选项长度是一样的，则是 ‘全选’
          if (value.length === this.options.length) {
            const optionsValue = []
            this.options.forEach((item) => {
              optionsValue.push(item.value)
            })
            this.formData.list[i].selectValue = ['selectAll', ...optionsValue]
            this.formData.list[i].selectAll = true
          } else {
            // 都是单选
            this.formData.list[i].selectValue = value
          }
        }
      }
      // 真实的勾选值
      const realSelect = this.formData.list[i].selectValue.filter(
        (item) => item != 'selectAll'
      )
      console.log('realSelect', realSelect)
    }
  }
}
</script>
