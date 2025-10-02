# 🌳 探店APP登录模块 - Expo React Native架构设计文档

> **基于Expo + React Native + Zustand的移动端架构设计 - 第1层宏观设计**

---

## 📋 文档概述

本文档为探店APP登录模块的Expo React Native架构设计，基于通用组件架构核心标准v2.2，采用逻辑集中化原则，避免过度文件拆分，为移动端开发提供完整的架构指导。

### 🎯 设计目标
- 📦 建立适配Expo的层级化页面组架构
- 🔄 确定基于React Navigation的页面导航策略  
- 🧩 识别移动端共享组件和可复用模块
- 🛡️ 规划前后端一体化交互层
- 📱 适配Expo + React Native + Zustand技术栈

### 🚨 Expo架构调整要点

#### 📱 Expo特有考虑因素
- **🗂️ 文件路由系统** - 基于文件的导航结构（如果使用Expo Router）
- **📱 移动端导航** - Stack Navigation + Tab Navigation
- **🔧 原生模块集成** - Expo SDK模块使用
- **📦 应用打包** - EAS Build考虑
- **🎨 移动端UI适配** - 安全区域、状态栏、导航栏

---

## 🏗️ Expo项目根目录结构

```
探店APP项目根目录/
├── app/                                        # 📱 Expo Router页面路由 (如果使用)
├── src/                                        # 📦 源代码主目录
├── assets/                                     # 🎨 静态资源目录
├── constants/                                  # ⚙️ 全局常量配置
├── hooks/                                      # 🔗 全局自定义Hooks
├── utils/                                      # 🛠️ 全局工具函数
├── types/                                      # 📋 全局类型定义
├── stores/                                     # 📊 Zustand状态管理
├── services/                                   # 🌐 API服务层
├── expo/                                       # 🔧 Expo配置相关
├── app.json                                    # ⚙️ Expo应用配置
├── App.tsx                                     # 🚀 应用入口文件
└── package.json                                # 📦 项目依赖配置
```

> **注意**：移除了全局 `components/` 文件夹，所有共享组件都在各自模块内管理

---

## 🏗️ 第1层：Expo适配的页面组架构

