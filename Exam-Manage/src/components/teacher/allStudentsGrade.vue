// 所有学生
<template>
  <div class="all">
    <el-row :gutter="20">
      <el-col :span="8">
        <el-input placeholder="请输入学生名字" class="input" v-model="name">
          <el-button
            slot="append"
            icon="el-icon-search"
            @click="search"
          ></el-button>
        </el-input>
      </el-col>
    </el-row>
    <el-table :data="pagination.records" border ref="TableId" name="myname">
      <el-table-column fixed="left" prop="studentName" label="姓名" width="180">
      </el-table-column>
      <el-table-column
        prop="institute"
        label="学院"
        width="200"
      ></el-table-column>
      <el-table-column prop="major" label="专业" width="200"></el-table-column>
      <el-table-column prop="grade" label="年级" width="200"></el-table-column>
      <el-table-column prop="clazz" label="班级" width="100"></el-table-column>
      <el-table-column prop="sex" label="性别" width="120"></el-table-column>
      <el-table-column
        prop="tel"
        label="联系方式"
        width="120"
      ></el-table-column>
      <el-table-column fixed="right" label="查看成绩" width="150">
        <template slot-scope="scope">
          <el-button
            @click="checkGrade(scope.row.studentId)"
            type="primary"
            size="small"
            >查看成绩</el-button
          >
        </template>
      </el-table-column>
    </el-table>
    <el-pagination
      @size-change="handleSizeChange"
      @current-change="handleCurrentChange"
      :current-page="pagination.current"
      :page-sizes="[6, 10]"
      :page-size="pagination.size"
      layout="total, sizes, prev, pager, next, jumper"
      :total="pagination.total"
      class="page"
    ></el-pagination>
  </div>
</template>

<script>
export default {
  data() {
    return {
      pagination: {
        //分页后的考试信息
        current: 1, //当前页
        total: null, //记录条数
        size: 6 //每页条数
      },
      name: "",
      allstu: {}
    };
  },
  created() {
    this.getAnswerInfo();
  },

  methods: {
    // 筛选变色

    getAnswerInfo() {
      //分页查询所有试卷信息
      this.$axios(
        `/api/students/${this.pagination.current}/${this.pagination.size}`
      )
        .then(res => {
          this.pagination = res.data.data;
        })
        .catch(error => {});
    },
    search() {
      var examname = this.name;
      const records = [];
      this.$axios(
        `/api/students/${this.pagination.current}/${this.pagination.size}`
      ).then(res => {
        if (res.data.code == 200) {
          this.allstu = res.data.data.records;
          console.log(res);
          this.allstu.map(function(item) {
            if (item.studentName.search(examname) != -1) {
              records.push(item);
            }
          });
        }
      });
      console.log(records);
      this.pagination.records = records;
    },
    //改变当前记录条数
    handleSizeChange(val) {
      this.pagination.size = val;
      this.getAnswerInfo();
    },
    //改变当前页码，重新发送请求
    handleCurrentChange(val) {
      this.pagination.current = val;
      this.getAnswerInfo();
    },
    checkGrade(studentId) {
      this.$router.push({ path: "/grade", query: { studentId: studentId } });
    }
  }
};
</script>
<style lang="scss" scoped>
.all {
  padding: 0px 40px;
  .page {
    margin-top: 20px;
    display: flex;
    justify-content: center;
    align-items: center;
  }
  .edit {
    margin-left: 20px;
  }
  .el-table tr {
    background-color: #dd5862 !important;
  }
}
.el-table .warning-row {
  background: #000 !important;
}

.el-table .success-row {
  background: #dd5862;
}
.input {
  margin-bottom: 10px;
}
</style>
