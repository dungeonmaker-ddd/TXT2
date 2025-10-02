# 🔽 第2层 - FilterPage 筛选页面详细设计文档 v1.0

> **基于第1层架构的筛选页面具体实施方案 - 线上/线下筛选系统设计**

---

## 📋 **文档信息**

| 项目信息 | 详细内容 |
|---------|---------|
| **文档层级** | 第2层 - 模块详细设计 |
| **设计版本** | v1.0 |
| **所属模块** | Homepage/FilterPage |
| **页面性质** | 首页子页面 - 筛选功能专页 |
| **页面变体** | OnlineFilterPage + OfflineFilterPage |
| **技术栈** | Expo + React Native + Zustand |
| **设计目标** | 多维度筛选系统的具体组件和交互设计 |
| **依赖文档** | 第1层宏观架构设计 + 首页模块架构设计 |

---

## 🎯 **FilterPage 页面职责定义**

### 🔽 **核心职责**
- **多维度筛选系统** - 提供游戏/生活服务的全面筛选功能
- **实时结果预览** - 筛选条件变化时实时显示符合条件的结果数量
- **筛选条件管理** - 支持筛选条件的保存、重置、应用
- **用户偏好学习** - 记录用户筛选习惯，优化推荐算法

### 🎨 **筛选类型区分**

#### 💻 **OnlineFilterPage - 线上筛选页面**
- **服务对象** - 游戏陪玩、虚拟服务等线上服务
- **筛选维度** - 游戏类型、价格范围、技能等级、性别偏好、在线状态、地理位置、服务特色
- **特色功能** - 游戏技能筛选、段位筛选、语音服务筛选

#### 🏪 **OfflineFilterPage - 线下筛选页面**
- **服务对象** - 探店、私影、台球、K歌等线下服务
- **筛选维度** - 服务类型、地理位置、时间安排、价格预算、性别偏好、服务评价、服务特色
- **特色功能** - 地理位置精准筛选、时间段筛选、环境特色筛选

---

## 📂 **FilterPage 文件结构设计**

### 🏗️ **页面文件组织**

```
src/features/Homepage/OnlineFilterPage/     # 💻 线上筛选页面
├── index.tsx                               # 🎯 线上筛选页主文件
├── types.ts                                # 📋 页面类型定义
├── constants.ts                            # ⚙️ 页面常量配置
├── styles.ts                               # 🎨 页面样式定义
└── README.md                               # 📖 页面文档

src/features/Homepage/OfflineFilterPage/    # 🏪 线下筛选页面
├── index.tsx                               # 🎯 线下筛选页主文件
├── types.ts                                # 📋 页面类型定义
├── constants.ts                            # ⚙️ 页面常量配置
├── styles.ts                               # 🎨 页面样式定义
└── README.md                               # 📖 页面文档

shared/components/FilterComponents/          # 🔧 共享筛选组件
├── FilterNavigationArea/                   # 🔝 筛选导航区域
│   ├── index.tsx                           # 导航区域主文件
│   ├── BackButton.tsx                      # 返回按钮
│   ├── FilterTitle.tsx                     # 筛选标题
│   └── ResetButton.tsx                     # 重置按钮
├── FilterTypeSelector/                     # 🏷️ 筛选类型选择器
│   ├── index.tsx                           # 类型选择器主文件
│   ├── OnlineTab.tsx                       # 线上服务Tab
│   └── OfflineTab.tsx                      # 线下服务Tab
├── FilterGroupArea/                        # 📊 筛选组区域
│   ├── index.tsx                           # 筛选组主文件
│   ├── ServiceTypeFilter.tsx               # 服务类型筛选
│   ├── PriceRangeFilter.tsx                # 价格范围筛选
│   ├── LocationFilter.tsx                  # 地理位置筛选
│   ├── TimeFilter.tsx                      # 时间筛选 (线下专用)
│   ├── SkillLevelFilter.tsx                # 技能等级筛选 (线上专用)
│   ├── GenderFilter.tsx                    # 性别筛选
│   ├── OnlineStatusFilter.tsx              # 在线状态筛选
│   ├── RatingFilter.tsx                    # 评分筛选
│   └── FeatureTagFilter.tsx                # 特色标签筛选
├── FilterPreviewArea/                      # 📊 筛选预览区域
│   ├── index.tsx                           # 预览区域主文件
│   ├── ResultCounter.tsx                   # 结果计数器
│   ├── FilterSummary.tsx                   # 筛选摘要
│   └── AppliedFilters.tsx                  # 已应用筛选
└── FilterActionArea/                       # 🔻 筛选操作区域
    ├── index.tsx                           # 操作区域主文件
    ├── ResetAllButton.tsx                  # 重置全部按钮
    ├── ApplyButton.tsx                     # 应用按钮
    └── SavePresetButton.tsx                # 保存预设按钮
```

