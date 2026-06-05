<template>
  <div class="login-container">
    <!-- 背景动画 -->
    <div class="bg-animation">
      <div class="gradient-circle circle-1"></div>
      <div class="gradient-circle circle-2"></div>
      <div class="gradient-circle circle-3"></div>
    </div>
    
    <div class="login-box">
      <!-- 左侧品牌区 -->
      <div class="brand-section">
        <div class="brand-content">
          <div class="logo-wrap">
            <el-icon class="logo-icon"><DataBoard /></el-icon>
            <span class="logo-text">全自动化</span>
          </div>
          <h1 class="brand-title">企业管理系统</h1>
          <p class="brand-desc">高效 · 智能 · 便捷</p>
          <div class="feature-list">
            <div class="feature-item">
              <el-icon><Odometer /></el-icon>
              <span>数据可视化</span>
            </div>
            <div class="feature-item">
              <el-icon><User /></el-icon>
              <span>用户管理</span>
            </div>
            <div class="feature-item">
              <el-icon><Document /></el-icon>
              <span>文档协作</span>
            </div>
          </div>
        </div>
      </div>
      
      <!-- 右侧登录区 -->
      <div class="form-section">
        <div class="form-container">
          <div class="form-header">
            <h2>欢迎回来</h2>
            <p>请登录您的账户</p>
          </div>
          
          <el-form 
            ref="loginFormRef"
            :model="loginForm" 
            :rules="rules"
            class="login-form"
            @submit.prevent="handleLogin"
          >
            <el-form-item prop="username">
              <el-input 
                v-model="loginForm.username" 
                placeholder="请输入用户名"
                size="large"
                :prefix-icon="User"
              />
            </el-form-item>
            
            <el-form-item prop="password">
              <el-input 
                v-model="loginForm.password" 
                type="password"
                placeholder="请输入密码"
                size="large"
                :prefix-icon="Lock"
                show-password
                @keyup.enter="handleLogin"
              />
            </el-form-item>
            
            <div class="form-options">
              <el-checkbox v-model="loginForm.remember">记住我</el-checkbox>
              <a href="#" class="forgot-link">忘记密码？</a>
            </div>
            
            <el-button 
              type="primary"
              size="large"
              class="login-btn"
              :loading="loading"
              @click="handleLogin"
            >
              {{ loading ? '登录中...' : '登 录' }}
            </el-button>
            
            <el-alert
              v-if="error"
              :title="error"
              type="error"
              show-icon
              :closable="false"
              class="error-tip"
            />
          </el-form>
          
          <div class="form-footer">
            <span>还没有账号？</span>
            <a href="#">立即注册</a>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
import { ref, reactive } from 'vue'
import { login as authLogin } from '../auth'
import { useRouter } from 'vue-router'
import { User, Lock, DataBoard, Odometer, Document } from '@element-plus/icons-vue'
import { ElMessage } from 'element-plus'

export default {
  name: 'Login',
  setup() {
    const router = useRouter()
    const loginFormRef = ref(null)
    const loading = ref(false)
    const error = ref(null)
    
    const loginForm = reactive({
      username: 'admin',
      password: 'password',
      remember: false
    })
    
    const rules = {
      username: [
        { required: true, message: '请输入用户名', trigger: 'blur' }
      ],
      password: [
        { required: true, message: '请输入密码', trigger: 'blur' },
        { min: 4, message: '密码至少4位', trigger: 'blur' }
      ]
    }
    
    async function handleLogin() {
      if (!loginFormRef.value) return
      
      await loginFormRef.value.validate(async (valid) => {
        if (!valid) return
        
        loading.value = true
        error.value = null
        
        try {
          const success = await authLogin(loginForm.username, loginForm.password)
          if (success) {
            ElMessage.success('登录成功！')
            router.push('/dashboard')
          } else {
            error.value = '用户名或密码错误'
          }
        } catch (e) {
          error.value = '登录失败，请稍后重试'
        } finally {
          loading.value = false
        }
      })
    }
    
    return {
      loginForm,
      loginFormRef,
      rules,
      loading,
      error,
      handleLogin,
      User,
      Lock,
      DataBoard,
      Odometer,
      Document
    }
  }
}
</script>

