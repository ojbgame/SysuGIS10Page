<template>
  <el-container style="height: 100vh;">
    <!-- 顶部菜单按钮，左上角 -->
    <el-header style="background-color: #409EFF; color: white; display: flex; align-items: center; position: fixed; top: 0; width: 100%; z-index: 100;">
      <el-button @click="drawerVisible = true" type="primary" :icon="Menu" circle  v-if="isMobile"></el-button>
      <h1 style="margin-left: 20px;">毕业通讯录</h1>
    </el-header>

    <el-container :style="isMobile ? '' : 'margin-left: 250px; margin-top: 60px;'">
      <!-- 侧边菜单栏：桌面上使用Aside，移动设备使用Drawer -->
      <el-aside width="250px" style="background-color: #f9f9f9; position: fixed; top: 60px; bottom: 0; left: 0; z-index: 90;"  v-if="!isMobile">
        <el-input v-model="searchName" placeholder="搜索姓名" clearable style="margin: 10px; width: 90%;"></el-input>
        <el-menu
          :default-active="activeClass"
          @select="handleClassChange"
          background-color="#f0f0f0"
          text-color="#333"
          active-text-color="#409EFF"
        >
          <el-submenu
            v-for="(students, className) in groupedContacts"
            :index="className"
            :key="className"
          >
            <template #title>{{ className }}</template>
            <el-menu-item
              v-for="student,  in filteredContacts(students)"
              :index="student.学号"
              :key="student.学号"
              @click="scrollToCard(student.学号)"
            >
              {{ student.姓名 }}
            </el-menu-item>
          </el-submenu>
        </el-menu>
      </el-aside>

      <!-- 移动设备的Drawer菜单 -->
      <el-drawer
        title="同学列表"
        v-model="drawerVisible"
        direction="ltr"
        :with-header="true"
        size="60%"
        v-if="isMobile"
      >
        <el-input v-model="searchName" placeholder="搜索姓名" clearable style="margin-bottom: 10px;"></el-input>
        <el-menu
          :default-active="activeClass"
          @select="handleClassChange"
          background-color="#f0f0f0"
          text-color="#333"
          active-text-color="#409EFF"
        >
          <el-submenu
            v-for="(students, className) in groupedContacts"
            :index="className"
            :key="className"
          >
            <template #title>{{ className }}</template>
            <el-menu-item
              v-for="student,  in filteredContacts(students)"
              :index="student.学号"
              :key="student.学号"
              @click="scrollToCard(student.学号); drawerVisible = false"
            >
              {{ student.姓名 }}
            </el-menu-item>
          </el-submenu>
        </el-menu>
      </el-drawer>

      <!-- 主体内容，展示所有联系人卡片 -->
      <el-main style="overflow-y: auto;">
        <div v-for="student,  in contacts" :key="student.学号" :id="student.学号" class="student-card">
          <el-card class="box-card" shadow="hover">
            <h2>{{ student.姓名 }}</h2>
            <p><strong>学号:</strong> {{ student.学号 }}</p>
            <p><strong>所属班级:</strong> {{ student.所属班级 }}</p>
            <p><strong>所在城市:</strong> {{ student.所在城市 }}</p>
            <p><strong>工作单位:</strong> {{ student.工作单位 }}</p>
            <p><strong>电话号码:</strong> {{ student.电话号码 }}</p>
            <p class="note">{{ student.要说的话 }}</p>
          </el-card>
        </div>
      </el-main>
    </el-container>
  </el-container>
</template>

<script setup>
import { ref, computed, onMounted, onBeforeUnmount } from 'vue'


import {
  Menu
} from '@element-plus/icons-vue'

// 联系人数据
const contacts = [
  {
    "学号": "202101",
    "所属班级": "地信2班",
    "姓名": "张三",
    "所在城市": "广州",
    "工作单位": "某科技公司",
    "电话号码": "123456789",
    "要说的话": "怀念大学的时光！"
  },
  {
    "学号": "202102",
    "所属班级": "地信2班",
    "姓名": "李四",
    "所在城市": "北京",
    "工作单位": "某互联网公司",
    "电话号码": "987654321",
    "要说的话": "希望大家一切顺利！"
  },
  {
    "学号": "202103",
    "所属班级": "地信1班",
    "姓名": "王五",
    "所在城市": "上海",
    "工作单位": "某电商公司",
    "电话号码": "123123123",
    "要说的话": "未来可期！"
  }
]

// 状态数据
const searchName = ref('')
const activeClass = ref('地信2班')
//const selectedContact = ref(null)
const drawerVisible = ref(false)
const isMobile = ref(false)

// 根据班级分组
const groupedContacts = computed(() => {
  return contacts.reduce((groups, contact) => {
    if (!groups[contact.所属班级]) {
      groups[contact.所属班级] = []
    }
    groups[contact.所属班级].push(contact)
    return groups
  }, {})
})

// 根据搜索过滤联系人
const filteredContacts = (students) => {
  if (searchName.value) {
    return students.filter(student =>
      student.姓名.includes(searchName.value)
    )
  }
  return students
}

// 滚动到选中的卡片位置
const scrollToCard = (studentId) => {
  const element = document.getElementById(studentId)
  if (element) {
    element.scrollIntoView({ behavior: 'smooth' })
  }
}

// 监听班级的选择变化
const handleClassChange = (className) => {
  activeClass.value = className
}

// 监听窗口大小变化，判断是否为移动设备
const handleResize = () => {
  isMobile.value = window.innerWidth < 768
}

// 当组件挂载时初始化
onMounted(() => {
  handleResize()
  window.addEventListener('resize', handleResize)
})

// 当组件卸载时移除监听器
onBeforeUnmount(() => {
  window.removeEventListener('resize', handleResize)
})
</script>

<style>
h1 {
  margin: 0;
  font-size: 22px;
  color: white;
}

.student-card {
  margin-bottom: 20px;
}

.box-card {
  margin-top: 20px;
  width: 100%;
}

.el-header {
  display: flex;
  align-items: center;
  padding: 0 20px;
}

.el-input {
  width: 100%;
}

.menu-toggle {
  text-align: right;
}

@media (max-width: 768px) {
  /* 移动设备上隐藏Aside */
  el-aside {
    display: none;
  }
}
</style>
