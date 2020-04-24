<template>
<el-container>
  <el-header>
    <el-menu :default-active="activeIndex" class="el-menu-demo" mode="horizontal" @select="handleSelect">
    <el-menu-item index="1">节点</el-menu-item>
    <el-menu-item index="2">虚拟机</el-menu-item>
    <el-menu-item index="3">虚拟机模板</el-menu-item>
    <el-menu-item index="4">防火墙</el-menu-item>
    <el-menu-item index="9">网络</el-menu-item>
    <el-menu-item index="5">场景</el-menu-item>
    <el-menu-item index="10">系统镜像</el-menu-item>
    <el-submenu index="8">
      <template slot="title">更多操作</template>
      <el-menu-item index="8-1">添加节点</el-menu-item>
      <el-menu-item index="8-2">创建虚拟机</el-menu-item>
      <el-menu-item index="8-3">添加网络</el-menu-item>
      <el-menu-item index="8-4">添加防火墙</el-menu-item>
      <el-menu-item index="8-5">上传镜像</el-menu-item>
      <el-menu-item index="8-6">上传模板</el-menu-item>
    </el-submenu>
    <el-submenu index="6" style="float:right">
      <template slot="title">用户</template>
      <el-menu-item index="6-1">退出</el-menu-item>
      <el-menu-item index="6-2">修改密码</el-menu-item>
    </el-submenu>
    <el-menu-item index="7" disabled style="float:right">消息中心</el-menu-item>
  </el-menu>
  </el-header>
  <el-main>
    <div id="conntent">
      <div v-if="activeIndex == '1'">
         <Node></Node>
      </div>
      <div v-else-if="activeIndex == '2'">
         <Vm></Vm>
      </div>
      <div v-else-if="activeIndex == '3'">
        <VmTpl></VmTpl>
      </div>
      <div v-else-if="activeIndex === '4'">
        <Firewall></Firewall>
      </div>
      <div v-else-if="activeIndex === '9'">
        <Network></Network>
      </div>
      <div v-else-if="activeIndex === '10'">
        <IsoPool></IsoPool>
      </div>
      <div v-else-if="activeIndex === '5'">
        <Scene></Scene>
      </div>
      <div v-else-if="activeIndex === '8-1'">
        <AddNode></AddNode>
      </div>
      <div v-else-if="activeIndex === '8-2'">
        <AddVm></AddVm>
      </div>
      <div v-else-if="activeIndex === '8-3'">
        <AddNetwork></AddNetwork>
      </div>
      <div v-else-if="activeIndex === '8-4'">
        <AddFirewall></AddFirewall>
      </div>
      <div v-else-if="activeIndex === '8-5'">
        <UploadIso></UploadIso>
      </div>
      <div v-else-if="activeIndex === '8-6'">
        <UploadVmTpl></UploadVmTpl>
      </div>
      <div v-else>
        非 A/B/C
      </div>
    </div>
  </el-main>
</el-container>
</template>

<script>
import VmTpl from "../components/VmTpl"
import Vm from "../components/Vm"
import Firewall from "../components/Firewall"
import Node from "../components/Node"
import IsoPool from "../components/IsoPool"
import Scene from "../components/Scene"
import Network from "../components/Network"
import AddFirewall from "../components/AddFirewall"
import AddNetwork from "../components/AddNetwork"
import AddNode from "../components/AddNode"
import AddVm from "../components/AddVm"
import UploadIso from "../components/UploadIso"
import UploadVmTpl from "../components/UploadVmTpl"
export default {
  data () {
    return {
      activeIndex: '1',
      show1: true
    }
  },
  components: {
    VmTpl,
    Vm,
    Firewall,
    Node,
    IsoPool,
    Scene,
    Network,
    AddFirewall,
    AddNode,
    AddVm,
    AddNetwork,
    UploadIso,
    UploadVmTpl,
  },
  created() {
    document.title="index";
    },
  methods: {
    handleSelect(key, keyPath) {
      console.log(key, keyPath);
      this.activeIndex = key
    }
  }
}
</script>
