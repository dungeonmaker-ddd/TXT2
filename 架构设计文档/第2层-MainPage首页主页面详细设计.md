# 📱 第2层 - MainPage 首页主页面详细设计文档 v1.0

> **基于第1层架构的首页主页面具体实施方案 - Expo + Zustand 技术栈**

---

## 📋 **文档信息**

| 项目信息 | 详细内容 |
|---------|---------|
| **文档层级** | 第2层 - 模块详细设计 |
| **设计版本** | v1.0 |
| **所属模块** | Homepage/MainPage |
| **技术栈** | Expo + React Native + Zustand |
| **设计目标** | 首页主页面的具体组件和交互设计 |
| **依赖文档** | 第1层宏观架构设计 + 共享组件库 |

---

## 🎯 **MainPage 页面职责定义**

### 🏠 **核心职责**
- **应用主入口页面** - 用户进入应用后的第一个页面
- **功能导航中心** - 提供所有核心功能的入口
- **用户发现平台** - 展示推荐用户和精选内容
- **状态信息展示** - 显示位置、搜索、筛选等状态信息

### 🎨 **设计原则**
- **配置驱动** - 所有UI和内容通过后端配置控制
- **性能优先** - 优化首屏加载和滚动性能
- **体验一致** - 统一的视觉风格和交互体验
- **状态同步** - 实时同步用户状态和数据更新

---

## 📂 **MainPage 文件结构设计**

### 🏗️ **页面文件组织**

```
src/features/Homepage/MainPage/
├── index.tsx                           # 🎯 主页面文件 (单文件集中管理)
├── types.ts                            # 📋 页面类型定义
├── constants.ts                        # ⚙️ 页面常量配置
├── styles.ts                           # 🎨 页面样式定义
└── README.md                           # 📖 页面文档

components/ (页面区域组件)
├── TopFunctionArea/                    # 🔝 顶部功能区域
│   ├── index.tsx                       # 区域主文件
│   ├── LocationSelector.tsx            # 位置选择组件
│   ├── SearchBarContainer.tsx          # 搜索栏容器
│   └── styles.ts                       # 区域样式
├── GameBannerArea/                     # 🎮 游戏横幅区域
│   ├── index.tsx                       # 区域主文件
│   ├── PromotionalBanner.tsx           # 宣传横幅组件
│   └── styles.ts                       # 区域样式
├── ServiceGridArea/                    # 🏷️ 服务网格区域
│   ├── index.tsx                       # 区域主文件
│   ├── ServiceIconGrid.tsx             # 服务图标网格
│   └── styles.ts                       # 区域样式
├── FeaturedUsersArea/                  # 🔥 限时专享区域
│   ├── index.tsx                       # 区域主文件
│   ├── SectionHeader.tsx               # 区域标题组件
│   ├── FeaturedUserCarousel.tsx        # 专享用户轮播
│   └── styles.ts                       # 区域样式
├── EventCenterArea/                    # 🎯 组队聚会区域
│   ├── index.tsx                       # 区域主文件
│   ├── EventPromoBanner.tsx            # 活动宣传横幅
│   └── styles.ts                       # 区域样式
├── UserListArea/                       # 📋 用户列表区域
│   ├── index.tsx                       # 区域主文件
│   ├── FilterToolbar.tsx               # 筛选工具栏
│   ├── UserInfiniteList.tsx            # 用户无限列表
│   └── styles.ts                       # 区域样式
└── BottomNavigationArea/               # 📱 底部导航区域
    ├── index.tsx                       # 区域主文件
    ├── TabNavigationBar.tsx            # Tab导航栏
    └── styles.ts                       # 区域样式
```

---

## 🎯 **MainPage 主文件设计**

### 📝 **index.tsx 八段式结构**

