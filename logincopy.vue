<template>
  <v-layout align-space-around justify-center column fill-height>
    <v-flex xs12 sm8 lg6 md6>
      <v-card max-width="1200px" class="pa-3">
        <v-layout align-space-around column>
          <v-layout class="pc-di" row wrap align-center justify-center>
            <v-flex xs10 sm10 lg8 md8>
              <v-layout row justify-center align-center fill-height>
                <div class="display-1 font-weight-bold">{{LoginTitle}}</div>
              </v-layout>
            </v-flex>
          </v-layout>
          <v-form ref="form" v-model="valid" lazy-validation>
            <v-layout row wrap class="pa-1">
              <v-flex md6 lg6 class="pa-1">
                <v-layout align-center justify-start>
                  <v-autocomplete label="所在部门" :items="unit" v-model="form.unit" :rules="unitRules"></v-autocomplete>
                </v-layout>
              </v-flex>
              <v-flex md3 lg3 class="pa-1">
                <v-sheet class="d-flex" color="white lighten-3" hover>
                  <v-text-field label="姓名" :rules="nameRules" v-model="form.name" required></v-text-field>
                </v-sheet>
              </v-flex>
              <v-flex md3 lg3 class="pa-1">
                <v-sheet class="d-flex" color="white lighten-3" hover>
                  <v-autocomplete
                    label="月份"
                    :items="month"
                    :rules="monthRules"
                    v-model.number="form.month"
                  ></v-autocomplete>
                </v-sheet>
              </v-flex>
            </v-layout>
            <v-layout align-space-around column >
              <v-textarea
                name="input-1"
                label="工作内容"
                :rules="logRules"
                v-model="form.log"
                auto-grow
              ></v-textarea>
            </v-layout>
            <v-layout align-space-around column >
              <v-textarea name="input-2" label="遇到的主要问题" v-model="form.problem" auto-grow></v-textarea>
            </v-layout>
            <v-layout align-space-around column >
              <v-textarea name="input-3" label="解决建议" v-model="form.advice" auto-grow></v-textarea>
            </v-layout>
          </v-form>
        </v-layout>
        <v-layout align-start justify-start row class="pa-3">
          <!-- <v-btn :disabled="!valid" color="success" class="mr-4" @click="validate">上传</v-btn> -->
          <v-btn :disabled="!valid" color="blue" class="mr-4" @click.stop="checkFrom">提交</v-btn>
          <v-dialog v-model="dialog" persistent width="900">
            <v-card>
              <v-layout align-start justify-start column  class="pa-3">
                <v-card-title class="headline">确认输入的如下信息</v-card-title>
                <v-card-text>
                  部门
                  <br />
                  {{form.unit}}
                </v-card-text>
                <v-card-text>
                  姓名
                  <br />
                  {{form.name}}
                </v-card-text>
                <v-card-text>
                  月份
                  <br />
                  {{form.month}}
                </v-card-text>
                <v-card-text>
                  工作内容
                  <br />
                  {{form.log}}
                </v-card-text>
                <v-card-text>
                  遇到的主要问题
                  <br />
                  {{form.problem}}
                </v-card-text>
                <v-card-text>
                  建议
                  <br />
                  {{form.advice}}
                </v-card-text>
                <v-card-actions>
                  <v-spacer></v-spacer>
                  <v-btn color="green darken-1" text @click="dialog = false">取消</v-btn>
                  <v-btn color="green darken-1" text @click="uploadFormdata">确认</v-btn>
                </v-card-actions>
              </v-layout>
            </v-card>
          </v-dialog>
        </v-layout>
      </v-card>
    </v-flex>
  </v-layout>
</template>
<script>
export default {
  created() {
    // console.log("form组件已加载");
  },
  methods: {
    validate() {
      // console.log(this.$slots);
      if (this.$refs.form.validate()) {
        this.snackbar = true;
        // console.log(this.$data);
        
      }
    },
    uploadFormdata() {
      let _this = this;
      this.axios
        .post("/api/data", this.$data.form)
        .then(function(response) {
          // console.log(response.data);
          if (response.data == "303") {
            // console.log("数据发送成功" );
            alert("数据发送成功" );
            _this.$refs.form.reset();
          } else if(response.data == "alive"){
            // console.log("检测到重复的数据原数据已覆盖" );
            alert("检测到重复的数据原数据已覆盖") ;
          }
          _this.$data.dialog = false;
        })
        .catch(function(error) {
          // console.log(error);
          alert("数据提交失败,错误" + error);
        });
    },
    checkFrom() {
      // console.log(this.$slots);
      if (this.$refs.form.validate()) {
        this.snackbar = true;
        // console.log(this.$data);
        let _this = this;
        _this.dialog = true;
      }
    }
  },

  data() {
    return {
      dialog: false,
      valid: true,
      form: {
        unit: "",
        name: "",
        month: "",
        log: "",
        problem: "",
        advice: ""
      },
      unit: [
        "销售支撑中心",
        "经营部",
        "研发中心",
        "ITO业务运营中心",
        "云计算解决方案中心"
      ],
      unitRules: [
        v => !!v || "必须选定一个所在部门",
        v => (v && v.length > 1) || "必须选定一个所在部门"
      ],
      nameRules: [
        v => !!v || "必须填入姓名",
        v => (v && v.length <= 5) || "请填入正确姓名"
      ],
      logRules: [v => !!v || "此文本框不能为空"],
      monthRules: [v => !!v || "必须填入月份"],
      month: [1, 2, 3, 4, 5, 6, 7, 8, 9, 10, 11, 12],
      LoginTitle: "月报收集MK.Ⅰ  ",
      Loginmsg: "月报收集MK.Ⅰ"
    };
  }
};
</script>
<style>
</style>