---

## 🧩 **共享筛选组件设计**

### 🔝 **FilterNavigationArea - 筛选导航区域**

```typescript
【组件职责】
- 筛选页面顶部导航管理
- 筛选标题动态显示
- 重置和返回功能控制

【布局特征】
- 固定高度：56px
- 白色背景，底部1px分隔线
- 三段式布局：返回 + 标题 + 重置

【主要Props】
interface FilterNavigationAreaProps {
  filterType: 'online' | 'offline';    // 筛选类型
  title: string;                       // 页面标题
  onBackPress: () => void;             // 返回处理
  onResetPress: () => void;            // 重置处理
  hasChanges: boolean;                 // 是否有筛选变更
}

【交互设计】
- 返回按钮：保存当前筛选状态并返回
- 重置按钮：清空所有筛选条件，灰色文字
- 标题显示：线上筛选/线下筛选动态标题
- 变更指示：有筛选变更时重置按钮高亮
```

### 🏷️ **FilterTypeSelector - 筛选类型选择器**

```typescript
【组件职责】
- 线上/线下筛选模式切换
- 筛选类型状态管理
- 页面内容动态切换

【布局特征】
- 固定高度：60px
- 白色背景，底部分隔线
- 两个Tab按钮，等宽分布

【主要Props】
interface FilterTypeSelectorProps {
  activeType: 'online' | 'offline';    // 当前筛选类型
  onTypeChange: (type: 'online' | 'offline') => void;  // 类型切换
  onlineCount?: number;                // 线上结果数量
  offlineCount?: number;               // 线下结果数量
}

【Tab设计】
- 游戏服务Tab：选中时紫色背景 + 白色文字
- 生活服务Tab：未选中时灰色背景 + 黑色文字
- 结果数量显示：Tab文字下方小字显示结果数量
- 切换动画：0.2s背景色过渡动画
```

---

## 💻 **OnlineFilterPage - 线上筛选页面设计**

### 📊 **筛选组设计**

#### 🎯 **ServiceTypeFilter - 服务类型筛选**
```typescript
【筛选功能】
- 游戏陪玩服务筛选：王者荣耀/英雄联盟/和平精英/荒野乱斗
- 虚拟服务筛选：语音陪聊/在线教学/观战指导等
- 多选支持：可同时选择多种服务类型

【布局设计】
- 区域高度：160px
- 网格布局：2行4列
- 卡片尺寸：80x80px
- 卡片间距：16px

【交互行为】
- 点击选择：卡片边框变为紫色，显示对勾
- 多选模式：支持同时选择多个服务类型
- 实时更新：选择变化时实时更新结果数量
```

#### 💰 **PriceRangeFilter - 价格范围筛选**
```typescript
【筛选功能】
- 双滑块价格选择器
- 价格预设选项
- 实时价格显示

【布局设计】
- 区域高度：120px
- 滑块区域：80px
- 预设按钮区域：40px

【价格预设】
- "10-30元" / "30-50元" / "50-100元" / "100元以上"
- 点击预设自动设置滑块位置
- 滑块拖动时预设按钮状态更新

【交互行为】
- 双滑块操作：最低价和最高价独立控制
- 实时预览：拖动时实时显示价格区间
- 限制逻辑：最低价不能超过最高价
```

#### ⭐ **SkillLevelFilter - 技能等级筛选**
```typescript
【筛选功能】
- 游戏技能等级选择：初级/中级/高级/专家
- 单选模式：只能选择一个技能等级
- 等级说明：每个等级有对应的说明文字

【布局设计】
- 区域高度：100px
- 按钮布局：水平排列4个按钮
- 按钮尺寸：等宽分布

【等级定义】
- 初级：新手陪玩，价格较低
- 中级：有一定经验，技术稳定
- 高级：技术过硬，经验丰富
- 专家：顶级技术，价格较高
```

