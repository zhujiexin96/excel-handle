<template>
  <div id="app">
    <div class="inner">
      <el-button class="upload" type="primary">
        <vue-xlsx-table @on-select-file="handleSelectedFile">上传 <i
            class="el-icon-upload el-icon--right"></i></vue-xlsx-table>
      </el-button>
      <el-button type="danger" @click="exportExcel">导出 <i
              class="el-icon-download el-icon--right"></i></el-button>
      <div class="table">
        <el-table :data="rawData" style="width: 100%">
          <el-table-column prop="key_0" label="col1" width="180">
          </el-table-column>
          <el-table-column prop="key_1" label="col2" width="180">
          </el-table-column>
          <el-table-column prop="key_2" label="col3">
          </el-table-column>
        </el-table>
      </div>
    </div>
  </div>
</template>

<script>

export default {
  name: 'App',
  components: {
  },
  data() {
    return {
      rawData: [],
      exportArr: [],
    }
  },
  methods: {
    initExcel(excel) {
      excel.sort((a, b) => {
        if (a.key_0 === b.key_0) {
          return a.key_1.localeCompare(b.key_1);
        } else {
          return a.key_0.localeCompare(b.key_0);
        }
      });
    },
    handleSelectedFile(excelData) {
      this.rawData = excelData.body
      // this.initExcel(this.rawData)
      console.log(this.rawData);
    },
    exportExcel() {
      let tempArr1 = [];
      let tempArr2 = [];
      this.rawData.forEach((item) => {
        const { key_0, key_1, key_2 } = item;
        let index = tempArr1.indexOf(key_0)
        if (index == -1) {
          this.exportArr.push({ key_0: key_0, key_1: ['O1.' + key_1], key_2: ['I1.' + key_2] });
          tempArr1.push(key_0);
          tempArr2.push(key_1)
        } else {
          let forward1 = this.exportArr[index].key_1.length + 1
          let index1 = tempArr2.indexOf(key_1)
          if (index1 == -1 && key_0 == tempArr1[index]) {
            this.exportArr[index].key_1.push('\n\nO' + forward1 + '.' + key_1)
            tempArr2.push(key_1)
          }
          let forward2 = this.exportArr[index].key_2.length + 1
          this.exportArr[index].key_2.push('\n\nI' + forward2 + '.' + key_2)
        }
      });
      import("@/vendor/Export2Excel").then((excel) => {
        const tHeader = [
          "姓名",
          "O",
          "I"
        ];
        const filterVal = [
          "key_0",
          "key_1",
          "key_2"
        ];
        const data = this.formatJson(filterVal);
        console.log(data);
        excel.export_json_to_excel({
          header: tHeader,
          data,
          filename: "列表",
        });
      });
    },
    formatJson(filterVal) {
      return this.exportArr.map((v) =>
        filterVal.map((j) => {
          return v[j];
        })
      );
    },
  },
}
</script>

<style>
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
}


.inner {
  width: 1000px;
  margin: 0 auto;
}

#app .el-button.upload {
  padding: 0;
}

.el-button .vue-xlsx-container * {
  width: auto;
}

.vue-xlsx-container .xlsx-button {
  background-color: transparent;
  border: 0;
  padding: 0;
  font-size: 14px;
  padding: 12px 20px;
}

.table {
  width: 1000px;
  margin: 0 auto;
}
</style>
