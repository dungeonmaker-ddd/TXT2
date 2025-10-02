# 🎤 第2层 - EventCenterPage 组局中心页面详细设计文档 v1.0

> **基于第1层架构的组局中心页面具体实施方案 - 线下活动组织系统设计**

---

## 📋 **文档信息**

| 项目信息 | 详细内容 |
|---------|---------|
| **文档层级** | 第2层 - 模块详细设计 |
| **设计版本** | v1.0 |
| **所属模块** | Homepage/EventCenterPage |
| **页面性质** | 首页子页面 - 组局中心功能专页 |
| **技术栈** | Expo + React Native + Zustand |
| **设计目标** | 线下活动组织系统的具体组件和交互设计 |
| **依赖文档** | 第1层宏观架构设计 + 首页模块架构设计 |

---

## 🎯 **EventCenterPage 页面职责定义**

### 🎤 **核心职责**
- **线下活动组织平台** - 提供K歌、聚餐、游戏等线下活动的组织功能
- **活动发布管理** - 支持用户发布新的组局活动
- **活动报名系统** - 提供活动报名、支付、管理功能
- **社交互动中心** - 促进用户间的线下社交互动

### 🎨 **功能特色**
- **时间管理** - 活动时间 + 报名截止时间的双时间管理
- **地理位置集成** - 具体地址 + 距离计算 + 地图定位
- **报名状态可视化** - 报名人数进度条 + 活动状态标识
- **多样化活动类型** - K歌/探店/聚餐/游戏/运动等多种活动

---

## 📂 **EventCenterPage 文件结构设计**

### 🏗️ **页面文件组织**

```
src/features/Homepage/EventCenterPage/
├── index.tsx                           # 🎯 组局中心页主文件 (单文件集中管理)
├── types.ts                            # 📋 页面类型定义
├── constants.ts                        # ⚙️ 页面常量配置
├── styles.ts                           # 🎨 页面样式定义
└── README.md                           # 📖 页面文档

components/ (页面区域组件)
├── EventNavigationArea/                # 🔝 组局导航区域
│   ├── index.tsx                       # 导航区域主文件
│   ├── BackButton.tsx                  # 返回按钮组件
│   ├── EventCenterTitle.tsx            # 组局中心标题
│   └── PublishEventButton.tsx          # 发布组局按钮
├── EventFilterArea/                    # 🔧 活动筛选区域
│   ├── index.tsx                       # 筛选区域主文件
│   ├── EventFilterToolbar.tsx          # 活动筛选工具栏
│   └── EventSortOptions.tsx            # 活动排序选项
├── EventListArea/                      # 📋 活动列表区域
│   ├── index.tsx                       # 列表区域主文件
│   ├── EventCard.tsx                   # 活动卡片组件
│   ├── EventInfiniteList.tsx           # 活动无限列表
│   └── EventEmptyState.tsx             # 空状态组件
├── PublishEventArea/                   # 📝 发布活动区域
│   ├── index.tsx                       # 发布区域主文件
│   ├── EventTypeSelector.tsx           # 活动类型选择
│   ├── EventInfoForm.tsx               # 活动信息表单
│   ├── EventTimeSelector.tsx           # 活动时间选择
│   ├── EventLocationSelector.tsx       # 活动地点选择
│   └── EventPublishButton.tsx          # 发布确认按钮
└── EventDetailModalArea/               # 🎤 活动详情模态区域
    ├── index.tsx                       # 详情模态主文件
    ├── EventDetailInfo.tsx             # 活动详情信息
    ├── ParticipantList.tsx             # 参与者列表
    ├── EventLocationMap.tsx            # 活动地点地图
    └── JoinEventButton.tsx             # 参加活动按钮
```

---

## 🧩 **页面区域组件设计**

### 🔝 **EventNavigationArea - 组局导航区域**

```typescript
【组件职责】
- 组局中心页面顶部导航管理
- 发布组局功能入口
- 页面标题和返回控制

【布局特征】
- 固定高度：56px
- 白色背景，底部1px分隔线
- 三段式布局：返回 + 标题 + 发布按钮

【主要Props】
interface EventNavigationAreaProps {
  onBackPress: () => void;             // 返回首页处理
  onPublishPress: () => void;          // 发布组局处理
  userCanPublish: boolean;             // 用户发布权限
  pendingEventCount?: number;          // 待处理活动数量
}

【子组件功能】
- BackButton: 返回首页，保存浏览状态
- EventCenterTitle: "组局中心"标题，18sp黑色粗体
- PublishEventButton: "发布组局"紫色文字，16sp

【权限控制】
- 发布权限检查：实名认证 + 信用良好
- 发布限制：每日最多发布3个活动
- 发布状态：显示待审核/已发布活动数量
```