#### 🚻 **GenderFilter - 性别筛选**
```typescript
【筛选功能】
- 性别偏好选择：不限性别/仅男性/仅女性
- 单选模式：只能选择一个性别选项
- 结果统计：显示各性别的服务提供者数量

【布局设计】
- 区域高度：100px
- 按钮布局：水平排列3个按钮
- 按钮尺寸：等宽分布

【选项设计】
- 不限性别：默认选中，显示所有服务提供者
- 仅男性：筛选男性服务提供者
- 仅女性：筛选女性服务提供者
```

#### 🟢 **OnlineStatusFilter - 在线状态筛选**
```typescript
【筛选功能】
- 在线状态选择：全部/仅在线/最近活跃
- 单选模式：只能选择一个状态选项
- 状态说明：每个状态有对应的时间说明

【状态定义】
- 全部：显示所有服务提供者
- 仅在线：只显示当前在线的服务提供者
- 最近活跃：显示24小时内活跃的服务提供者

【布局设计】
- 区域高度：100px
- 按钮布局：水平排列3个按钮
- 状态指示：绿色圆点表示在线状态
```

---

## 🏪 **OfflineFilterPage - 线下筛选页面设计**

### 📊 **线下专用筛选组设计**

#### 🏪 **LifestyleServiceFilter - 生活服务类型筛选**
```typescript
【筛选功能】
- 线下服务类型筛选：探店/私影/台球/K歌/喝酒/按摩/健身/更多
- 多选支持：可同时选择多种服务类型
- 服务描述：每种服务有简短描述

【布局设计】
- 区域高度：160px
- 网格布局：2行4列
- 卡片尺寸：80x80px
- 服务图标：彩色图标 + 服务名称

【服务类型定义】
- 探店：美食探店/购物陪同
- 私影：观影陪伴/电影解说
- 台球：技术指导/陪练对战
- K歌：K歌陪同/声乐指导
- 喝酒：酒友聚会/品酒体验
- 按摩：按摩服务/健康咨询
- 健身：健身指导/运动陪伴
- 更多：查看全部服务类型
```

#### 📍 **LocationFilter - 地理位置筛选**
```typescript
【筛选功能】
- 当前位置显示：显示用户当前定位城市和区域
- 距离范围设置：通过滑块设置最大服务距离
- 重新定位功能：点击重新获取当前位置

【布局设计】
- 区域高度：200px
- 当前位置区域：80px (浅蓝色卡片突出显示)
- 距离选择区域：80px (滑块 + 预设按钮)
- 重新定位区域：40px

【距离预设选项】
- "500米内" / "2km内" / "5km内" / "10km内" / "不限距离"
- 点击预设自动设置滑块位置
- 滑块拖动时预设按钮状态更新

【交互行为】
- 当前位置点击：重新定位或选择其他城市
- 距离滑块：实时显示距离范围
- 地图模式：可选择切换到地图选择模式
```

#### ⏰ **TimeFilter - 时间筛选**
```typescript
【筛选功能】
- 服务日期选择：今天/明天/后天/本周末/自定义日期
- 时间段选择：全天/上午/下午/晚上/深夜
- 时间组合：支持多个时间段组合选择

【布局设计】
- 区域高度：180px
- 日期选择区域：80px
- 时间段选择区域：60px
- 自定义时间区域：40px

【日期选择设计】
- 快捷选项：今天/明天/后天/本周末按钮
- 自定义日期：点击打开日历选择器
- 选中状态：紫色背景显示选中日期

【时间段设计】
- 全天：00:00-24:00
- 上午：06:00-12:00
- 下午：12:00-18:00
- 晚上：18:00-24:00
- 深夜：00:00-06:00
```

#### 💰 **BudgetFilter - 价格预算筛选**
```typescript
【筛选功能】
- 预算范围设置：通过双滑块设置预算区间
- 预算预设选项：常用价格区间快速选择
- 费用说明：显示费用包含的服务内容

【预算预设选项】
- "50元以下" / "50-100元" / "100-200元" / "200-500元" / "500元以上"
- 不同服务类型有不同的预设区间
- 基于历史数据的智能预设推荐

【布局设计】
- 区域高度：120px
- 滑块区域：60px
- 预设按钮区域：60px
```

