<script setup lang="ts">
import { RouteRecordName, useRouter } from "vue-router";
import { onBeforeMount, ref } from "vue";
import type { Component } from "vue";
import { useRouterStore } from "../../store/RouterStore.ts";
import { Menu } from "@element-plus/icons-vue";

const routers = ref<
  { name: RouteRecordName; path: string; icon: Component | undefined }[]
>([]);
onBeforeMount(() => {
  const vueRouter = useRouter();
  const routerStore = useRouterStore();
  routerStore.$patch({
    router: [
      {
        name: "测试页面",
        path: "test",
        component: () => import("./test.vue"),
        meta: { icon: Menu },
      },
    ],
  });
  const { router } = routerStore;
  for (let routerElement of router) {
    vueRouter.addRoute("index", routerElement);
    routers.value.push({
      name: routerElement.name!,
      path: routerElement.path,
      icon: routerElement.meta.icon as Component | undefined,
    });
  }
});
</script>

<template>
  <el-container>
    <el-header class="label">
      <span class="title">作业管理系统</span>
      <div class="extra-buttons">
        <el-button type="primary" class="logout-button">退出登录</el-button>
      </div>
    </el-header>
    <el-container>
      <el-aside width="200px">
        <el-menu
          default-active="2"
          background-color="#f2f2f2"
          text-color="#333"
          active-text-color="#409EFF"
          router
        >
          <el-menu-item
            v-for="route of routers"
            :key="route.path"
            :index="route.path"
          >
            <el-icon><component :is="route.icon" /></el-icon>
            <template #title>{{ route.name }}</template>
          </el-menu-item>
        </el-menu>
      </el-aside>
      <el-main>
        <router-view />
      </el-main>
    </el-container>
  </el-container>
</template>

<style scoped>
.label {
  display: flex;
  justify-content: space-between;
  align-items: center;
  padding: 0 20px;
  background-color: #f6f7fb;
  font-size: 20px;
  line-height: 50px; /* 调整行高以垂直居中文本 */
}

.title {
  font-weight: 600;
  color: #333;
}

.extra-buttons {
  display: flex;
}

.login-button {
  margin-right: 10px;
}
</style>