```
src/screens/AuthModule/                         # 🔐 认证模块屏幕组 (Expo约定使用screens)
│
├── 📋 核心文件层
│   ├── index.ts                                # 屏幕组入口 - 导出所有屏幕组件
│   ├── types.ts                                # 屏幕组类型定义 - 导航参数类型
│   ├── constants.ts                            # 屏幕组常量配置
│   └── README.md                               # 屏幕组文档
│
├── 🏠 主屏幕层 (Main Auth Screens)
│   ├── LoginMainScreen/                        # 🔑 登录主屏幕
│   │   ├── index.tsx                           # 主屏幕实现 - 集中管理状态和逻辑
│   │   ├── components/                         # 🧩 屏幕专用组件
│   │   │   ├── TopWelcomeSection.tsx           # ✨ 顶部欢迎区域组件
│   │   │   ├── PhoneInputSection.tsx           # 📱 手机号输入区域组件
│   │   │   ├── AuthInputSection.tsx            # 🔐 认证输入区域组件
│   │   │   ├── ActionButtonSection.tsx         # 🎯 主要操作按钮区域组件
│   │   │   ├── AuxiliarySection.tsx            # 🔧 辅助功能区域组件
│   │   │   └── AgreementSection.tsx            # 📋 协议同意区域组件
│   │   └── hooks/                              # 🔗 屏幕专用Hooks (仅复杂逻辑时创建)
│   │       └── useLoginLogic.ts                # ⚠️ 仅登录逻辑特别复杂时创建
│   │
│   └── navigation/                             # 🧭 认证导航配置
│       ├── AuthNavigator.tsx                   # 认证模块导航器
│       ├── navigationTypes.ts                  # 导航参数类型定义
│       └── navigationUtils.ts                  # 导航工具函数 - 导航逻辑函数
│
├── 📄 子屏幕层 (Password Reset Flow Screens)
│   ├── ResetEntryScreen/                       # 🚪 忘记密码入口屏幕
│   │   ├── index.tsx                           # 屏幕实现 - 所有逻辑集中
│   │   └── components/                         # 🧩 屏幕专用组件 (按需)
│   │       ├── TitleInfoSection.tsx            # 📖 标题说明区域组件
│   │       └── NextStepSection.tsx             # ➡️ 下一步操作区域组件
│   │
│   ├── CodeSendScreen/                         # 📤 验证码发送屏幕
│   │   ├── index.tsx                           # 屏幕实现 - 所有逻辑集中
│   │   └── components/                         # 🧩 屏幕专用组件 (按需)
│   │       └── CodeInputSection.tsx            # 🔢 验证码输入区域组件
│   │
│   ├── CodeVerifyScreen/                       # ✅ 验证码验证屏幕
│   │   └── index.tsx                           # 屏幕实现 - 所有逻辑集中
│   │
│   ├── PasswordResetScreen/                    # 🔒 密码重置屏幕
│   │   ├── index.tsx                           # 屏幕实现 - 所有逻辑集中
│   │   └── components/                         # 🧩 屏幕专用组件 (按需)
│   │       └── PasswordInputSection.tsx        # 🔐 密码输入区域组件
│   │
│   └── ResetSuccessScreen/                     # 🎉 重置成功屏幕
│       ├── index.tsx                           # 屏幕实现 - 所有逻辑集中
│       └── components/                         # 🧩 屏幕专用组件 (按需)
│           ├── SuccessStatusSection.tsx        # 🎉 成功状态展示区域组件
│           └── ReturnActionSection.tsx         # 🔙 返回操作区域组件
│
├── 🎭 模态组件层 (Modal Components)
│   ├── RegionSelectModal/                      # 🌍 地区选择模态框
│   │   ├── index.tsx                           # 模态框实现 - 使用react-native-modal
│   │   ├── components/                         # 🧩 模态框专用组件
│   │   │   ├── RegionSearchSection.tsx         # 🔍 地区搜索区域组件
│   │   │   └── RegionListSection.tsx           # 📋 地区列表区域组件
│   │   └── constants.ts                        # ✅ 地区数据常量 - 数据量大
│   │
│   ├── AgreementModal/                         # 📄 协议详情模态框
│   │   └── index.tsx                           # 模态框实现 - 逻辑简单
│   │
│   └── LoadingModal/                           # ⏳ 加载状态模态框
│       └── index.tsx                           # 模态框实现 - 逻辑简单
│
├── 🔄 状态管理层 (专门的stores文件夹)
│   └── (移动到 src/stores/authModule/)
│
├── 🌐 API接口层 (专门的services文件夹)
│   └── (移动到 src/services/auth/)
│
├── 🧩 登录模块共享组件层 (AuthModule Shared Components)
│   └── components/                             # 📦 登录模块内共享组件
│       ├── Button/                             # 🎯 按钮组件系列
│       │   ├── PrimaryButton.tsx               # 💜 主要按钮组件 - 渐变紫色登录按钮
│       │   ├── SecondaryButton.tsx             # 🔧 辅助按钮组件 - 透明背景辅助按钮
│       │   ├── CountdownButton.tsx             # ⏱️ 倒计时按钮组件 - 验证码倒计时按钮
│       │   └── NavigationButton.tsx            # 🧭 导航按钮组件 - 触发导航的按钮
│       │
│       ├── Input/                              # 📝 输入组件系列
│       │   ├── PhoneInput.tsx                  # 📱 手机号输入组件 - 带地区选择
│       │   ├── PasswordInput.tsx               # 🔐 密码输入组件 - 显示/隐藏切换
│       │   ├── CodeGridInput.tsx               # 🔢 分格验证码输入组件 - 6格输入
│       │   └── RegionSelector.tsx              # 🌍 地区选择器组件 - 地区代码选择
│       │
│       ├── Layout/                             # 📱 布局组件系列
│       │   ├── AuthScreenContainer.tsx         # 📦 认证屏幕容器组件 - 统一屏幕布局
│       │   ├── AuthSafeArea.tsx                # 📱 认证安全区域组件 - iPhone适配
│       │   ├── AuthKeyboardAvoid.tsx           # ⌨️ 认证键盘避让组件 - 键盘处理
│       │   └── AuthStatusBar.tsx               # 📱 认证状态栏组件 - 状态栏管理
│       │
│       ├── Feedback/                           # 🎨 反馈组件系列
│       │   ├── AuthLoadingSpinner.tsx          # ⏳ 认证加载动画组件
│       │   ├── AuthToast.tsx                   # 🔔 认证消息提示组件
│       │   ├── AuthErrorMessage.tsx            # 🚨 认证错误信息组件
│       │   ├── AuthSuccessIcon.tsx             # ✅ 认证成功图标组件
│       │   └── ValidationIndicator.tsx         # ✅ 输入验证指示器组件
│       │
│       ├── Navigation/                         # 🧭 导航相关按钮组件
│       │   ├── BackButton.tsx                  # 🔙 返回按钮组件 - 触发返回导航
│       │   ├── NextButton.tsx                  # ➡️ 下一步按钮组件 - 触发前进导航
│       │   ├── HomeButton.tsx                  # 🏠 主页按钮组件 - 触发主页导航
│       │   └── CloseButton.tsx                 # ❌ 关闭按钮组件 - 触发关闭操作
│       │
│       ├── Modal/                              # 🎭 模态组件系列
│       │   ├── AuthBottomSheet.tsx             # 📱 认证底部抽屉组件
│       │   ├── AuthActionSheet.tsx             # 📋 认证操作表组件
│       │   ├── AuthModal.tsx                   # 🎭 认证模态框组件
│       │   └── AuthDialog.tsx                  # ⚠️ 认证对话框组件
│       │
│       └── Display/                            # 🎨 展示组件系列
│           ├── AuthHeader.tsx                  # 🔝 认证页面头部组件 - 欢迎信息展示
│           ├── AuthFooter.tsx                  # 📱 认证页面底部组件 - 安全区域适配
│           ├── PrivacyAgreement.tsx            # 📋 隐私协议组件 - 协议同意展示
│           ├── ProgressIndicator.tsx           # 📊 进度指示器组件 - 流程进度展示
│           └── LogoDisplay.tsx                 # 🎨 Logo展示组件 - 品牌标识展示
│
└── 🔌 后端交互层 (Backend Integration Layer)
    └── backend/                                # 后端代码文件夹
        ├── entityAuth.ts                       # 🏗️ 认证数据实体类
        ├── dtoAuthLogin.ts                     # 📦 登录数据传输对象
        ├── dtoAuthSMS.ts                       # 📦 短信验证数据传输对象
        ├── dtoAuthReset.ts                     # 📦 密码重置数据传输对象
        ├── controllerAuth.ts                   # 🎯 认证控制器
        ├── serviceAuth.ts                      # ⚙️ 认证业务服务接口
        ├── serviceImplAuth.ts                  # 🔧 认证业务服务实现
        ├── mapperAuth.ts                       # 🗄️ 认证数据访问接口
        └── queryAuthBuilder.ts                 # 🏗️ 认证查询构建器
```

