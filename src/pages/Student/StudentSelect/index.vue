<template>
  <div class="teacherdata">
    <el-form :inline="true" :model="formInline" class="formselect">
      <el-form-item label="姓名">
        <el-input v-model="formInline.name" placeholder="姓名"></el-input>
      </el-form-item>
      <el-form-item label="研究方向">
        <el-input v-model="formInline.field" placeholder="研究方向"></el-input>
      </el-form-item>
      <el-form-item label="职称">
        <el-select v-model="formInline.title" placeholder="职称">
          <el-option label="教授" value="教授"></el-option>
          <el-option label="副教授" value="副教授"></el-option>
          <el-option label="讲师" value="讲师"></el-option>
        </el-select>
      </el-form-item>
      <el-form-item label="院系名称">
        <el-select v-model="formInline.college" placeholder="院系名称">
          <el-option
            label="计算机工程与科学学院"
            value="计算机工程与科学学院"
          ></el-option>
          <el-option label="理学院" value="理学院"></el-option>
          <el-option label="钱伟长学院" value="钱伟长学院"></el-option>
          <el-option
            label="材料科学与工程学院"
            value="材料科学与工程学院"
          ></el-option>
          <el-option
            label="通信与信息工程学院"
            value="通信与信息工程学院"
          ></el-option>
          <el-option label="经济学院" value="经济学院"></el-option>
          <el-option label="管理学院" value="管理学院"></el-option>
          <el-option label="文学院" value="文学院"></el-option>
          <el-option label="法学院" value="法学院"></el-option>
        </el-select>
      </el-form-item>
      <el-form-item>
        <el-button type="primary" @click="conditionQuery">查询</el-button>
        <el-button type="primary" @click="prevPage" v-show="pageIndex != 1"
          >上一页</el-button
        >{{ pageIndex }}/{{ PageLength }}
        <el-button
          type="primary"
          @click="nextPage"
          v-show="pageIndex < PageLength"
          >下一页</el-button
        >
      </el-form-item>
    </el-form>

    <el-table :data="TeacherList" border style="width: 100%">
      <el-table-column prop="id" label="工号" width="65" align="center"> </el-table-column>
      <el-table-column prop="gender" label="性别" width="65" align="center"> </el-table-column>
      <el-table-column prop="name" label="姓名" width="70" align="center"> </el-table-column>
      <el-table-column prop="title" label="职称" width="65" align="center"> </el-table-column>
      <el-table-column prop="college" label="院系名称" width="165" align="center">
      </el-table-column>
      <el-table-column prop="telephone" label="手机号" width="110" align="center">
      </el-table-column>
      <el-table-column prop="email" label="邮箱" width="140" align="center"> </el-table-column>
      <el-table-column prop="website" label="个人网站" width="200" align="center"><template slot-scope="scope1">
          <el-link :href="scope1.row.website" target="_blank">{{ scope1.row.website }}</el-link>
        </template></el-table-column>
      <el-table-column prop="field" label="科研方向" width="275" align="center">
      </el-table-column>
      <el-table-column prop="apply_num" label="申请人数" width="80" align="center">
      </el-table-column>
      <el-table-column prop="accept_num" label="已有学生" width="80" align="center">
        <template slot-scope="scope">
          <span v-if="scope.row.accept_num===4 " style="color: #E6A23C">{{scope.row.accept_num}}</span>
          <span v-else-if="scope.row.accept_num===5 " style="color: #F56C6C">{{scope.row.accept_num}}</span>
          <span v-else style="color: blue">{{scope.row.accept_num}}</span>
        </template>
      </el-table-column>
      <el-table-column prop="max_num" label="上限人数" width="80" align="center">
      </el-table-column>
      <el-table-column fixed="right" label="操作" align="center">
        <template slot-scope="scope">
          <el-popconfirm
            title="确定要申请吗？"
            @confirm="submitApply(scope.row.id)"
          >
            <el-button slot="reference" type="success">申请</el-button>
          </el-popconfirm>
        </template>
      </el-table-column>
    </el-table>

    <el-pagination
      background
      layout="prev, pager, next"
      :total="PageLength * 10"
      @prev-click="prevPage"
      @next-click="nextPage"
      @current-change="handleCurrentChange"
    >
    </el-pagination>
  </div>
</template>




<script>
import { mapState, mapGetters } from "vuex";
export default {
  data() {
    return {
      pageIndex: 1,
      formInline: {
        name: "",
        field: "",
        title: "",
        college: "",
      },
    };
  },
  computed: {
    ...mapGetters(["TeacherList"]),
    ...mapGetters(["PageLength"]),
  },
  mounted() {
    //页面加载获取初始数据
    console.log("start");
    this.$store.dispatch("getTeacherList", {
      type: "search",
      keyword: { college: null },
      page: 1,
      size: 50,
    });
  },
  methods: {
    conditionQuery() {
      //按条件查询
      this.pageIndex = 1;
      this.$store.dispatch("getTeacherList", {
        type: "search",
        keyword: {
          name: this.formInline.name || null,
          college: this.formInline.college || null,
          title: this.formInline.title || null,
          field: this.formInline.field || null,
        },
        page: 1,
        size: 50,
      });
      console.log("query!");
    },
    prevPage() {
      //前一页
      this.pageIndex -= 1;
      this.$store.dispatch("getTeacherList", {
        type: "search",
        keyword: {
          name: this.formInline.name || null,
          college: this.formInline.college || null,
          title: this.formInline.title || null,
          field: this.formInline.field || null,
        },
        page: this.pageIndex,
        size: 50,
      });
    },
    nextPage() {
      //后一页
      this.pageIndex += 1;
      this.$store.dispatch("getTeacherList", {
        type: "search",
        keyword: {
          name: this.formInline.name || null,
          college: this.formInline.college || null,
          title: this.formInline.title || null,
          field: this.formInline.field || null,
        },
        page: this.pageIndex,
        size: 50,
      });
    },
    handleCurrentChange(val) {
      // console.log(`当前页: ${val}`);
      this.pageIndex = val;
      this.$store.dispatch("getTeacherList", {
        type: "search",
        keyword: {
          name: this.formInline.name || null,
          college: this.formInline.college || null,
          title: this.formInline.title || null,
          field: this.formInline.field || null,
        },
        page: this.pageIndex,
        size: 50,
      });
    },
    async submitApply(id) {
      //递交申请
      try {
        await this.$store.dispatch("submitTutorApply", {
          id: id,
          type: "select",
        });
        alert("successs");
        this.pageIndex = 1;
        this.$store.dispatch("getTeacherList", {
          type: "search",
          keyword: {
            name: this.formInline.name || null,
            college: this.formInline.college || null,
            title: this.formInline.title || null,
            field: this.formInline.field || null,
          },
          page: 1,
          size: 50,
        });
        location.reload();
      } catch (error) {
        alert(error.message);
      }
    },
  },
};
</script>

<style scoped>
.teacherdata {
  position: relative;
  top: 0;
  left: 250px;
  padding-top: 100px;
  min-height: 90%;
  width: 1500px;
}

</style>