```typescript
// #region 1. File Banner & TOC
/**
 * MainPage - 首页主页面
 * 
 * TOC (快速跳转):
 * [1] Imports
 * [2] Types & Schema  
 * [3] Constants & Config
 * [4] Utils & Helpers
 * [5] State Management
 * [6] Domain Logic
 * [7] UI Components & Rendering
 * [8] Exports
 */
// #endregion

// #region 2. Imports
import React, { useEffect, useCallback, useMemo } from 'react';
import { ScrollView, RefreshControl, StatusBar } from 'react-native';
import { useFocusEffect } from '@react-navigation/native';
import { useRouter } from 'expo-router';

// Zustand状态管理
import { useHomepageStore } from '../../../stores/homepageStore';
import { useUserStore } from '../../../stores/userStore';
import { useLocationStore } from '../../../stores/locationStore';
import { useConfigStore } from '../../../stores/configStore';

// 区域组件
import TopFunctionArea from './components/TopFunctionArea';
import GameBannerArea from './components/GameBannerArea';
import ServiceGridArea from './components/ServiceGridArea';
import FeaturedUsersArea from './components/FeaturedUsersArea';
import EventCenterArea from './components/EventCenterArea';
import UserListArea from './components/UserListArea';
import BottomNavigationArea from './components/BottomNavigationArea';

// 共享组件
import LoadingOverlay from '../../../components/LoadingOverlay';
import ErrorBoundary from '../../../components/ErrorBoundary';

// 类型和常量
import { MainPageProps } from './types';
import { MAIN_PAGE_CONSTANTS } from './constants';
import { styles } from './styles';
// #endregion

// #region 3. Types & Schema
interface MainPageState {
  loading: boolean;
  refreshing: boolean;
  error: string | null;
  lastRefreshTime: number;
}

interface PageSection {
  id: string;
  name: string;
  component: React.ComponentType;
  enabled: boolean;
  loading: boolean;
}
// #endregion

// #region 4. Constants & Config
const REFRESH_THRESHOLD = 5 * 60 * 1000; // 5分钟
const SCROLL_THRESHOLD = 100;
const PAGE_SECTIONS = [
  'topFunction',
  'gameBanner', 
  'serviceGrid',
  'featuredUsers',
  'eventCenter',
  'userList'
] as const;
// #endregion

// #region 5. Utils & Helpers
/**
 * 检查是否需要刷新数据
 */
const shouldRefreshData = (lastRefreshTime: number): boolean => {
  return Date.now() - lastRefreshTime > REFRESH_THRESHOLD;
};

/**
 * 格式化错误信息
 */
const formatErrorMessage = (error: any): string => {
  if (typeof error === 'string') return error;
  if (error?.message) return error.message;
  return '页面加载失败，请重试';
};

/**
 * 生成页面埋点数据
 */
const generateTrackingData = (section: string, action: string) => ({
  page: 'main_page',
  section,
  action,
  timestamp: Date.now()
});
// #endregion

// #region 6. State Management
/**
 * 主页面状态管理Hook
 */
const useMainPageState = () => {
  // Zustand stores
  const {
    pageConfig,
    pageData,
    userInteraction,
    loadPageData,
    updateUserInteraction,
    resetPageState
  } = useHomepageStore();
  
  const {
    userList,
    loadUserList,
    loadMoreUsers
  } = useUserStore();
  
  const {
    currentLocation,
    updateLocation
  } = useLocationStore();
  
  const {
    componentConfigs,
    loadComponentConfig
  } = useConfigStore();
  
  // 本地状态
  const [localState, setLocalState] = React.useState<MainPageState>({
    loading: true,
    refreshing: false,
    error: null,
    lastRefreshTime: 0
  });
  
  // 页面配置计算
  const pageConfiguration = useMemo(() => ({
    sections: PAGE_SECTIONS.map(sectionId => ({
      id: sectionId,
      enabled: pageConfig?.[sectionId]?.enabled ?? true,
      loading: pageData?.[sectionId]?.loading ?? false
    }))
  }), [pageConfig, pageData]);
  
  // 页面数据状态
  const pageDataState = useMemo(() => ({
    hasData: !!pageData && Object.keys(pageData).length > 0,
    totalSections: PAGE_SECTIONS.length,
    loadedSections: PAGE_SECTIONS.filter(id => pageData?.[id]).length
  }), [pageData]);
  
  return {
    // 状态
    localState,
    setLocalState,
    pageConfiguration,
    pageDataState,
    userList,
    currentLocation,
    componentConfigs,
    
    // 操作方法
    loadPageData,
    updateUserInteraction,
    loadUserList,
    loadMoreUsers,
    updateLocation,
    loadComponentConfig,
    resetPageState
  };
};
// #endregion

// #region 7. Domain Logic
/**
 * 主页面业务逻辑Hook
 */
const useMainPageLogic = () => {
  const router = useRouter();
  const {
    localState,
    setLocalState,
    pageDataState,
    loadPageData,
    loadUserList,
    updateLocation,
    loadComponentConfig
  } = useMainPageState();
  
  /**
   * 初始化页面数据
   */
  const initializePageData = useCallback(async () => {
    try {
      setLocalState(prev => ({ ...prev, loading: true, error: null }));
      
      // 并行加载页面数据
      await Promise.all([
        loadComponentConfig('main-page'),
        loadPageData(),
        loadUserList({ page: 1, limit: 20 }),
        updateLocation()
      ]);
      
      setLocalState(prev => ({ 
        ...prev, 
        loading: false,
        lastRefreshTime: Date.now()
      }));
      
    } catch (error) {
      setLocalState(prev => ({ 
        ...prev, 
        loading: false,
        error: formatErrorMessage(error)
      }));
    }
  }, [loadComponentConfig, loadPageData, loadUserList, updateLocation]);
  
  /**
   * 下拉刷新处理
   */
  const handleRefresh = useCallback(async () => {
    setLocalState(prev => ({ ...prev, refreshing: true }));
    
    try {
      await initializePageData();
    } finally {
      setLocalState(prev => ({ ...prev, refreshing: false }));
    }
  }, [initializePageData]);
  
  /**
   * 导航处理
   */
  const handleNavigation = useCallback((route: string, params?: object) => {
    router.push({ pathname: route, params });
  }, [router]);
  
  /**
   * 错误重试处理
   */
  const handleRetry = useCallback(() => {
    initializePageData();
  }, [initializePageData]);
  
  /**
   * 页面焦点处理
   */
  const handlePageFocus = useCallback(() => {
    // 检查是否需要刷新数据
    if (shouldRefreshData(localState.lastRefreshTime)) {
      initializePageData();
    }
  }, [localState.lastRefreshTime, initializePageData]);
  
  return {
    // 状态
    loading: localState.loading,
    refreshing: localState.refreshing,
    error: localState.error,
    pageDataState,
    
    // 操作方法
    initializePageData,
    handleRefresh,
    handleNavigation,
    handleRetry,
    handlePageFocus
  };
};
// #endregion

// #region 8. UI Components & Rendering
/**
 * MainPage 主组件
 */
const MainPage: React.FC<MainPageProps> = (props) => {
  const {
    loading,
    refreshing,
    error,
    pageDataState,
    initializePageData,
    handleRefresh,
    handleNavigation,
    handleRetry,
    handlePageFocus
  } = useMainPageLogic();
  
  // 页面生命周期
  useEffect(() => {
    initializePageData();
  }, []);
  
  // 页面焦点处理
  useFocusEffect(
    useCallback(() => {
      handlePageFocus();
    }, [handlePageFocus])
  );
  
  // 错误状态渲染
  if (error && !pageDataState.hasData) {
    return (
      <ErrorBoundary
        error={error}
        onRetry={handleRetry}
        style={styles.errorContainer}
      />
    );
  }
  
  // 加载状态渲染
  if (loading && !pageDataState.hasData) {
    return (
      <LoadingOverlay
        loading={loading}
        text="正在加载首页..."
        style={styles.loadingContainer}
      />
    );
  }
  
  return (
    <ErrorBoundary>
      <StatusBar 
        barStyle="light-content" 
        backgroundColor={MAIN_PAGE_CONSTANTS.COLORS.PRIMARY}
      />
      
      <ScrollView
        style={styles.container}
        contentContainerStyle={styles.contentContainer}
        showsVerticalScrollIndicator={false}
        refreshControl={
          <RefreshControl
            refreshing={refreshing}
            onRefresh={handleRefresh}
            tintColor={MAIN_PAGE_CONSTANTS.COLORS.PRIMARY}
            title="下拉刷新"
          />
        }
      >
        {/* 顶部功能区域 */}
        <TopFunctionArea
          onLocationPress={() => handleNavigation('/homepage/location')}
          onSearchPress={() => handleNavigation('/homepage/search')}
        />
        
        {/* 游戏横幅区域 */}
        <GameBannerArea
          onBannerPress={(gameId) => 
            handleNavigation('/homepage/service-detail', { serviceType: gameId })
          }
        />
        
        {/* 服务网格区域 */}
        <ServiceGridArea
          onServicePress={(serviceType) =>
            handleNavigation('/homepage/service-detail', { serviceType })
          }
        />
        
        {/* 限时专享区域 */}
        <FeaturedUsersArea
          onUserPress={(userId) =>
            handleNavigation('/modal/user-detail', { userId })
          }
          onMorePress={() => handleNavigation('/homepage/featured')}
        />
        
        {/* 组队聚会区域 */}
        <EventCenterArea
          onEventPress={() => handleNavigation('/homepage/event-center')}
        />
        
        {/* 用户列表区域 */}
        <UserListArea
          onUserPress={(userId) =>
            handleNavigation('/modal/user-detail', { userId })
          }
          onFilterPress={() => handleNavigation('/homepage/filter-online')}
        />
      </ScrollView>
      
      {/* 底部导航区域 */}
      <BottomNavigationArea />
    </ErrorBoundary>
  );
};
// #endregion

// #region 9. Exports
export default MainPage;
export type { MainPageProps, MainPageState };
// #endregion
```