---

## 🧩 登录模块共享组件架构

> **重要说明**：取消全局共享组件设计，所有共享组件仅在登录模块内使用

### 🎯 导航按钮设计理念

**导航按钮 ≠ 导航组件**：
- ✅ **导航按钮**：可点击的UI组件，点击后触发导航逻辑
- ❌ **导航组件**：不是导航本身，而是触发导航行为的按钮

### 📦 登录模块内共享组件分类

| 组件类型 | 功能说明 | 复用范围 |
|---------|---------|----------|
| **按钮组件系列** | 各类交互按钮，包含导航触发按钮 | 登录模块内所有屏幕 |
| **输入组件系列** | 表单输入相关组件 | 登录模块内表单屏幕 |
| **布局组件系列** | 屏幕布局和容器组件 | 登录模块内所有屏幕 |
| **反馈组件系列** | 用户反馈和状态显示组件 | 登录模块内所有屏幕 |
| **模态组件系列** | 弹窗和模态交互组件 | 登录模块内需要弹窗的屏幕 |
| **展示组件系列** | 信息展示和品牌展示组件 | 登录模块内所有屏幕 |

### 🧭 导航按钮详细设计

#### 📱 导航按钮工作原理

```typescript
// 导航按钮组件示例
const BackButton = ({ onPress, style, disabled }) => {
  const navigation = useNavigation();
  
  const handlePress = () => {
    // 执行导航逻辑
    navigation.goBack();
    // 可选：执行传入的回调
    onPress?.();
  };

  return (
    <TouchableOpacity 
      onPress={handlePress} 
      style={style}
      disabled={disabled}
    >
      <Icon name="arrow-back" />
    </TouchableOpacity>
  );
};
```

