<template>
  <div class="left-panel">
    <!-- 收起状态下的触发器 -->
    <div v-show="!isExpanded" class="panel-trigger" @click="togglePanel">
      <NIcon size="20" :component="MenuOutline" />
    </div>

    <!-- 展开状态下的悬浮面板 -->
    <Transition name="panel-slide">
      <div v-show="isExpanded" class="floating-panel">
        <!-- 面板头部 -->
        <div class="panel-header">
          <span>工具面板</span>
          <NButton quaternary circle size="small" @click="togglePanel">
            <template #icon>
              <NIcon :component="ChevronBack" />
            </template>
          </NButton>
        </div>

        <!-- 上栏：分组标签 -->
        <div class="group-tabs">
          <div
            v-for="group in panelConfig"
            :key="group.key"
            class="group-tab"
            :class="{ active: activeGroupKey === group.key }"
            @click="selectGroup(group.key)"
          >
            <NIcon :component="group.icon" size="16" />
            <span>{{ group.title }}</span>
          </div>
        </div>

        <!-- 下栏：当前分组的功能列表 -->
        <div class="panel-content">
          <Transition name="function-fade" mode="out-in">
            <div v-if="activeGroup" :key="activeGroupKey" class="function-list">
              <NButton
                v-for="func in activeGroup.functions"
                :key="func.key"
                :type="func.active ? 'primary' : 'default'"
                :ghost="!func.active"
                block
                class="function-item"
                @click="handleFunctionClick(func)"
              >
                <template #icon>
                  <NIcon :component="func.icon" />
                </template>
                {{ func.title }}
              </NButton>
            </div>
          </Transition>
        </div>
      </div>
    </Transition>
  </div>
</template>