### 🔧 **EventFilterArea - 活动筛选区域**

```typescript
【组件职责】
- 活动筛选工具栏管理
- 活动排序方式控制
- 筛选条件状态管理

【布局特征】
- 筛选工具栏：48px高度
- 白色背景，底部1px分隔线
- 三个筛选按钮水平排列

【主要Props】
interface EventFilterAreaProps {
  sortBy: EventSortType;               // 当前排序方式
  genderFilter: GenderType;            // 性别筛选
  onSortChange: (sort: EventSortType) => void;  // 排序变化
  onGenderChange: (gender: GenderType) => void;  // 性别筛选变化
  onAdvancedFilter: () => void;        // 高级筛选
}

【筛选选项设计】
- 智能排序：最新发布/报名人数/开始时间/距离最近
- 性别要求：不限性别/仅限男性/仅限女性/男女混合
- 高级筛选：活动类型/时间范围/价格区间/地点距离

【排序算法】
- 最新发布：按活动发布时间倒序
- 报名人数：按当前报名人数倒序
- 开始时间：按活动开始时间正序
- 距离最近：按活动地点距离正序
```

### 📋 **EventListArea - 活动列表区域**

```typescript
【组件职责】
- 展示可报名的活动列表
- 活动卡片统一管理
- 无限滚动和刷新控制

【布局特征】
- 可滚动区域，占据剩余空间
- 活动卡片高度：160px
- 卡片间距：1px分隔线
- 列表内边距：16px

【主要Props】
interface EventListAreaProps {
  events: EventData[];                 // 活动数据列表
  onEventPress: (eventId: string) => void;  // 点击活动处理
  onParticipantPress: (userId: string) => void;  // 点击参与者
  onLoadMore: () => void;              // 加载更多
  onRefresh: () => void;               // 下拉刷新
  loading: boolean;                    // 加载状态
  hasMore: boolean;                    // 是否有更多数据
}

【活动数据结构】
interface EventData {
  eventId: string;
  eventType: 'ktv' | 'dining' | 'game' | 'sports' | 'other';
  title: string;                       // 活动标题
  organizer: {
    userId: string;
    nickname: string;
    avatar: string;
    certifications: string[];
  };
  eventInfo: {
    startTime: string;                 // 活动开始时间
    endTime: string;                   // 活动结束时间
    registrationDeadline: string;      // 报名截止时间
    location: {
      address: string;                 // 具体地址
      coordinates: {lat: number, lng: number};
      distance: number;                // 距离用户的距离
    };
    pricing: {
      basePrice: number;               // 基础价格
      currency: 'CNY' | 'COINS';       // 货币类型
      pricePerPerson: boolean;         // 是否按人计费
    };
  };
  participation: {
    currentCount: number;              // 当前报名人数
    maxCount: number;                  // 最大人数限制
    genderRequirement: 'mixed' | 'male_only' | 'female_only' | 'no_limit';
    ageRequirement?: {min: number, max: number};
  };
  status: 'open' | 'full' | 'closed' | 'cancelled';
}
```

---

## 🎤 **EventCard - 活动卡片组件设计**

### 📊 **卡片结构设计**

```typescript
【卡片布局】
高度：160px
容器：白色背景 + 圆角12px + 底部分隔线1px + 内边距16px

【区域划分】
├── 🔝 卡片头部区域 (60px高度)
│   ├── 👤 左侧发起人区域 (64x64px)
│   │   ├── 发起人头像 + 认证标识
│   │   └── 发起人昵称显示
│   ├── 📝 中央活动信息区域 (活动基本信息)
│   │   ├── 活动标题 + 活动类型图标
│   │   ├── 性别要求 + 价格信息
│   │   └── 活动状态标识
│   └── 🔻 右侧时间区域 (时间倒计时)
│       ├── 开始时间显示
│       └── 报名截止倒计时
├── 📄 卡片主体区域 (80px高度)
│   ├── ⏰ 时间地点行 (活动时间 + 地点信息)
│   ├── 📍 地址距离行 (具体地址 + 距离显示)
│   ├── 👥 参与状态行 (报名人数 + 人数限制)
│   └── 💰 费用说明行 (费用详情 + 支付方式)
└── 🔻 卡片底部区域 (20px高度)
    ├── 🏷️ 左侧活动标签 (活动类型标签)
    ├── 👥 中央参与者头像 (已报名用户头像)
    └── 📊 右侧状态指示 (活动状态 + 紧急程度)
```