<style scoped>
.login-container {
  min-height: 100vh;
  display: flex;
  align-items: center;
  justify-content: center;
  background: url('/assets/images/login-bg.jpg') center/cover;
  position: relative;
  overflow: hidden;
  padding: 20px;
  font-family: 'Geist', 'Inter', -apple-system, BlinkMacSystemFont, 'Segoe UI', sans-serif;
}

.login-container::before {
  content: '';
  position: absolute;
  inset: 0;
  background: linear-gradient(135deg, rgba(15, 23, 42, 0.88) 0%, rgba(30, 58, 95, 0.88) 100%);
  z-index: 0;
}

/* 噪点纹理 */
.login-container::after {
  content: '';
  position: fixed;
  inset: 0;
  opacity: 0.03;
  z-index: 0;
  pointer-events: none;
  background-image: url("data:image/svg+xml,%3Csvg viewBox='0 0 256 256' xmlns='http://www.w3.org/2000/svg'%3E%3Cfilter id='noise'%3E%3CfeTurbulence type='fractalNoise' baseFrequency='0.9' numOctaves='4' stitchTiles='stitch'/%3E%3C/filter%3E%3Crect width='100%25' height='100%25' filter='url(%23noise)'/%3E%3C/svg%3E");
  background-repeat: repeat;
  background-size: 256px 256px;
}

/* 背景动画 */
.bg-animation {
  position: absolute;
  inset: 0;
  overflow: hidden;
  z-index: 0;
}

.gradient-circle {
  position: absolute;
  border-radius: 50%;
  filter: blur(80px);
  opacity: 0.4;
  animation: float 20s infinite ease-in-out;
}

