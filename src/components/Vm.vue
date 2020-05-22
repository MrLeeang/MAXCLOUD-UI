<template>
  <div>
    <el-table
      :data="tableData.filter(data => !search || data.name.toLowerCase().includes(search.toLowerCase()))"
      v-loading="loading"
      style="width: 100%"
    >
      <el-table-column type="expand">
        <template slot-scope="props">
          <el-form label-position="left" inline class="demo-table-expand">
            <el-form-item label="id">
              <span>{{ props.row.id }}</span>
            </el-form-item>
            <el-form-item label="uuid">
              <span>{{ props.row.uuid }}</span>
            </el-form-item>
            <el-form-item label="服务器名称">
              <span>{{ props.row.name }}</span>
            </el-form-item>
            <el-form-item label="系统">
              <span>{{ props.row.os }}</span>
            </el-form-item>
            <el-form-item label="系统内存">
              <span>{{ props.row.memory }}</span>
            </el-form-item>
            <el-form-item label="系统cpu">
              <span>{{ props.row.cpu }}</span>
            </el-form-item>
            <el-form-item label="是否注册">
              <span>{{ props.row.on_define }}</span>
            </el-form-item>
            <el-form-item label="快速启动">
              <span>{{ props.row.quick_start }}</span>
            </el-form-item>
            <el-form-item label="状态">
              <span>{{ props.row.run_state }}</span>
            </el-form-item>
            <el-form-item label="login_user">
              <span>{{ props.row.login_user }}</span>
            </el-form-item>
            <el-form-item label="login_pass">
              <span>{{ props.row.login_pass }}</span>
            </el-form-item>
            <el-form-item label="node_uuid">
              <span>{{ props.row.node_uuid }}</span>
            </el-form-item>
            <el-form-item label="organization_uuid">
              <span>{{ props.row.organization_uuid }}</span>
            </el-form-item>
            <el-form-item label="parent_uuid">
              <span>{{ props.row.parent_uuid }}</span>
            </el-form-item>
            <el-form-item label="this_snapshot">
              <span>{{ props.row.this_snapshot }}</span>
            </el-form-item>
            <el-form-item label="vnc_enable">
              <span>{{ props.row.vnc_enable }}</span>
            </el-form-item>
            <el-form-item label="vnc_pass">
              <span>{{ props.row.vnc_pass }}</span>
            </el-form-item>
            <el-form-item label="vnc_port">
              <span>{{ props.row.vnc_port }}</span>
            </el-form-item>
            <el-form-item label="vnc_token">
              <span>{{ props.row.vnc_token }}</span>
            </el-form-item>
            <el-form-item label="xml_name">
              <span>{{ props.row.xml_name }}</span>
            </el-form-item>
            <el-form-item label="xml_path">
              <span>{{ props.row.xml_path }}</span>
            </el-form-item>
            <el-form-item label="描述">
              <span>{{ props.row.desc }}</span>
            </el-form-item>
            <!-- <el-form-item label="磁盘">
              <el-form inline v-for="(item,index) in props.row.disks" :key="index">
                <el-form-item label="uuid">
                  <span>{{ item.uuid }}</span>
                </el-form-item>
                <el-form-item label="dev">
                  <span>{{ item.dev }}</span>
                </el-form-item>
                <el-form-item label="device">
                  <span>{{ item.device }}</span>
                </el-form-item>
                <el-form-item label="driver_name">
                  <span>{{ item.driver_name }}</span>
                </el-form-item>
                <el-form-item label="driver_type">
                  <span>{{ item.driver_type }}</span>
                </el-form-item>
                <el-form-item label="name">
                  <span>{{ item.name }}</span>
                </el-form-item>
                <el-form-item label="size">
                  <span>{{ item.size }}</span>
                </el-form-item>
                <el-form-item label="source_file">
                  <span>{{ item.source_file }}</span>
                </el-form-item>
                <el-form-item label="storage_pool_uuid">
                  <span>{{ item.storage_pool_uuid }}</span>
                </el-form-item>
                <el-form-item label="type">
                  <span>{{ item.type }}</span>
                </el-form-item>
              </el-form>
            </el-form-item> -->
            <!-- <el-form-item label="网卡">
              <el-form inline v-for="(item,index) in props.row.net_cards" :key="index">
                <el-form-item label="uuid">
                  <span>{{ item.uuid }}</span>
                </el-form-item>
                <el-form-item label="index">
                  <span>{{ item.index }}</span>
                </el-form-item>
                <el-form-item label="名称">
                  <span>{{ item.name }}</span>
                </el-form-item>
                <el-form-item label="mac地址">
                  <span>{{ item.mac_address }}</span>
                </el-form-item>
                <el-form-item label="是否为静态ip">
                  <span>{{ item.is_static }}</span>
                </el-form-item>
                <el-form-item label="ip地址">
                  <span>{{ item.ip_address }}</span>
                </el-form-item>
                <el-form-item label="掩码">
                  <span>{{ item.net_mask }}</span>
                </el-form-item>
                <el-form-item label="网关">
                  <span>{{ item.gateway }}</span>
                </el-form-item>
                <el-form-item label="端口">
                  <span>{{ item.port_key }}</span>
                </el-form-item>
                <el-form-item label="类型">
                  <span>{{ item.type }}</span>
                </el-form-item>
                <el-form-item label="网络uuid">
                  <span>{{ item.network_uuid }}</span>
                </el-form-item>
                <el-form-item label="网络名称">
                  <span>{{ item.network.name }}</span>
                </el-form-item>
                <el-form-item label="网络vlan">
                  <span>{{ item.network.vlan_id }}</span>
                </el-form-item>
                <el-form-item label="网络是否开启dhcp">
                  <span>{{ item.network.is_dhcp }}</span>
                </el-form-item>
                <el-form-item label="网络是否删除">
                  <span>{{ item.network.is_delete }}</span>
                </el-form-item>
                <el-form-item label="网络虚拟化">
                  <span>{{ item.network.virtual_port_type }}</span>
                </el-form-item>
                <el-form-item label="网络描述">
                  <span>{{ item.network.desc }}</span>
                </el-form-item>
              </el-form>
            </el-form-item>  -->
            <!-- <el-form-item label="快照">
              <el-form inline v-for="(item,index) in props.row.snapshots" :key="index">
                <el-form-item label="uuid">
                  <span>{{ item.uuid }}</span>
                </el-form-item>
                <el-form-item label="name">
                  <span>{{ item.name }}</span>
                </el-form-item>
                <el-form-item label="描述">
                  <span>{{ item.desc }}</span>
                </el-form-item>
                <el-form-item label="时间">
                  <span>{{ item.create_time }}</span>
                </el-form-item>
              </el-form>
            </el-form-item> -->
          </el-form>
        </template>
      </el-table-column>
      <el-table-column label="虚拟机名称" prop="name" sortable></el-table-column>
      <el-table-column label="系统" prop="os" sortable></el-table-column>
      <el-table-column label="系统内存" prop="memory" sortable></el-table-column>
      <el-table-column label="系统cpu" prop="cpu" sortable></el-table-column>
      <el-table-column label="是否注册" prop="on_define" sortable></el-table-column>
      <el-table-column label="快速启动" prop="quick_start" sortable></el-table-column>
      <el-table-column label="状态" prop="run_state" sortable></el-table-column>
      <el-table-column align="right">
        <template slot="header" slot-scope="scope">
          <el-input v-model="search" size="mini" placeholder="输入关键字搜索" />
        </template>
        <template slot-scope="scope">
          <el-dropdown size="mini" split-button type="danger" @click="remove(scope.row.uuid)">
            删除
            <el-dropdown-menu slot="dropdown">
              <el-dropdown-item>
                <el-button
                  icon="el-icon-edit"
                  size="mini"
                  type="text"
                  @click="handleEdit(scope.row)"
                >编辑</el-button>
              </el-dropdown-item>
              <el-dropdown-item>
                <el-button
                  icon="el-icon-document-copy"
                  size="mini"
                  type="text"
                  @click="addsnapshot(scope.row.uuid)"
                >创建快照</el-button>
              </el-dropdown-item>
              <el-dropdown-item>
                <el-button
                  icon="el-icon-remove-outline"
                  size="mini"
                  type="text"
                  @click="snapshotshow(scope.row.uuid)"
                >快照管理</el-button>
              </el-dropdown-item>
              <el-dropdown-item>
                <el-button
                  icon="el-icon-remove-outline"
                  size="mini"
                  type="text"
                  @click="nc_show(scope.row.uuid)"
                >网卡管理</el-button>
              </el-dropdown-item>
              <el-dropdown-item>
                <el-button
                  icon="el-icon-remove-outline"
                  size="mini"
                  type="text"
                  @click="disk_show(scope.row.uuid)"
                >硬盘管理</el-button>
              </el-dropdown-item>
              <el-dropdown-item>
                <el-button
                  icon="el-icon-switch-button"
                  size="mini"
                  type="text"
                  @click="poweron(scope.row.uuid)"
                >开机</el-button>
              </el-dropdown-item>
              <el-dropdown-item>
                <el-button
                  icon="el-icon-switch-button"
                  size="mini"
                  type="text"
                  @click="poweroff(scope.row.uuid)"
                >关机</el-button>
              </el-dropdown-item>
              <el-dropdown-item>
                <el-button
                  icon="el-icon-refresh"
                  size="mini"
                  type="text"
                  @click="reset_vm(scope.row.uuid)"
                >配置生效(实例会重启)</el-button>
              </el-dropdown-item>
              <el-dropdown-item>
                <el-button
                  icon="el-icon-upload2"
                  size="mini"
                  type="text"
                  @click="up_tpl(scope.row.uuid)"
                >提交为模板</el-button>
              </el-dropdown-item>
              <el-dropdown-item>
                <el-link
                  :href="MaxCloudUrl+'/vnc?token='+scope.row.vnc_token"
                  target="_blank"
                  size="mini"
                  icon="el-icon-connection"
                  :underline="false"
                >VNC</el-link>
              </el-dropdown-item>
            </el-dropdown-menu>
          </el-dropdown>
        </template>
      </el-table-column>
    </el-table>

    <el-drawer
      title="编辑"
      :before-close="handleClose"
      :visible.sync="handleEditDis"
      direction="rtl"
      custom-class="demo-drawer"
      ref="drawer"
      size="40%"
    >
      <div class="demo-drawer__content">
        <el-form :model="editform">
          <el-form-item label="虚拟机名称" :label-width="formLabelWidth">
            <el-input v-model="editform.name" autocomplete="off"></el-input>
          </el-form-item>
          <el-form-item label="描述" :label-width="formLabelWidth">
            <el-input v-model="editform.desc" autocomplete="off"></el-input>
          </el-form-item>
          <el-form-item label="系统登录用户" :label-width="formLabelWidth">
            <el-input v-model="editform.login_user" autocomplete="off"></el-input>
          </el-form-item>
          <el-form-item label="系统登录密码" :label-width="formLabelWidth">
            <el-input v-model="editform.login_pass" autocomplete="off"></el-input>
          </el-form-item>
          <el-form-item label="vnc密码" :label-width="formLabelWidth">
            <el-input v-model="editform.vnc_pass" autocomplete="off"></el-input>
          </el-form-item>
          <el-form-item label="cpu" :label-width="formLabelWidth">
          <el-select v-model="editform.cpu" placeholder="请选择cpu数量">
            <el-option label="1" value="1"></el-option>
            <el-option label="2" value="2"></el-option>
            <el-option label="4" value="4"></el-option>
            <el-option label="8" value="8"></el-option>
            <el-option label="16" value="16"></el-option>
            <el-option label="32" value="32"></el-option>
          </el-select>
        </el-form-item>
        <el-form-item label="内存" :label-width="formLabelWidth">
          <el-select v-model="editform.memory" placeholder="请选择内存大小">
            <el-option label="1" value="1048576"></el-option>
            <el-option label="2" value="2097152"></el-option>
            <el-option label="4" value="4194304"></el-option>
            <el-option label="8" value="8388608"></el-option>
            <el-option label="16" value="16777216"></el-option>
            <el-option label="32" value="33554432"></el-option>
          </el-select>
        </el-form-item>
        </el-form>
        <div class="demo-drawer__footer">
          <el-button @click="cancelForm">取 消</el-button>
          <el-button
            type="primary"
            @click="$refs.drawer.closeDrawer()"
            :loading="editloading"
          >{{ editloading ? '提交中 ...' : '确 定' }}</el-button>
        </div>
      </div>
    </el-drawer>

    <el-dialog title="创建虚拟机快照" :visible.sync="dialogFormVisible" top="45px" width="650px">
      <el-form :model="add_snapshot_form" :label-position="labelPosition">
        <el-form-item label="快照名称" :label-width="formLabelWidth">
          <el-input v-model="add_snapshot_form.name" autocomplete="off"></el-input>
        </el-form-item>
        <el-form-item label="描述" :label-width="formLabelWidth">
          <el-input type="textarea" v-model="add_snapshot_form.desc"></el-input>
        </el-form-item>
      </el-form>
      <div slot="footer" class="dialog-footer">
        <el-button @click="dialogFormVisible = false">取 消</el-button>
        <el-button type="primary" @click="btn_add_snapshot">确 定</el-button>
      </div>
    </el-dialog>

    <el-dialog title="保存为模板" :visible.sync="uptpldialogFormVisible" top="45px" width="650px">
      <el-form :model="up_tpl_data" :label-position="labelPosition">
        <el-form-item label="模板名称" :label-width="formLabelWidth">
          <el-input v-model="up_tpl_data.name" autocomplete="off"></el-input>
        </el-form-item>
        <el-form-item label="描述" :label-width="formLabelWidth">
          <el-input type="textarea" v-model="up_tpl_data.desc"></el-input>
        </el-form-item>
      </el-form>
      <div slot="footer" class="dialog-footer">
        <el-button @click="dialogFormVisible = false">取 消</el-button>
        <el-button type="primary" @click="btn_up_tpl">确 定</el-button>
      </div>
    </el-dialog>

    <el-dialog title="快照管理" :visible.sync="snapshotdialogFormVisible" top="45px" width="650px">
      <div v-for="(vm_data, index) in tableData" :key="index">
        <div v-if="vm_data.uuid == this_vm_uuid">
          <el-table :data="vm_data.snapshots" style="width: 100%">
            <el-table-column label="日期" width="180">
              <template slot-scope="scope">
                <i class="el-icon-time"></i>
                <span style="margin-left: 10px">{{ scope.row.create_time }}</span>
              </template>
            </el-table-column>
            <el-table-column label="名称" width="180">
              <template slot-scope="scope">
                <el-popover trigger="hover" placement="top">
                  <p>名称: {{ scope.row.name }}</p>
                  <p>描述: {{ scope.row.desc }}</p>
                  <div slot="reference" class="name-wrapper">
                    <el-tag
                      size="medium"
                      :type="vm_data.this_snapshot == scope.row.uuid ? 'success':'info'"
                    >{{ scope.row.name }}</el-tag>
                  </div>
                </el-popover>
              </template>
            </el-table-column>
            <el-table-column label="操作">
              <template slot-scope="scope">
                <el-button size="mini" @click="revert_snapshot(scope.row)">恢复</el-button>
                <el-button
                  size="mini"
                  type="danger"
                  @click="delete_snapshot(scope.$index, scope.row)"
                >删除</el-button>
              </template>
            </el-table-column>
          </el-table>
        </div>
      </div>
    </el-dialog>

    <el-dialog title="网卡管理" :visible.sync="ncdialogFormVisible" top="45px" width="1100px">
      <div v-for="(vm_data, index) in tableData" :key="index">
        <div v-if="vm_data.uuid == this_vm_uuid">
          <el-table :data="vm_data.net_cards" style="width: 100%">
            <el-table-column label="索引" width="180">
              <template slot-scope="scope">
                <span style="margin-left: 10px">{{ scope.row.index }}</span>
              </template>
            </el-table-column>
            <el-table-column label="名称" width="180">
              <template slot-scope="scope">
                <el-popover trigger="hover" placement="top">
                  <p>uuid: {{ scope.row.uuid }}</p>
                  <div slot="reference" class="name-wrapper">
                    <el-tag
                      size="medium"
                      type="info"
                    >{{ scope.row.name }}</el-tag>
                  </div>
                </el-popover>
              </template>
            </el-table-column>
            <el-table-column label="mac地址" width="180">
              <template slot-scope="scope">
                <span style="margin-left: 10px">{{ scope.row.mac_address }}</span>
              </template>
            </el-table-column>
            <el-table-column label="ip地址" width="180">
              <template slot-scope="scope">
                <el-popover trigger="hover" placement="top">
                  <p>是否为静态: {{ scope.row.is_static }}</p>
                  <p>掩码: {{ scope.row.net_mask }}</p>
                  <p>网关: {{ scope.row.gateway }}</p>
                  <div slot="reference" class="name-wrapper">
                    <el-tag
                      size="medium"
                      type="info"
                    >{{ scope.row.ip_address }}</el-tag>
                  </div>
                </el-popover>
              </template>
            </el-table-column>
            <el-table-column label="网络" width="180">
              <template slot-scope="scope">
                <el-popover trigger="hover" placement="top">
                  <p>网络uuid: {{ scope.row.network_uuid }}</p>
                  <p>网络vlan: {{ scope.row.network.vlan_id }}</p>
                  <p>开启dhcp: {{ scope.row.network.is_dhcp }}</p>
                  <p>网络是否删除: {{ scope.row.network.is_delete }}</p>
                  <p>网络虚拟化: {{ scope.row.network.virtual_port_type }}</p>
                  <p>网络描述: {{ scope.row.network.desc }}</p>
                  <div slot="reference" class="name-wrapper">
                    <el-tag
                      size="medium"
                      type="info"
                    >{{ scope.row.network.name }}</el-tag>
                  </div>
                </el-popover>
              </template>
            </el-table-column>
            <el-table-column label="操作">
              <template slot="header">
                <el-button
                  icon="el-icon-edit"
                  size="mini"
                  type="text"
                  @click="add_nc()"
                >添加网卡</el-button>
            </template>
              <template slot-scope="scope">
                <el-button
                  size="mini"
                  type="danger"
                  @click="delete_nc(scope.$index, scope.row)"
                >删除</el-button>
              </template>
            </el-table-column>
          </el-table>
        </div>
      </div>
    </el-dialog>

    <el-dialog title="硬盘管理" :visible.sync="diskdialogFormVisible" top="45px" width="1050px">
      <div v-for="(vm_data, index) in tableData" :key="index">
        <div v-if="vm_data.uuid == this_vm_uuid">
          <el-table :data="vm_data.disks" style="width: 100%">
            <el-table-column label="名称" width="180">
              <template slot-scope="scope">
                <el-popover trigger="hover" placement="top">
                  <p>uuid: {{ scope.row.uuid }}</p>
                  <p>存储: {{ scope.row.storage_pool_uuid }}</p>
                  <div slot="reference" class="name-wrapper">
                    <el-tag
                      size="medium"
                      type="info"
                    >{{ scope.row.name }}</el-tag>
                  </div>
                </el-popover>
              </template>
            </el-table-column>
            <el-table-column label="设备编号" width="180">
              <template slot-scope="scope">
                <span style="margin-left: 10px">{{ scope.row.dev }}</span>
              </template>
            </el-table-column>
             <el-table-column label="源文件" width="180" :show-overflow-tooltip="true">
              <template slot-scope="scope">
                <span style="margin-left: 10px">{{ scope.row.source_file }}</span>
              </template>
            </el-table-column>
             <el-table-column label="硬盘大小" width="180">
              <template slot-scope="scope">
                <span style="margin-left: 10px">{{ scope.row.size }}</span>
              </template>
            </el-table-column>
            <el-table-column label="设备" width="180">
              <template slot-scope="scope">
                <el-popover trigger="hover" placement="top">
                  <p>driver_name: {{ scope.row.driver_name }}</p>
                  <p>driver_type: {{ scope.row.driver_type }}</p>
                  <p>type: {{ scope.row.type }}</p>
                  <div slot="reference" class="name-wrapper">
                    <el-tag
                      size="medium"
                      type="info"
                    >{{ scope.row.device }}</el-tag>
                  </div>
                </el-popover>
              </template>
            </el-table-column>
            <el-table-column label="操作">
              <template slot="header">
                <el-button
                  icon="el-icon-edit"
                  size="mini"
                  type="text"
                  @click="add_disk()"
                >添加硬盘</el-button>
            </template>
              <template slot-scope="scope">
                <el-button
                  size="mini"
                  type="danger"
                  @click="delete_disk(scope.$index, scope.row)"
                >删除</el-button>
              </template>
            </el-table-column>
          </el-table>
        </div>
      </div>
    </el-dialog>

  </div>