---

## 🧩 **区域组件设计概览**

### 🔝 **TopFunctionArea - 顶部功能区域**

```typescript
【组件职责】
- 系统状态栏管理 (iOS适配)
- 位置信息显示和选择
- 搜索功能入口

【主要Props】
interface TopFunctionAreaProps {
  onLocationPress: () => void;
  onSearchPress: () => void;
  style?: StyleProp<ViewStyle>;
}

【组件状态】
- 当前位置信息
- 搜索框状态
- 定位权限状态

【关键交互】
- 点击位置 → 跳转定位页面
- 点击搜索 → 跳转搜索页面
- 系统状态栏样式管理
```

### 🎮 **GameBannerArea - 游戏横幅区域**

```typescript
【组件职责】
- 展示主推游戏内容
- 动态横幅管理
- 点击跳转处理

【主要Props】
interface GameBannerAreaProps {
  onBannerPress: (gameId: string) => void;
  style?: StyleProp<ViewStyle>;
}

【组件状态】
- 横幅配置数据
- 图片加载状态
- 点击统计数据

【关键交互】
- 点击横幅 → 跳转服务详情页
- 图片懒加载处理
- 埋点数据上报
```

### 🏷️ **ServiceGridArea - 服务网格区域**

```typescript
【组件职责】
- 2x5服务图标网格展示
- 服务状态管理
- 动态配置支持

【主要Props】
interface ServiceGridAreaProps {
  onServicePress: (serviceType: string) => void;
  style?: StyleProp<ViewStyle>;
}

【组件状态】
- 服务配置列表
- 图标加载状态
- 服务可用性状态

【关键交互】
- 点击服务图标 → 跳转服务详情页
- 服务状态指示
- 网格布局自适应
```

