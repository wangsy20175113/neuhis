<template lang="html">
<div class="container"  style="height: 100vh; margin: 20px 20px 0 20px;">
  <!-- 科室管理功能区，做成卡片的样子 -->
  <el-card class="box-card" shadow="hover">
    <!-- 标题 -->
    <div slot="header" class="clearfix">
      <i class="el-icon-edit"></i>
      <span style="padding-left: 20px;">新增排班规则</span>
    </div>

    <el-form :inline="true">
      <el-form-item label="科室选择" required>
        <el-select placeholder="请选择科室" v-model="departmentId">
          <el-option v-for="department in departments" v-bind:key="department.name"  :label="department.name" :value="department.id"></el-option>
        </el-select>
      </el-form-item>
      <el-button type="text" :loading="loading1" icon="el-icon-document-add" :disabled="departmentId==''" @click="search">查询</el-button>

      <el-button style="float:right" type="text" :disabled="ruleName==''||multipleSelection==''" icon="el-icon-folder-checked" @click="save" :loading="loading2">保存</el-button>
      <el-form-item style="float:right" label="规则名称">
        <el-input placeholder="请输入规则名称" v-model="ruleName"></el-input>
      </el-form-item>
    </el-form>

    <el-table v-loading="loading3" :data="roles" border style="width: 100%" @selection-change="handleSelectionChange">
      <el-table-column type="selection" width="55">
      </el-table-column>
      <el-table-column prop="roleName" label="医生姓名" width="90">
      </el-table-column>
      <el-table-column prop="monAm" label="星期一上午" width="81">
        <template slot-scope="scope">
          <el-checkbox v-model="scope.row.monAm"></el-checkbox>
        </template>
      </el-table-column>
      <el-table-column prop="monPm" label="星期一下午" width="81">
        <template slot-scope="scope">
          <el-checkbox v-model="scope.row.monPm"></el-checkbox>
        </template>
      </el-table-column>
      <el-table-column prop="tueAm" label="星期二上午" width="81">
        <template slot-scope="scope">
          <el-checkbox v-model="scope.row.tueAm"></el-checkbox>
        </template>
      </el-table-column>
      <el-table-column prop="tuePm" label="星期二下午" width="81">
        <template slot-scope="scope">
          <el-checkbox v-model="scope.row.tuePm"></el-checkbox>
        </template>
      </el-table-column>
      <el-table-column prop="wedAm" label="星期三上午" width="81">
        <template slot-scope="scope">
          <el-checkbox v-model="scope.row.wedAm"></el-checkbox>
        </template>
      </el-table-column>
      <el-table-column prop="wedPm" label="星期三下午" width="81">
        <template slot-scope="scope">
          <el-checkbox v-model="scope.row.wedPm"></el-checkbox>
        </template>
      </el-table-column>
      <el-table-column prop="thuAm" label="星期四上午" width="81">
        <template slot-scope="scope">
          <el-checkbox v-model="scope.row.thuAm"></el-checkbox>
        </template>
      </el-table-column>
      <el-table-column prop="thuPm" label="星期四下午" width="81">
        <template slot-scope="scope">
          <el-checkbox v-model="scope.row.thuPm"></el-checkbox>
        </template>
      </el-table-column>
      <el-table-column prop="friAm" label="星期五上午" width="81">
        <template slot-scope="scope">
          <el-checkbox v-model="scope.row.friAm"></el-checkbox>
        </template>
      </el-table-column>
      <el-table-column prop="friPm" label="星期五下午" width="81">
        <template slot-scope="scope">
          <el-checkbox v-model="scope.row.friPm"></el-checkbox>
        </template>
      </el-table-column>
      <el-table-column prop="satAm" label="星期六上午" width="81">
        <template slot-scope="scope">
          <el-checkbox v-model="scope.row.satAm"></el-checkbox>
        </template>
      </el-table-column>
      <el-table-column prop="satPm" label="星期六下午" width="81">
        <template slot-scope="scope">
          <el-checkbox v-model="scope.row.satPm"></el-checkbox>
        </template>
      </el-table-column>
      <el-table-column prop="sunAm" label="星期日上午" width="81">
        <template slot-scope="scope">
          <el-checkbox v-model="scope.row.sunAm"></el-checkbox>
        </template>
      </el-table-column>
      <el-table-column prop="sunPm" label="星期日下午" width="81">
        <template slot-scope="scope">
          <el-checkbox v-model="scope.row.sunPm"></el-checkbox>
        </template>
      </el-table-column>                                          

    </el-table>
  </el-card>
</div>
</template>

<script>
import rule from "@/api/arrangement/rule";
import register from "@/api/register";
import { successDialog, failDialog } from "@/utils/notification";

export default {
  name: 'ArrangentRuleAdmin',
    data() {
      return {
        currentRoleId: "",
        registrationLevelId: "",
        ruleName: "",

        departments: [],
        departmentId: "",
        roles: [],
        multipleSelection: [],

        loading1: false,
        loading2: false,
        loading3: false
      }
  },


  methods: {
    save() {
      this.loading2 = true;
      this.loading3 = true;
      console.log(this.multipleSelection)
      var arrangementRuleParam = {};
      arrangementRuleParam.adminId = this.currentRoleId;
      arrangementRuleParam.departmentId = this.departmentId;
      arrangementRuleParam.arrangementRules = this.multipleSelection;
      arrangementRuleParam.ruleName = this.ruleName;

      rule.insertArrangementRule(arrangementRuleParam).then(
        response => {
          this.loading2 = false;
          this.loading3 = false;
          successDialog("保存成功");
        },
        error => {
          this.loading2 = false;
          this.loading3 = false;
          failDialog("[保存失败]" + error.data.data.message + "(" + error.data.data.code + ")");
        }
      );
    },

    handleSelectionChange(val) {
      this.multipleSelection = val;
    },

    search() {
      this.loading1 = true;
      this.loading3 = true;
      rule.listAllRolesByDepartmentId(this.departmentId).then(
        response => {
          this.loading1 = false;
          this.loading3 = false;
          successDialog("查询成功");
          console.log(response.data);
          var roles = response.data.data.roles;

          for(var i=0; i<roles.length; i++){
            roles[i].monAm = 0;
            roles[i].monPm = 0;
            roles[i].tueAm = 0;
            roles[i].tuePm = 0;
            roles[i].wedAm = 0;
            roles[i].wedPm = 0;
            roles[i].thuAm = 0;
            roles[i].thuPm = 0;
            roles[i].friAm = 0;
            roles[i].friPm = 0;
            roles[i].satAm = 0;
            roles[i].satPm = 0;
            roles[i].sunAm = 0;
            roles[i].sunPm = 0;
          }
          this.roles = roles;

      }, error => {
          this.loading1 = false;
          this.loading3 = false;
          failDialog("[查询失败]" + error.data.data.message + "(" + error.data.data.code + ")");
      })
    }
  },

  mounted() {
    register.listAllDepartments().then(response => {
        console.log(response.data);
        const data = response.data.data;
        this.departments = data;
    })
    
    // 初始化操作员id
    const currentRoleId = this.$store.getters["user/currentRoleId"];
    this.currentRoleId = currentRoleId;
  },

}
</script>

<style lang="css" scoped>
</style>