### 📡 **活动卡片交互设计**

```typescript
【交互行为设计】
1. 卡片整体点击
   - 点击卡片 → 跳转活动详情页面
   - 点击动画：0.2s缩放效果
   - 埋点上报：活动查看行为

2. 发起人头像点击
   - 点击头像 → 跳转发起人个人资料页面
   - 事件冒泡：阻止卡片点击事件
   - 用户画像：显示发起人信用等级

3. 参与者头像点击
   - 点击参与者头像 → 查看参与者列表
   - 头像叠加：最多显示3个参与者头像
   - 更多指示：超过3人显示"+N"指示

4. 活动状态交互
   - 报名中：绿色状态，可点击报名
   - 已满员：橙色状态，可点击排队
   - 已结束：灰色状态，不可操作
   - 已取消：红色状态，显示取消原因

【状态指示设计】
- 🟢 报名中：绿色圆点 + "报名中"文字
- 🟠 即将满员：橙色圆点 + "仅剩N个名额"
- 🔴 已满员：红色圆点 + "已满员"
- ⏰ 即将截止：黄色圆点 + "X小时后截止"
- ⭐ 热门活动：金色星星 + "热门"标识
```

---

## 📝 **PublishEventArea - 发布活动区域设计**

### 🎯 **活动发布流程**

```typescript
【发布流程设计】
1. 活动类型选择 (EventTypeSelector)
   - K歌活动：KTV包厢 + 时长 + 人数
   - 聚餐活动：餐厅 + 菜系 + 人均消费
   - 游戏活动：游戏类型 + 技能要求
   - 运动活动：运动类型 + 场地 + 装备
   - 其他活动：自定义活动类型

2. 活动信息填写 (EventInfoForm)
   - 活动标题：吸引人的活动名称
   - 活动描述：详细的活动说明
   - 活动封面：上传活动宣传图片
   - 活动标签：添加活动特色标签

3. 时间设置 (EventTimeSelector)
   - 活动开始时间：日期 + 具体时间
   - 活动持续时长：预计活动时长
   - 报名截止时间：自动计算或手动设置
   - 时间冲突检查：避免时间冲突

4. 地点设置 (EventLocationSelector)
   - 地点搜索：搜索具体场所
   - 地图选点：在地图上选择地点
   - 地址确认：确认详细地址信息
   - 交通信息：添加交通指引

5. 参与要求设置
   - 人数限制：最少/最多参与人数
   - 性别要求：男女混合/仅限男性/仅限女性
   - 年龄要求：年龄范围限制
   - 其他要求：特殊要求说明

6. 费用设置
   - 活动费用：人均费用或总费用
   - 费用说明：费用包含的内容
   - 支付方式：现场支付/在线支付
   - 退款政策：取消活动的退款规则
```

### 📋 **发布活动数据结构**

```typescript
【活动发布数据】
interface PublishEventData {
  // 活动基本信息
  eventInfo: {
    title: string;                     // 活动标题
    description: string;               // 活动描述
    type: EventType;                   // 活动类型
    coverImage?: string;               // 活动封面
    tags: string[];                    // 活动标签
  };
  
  // 时间安排
  schedule: {
    startTime: string;                 // 开始时间 ISO格式
    endTime: string;                   // 结束时间 ISO格式
    duration: number;                  // 持续时长 (分钟)
    registrationDeadline: string;      // 报名截止时间
    timezone: string;                  // 时区信息
  };
  
  // 地点信息
  location: {
    name: string;                      // 地点名称
    address: string;                   // 详细地址
    coordinates: {
      lat: number;
      lng: number;
    };
    transportation: string[];          // 交通方式
    landmarks: string[];               // 地标信息
  };
  
  // 参与要求
  requirements: {
    minParticipants: number;           // 最少人数
    maxParticipants: number;           // 最多人数
    genderRequirement: GenderRequirement;
    ageRange?: {min: number, max: number};
    skillRequirement?: string;         // 技能要求
    otherRequirements?: string;        // 其他要求
  };
  
  // 费用信息
  pricing: {
    type: 'per_person' | 'total' | 'aa_payment';
    amount: number;                    // 费用金额
    currency: 'CNY' | 'COINS';         // 货币类型
    description: string;               // 费用说明
    paymentMethod: 'online' | 'offline' | 'both';
    refundPolicy: string;              // 退款政策
  };
  
  // 发布设置
  publishSettings: {
    visibility: 'public' | 'friends' | 'private';
    autoApproval: boolean;             // 自动批准报名
    reminderEnabled: boolean;          // 活动提醒
    chatGroupEnabled: boolean;         // 自动创建群聊
  };
}
```