#### ⭐ **RatingFilter - 服务评价筛选**
```typescript
【筛选功能】
- 评分等级选择：全部/4星以上/4.5星以上/5星
- 评价数量筛选：最少评价数量要求
- 评价质量筛选：优质评价比例要求

【布局设计】
- 区域高度：100px
- 评分按钮：水平排列4个按钮
- 评价说明：每个等级显示对应的服务质量说明

【评分标准】
- 全部：显示所有有评分的服务提供者
- 4星以上：评分≥4.0的服务提供者
- 4.5星以上：评分≥4.5的服务提供者
- 5星：评分=5.0的服务提供者
```

---

## 🔄 **页面状态管理设计 (Zustand)**

### 📊 **FilterPage 状态结构**

```typescript
【筛选页面状态管理】
interface FilterPageState {
  // 页面基础状态
  pageState: {
    filterType: 'online' | 'offline';  // 当前筛选类型
    loading: boolean;                  // 页面加载状态
    saving: boolean;                   // 保存状态
    error: string | null;              // 错误信息
  };
  
  // 线上筛选条件
  onlineFilters: {
    serviceTypes: string[];            // 选中的服务类型
    priceRange: [number, number];      // 价格范围
    skillLevel: string;                // 技能等级
    gender: 'all' | 'male' | 'female'; // 性别偏好
    onlineStatus: 'all' | 'online' | 'recent';  // 在线状态
    location: {
      maxDistance: number;             // 最大距离
      coordinates?: {lat: number, lng: number};
    };
    features: string[];                // 服务特色标签
  };
  
  // 线下筛选条件
  offlineFilters: {
    serviceTypes: string[];            // 选中的服务类型
    location: {
      currentCity: string;             // 当前城市
      currentDistrict: string;         // 当前区域
      maxDistance: number;             // 最大距离
      coordinates?: {lat: number, lng: number};
    };
    timeSchedule: {
      dates: string[];                 // 选中的日期
      timeSlots: string[];             // 选中的时间段
      customDate?: string;             // 自定义日期
    };
    budget: [number, number];          // 预算范围
    gender: 'all' | 'male' | 'female'; // 性别偏好
    rating: number;                    // 最低评分要求
    features: string[];                // 服务特色标签
  };
  
  // 筛选结果预览
  filterPreview: {
    totalCount: number;                // 总结果数量
    onlineCount: number;               // 线上结果数量
    offlineCount: number;              // 线下结果数量
    loading: boolean;                  // 预览加载状态
    lastUpdated: number;               // 最后更新时间
  };
  
  // 筛选历史和预设
  filterPresets: {
    savedPresets: FilterPreset[];      // 保存的筛选预设
    recentFilters: FilterState[];      // 最近使用的筛选
    defaultFilters: FilterState;       // 默认筛选条件
  };
}

【状态操作方法】
- updateOnlineFilter(filterKey: string, value: any)     # 更新线上筛选
- updateOfflineFilter(filterKey: string, value: any)    # 更新线下筛选
- switchFilterType(type: 'online' | 'offline')         # 切换筛选类型
- previewFilterResults()                               # 预览筛选结果
- applyFilters()                                       # 应用筛选条件
- resetFilters(type?: 'online' | 'offline')           # 重置筛选条件
- saveFilterPreset(name: string)                       # 保存筛选预设
- loadFilterPreset(presetId: string)                   # 加载筛选预设
- addToRecentFilters(filters: FilterState)             # 添加到最近筛选
```

---

## 📊 **筛选预览系统设计**

### 🔍 **FilterPreviewArea - 筛选预览区域**

```typescript
【组件职责】
- 实时显示筛选结果数量
- 展示当前应用的筛选条件摘要
- 提供筛选条件的快速移除功能

【布局特征】
- 固定在筛选内容区域顶部
- 高度：60px
- 白色背景，底部分隔线

【主要Props】
interface FilterPreviewAreaProps {
  resultCount: number;                 // 结果数量
  appliedFilters: AppliedFilter[];     // 已应用筛选
  onRemoveFilter: (filterId: string) => void;  // 移除筛选
  loading: boolean;                    // 加载状态
}

【已应用筛选展示】
interface AppliedFilter {
  id: string;
  label: string;                       // 筛选标签文字
  value: string;                       // 筛选值描述
  removable: boolean;                  // 是否可移除
  color: string;                       // 标签颜色
}

【实时更新机制】
- 筛选条件变化时立即更新预览
- 防抖处理：300ms延迟更新结果数量
- 加载状态：显示加载指示器
- 错误处理：网络异常时显示缓存结果
```