### 🔥 **FeaturedUsersArea - 限时专享区域**

```typescript
【组件职责】
- 精选用户轮播展示
- 区域标题管理
- 水平滚动处理

【主要Props】
interface FeaturedUsersAreaProps {
  onUserPress: (userId: string) => void;
  onMorePress: () => void;
  style?: StyleProp<ViewStyle>;
}

【组件状态】
- 精选用户数据
- 滚动位置状态
- 加载更多状态

【关键交互】
- 点击用户卡片 → 跳转用户详情模态
- 点击更多 → 跳转限时专享页面
- 水平滚动优化
```

### 📋 **UserListArea - 用户列表区域**

```typescript
【组件职责】
- 用户列表展示
- 筛选工具栏管理
- 无限滚动处理

【主要Props】
interface UserListAreaProps {
  onUserPress: (userId: string) => void;
  onFilterPress: () => void;
  style?: StyleProp<ViewStyle>;
}

【组件状态】
- 用户列表数据
- 筛选条件状态
- 分页加载状态

【关键交互】
- 点击用户 → 跳转用户详情模态
- 点击筛选 → 跳转筛选页面
- 上滑加载更多
- 筛选条件切换
```

---

## 🔄 **状态管理集成**

### 📊 **Zustand Store 集成**

```typescript
// 主页面使用的 Store
const useMainPageStores = () => {
  // 首页状态
  const homepageStore = useHomepageStore(state => ({
    pageConfig: state.pageConfig,
    pageData: state.pageData,
    userInteraction: state.userInteraction,
    loadPageData: state.loadPageData,
    updateUserInteraction: state.updateUserInteraction
  }));
  
  // 用户数据状态
  const userStore = useUserStore(state => ({
    userList: state.userList,
    loadUserList: state.loadUserList,
    loadMoreUsers: state.loadMoreUsers
  }));
  
  // 位置状态
  const locationStore = useLocationStore(state => ({
    currentLocation: state.currentLocation,
    updateLocation: state.updateLocation
  }));
  
  // 配置状态
  const configStore = useConfigStore(state => ({
    componentConfigs: state.componentConfigs,
    loadComponentConfig: state.loadComponentConfig
  }));
  
  return {
    homepageStore,
    userStore,
    locationStore,
    configStore
  };
};
```