---

## 📊 **活动管理状态设计**

### 🔄 **EventCenterPage 状态结构**

```typescript
【组局中心状态管理】
interface EventCenterPageState {
  // 页面基础状态
  pageState: {
    loading: boolean;                  // 页面加载状态
    refreshing: boolean;               // 下拉刷新状态
    error: string | null;              // 错误信息
    lastRefreshTime: number;           // 最后刷新时间
  };
  
  // 活动列表状态
  eventList: {
    data: EventData[];                 // 活动数据列表
    hasMore: boolean;                  // 是否有更多数据
    loading: boolean;                  // 列表加载状态
    page: number;                      // 当前页码
    totalCount: number;                // 总活动数量
  };
  
  // 筛选状态
  filterState: {
    sortBy: 'latest' | 'participants' | 'start_time' | 'distance';
    genderFilter: 'no_limit' | 'male_only' | 'female_only' | 'mixed';
    advancedFilters: {
      eventTypes: string[];            // 活动类型筛选
      timeRange: {
        startDate: string;
        endDate: string;
      };
      priceRange: [number, number];    // 价格区间
      distanceRange: number;           // 距离范围
    };
  };
  
  // 发布活动状态
  publishEvent: {
    visible: boolean;                  // 发布面板显示状态
    step: number;                      // 当前发布步骤
    eventData: PublishEventData;       // 发布的活动数据
    validationErrors: Record<string, string>;  // 验证错误
    publishing: boolean;               // 发布中状态
  };
  
  // 活动详情模态状态
  eventDetail: {
    visible: boolean;                  // 详情模态显示状态
    selectedEventId: string | null;    // 选中的活动ID
    eventDetail: EventDetailData | null;  // 活动详情数据
    participants: ParticipantData[];   // 参与者列表
    loading: boolean;                  // 详情加载状态
    joinStatus: 'none' | 'pending' | 'approved' | 'rejected';  // 报名状态
  };
  
  // 用户活动状态
  userEvents: {
    publishedEvents: EventData[];      // 用户发布的活动
    joinedEvents: EventData[];         // 用户参与的活动
    favoriteEvents: string[];          // 收藏的活动ID
  };
}

【状态操作方法】
- loadEventList(refresh?: boolean)                     # 加载活动列表
- loadMoreEvents()                                     # 加载更多活动
- updateEventFilter(filters: EventFilterState)        # 更新筛选条件
- showEventDetail(eventId: string)                     # 显示活动详情
- hideEventDetail()                                    # 隐藏活动详情
- startPublishEvent()                                  # 开始发布活动
- updatePublishEventData(data: Partial<PublishEventData>)  # 更新发布数据
- submitPublishEvent()                                 # 提交发布活动
- joinEvent(eventId: string)                           # 报名参加活动
- cancelJoinEvent(eventId: string)                     # 取消报名
- favoriteEvent(eventId: string)                       # 收藏活动
```

---

## 🎤 **活动详情模态设计**

### 📊 **EventDetailModalArea - 活动详情模态区域**