<script setup lang="ts">
  import { ref, reactive, computed } from "vue";
  import { NIcon, NButton } from "naive-ui";
  import {
    MenuOutline,
    ChevronBack,
    LayersOutline,
    LocationOutline,
    SettingsOutline,
    CameraOutline,
    MapOutline,
    TrendingUpOutline,
    ColorPaletteOutline,
    CloudOutline,
  } from "@vicons/ionicons5";
  import type { PanelFunction, PanelGroup } from "@/types/panel";

  // 定义事件
  const emit = defineEmits<{
    functionClick: [func: PanelFunction];
  }>();

  // 面板展开状态
  const isExpanded = ref(false);
  // 当前选中的分组
  const activeGroupKey = ref("layers");
  // 计算当前活跃的分组
  const activeGroup = computed(() => panelConfig.find(group => group.key === activeGroupKey.value));

  // 面板配置（可配置的数据结构）- 扩展为更多分组
  const panelConfig = reactive<PanelGroup[]>([
    {
      key: "layers",
      title: "图层",
      icon: LayersOutline,
      functions: [
        {
          key: "terrain",
          title: "地形图层",
          icon: MapOutline,
          active: false,
          action: () => handleTerrainToggle(),
        },
        {
          key: "imagery",
          title: "影像图层",
          icon: CameraOutline,
          active: true,
          action: () => handleImageryToggle(),
        },
        {
          key: "weather",
          title: "天气图层",
          icon: CloudOutline,
          active: false,
          action: () => handleWeatherToggle(),
        },
        {
          key: "vector",
          title: "矢量图层",
          icon: MapOutline,
          active: false,
          action: () => console.log("切换矢量图层"),
        },
      ],
    },
    {
      key: "tools",
      title: "工具",
      icon: TrendingUpOutline,
      functions: [
        {
          key: "measure",
          title: "测量工具",
          icon: LocationOutline,
          active: false,
          action: () => handleMeasureTool(),
        },
        {
          key: "analysis",
          title: "空间分析",
          icon: TrendingUpOutline,
          active: false,
          action: () => handleSpatialAnalysis(),
        },
        {
          key: "buffer",
          title: "缓冲区分析",
          icon: LocationOutline,
          active: false,
          action: () => console.log("缓冲区分析"),
        },
      ],
    },
    {
      key: "style",
      title: "样式",
      icon: ColorPaletteOutline,
      functions: [
        {
          key: "theme",
          title: "主题切换",
          icon: ColorPaletteOutline,
          active: false,
          action: () => handleThemeToggle(),
        },
        {
          key: "settings",
          title: "显示设置",
          icon: SettingsOutline,
          active: false,
          action: () => handleDisplaySettings(),
        },
        {
          key: "lighting",
          title: "光照设置",
          icon: SettingsOutline,
          active: false,
          action: () => console.log("光照设置"),
        },
      ],
    },
    {
      key: "data",
      title: "数据",
      icon: CloudOutline,
      functions: [
        {
          key: "import",
          title: "数据导入",
          icon: CloudOutline,
          active: false,
          action: () => console.log("数据导入"),
        },
        {
          key: "export",
          title: "数据导出",
          icon: CloudOutline,
          active: false,
          action: () => console.log("数据导出"),
        },
        {
          key: "filter",
          title: "数据筛选",
          icon: TrendingUpOutline,
          active: false,
          action: () => console.log("数据筛选"),
        },
      ],
    },
    {
      key: "view",
      title: "视图",
      icon: CameraOutline,
      functions: [
        {
          key: "bookmark",
          title: "视点收藏",
          icon: CameraOutline,
          active: false,
          action: () => console.log("视点收藏"),
        },
        {
          key: "navigation",
          title: "导航控制",
          icon: LocationOutline,
          active: false,
          action: () => console.log("导航控制"),
        },
        {
          key: "screenshot",
          title: "截图保存",
          icon: CameraOutline,
          active: false,
          action: () => console.log("截图保存"),
        },
      ],
    },
    {
      key: "effects",
      title: "特效",
      icon: CloudOutline,
      functions: [
        {
          key: "particles",
          title: "粒子效果",
          icon: CloudOutline,
          active: false,
          action: () => console.log("粒子效果"),
        },
        {
          key: "postprocess",
          title: "后处理",
          icon: ColorPaletteOutline,
          active: false,
          action: () => console.log("后处理"),
        },
        {
          key: "animation",
          title: "动画效果",
          icon: TrendingUpOutline,
          active: false,
          action: () => console.log("动画效果"),
        },
      ],
    },
  ]);

  // 切换面板展开/收起
  const togglePanel = () => {
    isExpanded.value = !isExpanded.value;
  };

  // 选择分组
  const selectGroup = (groupKey: string) => {
    activeGroupKey.value = groupKey;
  };

  // 处理功能点击
  const handleFunctionClick = (func: PanelFunction) => {
    // 切换功能状态
    func.active = !func.active;

    // 执行功能逻辑
    if (func.action) {
      func.action();
    }

    // 向父组件发送事件
    emit("functionClick", func);
  };

  // 具体功能处理函数（示例）
  const handleTerrainToggle = () => {
    console.log("切换地形图层");
  };

  const handleImageryToggle = () => {
    console.log("切换影像图层");
  };

  const handleWeatherToggle = () => {
    console.log("切换天气图层");
  };

  const handleMeasureTool = () => {
    console.log("启用测量工具");
  };

  const handleSpatialAnalysis = () => {
    console.log("启用空间分析");
  };

  const handleThemeToggle = () => {
    console.log("切换主题");
  };

  const handleDisplaySettings = () => {
    console.log("打开显示设置");
  };

  // 暴露配置接口供父组件使用
  defineExpose({
    panelConfig,
    togglePanel,
    isExpanded,
  });
</script>