---

## 🔻 **FilterActionArea - 筛选操作区域**

### 📱 **底部操作设计**

```typescript
【组件职责】
- 筛选条件的应用和重置
- 筛选预设的保存和管理
- 用户操作的确认和取消

【布局特征】
- 固定底部：80px + 安全区域
- 白色背景，顶部1px分隔线
- 左右两个操作区域

【主要Props】
interface FilterActionAreaProps {
  hasChanges: boolean;                 // 是否有筛选变更
  resultCount: number;                 // 筛选结果数量
  onReset: () => void;                 // 重置操作
  onApply: () => void;                 # 应用操作
  onSavePreset?: () => void;           // 保存预设
  loading: boolean;                    // 操作加载状态
}

【操作按钮设计】
- 重置筛选按钮：100x44px，灰色边框，黑色文字
- 确定按钮：200x44px，紫色背景，白色文字
- 结果数量显示：确定按钮内显示"确定(123个结果)"
- 保存预设：长按确定按钮显示保存预设选项

【操作反馈】
- 按钮点击：0.2s缩放动画
- 应用成功：Toast提示 + 页面返回
- 重置确认：显示确认对话框
- 保存成功：Toast提示 + 预设列表更新
```

---

## 🎨 **筛选UI设计系统**

### 🎯 **筛选组件样式规范**

```typescript
【筛选组标题样式】
- 字体大小：16sp
- 字体颜色：#000000 (黑色)
- 字体粗细：bold (粗体)
- 边距：marginBottom: 12px

【筛选选项按钮样式】
- 未选中状态：
  - 背景色：#F5F5F5 (浅灰色)
  - 文字色：#666666 (灰色)
  - 边框色：#E0E0E0 (边框灰色)
  
- 选中状态：
  - 背景色：#8A2BE2 (紫色)
  - 文字色：#FFFFFF (白色)
  - 边框色：#8A2BE2 (紫色边框)

【滑块组件样式】
- 滑块轨道：#E0E0E0 (灰色轨道)
- 滑块填充：#8A2BE2 (紫色填充)
- 滑块手柄：#8A2BE2 (紫色圆形)
- 数值显示：实时显示当前值
```

### 📱 **响应式布局设计**

```typescript
【屏幕适配策略】
1. 筛选组网格适配
   - 小屏幕：2行3列布局
   - 标准屏幕：2行4列布局
   - 大屏幕：2行5列布局

2. 滑块组件适配
   - 滑块宽度：屏幕宽度 - 64px (左右边距)
   - 手柄大小：根据屏幕密度调整
   - 触摸区域：扩大触摸热区

3. 按钮组件适配
   - 按钮高度：44px (标准触摸尺寸)
   - 按钮间距：根据屏幕宽度自适应
   - 文字大小：根据屏幕密度调整
```

---

## ⚡ **性能优化设计**

### 🔄 **筛选性能优化**

```typescript
【实时预览优化】
1. 防抖处理
   - 筛选条件变化防抖：300ms
   - 滑块拖动防抖：100ms
   - 文本输入防抖：500ms

2. 缓存策略
   - 筛选结果缓存：相同条件缓存5分钟
   - 筛选配置缓存：筛选选项缓存30分钟
   - 用户偏好缓存：本地持久化存储

3. 请求优化
   - 批量请求：多个筛选条件合并请求
   - 请求取消：新筛选取消旧请求
   - 并行加载：筛选选项和结果预览并行

【内存管理】
- 筛选组件按需渲染
- 非活跃筛选组延迟加载
- 筛选历史定期清理
- 页面卸载时清理状态
```

### 📊 **用户体验优化**