```typescript
【模态组件职责】
- 展示活动的完整详细信息
- 提供报名参与功能
- 显示参与者列表和互动

【布局特征】
- 全屏模态，从底部滑入
- 可滚动内容，支持下拉关闭
- 固定底部操作区域

【主要Props】
interface EventDetailModalAreaProps {
  visible: boolean;                    // 模态显示状态
  eventDetail: EventDetailData;        // 活动详情数据
  participants: ParticipantData[];     // 参与者列表
  userJoinStatus: JoinStatus;          // 用户参与状态
  onClose: () => void;                 // 关闭模态
  onJoinEvent: () => void;             // 报名参加
  onCancelJoin: () => void;            // 取消报名
  onContactOrganizer: () => void;      // 联系发起人
}

【详情展示内容】
1. 活动基本信息
   - 活动标题和类型
   - 发起人信息和认证
   - 活动时间和地点
   - 活动状态和紧急程度

2. 活动详细描述
   - 活动详细说明
   - 活动亮点和特色
   - 注意事项和要求
   - 活动图片展示

3. 参与者信息
   - 已报名用户列表
   - 用户头像和昵称
   - 用户认证状态
   - 报名时间顺序

4. 地点信息
   - 详细地址显示
   - 地图定位展示
   - 交通路线指引
   - 周边环境介绍

5. 费用明细
   - 活动费用构成
   - 支付方式说明
   - 退款政策说明
   - 优惠活动信息
```

---

## 🚀 **活动生命周期管理**

### ⏰ **活动时间管理**

```typescript
【时间状态管理】
1. 活动时间计算
   - 开始时间倒计时
   - 报名截止倒计时
   - 活动进行状态
   - 活动结束处理

2. 时间显示格式
   - 今天：显示具体时间 "今天 19:00"
   - 明天：显示 "明天 19:00"
   - 本周：显示 "周六 19:00"
   - 其他：显示 "12月25日 19:00"

3. 紧急状态提示
   - 即将开始：开始前1小时高亮显示
   - 即将截止：截止前2小时高亮显示
   - 人数不足：开始前4小时提示人数不足
   - 已满员：达到人数上限时显示满员状态

【时间自动更新】
- 实时倒计时更新
- 状态自动切换
- 过期活动自动隐藏
- 时间冲突检测
```

### 👥 **参与者管理**

```typescript
【参与者状态管理】
1. 报名状态跟踪
   - 待审核：发起人审核中
   - 已通过：报名成功
   - 已拒绝：报名被拒绝
   - 已取消：用户主动取消

2. 人数控制
   - 最少人数：活动成行的最少人数
   - 最多人数：活动人数上限
   - 候补名单：满员后的候补机制
   - 自动批准：满足条件自动批准报名

3. 参与者互动
   - 参与者群聊：自动创建活动群聊
   - 参与者资料：查看其他参与者信息
   - 参与者评价：活动结束后相互评价
   - 参与者屏蔽：屏蔽不合适的参与者

【参与者数据结构】
interface ParticipantData {
  userId: string;
  nickname: string;
  avatar: string;
  joinTime: string;                    // 报名时间
  status: 'pending' | 'approved' | 'rejected' | 'cancelled';
  paymentStatus: 'unpaid' | 'paid' | 'refunded';
  specialRequests?: string;            // 特殊要求
  contactInfo?: {
    phone?: string;
    wechat?: string;
  };
}
```

---

## 📍 **地理位置集成设计**

### 🗺️ **活动地点管理**

```typescript
【地点功能设计】
1. 地点选择方式
   - 搜索选择：搜索具体场所名称
   - 地图选点：在地图上直接选择
   - 历史地点：使用之前用过的地点
   - 推荐地点：基于活动类型推荐

2. 地点信息展示
   - 地点名称：场所名称 + 品牌信息
   - 详细地址：完整的地址信息
   - 距离显示：距离用户的距离
   - 交通指引：地铁/公交/自驾路线

3. 地点服务集成
   - 营业时间：查询场所营业时间
   - 价格信息：查询场所价格信息
   - 预订状态：查询场所预订情况
   - 用户评价：显示场所用户评价

【地图集成功能】
- 地图显示：高德地图/百度地图集成
- 路线规划：自动规划最佳路线
- 实时导航：支持一键导航
- 周边搜索：搜索周边相关设施
```

### 📊 **距离计算服务**

```typescript
【距离计算功能】
1. 实时距离计算
   - GPS定位获取用户位置
   - 计算用户到活动地点的距离
   - 考虑交通方式的实际距离
   - 实时更新距离信息

2. 距离显示策略
   - 500米内：显示步行时间
   - 2km内：显示骑行时间
   - 10km内：显示公交时间
   - 更远：显示自驾时间

3. 距离筛选支持
   - 按距离排序活动列表
   - 距离范围筛选
   - 交通便利程度评分
   - 路线复杂度提示

【位置权限管理】
- 位置权限请求
- 权限拒绝降级方案
- 位置精度等级
- 位置隐私保护
```