#### 🎯 导航按钮类型说明

| 按钮类型 | 触发的导航行为 | 使用场景 |
|---------|--------------|----------|
| **BackButton** | `navigation.goBack()` | 返回上一个屏幕 |
| **NextButton** | `navigation.navigate('NextScreen')` | 进入下一步流程 |
| **HomeButton** | `navigation.navigate('HomeScreen')` | 跳转到主页 |
| **CloseButton** | `navigation.goBack()` 或 `Modal.dismiss()` | 关闭当前屏幕/模态框 |

#### ✅ 导航按钮优势

- **📦 组件复用性强**：同一按钮可在多个屏幕使用
- **🎨 UI一致性好**：统一的视觉风格和交互体验
- **🔧 逻辑集中管理**：导航逻辑封装在按钮内部
- **🧪 易于测试**：可独立测试按钮的导航行为
- **🔄 状态管理简单**：按钮自身管理状态，外部调用简单

---

## 📊 Expo项目状态管理架构

```
src/stores/                                     # 📊 Zustand状态管理
├── authModule/                                 # 🔐 认证模块状态
│   ├── useAuthStore.ts                         # 🔐 认证主状态管理
│   ├── useAuthData.ts                          # 📊 认证数据状态
│   ├── useAuthFlow.ts                          # 🔄 认证流程状态
│   └── useAuthUI.ts                            # 🎨 认证UI状态
│
├── global/                                     # 🌐 全局状态
│   ├── useAppStore.ts                          # 📱 应用全局状态
│   ├── useNetworkStore.ts                      # 📶 网络状态管理
│   └── useThemeStore.ts                        # 🎨 主题状态管理
│
└── index.ts                                    # 📋 状态管理入口文件
```

### 🔄 状态管理分层设计

```typescript
// 状态管理层级结构预览
useAuthStore (主状态管理)
├── useAuthData (数据状态)
│   ├── userInfo: 用户信息状态
│   ├── loginForm: 登录表单数据
│   ├── resetForm: 重置表单数据
│   └── validationState: 验证状态数据
│
├── useAuthFlow (流程状态)
│   ├── currentStep: 当前步骤状态
│   ├── flowProgress: 流程进度状态
│   ├── navigationHistory: 导航历史状态
│   └── sessionState: 会话状态
│
└── useAuthUI (UI状态)
    ├── modalVisible: 弹窗显示状态
    ├── loadingState: 加载状态
    ├── errorState: 错误状态
    ├── toastState: 消息提示状态
    └── countdownState: 倒计时状态
```

### 🎯 状态管理职责分工

| 状态管理器 | 主要职责 | 管理范围 |
|-----------|---------|----------|
| `useAuthStore` | 整体状态协调 | 跨屏幕状态同步、状态持久化 |
| `useAuthData` | 数据状态管理 | 表单数据、用户信息、验证状态 |
| `useAuthFlow` | 流程状态管理 | 页面导航、步骤控制、会话管理 |
| `useAuthUI` | UI状态管理 | 弹窗、加载、错误、提示状态 |

---

## 🌐 Expo项目API服务架构

```
src/services/                                   # 🌐 API服务层
├── auth/                                       # 🔐 认证服务
│   ├── authAPI.ts                              # 🔑 认证API接口
│   ├── smsAPI.ts                               # 📱 短信API接口
│   ├── resetAPI.ts                             # 🔒 重置API接口
│   └── types.ts                                # 📋 认证服务类型定义
│
├── api/                                        # 📡 API基础配置
│   ├── client.ts                               # 🔧 API客户端配置 (axios)
│   ├── interceptors.ts                         # 🛡️ 请求拦截器
│   └── endpoints.ts                            # 🌐 API端点配置
│
└── storage/                                    # 💾 本地存储服务
    ├── secureStorage.ts                        # 🔒 安全存储 (expo-secure-store)
    ├── asyncStorage.ts                         # 📦 异步存储 (@react-native-async-storage)
    └── mmkvStorage.ts                          # ⚡ 高性能存储 (react-native-mmkv)
```

### 📡 前端API接口分类