<style scoped>
  .left-panel {
    position: fixed;
    top: 0;
    left: 0;
    height: 100vh;
    z-index: 1000;
    pointer-events: none;
  }

  /* 触发器样式 - 黑色主题 */
  .panel-trigger {
    position: absolute;
    top: 20px;
    left: 20px;
    width: 48px;
    height: 48px;
    background: linear-gradient(135deg, rgba(30, 30, 30, 0.95), rgba(20, 20, 20, 0.9));
    border-radius: 12px;
    display: flex;
    align-items: center;
    justify-content: center;
    cursor: pointer;
    pointer-events: auto;
    box-shadow:
      0 4px 20px rgba(0, 0, 0, 0.3),
      0 2px 8px rgba(0, 0, 0, 0.2),
      inset 0 1px 0 rgba(255, 255, 255, 0.1);
    transition: all 0.3s cubic-bezier(0.4, 0, 0.2, 1);
    backdrop-filter: blur(12px);
    border: 1px solid rgba(255, 255, 255, 0.1);
    color: rgba(255, 255, 255, 0.9);
  }

  .panel-trigger:hover {
    background: linear-gradient(135deg, rgba(40, 40, 40, 0.98), rgba(30, 30, 30, 0.95));
    box-shadow:
      0 8px 28px rgba(0, 0, 0, 0.4),
      0 4px 12px rgba(0, 0, 0, 0.25),
      inset 0 1px 0 rgba(255, 255, 255, 0.15);
    transform: translateX(3px) translateY(-1px);
    border: 1px solid rgba(255, 255, 255, 0.15);
    color: rgba(255, 255, 255, 1);
  }

  /* 悬浮面板样式 - 黑色主题 */
  .floating-panel {
    position: absolute;
    top: 20px;
    left: 80px;
    width: 350px;
    max-height: calc(100vh - 40px);
    background: linear-gradient(to bottom, rgba(25, 25, 25, 0.98), rgba(20, 20, 20, 0.96));
    backdrop-filter: blur(20px) saturate(120%);
    border-radius: 16px;
    border: 1px solid rgba(255, 255, 255, 0.1);
    box-shadow:
      0 20px 40px rgba(0, 0, 0, 0.4),
      0 8px 16px rgba(0, 0, 0, 0.3),
      inset 0 1px 0 rgba(255, 255, 255, 0.05);
    pointer-events: auto;
    overflow: hidden;
    color: rgba(255, 255, 255, 0.9);
    display: flex;
    flex-direction: column;
  }

  /* 面板滑入动画 */
  .panel-slide-enter-active,
  .panel-slide-leave-active {
    transition: all 0.4s cubic-bezier(0.4, 0, 0.2, 1);
  }

  .panel-slide-enter-from {
    opacity: 0;
    transform: translateX(-20px) scale(0.95);
  }

  .panel-slide-leave-to {
    opacity: 0;
    transform: translateX(-20px) scale(0.95);
  }

  /* 面板头部 - 黑色主题 */
  .panel-header {
    display: flex;
    justify-content: space-between;
    align-items: center;
    font-weight: 600;
    font-size: 16px;
    padding: 16px 20px;
    border-bottom: 1px solid rgba(255, 255, 255, 0.08);
    color: rgba(255, 255, 255, 0.95);
    flex-shrink: 0;
  }

  /* 分组标签栏 */
  .group-tabs {
    display: flex;
    flex-wrap: wrap;
    gap: 4px;
    padding: 12px 16px;
    border-bottom: 1px solid rgba(255, 255, 255, 0.06);
    background: rgba(0, 0, 0, 0.1);
    flex-shrink: 0;
  }

  .group-tab {
    display: flex;
    align-items: center;
    gap: 6px;
    padding: 8px 12px;
    border-radius: 8px;
    cursor: pointer;
    transition: all 0.2s cubic-bezier(0.4, 0, 0.2, 1);
    font-size: 13px;
    font-weight: 500;
    color: rgba(255, 255, 255, 0.6);
    background: rgba(255, 255, 255, 0.03);
    border: 1px solid rgba(255, 255, 255, 0.05);
    min-width: 0;
    white-space: nowrap;
  }

  .group-tab:hover {
    color: rgba(255, 255, 255, 0.8);
    background: rgba(255, 255, 255, 0.08);
    border-color: rgba(255, 255, 255, 0.1);
    transform: translateY(-1px);
  }

  .group-tab.active {
    color: rgba(255, 255, 255, 0.95);
    background: linear-gradient(135deg, #667eea 0%, #764ba2 100%);
    border-color: rgba(102, 126, 234, 0.5);
    box-shadow: 0 2px 8px rgba(102, 126, 234, 0.3);
  }

  .group-tab.active:hover {
    background: linear-gradient(135deg, #7c8ef0 0%, #8a5cb8 100%);
    transform: translateY(-1px);
  }

  /* 面板内容 */
  .panel-content {
    padding: 16px 20px;
    flex: 1;
    overflow-y: auto;
    min-height: 0;
  }

  /* 功能切换动画 */
  .function-fade-enter-active,
  .function-fade-leave-active {
    transition: all 0.3s cubic-bezier(0.4, 0, 0.2, 1);
  }

  .function-fade-enter-from {
    opacity: 0;
    transform: translateX(10px);
  }

  .function-fade-leave-to {
    opacity: 0;
    transform: translateX(-10px);
  }

  /* 自定义滚动条 - 黑色主题 */
  .panel-content::-webkit-scrollbar {
    width: 6px;
  }

  .panel-content::-webkit-scrollbar-track {
    background: rgba(255, 255, 255, 0.05);
    border-radius: 3px;
  }

  .panel-content::-webkit-scrollbar-thumb {
    background: rgba(255, 255, 255, 0.2);
    border-radius: 3px;
  }

  .panel-content::-webkit-scrollbar-thumb:hover {
    background: rgba(255, 255, 255, 0.3);
  }

  /* 功能列表 */
  .function-list {
    display: flex;
    flex-direction: column;
    gap: 8px;
    padding: 0;
  }

  .function-item {
    justify-content: flex-start !important;
    height: 42px;
    border-radius: 10px;
    transition: all 0.25s cubic-bezier(0.4, 0, 0.2, 1);
    font-weight: 500;
    position: relative;
    overflow: hidden;
    border: 1px solid rgba(255, 255, 255, 0.08);
  }

  .function-item::before {
    content: "";
    position: absolute;
    top: 0;
    left: -100%;
    width: 100%;
    height: 100%;
    background: linear-gradient(90deg, transparent, rgba(255, 255, 255, 0.1), transparent);
    transition: left 0.5s;
  }

  .function-item:hover::before {
    left: 100%;
  }

  .function-item:hover {
    transform: translateX(4px);
    box-shadow: 0 6px 20px rgba(0, 0, 0, 0.3);
    border-color: rgba(255, 255, 255, 0.15);
  }

  /* Naive UI 按钮样式覆盖 - 黑色主题 */
  :deep(.n-button) {
    border-color: rgba(255, 255, 255, 0.08);
    background: rgba(255, 255, 255, 0.03);
    color: rgba(255, 255, 255, 0.85);
  }

  :deep(.n-button:hover) {
    border-color: rgba(255, 255, 255, 0.15);
    background: rgba(255, 255, 255, 0.08);
    color: rgba(255, 255, 255, 0.95);
  }

  :deep(.n-button--primary-type) {
    background: linear-gradient(135deg, #667eea 0%, #764ba2 100%);
    border-color: rgba(102, 126, 234, 0.5);
    color: white;
  }

  :deep(.n-button--primary-type:hover) {
    background: linear-gradient(135deg, #7c8ef0 0%, #8a5cb8 100%);
    border-color: rgba(102, 126, 234, 0.8);
  }

  :deep(.n-button--ghost.n-button--primary-type) {
    background: transparent;
    border-color: rgba(102, 126, 234, 0.4);
    color: rgba(102, 126, 234, 0.9);
  }

  :deep(.n-button--ghost.n-button--primary-type:hover) {
    background: rgba(102, 126, 234, 0.1);
    border-color: rgba(102, 126, 234, 0.6);
    color: rgba(102, 126, 234, 1);
  }
</style>
