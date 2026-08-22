<template>
  <div class="login-container">
    <div class="login-box">
      <div style="font-weight: bold; font-size: 40px; text-align: center; margin-bottom: 30px; color: #0d47d3">登 录</div>
      <el-form :model="data.form"  ref="formRef" :rules="data.rules">
        <el-form-item prop="username">
          <el-input :prefix-icon="User" size="large" v-model="data.form.username" placeholder="请输入账号" />
        </el-form-item>
        <el-form-item prop="password">
          <el-input :prefix-icon="Lock" size="large" v-model="data.form.password" placeholder="请输入密码" show-password />
        </el-form-item>
        <el-form-item prop="role">
          <el-select size="large" style="width: 100%" v-model="data.form.role">
            <el-option value="普通用户" label="普通用户"></el-option>
            <el-option value="管理员" label="管理员"></el-option>
          </el-select>
        </el-form-item>
        <el-form-item>
          <el-button size="large" type="primary" style="width: 100%" @click="login">登 录</el-button>
        </el-form-item>
      </el-form>
      <div style="text-align: center;">
        还没有账号？请 <a href="/register">注册</a>
      </div>
    </div>

    <el-dialog title="校园小卖部" v-model="data.dialogVisible" :show-close="false" width="40%" :close-on-click-modal="false" destroy-on-close>
      <div style="font-size: 18px; line-height: 26px; margin-bottom: 20px; text-align: center;color: #00b0ef">
        <b>项目登录入口</b>
      </div>
      <div style="margin-top: 5px; font-size: 20px"></div>
      <template #footer>
        <span class="dialog-footer">
          <el-button type="primary" @click="data.dialogVisible=false">确定</el-button>
        </span>
      </template>
    </el-dialog>

  </div>
</template>

<script setup>
  import { reactive, ref } from "vue";
  import { User, Lock } from "@element-plus/icons-vue";
  import request from "@/utils/request";
  import {ElMessage} from "element-plus";
  import router from "@/router";

  const data = reactive({
    dialogVisible: true,
    form: { role: '普通用户' },
    rules: {
      username: [
        { required: true, message: '请输入账号', trigger: 'blur' },
      ],
      password: [
        { required: true, message: '请输入密码', trigger: 'blur' },
      ],
    }
  })

  const formRef = ref()

  // 点击登录按钮的时候会触发这个方法
  const login = () => {
    formRef.value.validate((valid => {
      if (valid) {
        // 调用后台的接口
        request.post('/login', data.form).then(res => {
          if (res.code === '200') {
            ElMessage.success("登录成功")
            localStorage.setItem('system-user', JSON.stringify(res.data))
            if (res.data.role === '管理员') {
              router.push('/manager/home')
            } else {
              router.push('/front/home')
            }
          } else {
            ElMessage.error(res.msg)
          }
        })
      }
    })).catch(error => {
      console.error(error)
    })
  }

</script>
<style scoped>
.login-container {
  height: 100vh;
  overflow:hidden;
  display: flex;
  justify-content: center;
  align-items: center;
  background: #021245;
  background-size: cover;
}
.login-box {
  width: 500px;
  padding: 50px 30px;
  border-radius: 70px;
  box-shadow: 0 0 10px rgba(50, 2, 2, 0.3);
  background-color: #bfbd32;
}
</style>