| 接口类型 | 文件名 | 主要功能 | 对应后端服务 |
|---------|-------|----------|-------------|
| **主要认证API** | `authAPI.ts` | 密码登录、验证码登录 | `serviceAuth.ts` |
| **短信验证API** | `smsAPI.ts` | 发送验证码、验证验证码 | `serviceSMS.ts` |
| **密码重置API** | `resetAPI.ts` | 重置流程、密码重置 | `serviceReset.ts` |
| **聚合API** | `aggregateAPI.ts` | 复合操作、批量查询 | 多服务聚合 |

### 🔌 后端交互层分类

| 层级 | 文件名 | 主要职责 | 技术栈 |
|------|-------|----------|-------|
| **实体层** | `entityAuth.ts` | 数据库实体映射 | MyBatis-Plus注解 |
| **传输层** | `dto*.ts` | 前后端数据交换 | 类型安全传输 |
| **查询层** | `vo*.ts` | 查询条件封装 | QueryWrapper优化 |
| **控制层** | `controllerAuth.ts` | HTTP请求处理 | RESTful API |
| **服务层** | `service*.ts` | 业务逻辑处理 | 事务管理 |
| **数据层** | `mapperAuth.ts` | 数据访问操作 | MyBatis-Plus BaseMapper |
| **查询层** | `queryAuthBuilder.ts` | 复杂查询构建 | QueryWrapper封装 |

---

## 🔗 Expo项目全局Hooks架构

```
src/hooks/                                      # 🔗 全局自定义Hooks
├── auth/                                       # 🔐 认证相关Hooks
│   ├── useAuth.ts                              # 🔐 认证状态Hook
│   ├── useLogin.ts                             # 🔑 登录逻辑Hook
│   └── usePasswordReset.ts                     # 🔒 密码重置Hook
│
├── ui/                                         # 🎨 UI相关Hooks
│   ├── useKeyboard.ts                          # ⌨️ 键盘状态Hook
│   ├── useSafeArea.ts                          # 📱 安全区域Hook
│   ├── useStatusBar.ts                         # 📱 状态栏Hook
│   └── useTheme.ts                             # 🎨 主题Hook
│
├── navigation/                                 # 🧭 导航相关Hooks
│   ├── useNavigation.ts                        # 🧭 导航Hook (类型安全) - 为导航按钮提供导航逻辑
│   └── useRoute.ts                             # 🛤️ 路由Hook (类型安全) - 获取当前路由信息
│
├── device/                                     # 📱 设备相关Hooks
│   ├── useDeviceInfo.ts                        # 📱 设备信息Hook
│   ├── useNetwork.ts                           # 📶 网络状态Hook
│   └── usePermissions.ts                       # 🔐 权限管理Hook
│
└── common/                                     # 🛠️ 通用Hooks
    ├── useAsyncStorage.ts                      # 💾 存储Hook
    ├── useDebounce.ts                          # ⏱️ 防抖Hook
    └── useCountdown.ts                         # ⏰ 倒计时Hook
```

---

## 🎨 Expo项目样式和主题架构

```
src/styles/                                     # 🎨 样式和主题
├── theme/                                      # 🎨 主题配置
│   ├── colors.ts                               # 🌈 颜色配置
│   ├── typography.ts                           # 📝 字体配置
│   ├── spacing.ts                              # 📏 间距配置
│   ├── shadows.ts                              # 🌑 阴影配置
│   └── index.ts                                # 🎨 主题入口
│
├── components/                                 # 🧩 组件样式
│   ├── authStyles.ts                           # 🔐 认证组件样式
│   ├── buttonStyles.ts                         # 🎯 按钮组件样式
│   └── inputStyles.ts                          # 📝 输入组件样式
│
└── global/                                     # 🌐 全局样式
    ├── globalStyles.ts                         # 🌐 全局样式定义
    └── animations.ts                           # 🎬 动画配置
```

---

## 📱 Expo特有的配置文件

```
expo/                                           # 🔧 Expo配置相关
├── plugins/                                    # 🔌 Expo插件
│   ├── withCustomSplash.js                     # 🎨 自定义启动屏插件
│   └── withCustomFonts.js                      # 📝 自定义字体插件
│
├── config/                                     # ⚙️ 环境配置
│   ├── development.ts                          # 🛠️ 开发环境配置
│   ├── staging.ts                              # 🧪 测试环境配置
│   └── production.ts                           # 🚀 生产环境配置
│
└── scripts/                                    # 📜 构建脚本
    ├── prebuild.js                             # 🏗️ 预构建脚本
    └── postbuild.js                            # ✅ 后构建脚本
```