</template>

<style>
.el-drawer__header > :first-child {
  -webkit-box-flex: 1;
  -ms-flex: 1;
  flex: 1;
  font-size: 25px;
}
.demo-drawer__content form {
  flex: 1;
}
.demo-drawer__content {
  display: flex;
  flex-direction: column;
  height: 100%;
}
.el-drawer__body {
  padding: 20px;
}
.demo-drawer__footer {
  display: flex;
}
.demo-drawer__footer button {
  flex: 1;
}
.demo-table-expand .el-form-item .el-form label {
  width: 135px;
  color: #99a9bf;
}

.demo-table-expand .el-form-item .el-form .el-form-item {
  margin-left: 135px;
  margin-right: 0;
  margin-bottom: 0;
  width: 100%;
}

.demo-table-expand .el-form-item__content {
  line-height: 20px;
}

.demo-table-expand .el-form-item__label {
  line-height: 20px;
}

.demo-table-expand {
  font-size: 0;
}
.demo-table-expand label {
  width: 135px;
  color: #99a9bf;
}
.demo-table-expand .el-form-item {
  margin-right: 0;
  margin-bottom: 0;
  width: 49%;
}
</style>

<script>
export default {
  data() {
    return {
      tableData: [],
      search: "",
      dialogFormVisible: false,
      uptpldialogFormVisible: false,
      snapshotdialogFormVisible: false,
      ncdialogFormVisible: false,
      diskdialogFormVisible: false,
      labelPosition: "left",
      error_messages: "",
      this_vm_uuid: "",
      up_tpl_data: {
        vm_uuid: "",
        name: "",
        desc: "",
      },
      loading: true,
      add_snapshot_form: {
        vm_uuid: "",
        name: "",
        desc: ""
      },
      editloading: false,
      handleEditDis: false,
      timer: null,
      formLabelWidth: "80px",
      editform: {
        vm_uuid: "",
        name: "",
        desc: "",
        login_user: "",
        login_pass: "",
        vnc_pass: "",
        cpu: "",
        memory: ""
      }
    };
  },
  mounted() {
    let self = this;
    self.MaxCloudUrl = self.GLOBAL.MaxCloudUrl;
    axios
      .get(self.GLOBAL.MaxCloudUrl + "/vm/list")
      .then(function(res) {
        if (res.data.RespHead.ErrorCode == 1004) {
          self.$router.push({ path: "/login" });
        } else {
          self.tableData = res.data.RespBody.Result;
          self.loading = false;
          console.log(self.tableData);
        }
      })
      .catch(function(error) {
        // 请求失败处理
        console.log(error);
      });
  },
  methods: {
    query_task(uuid) {
      let self = this;
      axios
        .get(self.GLOBAL.MaxCloudUrl + "/task?uuid=" + uuid)
        .then(function(res) {
          if (
            res.data.RespBody.Result.status != "PENDING" &&
            res.data.RespBody.Result.status != "DEFAULT"
          ) {
            self.$message({
              message: res.data.RespBody.Result.message,
              type: "success"
            });
          } else {
            self.query_task(uuid);
          }
        })
        .catch(function(error) {
          // 请求失败处理
          self.$message.error(error);
        });
    },

    remove(uuid) {
      this.$confirm("此操作将永久删除该实例, 是否继续?", "提示", {
        confirmButtonText: "确定",
        cancelButtonText: "取消",
        type: "warning"
      })
        .then(() => {
          let data = [{ uuid: uuid }];
          let self = this;
          axios
            .post(self.GLOBAL.MaxCloudUrl + "/vm/remove", data)
            .then(function(res) {
              var data = res.data;
              if (
                data.RespHead.ErrorCode == 0 &&
                data.RespHead.Message == "SUCCESS"
              ) {
                $.each(self.tableData, function(index, vm_data) {
                  if (vm_data && vm_data["uuid"] == uuid) {
                    self.tableData.splice(index, 1);
                    return true;
                  }
                });
                self.query_task(data.RespBody.Result.task_id);
              } else {
                self.$message({
                  message: data.RespHead.Message,
                  type: "warning"
                });
              }
            })
            .catch(function(error) {
              // 请求失败处理
              console.log(error);
              self.$message.error("系统错误");
            });
        })
        .catch(() => {
          this.$message({
            type: "info",
            message: "已取消删除"
          });
        });
    },

    addsnapshot(vm_uuid) {
      this.add_snapshot_form = {
        vm_uuid: vm_uuid,
        name: "",
        desc: ""
      };
      this.dialogFormVisible = true;
    },

    btn_add_snapshot() {
      let self = this;
      let data = self.add_snapshot_form;
      axios
        .post(self.GLOBAL.MaxCloudUrl + "/snapshot/add", data)
        .then(function(res) {
          var data = res.data;
          if (
            data.RespHead.ErrorCode == 0 &&
            data.RespHead.Message == "SUCCESS"
          ) {
            $.each(self.tableData, function(index, vm_data) {
              if (
                vm_data &&
                vm_data["uuid"] == self.add_snapshot_form["vm_uuid"]
              ) {
                self.tableData[index]["snapshots"].push(self.add_snapshot_form);
                self.dialogFormVisible = false;
                return true;
              }
            });
            self.query_task(data.RespBody.Result.task_id);
          } else {
            self.$message({
              message: data.RespHead.Message,
              type: "warning"
            });
          }
        })
        .catch(function(error) {
          // 请求失败处理
          console.log(error);
          self.$message.error("系统错误");
        });
    },

    snapshotshow(vm_uuid) {
      this.this_vm_uuid = vm_uuid;
      this.snapshotdialogFormVisible = true;
    },

    nc_show(vm_uuid) {
      this.this_vm_uuid = vm_uuid;
      this.ncdialogFormVisible = true;
    },

    disk_show(vm_uuid) {
      this.this_vm_uuid = vm_uuid;
      this.diskdialogFormVisible = true;
    },

    revert_snapshot(data) {
      let self = this;
      let form_data = data;
      axios
        .post(self.GLOBAL.MaxCloudUrl + "/snapshot/revert", form_data)
        .then(function(res) {
          var data = res.data;
          if (
            data.RespHead.ErrorCode == 0 &&
            data.RespHead.Message == "SUCCESS"
          ) {
            $.each(self.tableData, function(index, vm_data) {
              if (vm_data && vm_data.uuid == form_data.vm_uuid) {
                self.tableData[index]["this_snapshot"] = form_data.uuid;
                return true;
              }
            });
            self.query_task(data.RespBody.Result.task_id);
          } else {
            self.$message({
              message: data.RespHead.Message,
              type: "warning"
            });
          }
        })
        .catch(function(error) {
          // 请求失败处理
          console.log(error);
          self.$message.error("系统错误");
        });
    },

    delete_snapshot(snap_index, data) {
      let self = this;
      let form_data = data;
      axios
        .post(self.GLOBAL.MaxCloudUrl + "/snapshot/remove", form_data)
        .then(function(res) {
          var data = res.data;
          if (
            data.RespHead.ErrorCode == 0 &&
            data.RespHead.Message == "SUCCESS"
          ) {
            $.each(self.tableData, function(index, vm_data) {
              if (vm_data && vm_data.uuid == form_data.vm_uuid) {
                self.tableData[index]["snapshots"].splice(snap_index, 1);
                return true;
              }
            });
            self.query_task(data.RespBody.Result.task_id);
          } else {
            self.$message({
              message: data.RespHead.Message,
              type: "warning"
            });
          }
        })
        .catch(function(error) {
          // 请求失败处理
          console.log(error);
          self.$message.error("系统错误");
        });
    },

    add_nc() {
      this.$message.error("功能正在开发中");
      let vm_uuid = this.this_vm_uuid
      return false;
    },

    delete_nc(nc_index, data) {
      this.$message.error("功能正在开发中");
      return false;
    },

    add_disk() {
      this.$message.error("功能正在开发中");
      let vm_uuid = this.this_vm_uuid
      return false;
    },

    delete_disk(nc_index, data) {
      this.$message.error("功能正在开发中");
      return false;
    },

    handleEdit(data) {
      this.handleEditDis = true;
      this.editform =  {
        vm_uuid: data.uuid,
        name: data.name,
        desc: data.desc,
        login_user: data.login_user,
        login_pass: data.login_pass,
        vnc_pass: data.vnc_pass,
        cpu: data.cpu,
        memory: data.memory
      }
    },

    submitEdit(done) {
      let self = this;
      axios
        .post(self.GLOBAL.MaxCloudUrl + "/vm/edit1", self.editform)
        .then(function(res) {
          var data = res.data;
          if (
            data.RespHead.ErrorCode == 0 &&
            data.RespHead.Message == "SUCCESS"
          ) {
            done();
          } else {
             self.$message({
              message: data.RespHead.Message,
              type: "warning"
            });
          }
        })
        .catch(function(error) {
          // 请求失败处理
          console.log(error);
          self.$message.error("系统错误");
        });
    },

    handleClose(done) {
      let self = this;
      if (this.editloading) {
        return;
      }
      this.$confirm("确定要提交表单吗？")
        .then(_ => {
          this.editloading = true;
          this.timer = setTimeout(() => {
            self.submitEdit(done);
            // 动画关闭需要一定的时间
            setTimeout(() => {
              self.editloading = false;
            }, 400);
          }, 2000);
        })
        .catch(_ => {});
    },

    up_tpl(vm_uuid){
      this.uptpldialogFormVisible=true;
      this.up_tpl_data.vm_uuid = vm_uuid;
    },

    reset_vm(vm_uuid){
      let self = this;
      axios
        .post(self.GLOBAL.MaxCloudUrl + "/vm/reset", {"vm_uuid": vm_uuid})
        .then(function(res) {
          var data = res.data;
          if (
            data.RespHead.ErrorCode == 0 &&
            data.RespHead.Message == "SUCCESS"
          ) {
            self.uptpldialogFormVisible=false;
            self.query_task(data.RespBody.Result.task_id);
          } else {
            self.$message({
              message: data.RespHead.Message,
              type: "warning"
            });
          }
        })
        .catch(function(error) {
          // 请求失败处理
          console.log(error);
          self.$message.error("系统错误");
        });
    },

    btn_up_tpl(){
      let self = this;
      let form_data = this.up_tpl_data;
      axios
        .post(self.GLOBAL.MaxCloudUrl + "/vm_tpl/save", form_data)
        .then(function(res) {
          var data = res.data;
          if (
            data.RespHead.ErrorCode == 0 &&
            data.RespHead.Message == "SUCCESS"
          ) {
            self.uptpldialogFormVisible=false;
            self.query_task(data.RespBody.Result.task_id);
          } else {
            self.$message({
              message: data.RespHead.Message,
              type: "warning"
            });
          }
        })
        .catch(function(error) {
          // 请求失败处理
          console.log(error);
          self.$message.error("系统错误");
        });
    },

    poweron(vm_uuid) {
      let self = this;
      let form_data = [
        {
          uuid: vm_uuid
        }
      ];
      axios
        .post(self.GLOBAL.MaxCloudUrl + "/vm/start", form_data)
        .then(function(res) {
          var data = res.data;
          if (
            data.RespHead.ErrorCode == 0 &&
            data.RespHead.Message == "SUCCESS"
          ) {
            self.query_task(data.RespBody.Result.task_id);
          } else {
            self.$message({
              message: data.RespHead.Message,
              type: "warning"
            });
          }
        })
        .catch(function(error) {
          // 请求失败处理
          console.log(error);
          self.$message.error("系统错误");
        });
    },

    poweroff(vm_uuid) {
      let self = this;
      let form_data = [
        {
          uuid: vm_uuid
        }
      ];
      axios
        .post(self.GLOBAL.MaxCloudUrl + "/vm/destroy", form_data)
        .then(function(res) {
          var data = res.data;
          if (
            data.RespHead.ErrorCode == 0 &&
            data.RespHead.Message == "SUCCESS"
          ) {
            self.query_task(data.RespBody.Result.task_id);
          } else {
            self.$message({
              message: data.RespHead.Message,
              type: "warning"
            });
          }
        })
        .catch(function(error) {
          // 请求失败处理
          console.log(error);
          self.$message.error("系统错误");
        });
    },

    cancelForm() {
      this.editloading = false;
      this.handleEditDis = false;
      clearTimeout(this.timer);
    }
  }
};
</script>