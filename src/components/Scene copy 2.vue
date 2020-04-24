<template>
  <el-row>
      <!-- <el-col :span="14" :offset="5" style="text-align: left;">
        <div class="btn-group">
          <div class="btn btn-default btn-sm active" title="默认模式">
            <div class="q-icon toolbar-default"></div>
          </div>
          <div class="btn btn-default btn-sm" title="框选模式">
            <div class="q-icon toolbar-rectangle_selection"></div>
          </div>
          <div class="btn btn-default btn-sm" title="浏览模式">
            <div class="q-icon toolbar-pan"></div>
          </div>
          <div class="btn btn-default btn-sm" title="创建连线">
            <div class="q-icon toolbar-edge"></div>
          </div>
          <div class="btn btn-default btn-sm" title="导出图片">
            <div class="q-icon toolbar-print"></div>
          </div>
          <div id="btnOverview" title="Show Overview Panel" class="btn btn-default btn-sm">
            <div class="q-icon toolbar-overview"></div>
          </div>
          <div title="Maximize" id="maximize" class="btn btn-default btn-sm">
            <div class="q-icon toolbar-max"></div>
          </div>
        </div>
        <div style="display: inline-block; vertical-align: middle; width: 170px;">
          <div class="input-group input-group-sm">
            <input type="text" class="form-control" placeholder="Name" id="search_input" />
            <span class="input-group-btn">
              <div class="btn btn-default" type="button">
                <div class="q-icon toolbar-search"></div>
              </div>
            </span>
          </div>
        </div>
      </el-col> -->
    <el-col>
      <div style="height: 800px;" id="editor" />
    </el-col>
  </el-row>
</template>
<style>
</style>
<script>
export default {
  data() {
    return {
      tableData: {},
      json_str: {
        nodes: [
          { name: "hello", x: -100, y: -50, img: "Q-server" },
          { name: "Qunee", x: 100, y: 50, img: "Q-exchanger2" }
        ],
        edges: [{ start: "hello", end: "Qunee", name: "Hello\nQunee" }]
      },
      node_ins: {},
      edge_ins: {}
    };
  },
  mounted() {
    // let graph = new Q.Graph("canvas");

    let self = this;

    Q.registerImage('lamp', Q.Shapes.getShape(Q.Consts.SHAPE_CIRCLE, -8, -8, 16, 16));
    var lampGradient = new Q.Gradient(Q.Consts.GRADIENT_TYPE_RADIAL, [Q.toColor(0xAAFFFFFF), Q.toColor(0x33EEEEEE), Q.toColor(0x44888888), Q.toColor(0x33666666)],
            [0.1, 0.3, 0.7, 0.9], 0, -0.2, -0.2);


    $('#editor').graphEditor({callback: function(editor){
        let graph = editor.graph;
        $.each(self.json_str.nodes, function(index, node) {
      let n = graph.createNode(node.name, node.x, node.y);
      n.image = node.img;
      self.node_ins[node.name] = n;
    });
    $.each(self.json_str.edges, function(index, edge) {
      let e = graph.createEdge(
        edge.name,
        self.node_ins[edge.start],
        self.node_ins[edge.end]
      );
      self.edge_ins[edge.name] = e;
    });

    // graph.ondblclick = function(evt) {
    //   let node = graph.getElementByMouseEvent(evt);
    //   if (node) {
    //     let newName = prompt("New Name:");
    //     if (newName) {
    //       node.name = newName;
    //       console.log(node.name);
    //     }
    //   }
    // };

        graph.moveToCenter();
    }});

    
    self.MaxCloudUrl = self.GLOBAL.MaxCloudUrl;
    axios
      .get(self.GLOBAL.MaxCloudUrl + "/iso/list")
      .then(function(res) {
        if (res.data.RespHead.ErrorCode == 1004) {
          self.$router.push({ path: "/login" });
        } else {
          self.tableData = res.data.RespBody.Result;
          self.loading = false;
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
    }
  }
};
</script>