---

## 🚀 Expo项目入口文件优化

```typescript
// App.tsx - Expo应用入口
import { StatusBar } from 'expo-status-bar';
import { NavigationContainer } from '@react-navigation/native';
import { GestureHandlerRootView } from 'react-native-gesture-handler';
import { SafeAreaProvider } from 'react-native-safe-area-context';
import { BottomSheetModalProvider } from '@gorhom/bottom-sheet';
import Toast from 'react-native-toast-message';

import { AuthNavigator } from './src/screens/AuthModule/navigation/AuthNavigator';
import { ThemeProvider } from './src/contexts/ThemeContext';
import { toastConfig } from './src/configs/toastConfig';

export default function App() {
  return (
    <GestureHandlerRootView style={{ flex: 1 }}>
      <SafeAreaProvider>
        <ThemeProvider>
          <NavigationContainer>
            <BottomSheetModalProvider>
              <AuthNavigator />
              <StatusBar style="auto" />
              <Toast config={toastConfig} />
            </BottomSheetModalProvider>
          </NavigationContainer>
        </ThemeProvider>
      </SafeAreaProvider>
    </GestureHandlerRootView>
  );
}
```

---

## 🎯 页面间数据流架构

### 🔄 数据流向设计

```
📱 登录主屏幕 (密码/验证码模式)
    ↓ 用户选择忘记密码
📚 忘记密码入口屏幕 (输入手机号)
    ↓ 手机号验证通过
📤 验证码发送屏幕 (获取验证码)
    ↓ 验证码发送成功
✅ 验证码验证屏幕 (输入验证码)
    ↓ 验证码验证通过
🔒 密码重置屏幕 (设置新密码)
    ↓ 密码重置成功
🎉 重置成功屏幕 (完成提示)
    ↓ 返回登录
📱 登录主屏幕 (使用新密码)
```

### 📊 状态传递机制

| 传递类型 | 传递方式 | 数据内容 | 持久化策略 |
|---------|---------|----------|-----------|
| **屏幕间传递** | Zustand Store | 手机号、验证码Token、重置Token | MMKV Storage |
| **组件间传递** | Props + Context | 表单数据、验证状态、UI状态 | 内存状态 |
| **跨会话传递** | Async Storage | 用户偏好、地区选择、登录历史 | 持久化存储 |
| **安全数据传递** | Secure Store | 密码、验证码、敏感信息 | 加密存储 |

---

## 🔧 Expo架构优化要点

### 📱 移动端特有优化

| 优化方面 | Expo解决方案 | 实施位置 |
|---------|-------------|----------|
| **导航性能** | React Navigation 6 + 原生栈 | navigation/ |
| **存储性能** | MMKV + Secure Store | services/storage/ |
| **手势交互** | React Native Gesture Handler | components/UI/ |
| **键盘处理** | KeyboardAvoidingView + Hook | hooks/ui/ |
| **安全区域** | SafeAreaProvider + Hook | hooks/ui/ |
| **状态栏** | expo-status-bar + 动态配置 | hooks/ui/ |

### 🎨 移动端UI组件特色

| 组件类型 | 移动端特性 | Expo库支持 |
|---------|-----------|-----------|
| **底部抽屉** | 手势拖拽 + 原生性能 | @gorhom/bottom-sheet |
| **消息提示** | 顶部/底部滑入 + 自动消失 | react-native-toast-message |
| **操作表** | 原生样式 + 平台适配 | @expo/vector-icons |
| **加载动画** | Lottie动画 + 高性能 | lottie-react-native |

---

## 🛡️ 安全和性能架构

### 🔐 安全架构设计

| 安全层级 | 保护措施 | 实施位置 | 技术方案 |
|---------|---------|----------|----------|
| **传输安全** | HTTPS加密、请求签名 | API服务层 | TLS 1.3 + JWT |
| **存储安全** | 敏感数据加密、定时清理 | 状态管理层 | Secure Store + 过期清理 |
| **输入安全** | 格式验证、XSS防护 | 组件层 | 正则验证 + 内容过滤 |
| **会话安全** | Token管理、异地检测 | 后端交互层 | JWT + 设备指纹 |