### 🔄 **状态同步策略**

```typescript
// 页面数据同步
const syncPageData = async () => {
  // 1. 优先级加载
  await loadComponentConfig('main-page');
  
  // 2. 并行数据加载
  const [pageData, userData, locationData] = await Promise.all([
    loadPageData(),
    loadUserList({ page: 1, limit: 20 }),
    updateLocation()
  ]);
  
  // 3. 状态更新
  updatePageState(pageData, userData, locationData);
};

// 实时状态更新
const useRealtimeUpdates = () => {
  useEffect(() => {
    // WebSocket 连接用于实时更新
    const ws = new WebSocket(WS_URL);
    
    ws.onmessage = (event) => {
      const update = JSON.parse(event.data);
      
      switch (update.type) {
        case 'USER_LIST_UPDATE':
          updateUserList(update.data);
          break;
        case 'FEATURED_USERS_UPDATE':
          updateFeaturedUsers(update.data);
          break;
        case 'CONFIG_UPDATE':
          updateComponentConfig(update.data);
          break;
      }
    };
    
    return () => ws.close();
  }, []);
};
```

---

## 🎨 **样式设计系统**

### 📐 **layouts 布局样式**

```typescript
// styles.ts
export const styles = StyleSheet.create({
  // 容器样式
  container: {
    flex: 1,
    backgroundColor: '#FFFFFF',
  },
  
  contentContainer: {
    paddingBottom: 100, // 底部导航空间
  },
  
  // 错误状态样式
  errorContainer: {
    flex: 1,
    justifyContent: 'center',
    alignItems: 'center',
    backgroundColor: '#FFFFFF',
    padding: 20,
  },
  
  // 加载状态样式
  loadingContainer: {
    flex: 1,
    backgroundColor: '#FFFFFF',
  },
  
  // 区域间距样式
  sectionSpacing: {
    marginBottom: 20,
  },
  
  // 区域分隔样式
  sectionDivider: {
    height: 8,
    backgroundColor: '#F5F5F5',
  }
});
```

