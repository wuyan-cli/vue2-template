<template>
  <div class="menu-wrapper">
    <template v-for="item in menu">
      <el-menu-item v-if="validatenull(item[childrenKey]) && vaildRoles(item)" :index="item[pathKey]" @click="open(item)"
        :key="item[labelKey]">
        <i :class="item[iconKey]"></i>
        <span slot="title" :alt="item[pathKey]">{{ generateTitle(item) }}</span>
      </el-menu-item>
      <el-submenu v-else-if="!validatenull(item[childrenKey]) && vaildRoles(item)" :index="item[pathKey]"
        :key="item[labelKey]">
        <template slot="title">
          <i :class="item[iconKey]"></i>
          <span slot="title" :class="{ 'el-menu--display': collapse && first }">{{ generateTitle(item) }}</span>
        </template>
        <template v-for="(child, cindex) in item[childrenKey]">
          <el-menu-item :index="child[pathKey] + cindex" @click="open(child)" :class="{ 'is-active': vaildAvtive(child) }"
            v-if="validatenull(child[childrenKey])" :key="child[labelKey]">
            <i :class="child[iconKey]"></i>
            <span slot="title">{{ generateTitle(child) }}</span>
          </el-menu-item>
          <sidebar-item v-else :menu="[child]" :key="cindex" :props="props" :screen="screen"
            :collapse="collapse"></sidebar-item>
        </template>
      </el-submenu>
    </template>
  </div>
</template>
<script>
import { mapGetters } from 'vuex'
import { validatenull } from '@/utils/validate'
import config from './config.js'
import { generateTitle } from '@/router/generator-routers'
import { getValue } from '@/router/generator-routers'
export default {
  name: 'sidebarItem',
  data() {
    return {
      config: config,
    }
  },
  props: {
    menu: {
      type: Array,
    },
    screen: {
      type: Number,
    },
    first: {
      type: Boolean,
      default: false,
    },
    props: {
      type: Object,
      default: () => {
        return {}
      },
    },
    collapse: {
      type: Boolean,
    },
  },
  created() { },
  mounted() { },
  computed: {
    ...mapGetters(['roles']),
    labelKey() {
      return this.props.label || this.config.propsDefault.label
    },
    pathKey() {
      return this.props.path || this.config.propsDefault.path
    },
    iconKey() {
      return this.props.icon || this.config.propsDefault.icon
    },
    childrenKey() {
      return this.props.children || this.config.propsDefault.children
    },
    nowTagValue() {
      return getValue(this.$route)
    },
  },
  methods: {
    generateTitle(item) {
      return generateTitle(
        item[this.labelKey],
        (item.meta || {}).i18n
      )
    },
    vaildAvtive(item) {
      return this.nowTagValue === item[this.pathKey]
    },

    vaildRoles(item) {
      item.meta = item.meta || {}
      return item.meta.roles ? item.meta.roles.includes(this.roles) : true
    },
    validatenull(val) {
      return validatenull(val)
    },
    open(item) {
      if (this.screen <= 1) this.$store.commit('SET_COLLAPSE')
      const path = item[this.pathKey]
      this.$router.push({
        path: path,
        query: item.query,
      })
    },
  },
}
</script>