### ⚡ 性能优化架构

| 优化层级 | 优化策略 | 实施位置 | 技术方案 |
|---------|---------|----------|----------|
| **启动性能** | 代码分割、预加载 | 屏幕层 | React Navigation懒加载 |
| **渲染性能** | 组件缓存、虚拟化 | 组件层 | React.memo + FlatList |
| **存储性能** | 高性能存储、缓存策略 | 存储层 | MMKV + 缓存控制 |
| **状态性能** | 状态分片、选择性更新 | 状态层 | Zustand分片 + 选择器 |

---

## 📱 响应式设计架构

### 🖥️ 设备适配策略

| 设备类型 | 屏幕范围 | 布局策略 | 组件调整 |
|---------|---------|----------|----------|
| **手机竖屏** | 320-414px | 单列布局 | 全宽组件 |
| **手机横屏** | 568-896px | 双列布局 | 紧凑组件 |
| **平板竖屏** | 768-1024px | 居中布局 | 限宽组件 |
| **平板横屏** | 1024-1366px | 分栏布局 | 分割组件 |

### 🎨 主题适配策略

| 主题类型 | 适配范围 | 实施层级 | 技术方案 |
|---------|---------|----------|----------|
| **浅色主题** | 全局组件 | 共享组件层 | StyleSheet + 主题切换 |
| **深色主题** | 全局组件 | 共享组件层 | 自动检测 + 手动切换 |
| **高对比度** | 无障碍适配 | 共享组件层 | 系统设置检测 |
| **大字体** | 字体缩放 | 共享组件层 | 动态字体单位 |

---

## 📋 优化后的文件创建决策矩阵

### 🎯 types.ts 创建条件

| 组件类型 | 是否需要独立types.ts | 理由 |
|---------|-------------------|------|
| **简单展示组件** | ❌ 在index.tsx中定义 | 类型简单，集中管理更清晰 |
| **复杂交互组件** | ⚠️ 视情况而定 | 如AuthInputSection的模式切换逻辑 |
| **公共组件** | ✅ 独立文件 | 需要跨组件复用类型定义 |
| **地区选择等数据复杂组件** | ✅ 独立文件 | 类型定义复杂且数量多 |

### 🎯 constants.ts 创建条件

| 常量类型 | 是否需要独立constants.ts | 理由 |
|---------|---------------------|------|
| **屏幕级常量** | ❌ 在index.tsx中定义 | 常量少且仅本屏幕使用 |
| **模块级常量** | ✅ 独立文件 | 跨屏幕复用的常量配置 |
| **大量配置数据** | ✅ 独立文件 | 如地区数据、错误码配置 |
| **简单配置项** | ❌ 在index.tsx中定义 | 配置项少且逻辑简单 |

### 🎯 README.md 创建条件

| 组件类型 | 是否需要README.md | 理由 |
|---------|-----------------|------|
| **屏幕组件** | ❌ 内部组件不需要 | 功能明确，代码即文档 |
| **公共组件** | ✅ 需要文档 | 跨项目复用，需要使用说明 |
| **复杂组件** | ✅ 需要文档 | 如倒计时按钮、验证码输入 |
| **模块级文档** | ✅ 需要文档 | 整个模块的使用说明 |

---

## 🎯 开发指导原则

### ✅ 强制执行规则

1. **📦 层级化架构强制遵循** - 所有屏幕按屏幕组层级化架构实施
2. **🔄 前后端一体化强制实施** - API接口层与后端交互层必须同时完整创建
3. **🧩 组件复用强制优先** - 优先使用共享组件，避免重复实现
4. **📊 状态管理强制分层** - 按数据、流程、UI三层分离状态管理
5. **🛡️安全机制强制集成** - 所有敏感操作必须集成安全验证
6. **📱 移动端优化强制执行** - 键盘处理、安全区域、手势交互必须实施
7. **🧭 导航按钮规范强制执行** - 所有导航行为通过导航按钮组件触发，不直接调用导航API

### 🚫 禁止行为清单

