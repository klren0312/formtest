<template>
  <div class="common-layout">
    <el-container class="common-layout-content">
      <el-aside class="block" width="200px">Aside</el-aside>
      <el-container>
        <el-header class="block">Header</el-header>
        <el-main>
          <RouterView />
        </el-main>
      </el-container>
    </el-container>
  </div>
</template>

<style lang="scss">
  html,
  body {
    margin: 0;
  }
  .common-layout {
    height: 100vh;
    &-content {
      height: 100%;
    }
    .block {
      background: #dfdfdf;
    }
  }
</style>