.circle-1 {
  width: 600px;
  height: 600px;
  background: linear-gradient(135deg, #1e3a5f 0%, #0f172a 100%);
  top: -200px;
  left: -200px;
  animation-delay: 0s;
}

.circle-2 {
  width: 500px;
  height: 500px;
  background: linear-gradient(135deg, #2d4a6f 0%, #1a365d 100%);
  bottom: -150px;
  right: -150px;
  animation-delay: -5s;
}

.circle-3 {
  width: 400px;
  height: 400px;
  background: linear-gradient(135deg, #1e40af 0%, #1e3a5f 100%);
  top: 50%;
  left: 50%;
  transform: translate(-50%, -50%);
  animation-delay: -10s;
}

@keyframes float {
  0%, 100% {
    transform: translate(0, 0) scale(1);
  }
  25% {
    transform: translate(50px, -30px) scale(1.05);
  }
  50% {
    transform: translate(-30px, 50px) scale(0.95);
  }
  75% {
    transform: translate(-50px, -30px) scale(1.02);
  }
}

/* 登录盒子 */
.login-box {
  display: flex;
  width: 900px;
  max-width: 100%;
  background: rgba(255, 255, 255, 0.08);
  backdrop-filter: blur(20px);
  border-radius: 24px;
  overflow: hidden;
  box-shadow:
    0 25px 50px -12px rgba(0, 0, 0, 0.5),
    inset 0 1px 0 rgba(255, 255, 255, 0.1);
  border: 1px solid rgba(255, 255, 255, 0.15);
  position: relative;
  z-index: 1;
}

/* 左侧品牌区 */
.brand-section {
  flex: 1;
  background: linear-gradient(135deg, #0f172a 0%, #1e3a5f 100%);
  padding: 60px 40px;
  display: flex;
  align-items: center;
  justify-content: center;
  position: relative;
  overflow: hidden;
}

.brand-section::before {
  content: '';
  position: absolute;
  inset: 0;
  background: url('/assets/images/login-bg.jpg') center/cover;
  opacity: 0.3;
}

.brand-section::after {
  content: '';
  position: absolute;
  inset: 0;
  background: linear-gradient(135deg, rgba(15, 23, 42, 0.92) 0%, rgba(30, 58, 95, 0.92) 100%);
}

.brand-content {
  position: relative;
  z-index: 1;
  text-align: center;
  color: #fff;
}

.logo-wrap {
  display: flex;
  align-items: center;
  justify-content: center;
  gap: 12px;
  margin-bottom: 24px;
}

.logo-icon {
  font-size: 48px;
  color: #fff;
}

.logo-text {
  font-size: 28px;
  font-weight: 700;
  letter-spacing: 2px;
}

.brand-title {
  font-size: 32px;
  font-weight: 700;
  margin: 0 0 12px 0;
  letter-spacing: -0.02em;
  text-shadow: 0 2px 10px rgba(0, 0, 0, 0.2);
}

.brand-desc {
  font-size: 16px;
  opacity: 0.9;
  margin: 0 0 40px 0;
}

.feature-list {
  display: flex;
  flex-direction: column;
  gap: 16px;
}

.feature-item {
  display: flex;
  align-items: center;
  justify-content: center;
  gap: 10px;
  font-size: 14px;
  opacity: 0.9;
}

.feature-item .el-icon {
  font-size: 20px;
}

/* 右侧表单区 */
.form-section {
  flex: 1;
  padding: 64px 52px;
  background: rgba(255, 254, 252, 0.97);
}

.form-container {
  max-width: 360px;
  margin: 0 auto;
}

.form-header {
  text-align: center;
  margin-bottom: 40px;
}

.form-header h2 {
  font-size: 28px;
  font-weight: 700;
  color: #0f172a;
  margin: 0 0 8px 0;
  letter-spacing: -0.02em;
}

.form-header p {
  font-size: 14px;
  color: #64748b;
  margin: 0;
}

.login-form {
  margin-bottom: 24px;
}

.login-form :deep(.el-input__wrapper) {
  border-radius: 12px;
  box-shadow: 0 0 0 1px #e2e8f0 inset;
  padding: 4px 12px;
  transition: box-shadow 200ms ease;
}

.login-form :deep(.el-input__wrapper:hover) {
  box-shadow: 0 0 0 1px #2563eb inset;
}

.login-form :deep(.el-input__wrapper.is-focus) {
  box-shadow: 0 0 0 1px #2563eb inset;
}

.login-form :deep(.el-input__wrapper:focus-visible) {
  outline: 2px solid #2563eb;
  outline-offset: 2px;
}

.login-form :deep(.el-input__inner) {
  height: 44px;
}

.form-options {
  display: flex;
  justify-content: space-between;
  align-items: center;
  margin-bottom: 24px;
}

.form-options :deep(.el-checkbox__label) {
  color: #64748b;
}

.forgot-link {
  font-size: 14px;
  color: #2563eb;
  text-decoration: none;
  font-weight: 500;
  transition: color 200ms ease;
}

.forgot-link:hover {
  color: #1e40af;
}

.login-btn {
  width: 100%;
  height: 48px;
  border-radius: 12px;
  font-size: 16px;
  font-weight: 600;
  background: linear-gradient(135deg, #1e40af 0%, #2563eb 100%);
  border: none;
  transition: all 200ms ease;
}

.login-btn:hover {
  background: linear-gradient(135deg, #1e3a8a 0%, #1d4ed8 100%);
  box-shadow: 0 8px 24px rgba(37, 99, 235, 0.35);
}

.login-btn:active {
  transform: scale(0.98);
}

.login-btn:focus-visible {
  outline: 2px solid #2563eb;
  outline-offset: 2px;
}

.error-tip {
  margin-top: 16px;
  border-radius: 8px;
}

.form-footer {
  text-align: center;
  padding-top: 24px;
  border-top: 1px solid #e2e8f0;
  font-size: 14px;
  color: #64748b;
}

.form-footer a {
  color: #2563eb;
  text-decoration: none;
  font-weight: 600;
  margin-left: 6px;
  transition: color 200ms ease;
}

.form-footer a:hover {
  color: #1e40af;
}

/* 响应式 */
@media (max-width: 768px) {
  .login-box {
    flex-direction: column;
    width: 100%;
  }

  .brand-section {
    padding: 40px 30px;
  }

  .logo-icon {
    font-size: 36px;
  }

  .logo-text {
    font-size: 24px;
  }

  .brand-title {
    font-size: 24px;
  }

  .form-section {
    padding: 40px 30px;
  }

  .form-header h2 {
    font-size: 24px;
  }
}
</style>