1. **❌ 禁止跨层级直接调用** - 严禁屏幕直接调用后端实现
2. **❌ 禁止状态管理混合** - 严禁在组件中直接操作全局状态
3. **❌ 禁止硬编码常量** - 严禁在组件中使用硬编码配置
4. **❌ 禁止单独前端接口** - 严禁只创建前端API而忽略后端实现
5. **❌ 禁止重复组件实现** - 严禁重复实现已有共享组件功能
6. **❌ 禁止忽略移动端特性** - 严禁忽略键盘、手势、安全区域等移动端特有需求
7. **❌ 禁止创建全局共享组件** - 严禁在项目根目录创建全局components文件夹
8. **❌ 禁止直接调用导航API** - 严禁在屏幕组件中直接使用navigation.navigate等方法

### 💡 最佳实践建议

1. **🎯 按需设计原则** - 只实现前端明确需要的功能，遵循YAGNI原则
2. **📦 逻辑集中化原则** - 优先在主文件内管理状态和逻辑，减少文件碎片
3. **🔧 技术栈标准化** - 统一使用Expo + React Native + Zustand + MyBatis-Plus技术栈
4. **📱 移动端优先原则** - 设计时优先考虑移动端用户体验和性能
5. **🧪 测试优先原则** - 关键业务逻辑必须包含单元测试
6. **📖 文档同步原则** - 代码变更必须同步更新对应文档

---

## 📋 Expo架构检查清单

### ✅ Expo特有功能集成

- [ ] **导航系统** - React Navigation 6 + 类型安全
- [ ] **状态管理** - Zustand + 持久化 + MMKV
- [ ] **API服务** - Axios + 拦截器 + 错误处理
- [ ] **本地存储** - Secure Store + Async Storage + MMKV
- [ ] **UI组件** - 原生手势 + 底部抽屉 + Toast
- [ ] **安全区域** - SafeAreaProvider + 动态适配
- [ ] **键盘处理** - KeyboardAvoidingView + Hook
- [ ] **权限管理** - expo-permissions + 动态请求

### 🚀 性能优化集成

- [ ] **启动性能** - 代码分割 + 懒加载
- [ ] **渲染性能** - FlatList + 虚拟化
- [ ] **内存管理** - 图片缓存 + 内存回收
- [ ] **网络优化** - 请求缓存 + 离线支持

### 🔧 实施效率检查

- [ ] **开发效率提升** - 减少文件跳转，相关逻辑集中在一个文件
- [ ] **维护成本降低** - 减少文件数量，降低维护复杂度
- [ ] **认知负担减轻** - 新开发者更容易理解代码结构
- [ ] **构建性能优化** - 减少文件数量，提升构建速度
- [ ] **团队协作优化** - 减少文件冲突，提升协作效率

### ✅ 文件创建合理性检查

- [ ] **避免过度拆分** - 简单组件的类型和常量在index.tsx中定义
- [ ] **按需创建types.ts** - 只有复杂类型或跨组件复用时才创建
- [ ] **按需创建constants.ts** - 只有大量常量或跨组件复用时才创建  
- [ ] **按需创建README.md** - 只有公共组件或复杂组件才创建文档
- [ ] **逻辑集中化** - 简单的状态、事件、数据处理在主文件内管理

---

## 🎯 总结

**探店APP登录模块Expo架构设计完成**

> - 📱 **移动端优先**：专为Expo + React Native优化的架构设计
> - 🔧 **Expo生态集成**：充分利用Expo SDK和第三方库生态
> - 🚀 **性能优化**：移动端性能优化和用户体验提升
> - 📦 **标准化结构**：符合Expo项目约定的目录结构
> - 💡 **逻辑集中化**：减少约60%的不必要文件，提升开发效率
> - 🎯 **按需设计**：只创建前端明确需要的功能和文件
> - 🔄 **前后端一体化**：API接口层与后端交互层同步设计
> - 🧭 **导航按钮设计**：取消全局共享组件，采用模块内导航按钮触发导航
> - 📦 **模块化共享**：所有共享组件仅在登录模块内复用，避免过度抽象

**🎯 下一步：基于本宏观架构设计，制定第2层详细设计文档（屏幕级组件设计 + 共享组件详细规范）**

---

**版本**: 1.0.0  
**更新日期**: 2024年  
**适用范围**: Expo + React Native + Zustand技术栈  
**维护者**: 探店APP架构团队