```typescript
【交互体验优化】
1. 操作反馈
   - 筛选变化：立即显示加载状态
   - 选项选择：0.2s动画反馈
   - 结果更新：平滑的数字变化动画
   - 错误提示：友好的错误信息显示

2. 状态保持
   - 页面返回：保持筛选状态
   - 应用重启：恢复上次筛选条件
   - 网络恢复：自动重新加载数据

3. 智能提示
   - 零结果提示：建议调整筛选条件
   - 过度筛选提示：筛选条件过于严格时提示
   - 推荐筛选：基于用户行为推荐筛选条件

【无障碍设计】
- 筛选组件语义标签
- 滑块组件可键盘操作
- 筛选结果语音播报
- 高对比度模式支持
```

---

## 🔗 **页面间集成设计**

### 🧭 **导航集成**

```typescript
【页面导航设计】
1. 进入筛选页面
   - 来源页面：MainPage、ServiceDetailPage
   - 路由：/homepage/filter-online 或 /homepage/filter-offline
   - 参数：currentFilters (当前筛选条件)

2. 筛选完成跳转
   - 应用筛选：返回来源页面，携带筛选结果
   - 取消筛选：返回来源页面，保持原筛选状态
   - 重置筛选：清空筛选条件，返回来源页面

【状态传递设计】
- 筛选条件双向传递
- 筛选结果数量传递
- 用户偏好数据传递
- 页面来源标识传递
```

### 🔄 **数据同步设计**

```typescript
【筛选数据同步】
1. 筛选条件同步
   - 实时同步到 filterStore
   - 筛选应用时同步到来源页面
   - 筛选偏好同步到用户配置

2. 筛选结果同步
   - 预览结果实时更新
   - 应用后结果同步到列表页面
   - 筛选统计数据上报

3. 用户偏好同步
   - 筛选习惯学习和保存
   - 个性化筛选推荐
   - 跨设备筛选偏好同步

【缓存同步策略】
- 筛选配置本地缓存
- 筛选结果短期缓存
- 用户偏好云端同步
- 筛选历史定期清理
```

---

## ✅ **质量保证标准**

### 🎯 **功能完整性检查**
- [ ] 线上筛选所有维度正常工作
- [ ] 线下筛选所有维度正常工作
- [ ] 筛选类型切换功能正常
- [ ] 实时结果预览准确
- [ ] 筛选条件应用正确
- [ ] 筛选重置功能正常

### 🔽 **筛选质量检查**
- [ ] 筛选结果准确性高
- [ ] 筛选响应时间 < 300ms
- [ ] 筛选组合逻辑正确
- [ ] 筛选边界条件处理
- [ ] 筛选冲突检测正常
- [ ] 筛选建议合理

### 🎨 **用户体验检查**
- [ ] 筛选界面清晰易懂
- [ ] 筛选操作流畅自然
- [ ] 筛选反馈及时准确
- [ ] 筛选状态保持正确
- [ ] 筛选错误处理友好
- [ ] 筛选无障碍支持完善

### 📱 **技术性能检查**
- [ ] 筛选防抖处理正确
- [ ] 内存使用控制合理
- [ ] 网络请求优化到位
- [ ] 缓存策略执行正确
- [ ] 状态管理设计合理
- [ ] 组件复用效率高

---

## 🚀 **下一步计划**

### 📋 **第2层设计继续**
完成 FilterPage 详细设计后，接下来的第2层设计：
1. **EventCenterPage 详细设计** - 组局中心页面组件设计
2. **LocationPage 详细设计** - 位置服务页面组件设计
3. **FeaturedPage 详细设计** - 限时专享页面组件设计
4. **RegionPage 详细设计** - 区域选择页面组件设计

### 🎯 **FilterPage 优化方向**
根据审核反馈，可能的优化方向：
- 筛选维度组合逻辑优化
- 筛选UI交互体验优化
- 筛选性能策略增强
- 筛选结果准确性提升
- 用户筛选偏好学习优化

---

## 📞 **审核和反馈**

**当前状态**：等待 FilterPage 第2层设计审核
**反馈方式**：请对以下方面提供意见
- 线上/线下筛选页面设计是否合理
- 筛选维度覆盖是否完整
- 筛选交互体验是否友好
- 实时预览功能是否实用
- 状态管理和性能优化是否合适
- 是否需要调整某些筛选组件或功能

**修改计划**：根据审核反馈进行设计调整，确保 FilterPage 设计达到理想状态后再继续其他页面的第2层设计。

---

**© 2025 第2层 FilterPage 筛选页面详细设计文档 - Expo + Zustand 技术栈**
