<!--
 * @Description: 文件内容描述
 * @Date: 2022-09-15 21:02:46
 * @LastEditors: 无言的对话
 * @LastEditTime: 2023-04-21 13:59:09
 * @FilePath: \vue2\src\layout\top\top-menu.vue
-->
<template>
  <div class="top-menu">
    <el-menu :default-active="activeIndex" mode="horizontal" text-color="#333">
      <template v-for="(item, index) in items">
        <el-menu-item :index="item.parentId + ''" @click.native="openMenu(item)" :key="index">
          <template slot="title">
            <i :class="item.icon"></i>
            <span>{{ generateTitle(item) }}</span>
          </template>
        </el-menu-item>
      </template>
    </el-menu>
  </div>
</template>

<script>
import { mapGetters } from 'vuex'
import { getPath } from '@/router/generator-routers'
export default {
  name: 'top-menu',
  data() {
    return {
      activeIndex: '0',
      items: [],
    }
  },
  created() {
    // this.getMenu()
  },
  computed: {
    ...mapGetters(['tagCurrent', 'menu']),
  },
  methods: {
    // getMenu() {
    //   this.$store.dispatch('GetTopMenu').then((res) => {
    //     this.items = res
    //   })
    // },
    generateTitle(item) {
      return this.$router.$avueRouter.generateTitle(
        item.label,
        (item.meta || {}).i18n
      )
    },
    openMenu(item) {
      this.$store.dispatch('GetMenu', item.parentId).then((data) => {
        if (data.length !== 0) {
          // this.$router.$avueRouter.formatRoutes(data, true)
        }
        let itemActive,
          childItemActive = 0
        if (item.path) {
          itemActive = item
        } else {
          if (this.menu[childItemActive].length == 0) {
            itemActive = this.menu[childItemActive]
          } else {
            itemActive = this.menu[childItemActive].children[childItemActive]
          }
        }
        this.$router.push({
          path: getPath({
            name: itemActive.label,
            src: itemActive.path,
            i18n: itemActive.meta.i18n,
          }),
        })
      })
    },
  },
}
</script>