---

## 💰 **活动支付系统设计**

### 💳 **支付流程管理**

```typescript
【活动支付设计】
1. 支付类型
   - 报名费：报名时支付的费用
   - 活动费：活动现场支付的费用
   - 保证金：防止恶意报名的保证金
   - 平台费：平台服务费用

2. 支付方式
   - 在线支付：微信支付/支付宝/银行卡
   - 钱包支付：平台内部钱包余额
   - 现场支付：活动现场AA或统一支付
   - 组合支付：部分在线 + 部分现场

3. 支付状态管理
   - 待支付：报名成功待支付
   - 已支付：支付完成
   - 支付失败：支付异常处理
   - 已退款：取消活动退款

【支付安全设计】
- 支付密码验证
- 支付限额控制
- 异常交易监控
- 退款流程自动化
```

### 📊 **费用计算系统**

```typescript
【费用计算逻辑】
1. 基础费用计算
   - 人均费用 × 参与人数
   - 场地费用分摊
   - 额外服务费用
   - 平台服务费

2. 优惠计算
   - 新用户优惠
   - 活动满减优惠
   - 会员折扣
   - 推荐奖励

3. 费用分摊
   - AA制分摊计算
   - 发起人优惠
   - 不同参与者不同费用
   - 费用明细透明化

【费用数据结构】
interface EventPricing {
  basePrice: number;                   // 基础价格
  perPersonPrice: number;              // 人均价格
  platformFee: number;                 // 平台费用
  totalPrice: number;                  // 总价格
  discounts: Array<{
    type: string;
    amount: number;
    description: string;
  }>;
  finalPrice: number;                  // 最终价格
}
```

---

## ✅ **质量保证标准**

### 🎯 **功能完整性检查**
- [ ] 活动列表展示正常
- [ ] 活动筛选功能完整
- [ ] 活动发布流程完整
- [ ] 活动报名功能正常
- [ ] 活动详情展示完整
- [ ] 支付流程安全可靠

### 🎤 **活动管理检查**
- [ ] 活动时间管理准确
- [ ] 参与者管理完善
- [ ] 地理位置功能正常
- [ ] 费用计算准确
- [ ] 活动状态同步及时
- [ ] 活动通知推送正常

### 🎨 **用户体验检查**
- [ ] 活动信息展示清晰
- [ ] 发布流程简洁易懂
- [ ] 报名操作便捷快速
- [ ] 活动搜索功能好用
- [ ] 错误处理友好
- [ ] 活动提醒及时

### 📱 **技术性能检查**
- [ ] 活动列表加载性能良好
- [ ] 地图集成性能优化
- [ ] 实时更新机制稳定
- [ ] 支付安全措施完善
- [ ] 数据同步准确
- [ ] 内存使用控制合理

---

## 🚀 **下一步计划**

### 📋 **第2层设计继续**
完成 EventCenterPage 详细设计后，接下来的第2层设计：
1. **LocationPage 详细设计** - 位置服务页面组件设计
2. **FeaturedPage 详细设计** - 限时专享页面组件设计
3. **RegionPage 详细设计** - 区域选择页面组件设计

### 🎯 **EventCenterPage 优化方向**
根据审核反馈，可能的优化方向：
- 活动发布流程优化
- 活动管理功能增强
- 地理位置集成优化
- 支付安全机制强化
- 用户体验流程优化

---

## 📞 **审核和反馈**

**当前状态**：等待 EventCenterPage 第2层设计审核
**反馈方式**：请对以下方面提供意见
- 组局中心页面整体设计是否合理
- 活动发布和管理流程是否完善
- 活动卡片和详情展示是否清晰
- 地理位置和时间管理是否合适
- 支付和参与者管理是否安全
- 是否需要调整某些功能或组件

**修改计划**：根据审核反馈进行设计调整，确保 EventCenterPage 设计达到理想状态后再继续其他页面的第2层设计。

---

**© 2025 第2层 EventCenterPage 组局中心页面详细设计文档 - Expo + Zustand 技术栈**