### 🎯 **响应式设计**

```typescript
// 屏幕适配
const { width, height } = Dimensions.get('window');

export const RESPONSIVE_SIZES = {
  // 基于屏幕宽度的动态尺寸
  serviceGrid: {
    itemWidth: (width - 32 - 64) / 5, // 减去边距和间距
    itemHeight: 80,
    spacing: 16
  },
  
  // 基于屏幕高度的动态尺寸
  banner: {
    height: height * 0.22, // 屏幕高度的22%
    maxHeight: 200,
    minHeight: 160
  },
  
  // 安全区域适配
  safeArea: {
    top: Platform.OS === 'ios' ? 44 : StatusBar.currentHeight || 0,
    bottom: Platform.OS === 'ios' ? 34 : 0
  }
};
```

---

## 🚀 **性能优化策略**

### ⚡ **渲染优化**

```typescript
// 组件记忆化
const MemoizedTopFunctionArea = React.memo(TopFunctionArea);
const MemoizedServiceGridArea = React.memo(ServiceGridArea);
const MemoizedUserListArea = React.memo(UserListArea);

// 列表优化
const optimizedListProps = {
  removeClippedSubviews: true,
  maxToRenderPerBatch: 10,
  updateCellsBatchingPeriod: 50,
  initialNumToRender: 20,
  windowSize: 10,
  getItemLayout: (data, index) => ({
    length: 120,
    offset: 120 * index,
    index
  })
};
```

### 💾 **数据缓存策略**

```typescript
// 页面级缓存
const usePageCache = () => {
  return useMemo(() => ({
    // 缓存配置
    cacheConfig: {
      ttl: 5 * 60 * 1000, // 5分钟
      maxSize: 100,
      strategy: 'lru'
    },
    
    // 缓存键生成
    generateCacheKey: (type: string, params: any) => 
      `main_page_${type}_${JSON.stringify(params)}`,
    
    // 缓存验证
    validateCache: (cacheTime: number) => 
      Date.now() - cacheTime < 5 * 60 * 1000
  }), []);
};
```

---

## ✅ **质量保证标准**

### 🎯 **功能完整性检查**
- [ ] 所有区域组件正常渲染
- [ ] 状态管理集成正确
- [ ] 路由跳转功能正常
- [ ] 下拉刷新功能正常
- [ ] 错误处理机制完善
- [ ] 加载状态显示正确

### 🎨 **用户体验检查**
- [ ] 首屏加载时间 < 2秒
- [ ] 滚动性能流畅 (60fps)
- [ ] 交互反馈及时 (< 200ms)
- [ ] 错误提示友好清晰
- [ ] 空状态处理得当
- [ ] 网络异常处理完善

### 🔧 **代码质量检查**
- [ ] 严格遵循八段式结构
- [ ] TypeScript类型定义完整
- [ ] 组件职责边界清晰
- [ ] 性能优化措施到位
- [ ] 错误边界覆盖完整
- [ ] 可测试性设计良好

---

## 📋 **下一步计划**

完成 MainPage 详细设计后，接下来的第2层设计：
1. **ServiceDetailPage 详细设计** - 通用服务页面组件设计
2. **SearchPage 详细设计** - 搜索功能组件设计  
3. **FilterPage 详细设计** - 筛选功能组件设计
4. **区域组件详细设计** - 各个区域组件的具体实现设计

---

**© 2025 第2层 MainPage 首页主页面详细设计文档 - Expo + Zustand 技术栈**
