<template>
  <div>
    <header class="header__content" style="position: relative; overflow: hidden;">
      <Decorate />
      <div class="container">
        <div class="he-row">
          <div class="header_left">
            <div class="logo">
              <Icon type="md-add" />
            </div>
            <div class="header__info">
              <span>
                <h2>创建项目</h2>
                <p>在这里可以创建一个新的项目，同时也可以迭代之前的项目。项目的创建分为静态部署与自动部署，可根据自身需求进行选择。</p>
              </span>
            </div>
          </div>

          <div class="header__change">
            <div
              class="em-header__nav__item"
              @click="handleCharge('0')"
              :class="[modeType==='0'?'is-active':'']"
            >
              <i class="ivu-icon ivu-icon-android-list"></i> 静态部署
            </div>
            <div
              class="em-header__nav__item"
              @click="handleCharge('1')"
              :class="[modeType==='1'?'is-active':'']"
            >
              <i class="ivu-icon ivu-icon-gear-a"></i> 自动部署
            </div>
          </div>
        </div>
      </div>
    </header>
    <section>
      <div class="add-page">
        <!-- <Modal v-model="oneBugIsShow" width="70%" @on-cancel="handleShow(false)"> -->
        <!-- <div slot="header"> -->
        <!--<h3>
					<Icon type="ios-create-outline" size="22" />新建部署
				</h3>
        </div>-->
        <!-- <div class="qhan">
          <span></span>
          <span></span>
        </div>-->
        <div class="warp">
          <!-- 所属项目 -->
          <div v-if="isAddClear">
            <label>
              <i class="star">*</i> 所属项目：
            </label>
            <Select
              v-model="key"
              style="width:300px;margin-right:15px;"
              @on-change="projectNameChange"
            >
              <Option
                v-for="(item,i) in sideList"
                :value="item.key"
                :key="i+'w'"
              >{{ item.projectName }}</Option>
            </Select>
            <Icon
              type="ios-add-circle-outline"
              class="add-clear"
              size="22"
              @click="handleAddClear"
            />
          </div>
          <div v-else>
            <label>
              <i class="star">*</i>所属项目：
            </label>
            <Input
              v-model="projectName"
              placeholder="请输入新项目名称..."
              style="width: 300px;margin-right:15px;"
            />
            <Icon
              type="ios-close-circle-outline"
              class="add-clear"
              size="22"
              @click="handleAddClear"
            />
          </div>
          <div v-if="!isAddClear">
            <label>
              <i class="star">*</i>部署目录：
            </label>
            <Input v-model="root" placeholder="例如：dist" style="width: 300px" />
            <Tooltip max-width="200" content="指部署到服务器上的目录，也就是一级目录。" placement="right">
              <Icon type="ios-help-circle-outline tip" size="22" />
            </Tooltip>
            <span v-if="isEx" class="isEx">此目录已存在，请重新输入！</span>
          </div>
          <div>
            <label>部署端口：</label>
            <Input v-model="port" placeholder="例如：8080 （选填）" style="width: 300px" />
            <Tooltip max-width="200" content="默认：本系统端口号/部署目录/index.html" placement="right">
              <Icon type="ios-help-circle-outline tip" size="22" />
            </Tooltip>
            <span v-if="isPort==0" class="isEx">此端口已存在，请重新输入！</span>
            <span v-if="isPort==-2" class="isEx">端口号格式错误，请正确输入端口号！</span>
            <span v-if="isPort==-3" class="isEx">端口应大于等于0且小于65536！</span>
          </div>
          <div>
            <label>代理地址：</label>
            <Input v-model="target" placeholder="例如：http://127.0.0.1 （选填）" style="width: 300px" />
            <Tooltip max-width="200" content="默认：若需解决跨域问题，可在这里填入需要代理的接口。" placement="right">
              <Icon type="ios-help-circle-outline tip" size="22" />
            </Tooltip>
            <span class="tip-text" v-if="target">
              将部署项目的 baseURL 改为：
              <i>{{baseUrl+'/'+root}}</i>
            </span>
          </div>
          <div v-if="modeType==='1'">
            <label>
              <i class="star">*</i>Git 地址：
            </label>
            <Input
              v-model="gitUrl"
              placeholder="例如：http://10.0.86.12/zll/science.git"
              style="width: 300px"
            />
            <Tooltip max-width="200" content="此处填写 gitLab 仓库地址。" placement="right">
              <Icon type="ios-help-circle-outline tip" size="22" />
            </Tooltip>
          </div>
          <transition name="fade">
            <div v-if="modeType==='1'">
              <label>
                <i class="star">*</i>项目分支：
              </label>
              <Input v-model="branch" placeholder="默认：master" style="width: 300px" />
              <Tooltip max-width="200" content="此处填写 gitLab 仓库分支，默认 master 主分支。" placement="right">
                <Icon type="ios-help-circle-outline tip" size="22" />
              </Tooltip>
            </div>
          </transition>
          <transition name="fade">
            <div v-if="modeType==='1'">
              <label>
                <i class="star">*</i>部署命令：
              </label>
              <Input v-model="order" placeholder="例如：npm run build" style="width: 300px" />
              <Tooltip max-width="200" content="填写项目打包指令，支持 npm 和 cnpm 指令。" placement="right">
                <Icon type="ios-help-circle-outline tip" size="22" />
              </Tooltip>
            </div>
          </transition>
          <transition name="fade">
            <div v-if="modeType==='1'">
              <label>
                <i class="star">*</i>打包目录：
              </label>
              <Input v-model="dist" placeholder="默认：dist" style="width: 300px" />
              <Tooltip max-width="200" content="此处填写此项目的打包目录。" placement="right">
                <Icon type="ios-help-circle-outline tip" size="22" />
              </Tooltip>
            </div>
          </transition>
          <transition name="fade">
            <div v-if="modeType==='0'">
              <label>
                <i class="star">*</i>部署文件：
              </label>
              <uploader
                :key="uploader_key"
                :options="options"
                ref="uploader"
                class="uploader-example"
                @file-complete="fileComplete"
                @complete="complete"
                :autoStart="true"
                :fileStatusText="fileStatusText"
              >
                <!-- <uploader
						:key="uploader_key"
						:options="options"
						ref="uploader"
						class="uploader-example"
						@file-success="onFileSuccess"
						@upload-start="onFileStart"
						@file-removed="onFileRemoved"
						@file-added="onFileAdded"
						@files-submitted="onFileSubmitted"
						@file-complete="fileComplete"
						@complete="complete"
						:autoStart="true"
						:fileStatusText="fileStatusText"
                >-->
                <!-- <uploader-drop>拖拽上传<uploader-drop/> -->
                <uploader-unsupport></uploader-unsupport>
                <uploader-drop class="uploader-drop-botton" v-if="isUpLoader">
                  <uploader-btn :directory="true" :single="true">
                    <Icon type="ios-cloud-upload" size="52" style="color: #3399ff"></Icon>
                  </uploader-btn>
                </uploader-drop>
                <uploader-list></uploader-list>
              </uploader>
              <Tooltip max-width="200" content="选择上传此项目的打包目录即可。" placement="right">
                <Icon type="ios-help-circle-outline tip" size="22" />
              </Tooltip>
            </div>
          </transition>
          <div class="banben">
            <!-- <div>
              <label>立即部署：</label>
              <RadioGroup v-model="idDeployment">
                <Radio label="yes">是</Radio>
                <Radio label="no">否</Radio>
              </RadioGroup>
            </div>-->
            <div>
              <label>部署方式：</label>
              <span v-if="modeType==='1'">自动部署</span>
              <span v-else>静态部署</span>
              <Tooltip
                max-width="200"
                :content="modeType==='1'?'此次部署之后，只要提交代码到 GitLab 仓库即可自动化部署。':'只需上传静态资源即可。'"
                placement="right"
              >
                <Icon type="ios-help-circle-outline tip" size="22" />
              </Tooltip>
            </div>
            <div>
              <label>部署版本：</label>
              <span>v{{version}}</span>
            </div>
          </div>
          <!-- <div class="banben">
            <div style="width:165px">
              <label>部署方式：</label>
              <span>自动部署</span>
            </div>
            <Tooltip max-width="200" content="此次部署之后，只要提交代码到 GitLab 仓库即可自动化部署。" placement="right">
              <Icon type="ios-help-circle-outline tip" size="22" />
            </Tooltip>
          </div>-->
          <div>
            <label>部署摘要：</label>
            <Input
              v-model="remark"
              type="textarea"
              :rows="4"
              placeholder="请输入概要内容...'"
              style="width: 60%;"
            />
          </div>
          <div class="tip-box">
            <div v-if="modeType==='1'">
              <h3>
                <Icon type="ios-help-circle-outline" size="18" />
                <span @click="handleHelp" :class="isHelp?'active':''">关联 Git 仓库</span>
              </h3>
              <div class="tisi" v-if="isHelp">
                <div>1、打开git：项目仓库 -> Settings -> Webhooks -> Add webhook</div>
                <div>
                  2、在
                  <span class="code">Target URL</span> 中填入
                  <span class="url">{{$url}}/api/deploy/git</span> 地址
                </div>
                <div>
                  3、
                  <span class="code">POST Content Type</span> 选择
                  <span class="select">application/json</span>
                </div>
                <div>
                  4、
                  <span v-if="isOk">
                    在
                    <span class="code">Secret</span> 中填入秘钥Key：
                    <span class="url">{{key}}</span>；
                  </span>
                  <span v-else>
                    在
                    <span class="code">Secret</span> 中填入部署成功后返回的 Key 值。
                  </span>
                </div>
                <div>
                  5、
                  <span class="code">Trigger On</span>选择
                  <span class="select">Push Events</span>
                </div>
                <div>
                  <span style="color:red">注意：</span>若项代码托管平台为GitHub时，在第 2 步中需要填入
                  <span
                    class="url"
                  >{{$url}}/api/deploy/git?key={{key?key:"返回的key值"}}</span> 地址。
                </div>
              </div>
            </div>
          </div>
        </div>
        <!-- <div slot="footer"> -->
        <div class="button-footer">
          <Button v-if="modeType==='0'" ghost type="success" @click="handleSubmit">提交部署</Button>
          <!-- <Button v-else type="success" @click="handleSubmit">开始部署</Button> -->
          <Button v-else type="success" ghost @click="handleAutoSubmit">开始部署</Button>
          <Button type="error" ghost @click.stop="handleClear">重置</Button>
          <Button type="primary" ghost @click.stop="handleBack">返回首页</Button>
        </div>
        <!-- </div> -->
        <div v-if="zzcAutoSubmit" @click="handleZzcAutoSubmit" class="auto-submit-zzc"></div>
        <!-- </Modal> -->
      </div>
    </section>
    <!-- 权限控制 -->
    <Modal v-model="isToLogin" width="360" @on-cancel="handleToLogin">
      <p slot="header" style="color:#f60;text-align:center">
        <Icon type="ios-information-circle"></Icon>
        <span>系统提示</span>
      </p>
      <div style="text-align:center">
        <p>由于您不是注册用户，暂无权执行此操作，请注册登录后操作！</p>
      </div>
      <div slot="footer">
        <Button type="info" size="large" long @click="handleLoginModal">我要去注册</Button>
      </div>
    </Modal>
    <!-- 秘钥key -->
    <!-- <Modal title="温馨提示" v-model="isKey" :mask-closable="false">
      <p>秘钥key：{{key}}</p>
    </Modal>-->
    <!-- 部署日志信息 -->
    <Modal title="部署日志" v-model="isRizhi" scrollable :mask-closable="false" :footer-hide="true">
      <div class="rz-box"></div>
      <div class="rz-tip" v-if="isOk">
        <div class="tip-box">
          <div>
            <p>
              √ 秘钥Key：
              <span style="color:#3390FF;">{{key}}</span>
            </p>
            <Divider orientation="left">关联 Git 仓库</Divider>
            <div class="tisi">
              <div>1、打开git：项目仓库 -> Settings -> Webhooks -> Add webhook；</div>
              <div>
                2、在
                <span class="code">Target URL</span> 中填入
                <span class="url">{{$url}}/api/deploy/git</span> 地址；
              </div>
              <div>
                3、
                <span class="code">POST Content Type</span> 选择
                <span class="select">application/json</span>；
              </div>
              <div>
                4、在
                <span class="code">Secret</span> 中填入秘钥Key：
                <span class="url">{{key}}</span>；
              </div>
              <div>
                5、
                <span class="code">Trigger On</span>选择
                <span class="select">Push Events</span>；
              </div>
              <div>
                <span style="color:red">注意：</span>若项代码托管平台为 GitHub 时，在第 2 步中需要填入
                <span
                  class="url"
                >{{$url}}/api/deploy/git?key={{key?key:"返回的key值"}}</span> 地址。
              </div>
            </div>
          </div>
        </div>
      </div>
    </Modal>
  </div>
</template>
<script>
import Decorate from "../header/decorate";
// import io from "socket.io-client";
export default {
  components: {
    Decorate,
  },
  props: ["typeArr", "classArr"],

  data() {
    return {
      sideList: [],
      author: "",
      url: "",
      usre: {},
      content: {},
      isToLogin: false,
      isRizhi: false,
      socket: null,
      isKey: true,
      isIp: false,
      key: "",
      target: "",

      isClickBushu: false,
      // **********************************************
      isPort: 1,
      port: "",
      // portArr: [],
      oneBugIsShow: false,
      zzcAutoSubmit: false,
      isHelp: true,
      isEx: false,
      isUpLoader: true,
      uploader_key: new Date().getTime(), //这个用来刷新组件--解决不刷新页面连续上传的缓存上传数据（注：每次上传时，强制这个值进行更改---根据自己的实际情况重新赋值）
      options: {
        target: this.$url + "/api/deploy/files/add", //SpringBoot后台接收文件夹数据的接口
        query: {
          root: "",
          version: "1.0.1",
        },
        testChunks: false, //是否分片-不分片
        fileParameterName: "file", //上传文件时文件的参数名，默认file
      },
      root: "",
      version: "1.0.1",
      versionVal: "1.0.1",
      fileStatusText: {
        error: "上传失败",
        paused: "等待上传",
        success: "上传成功",
        uploading: "正在上传...",
        waiting: "等待中...",
      },
      baseUrl: "",
      projectName: "",
      idDeployment: "yes",
      remark: "",
      catalog: "", //上传目录
      isAddClear: true,
      isOk: false,
      uid: "",

      // itemData: {}, //静态数据
      // projectNameData: {}, //单个数据
      mkdirArr: [], //服务器目录

      isHistory: "yes",
      dist: "dist",
      modeType: "0",
      gitUrl: "", //git 地址
      branch: "master", //git 分支
      order: "npm run build", //部署命令
      // mode: "静态部署" //模式
      // ----------------------------------------------------
    };
  },
  watch: {
    root(val) {
      let arr = this.mkdirArr.filter((item) => {
        return item === val;
      });
      if (arr.length === 0) {
        this.options.query.root = val;
        this.isEx = false;
      } else {
        this.isEx = true;
      }
    },
    isRizhi(val) {
      if (!val && this.isOk) {
        this.$router.push({
          path: "/tablePage",
          query: { bid: this.key },
        });
        // this.socket.close();
        // this.socket.disconnect();
        // this.socket = null;
      }
    },
    port(val) {
      if (this.content.port) {
        this.checkPort(val);
      }
      let origin = window.location.origin;
      this.baseUrl = this.port
        ? origin.slice(0, origin.lastIndexOf(":")) + ":" + this.port
        : this.$url;
      // if (val) {
      //   this.isPort = this.portArr.indexOf(val) > -1;
      // } else {
      //   this.isPort = false;
      // }
    },
  },

  mounted() {
    this.usre = this.$store.state.variable.info;
    this.author = this.usre.name;
    this.url = this.usre.url;
    this.sideList = this.$store.state.variable.projectTitleArr;
    this.isIp = this.isCheckIP();

    let origin = window.location.origin;
    this.baseUrl = this.port
      ? origin.slice(0, origin.lastIndexOf(":")) + ":" + this.port
      : this.$url;

    this.getMkdir();
    // this.checkPort();
    let project = this.$router.currentRoute.query;

    this.projectName = project.title;
    this.key = project.bid;
    if (this.key) {
      this.getProjectNameData();
    } else {
      this.handleClear();
      this.isAddClear = false;
    }
    // this.handleBuShuRiZhi();
  },
  methods: {
    handleBuShuRiZhi() {
      // if (this.socket) {
      //   this.socket.close();
      //   this.socket.disconnect();
      //   // this.socket = null;
      // }
      // this.socket = io.connect(this.$url + "/news"); //连接聊天室的io服务器 io服务器的根地址
      //当服务器广播消息时，触发message事件，消息内容在回调函数中
      // this.socket.off("message");
      this.isClickBushu = true;
      this.$socket.on("message", function (mm) {
        let rzBox = document.querySelector(".rz-box");
        let p = document.createElement("p");
        p.innerText = `> ` + mm;
        if (mm.indexOf("rror") > -1) {
          p.style.cssText = "color:red;";
        } else if (mm.indexOf("ucces") > -1) {
          p.style.cssText = "color:#19be6b;";
        }
        rzBox.appendChild(p);
      });
      this.isRizhi = true;
    },
    isCheckIP() {
      let href = window.location.href;
      if (href.indexOf("//localhost") > -1 || href.split(".").length >= 4) {
        return true;
      } else {
        return false;
      }
    },
    handleClear() {
      this.bid = "";
      this.dist = "dist";
      this.gitUrl = "";
      this.branch = "master";
      this.order = "npm run build";
      this.root = "";
      this.isHistory = "yes";
      this.target = "";
      this.port = "";
      this.key = "";
      this.version = "1.0.1";
      this.versionVal = "1.0.1";
      this.options.query.version = "1.0.1";
      this.options.query.root = "";
      this.projectName = "";
      this.idDeployment = "yes";
      this.catalog = "";
      this.remark = "";
      this.isOk = false;
    },
    handleAddClear() {
      this.isAddClear = !this.isAddClear;
      if (!this.isAddClear) {
        this.handleClear();
      }
    },
    projectNameChange(e) {
      // console.log(e);
      // this.projectName = e;
      // console.log(e);

      // this.key = e;
      if (e) {
        this.getProjectNameData();
      }
    },
    // 获取单个数据
    getProjectNameData() {
      this.$Message.destroy();
      let data = {
        // projectName: this.projectName,
        key: this.key,
        accurate: 1, //精确匹配
        select: "one",
      };
      if (this.modeType == "1") {
        data.mode = this.modeType;
      }
      this.$axios
        .get("/api/deploy/edition/get", { params: data })
        .then((res) => {
          if (res.data.code == 200) {
            this.content = res.data.data;
            this.setData();
            this.versionVal = res.data.version;
          } else {
            this.$Message["error"]({
              background: true,
              content: "自动部署数据请求失败！",
            });
          }
        })
        .catch(function (error) {
          console.log(error);
        });
    },
    setData() {
      this.gitUrl = this.content.gitUrl; //git 地址
      this.root = this.content.root;
      this.target = this.content.target;
      this.branch = this.content.branch; //git 分支
      this.order = this.content.order; //部署命令
      this.versionVal = this.content.version;
      this.getBidData();
    },
    getBidData() {
      this.isHistory = this.content.isHistory;
      let arr = this.versionVal.split(".");
      if (arr[2].indexOf("9") > -1) {
        arr[2] = 0;
        if (arr[1].indexOf("9") > -1) {
          arr[1] = 0;
          arr[0] = arr[0] * 1 + 1;
        } else {
          arr[1] = arr[1] * 1 + 1;
        }
      } else {
        arr[2] = arr[2] * 1 + 1;
      }
      this.version = arr[0] + "." + arr[1] + "." + arr[2];
      this.options.query.version = this.version;
      this.options.query.root = this.content.root;
      this.root = this.content.root;
      this.projectName = this.content.projectName;
      this.uid = this.content.bid;
      this.port = this.content.port;
      this.key = this.content.key;
      this.catalog = this.content.catalog;
    },
    // 上传完成
    complete() {
      // console.log("complete", arguments);
      this.isUpLoader = false;
    },
    // 一个根文件（文件夹）成功上传完成。
    fileComplete(catalog, file) {
      this.catalog = catalog.name;
      this.isUpLoader = false;
    },
    // //注，这里从文件夹每上传成功一个文件会调用一次这个方法
    // onFileSuccess: function(rootFile, file, response, chunk) {
    // 	// console.log("上传成功");
    // 	//这里可以根据response（接口）返回的数据处理自己的实际问题（如：从response拿到后台返回的想要的数据进行组装并显示）
    // },
    // //开始上传。
    // onFileStart: function(file) {
    // 	// console.log("开始上传");
    // 	// console.log(file);
    // },
    // //移除一个文件（文件夹）。
    // onFileRemoved: function(file) {
    // 	// console.log("移除一个文件");
    // 	// console.log(file);
    // },
    // //添加了一个文件，一般用作文件校验，如果给 file 增加 ignored 属性为 true 的话就会被过滤掉。
    // onFileAdded: function(file) {
    // 	// console.log("添加了一个文件");
    // 	// console.log(file);
    // },
    // //所选择的文件们添加到上传队列后触发。
    // onFileSubmitted: function(files, fileList) {
    // 	// console.log(files, fileList);
    // },
    getMkdir() {
      this.$axios
        .post("/api/deploy/files/read")
        .then((res) => {
          this.mkdirArr = res.data.mkdir;
        })
        .catch(function (error) {
          console.log(error);
        });
    },
    checkPort(port) {
      if (this.content.port === port) {
        this.isPort = 1;
      } else {
        this.$axios
          .post("/api/service/operation/test", this.$qs.stringify({ port }))
          .then((res) => {
            if (res.data.result) {
              this.isPort = res.data.state;
            } else {
              this.isPort = -2;
            }
            // let data = res.data.data;
            // // data.push("82");
            // this.portArr = data;
          })
          .catch(function (error) {
            console.log(error);
          });
      }
    },

    // 静态部署
    handleSubmit() {
      if (this.usre.name === "admin") {
        this.$Notice.destroy();
        this.$Notice.error({
          title: "系统温馨提示",
          desc: "您不是注册用户，请注册登录后操作！",
        });
      } else {
        this.$Message.destroy();
        // 判断输入内容是否为空
        if (!this.projectName) {
          this.$Message["error"]({
            background: true,
            content: "请选择所属项目！",
          });
          return;
        }
        // if (!this.port) {
        //   this.$Message["error"]({
        //     background: true,
        //     content: "请输入端口号！"
        //   });
        //   return;
        // }
        if (!this.root) {
          this.$Message["error"]({
            background: true,
            content: "部署目录不得为空！",
          });
          return;
        }
        if (this.isUpLoader) {
          this.$Message["error"]({
            background: true,
            content: "请上传部署文件！",
          });
          return;
        }
        // if (this.isEx) {
        //   this.$Message["error"]({
        //     background: true,
        //     content: "部署目录已存在，请重新输入！"
        //   });
        //   return;
        // }
        // let project = this.$store.state.variable.itemData;
        let data = {
          projectName: this.projectName,
          author: this.author,
          authorId: this.usre.bid,
          url: this.url ? this.url : "/images/dt.png",
          // idDeployment: this.idDeployment,
          root: this.root,
          isHistory: this.isHistory,
          root: this.root,
          target: this.target,
          version: this.version,
          uid: this.uid,
          port: this.port,
          key: this.key,
          // isNew: this.projectName === project.projectName,
          catalog: this.catalog ? this.catalog : this.dist,
          versionRoot: "./" + this.root + "/" + this.version,
          remark: this.remark
            ? this.remark
            : "更新" + this.projectName + "项目。",

          dist: this.dist ? this.dist : "dist",
          gitUrl: this.gitUrl, //git 地址
          branch: this.branch ? this.branch : "master", //git 分支
          order: this.order ? this.order : "npm run build", //部署命令
          mode: this.modeType, //模式
        };
        this.$Message.loading({
          content: "项目部署中...",
          duration: 0,
        });
        this.$axios
          .post("/api/deploy/edition/add", this.$qs.stringify(data))
          .then((res) => {
            this.$Message.destroy();
            if (res.data.result) {
              // this.$Message["success"]({
              //   background: true,
              //   content: "此项目部署成功！"
              // });
              this.key = res.data.key;
              this.zzcAutoSubmit = false;
              // this.handleClear();
              if (this.modeType === "1") {
                this.$Modal.success({
                  title: "项目部署成功！",
                  content: "秘钥Key：" + this.key,
                  okText: "确定",
                  onOk: () => {
                    // let rzBox = document.querySelector(".rz-box");
                    // let p = document.createElement("p");
                    // p.innerHTML = `√ 秘钥Key：<span style="color:#3390FF;">${this.key}</span>`;
                    //  rzBox.appendChild(p);
                    this.isOk = true;
                    // if (mm.indexOf("rror") > -1) {
                    //   p.style.cssText = "color:red;";
                    // } else if (mm.indexOf("ucces") > -1) {
                    //   p.style.cssText = "color:#19be6b;";
                    // }

                    // this.$router.push({
                    //   path: "/tablePage",
                    //   query: { bid: this.key }
                    // });
                  },
                });

                // this.$Modal.confirm({
                //   title: "项目部署成功！",
                //   content: "秘钥Key：" + this.key,
                //   okText: "复制Key",
                //   cancelText: "关闭",
                //   onOk: () => {

                //   },
                //   onCancel: () => {
                //     this.$Message.info("Clicked cancel");
                //   }
                // });
              } else {
                this.$router.push({
                  path: "/tablePage",
                  query: { bid: this.key },
                });
              }
            } else {
              // this.$Message["error"]({
              //   background: true,
              //   content: "部署失败！"
              // });
              this.$Message.destroy();
              this.$Modal.error({
                title: "异常提示",
                content: "发生未知错误，部署失败！",
              });
              this.zzcAutoSubmit = false;
            }
          })
          .catch((error) => {
            this.$Message.destroy();
            // this.$Message["error"]({
            //   background: true,
            //   content: "部署路径出现问题，部署失败！"
            // });
            this.$Modal.error({
              title: "异常提示",
              content: "部署路径出现问题，部署失败！",
            });
            this.zzcAutoSubmit = false;
            console.log(error);
          });
      }
    },

    //拉取项目(自动部署)
    handleAutoSubmit() {
      if (this.usre.name === "admin") {
        this.$Notice.destroy();
        this.$Notice.error({
          title: "系统温馨提示",
          desc: "您不是注册用户，请注册登录后操作！",
        });
      } else {
        this.$Message.destroy();

        // 判断输入内容是否为空
        if (!this.projectName) {
          this.$Message["error"]({
            background: true,
            content: "请选择所属项目！",
          });
          return;
        }
        if (!this.root) {
          this.$Message["error"]({
            background: true,
            content: "部署目录不得为空！",
          });
          return;
        }
        // if (!this.port) {
        //   this.$Message["error"]({
        //     background: true,
        //     content: "请输入端口号！"
        //   });
        //   return;
        // }
        if (!this.gitUrl) {
          this.$Message["error"]({
            background: true,
            content: "Git 地址不得为空！",
          });
          return;
        }
        if (!this.order) {
          this.$Message["error"]({
            background: true,
            content: "部署命令不得为空！",
          });
          return;
        }
        // if (this.isEx) {
        //   this.$Message["error"]({
        //     background: true,
        //     content: "部署目录已存在，请重新输入！"
        //   });
        //   return;
        // }
        this.handleBuShuRiZhi();
        this.zzcAutoSubmit = true;
        let data = {
          gitUrl: this.gitUrl, //git 地址
          root: this.root,
          port: this.port,
          branch: this.branch ? this.branch : "master", //git 分支
          order: this.order, //部署命令
        };
        this.$Message.loading({
          content: "请勿关闭浏览器，项目拉取中...",
          duration: 0,
        });
        this.$axios
          .post("/api/deploy/auto/clone", this.$qs.stringify(data))
          .then((res) => {
            this.$Message.destroy();
            if (res.data.result) {
              this.$Message["success"]({
                background: true,
                content: "项目拉取成功！",
              });
              this.handleInit(data);
            } else {
              this.$Message.destroy();
              this.$Modal.error({
                title: "异常提示",
                content: "发生未知错误，项目拉取失败！",
              });
              this.zzcAutoSubmit = false;
            }
          })
          .catch(function (error) {
            console.log(error);
            this.$Message.destroy();
            this.$Modal.error({
              title: "异常提示",
              content: "发生未知错误，项目拉取失败！",
            });
            this.zzcAutoSubmit = false;
          });
      }
    },
    //初始化项目 安装依赖
    handleInit(data) {
      this.$Message.destroy();
      this.$Message.loading({
        content: "请勿关闭当前界面，项目正在初始化，请耐心等待...",
        duration: 0,
      });
      this.$axios
        .post("/api/deploy/auto/init", this.$qs.stringify(data))
        .then((res) => {
          this.$Message.destroy();
          if (res.data.result) {
            this.$Message["success"]({
              background: true,
              content: "项目初始化成功！",
            });
            this.handleBuild(data);
          } else {
            // this.$Message["error"]({
            //   background: true,
            //   content: "项目初始化失败！"
            // });
            this.$Message.destroy();
            this.$Modal.error({
              title: "异常提示",
              content: "发生未知错误，项目初始化失败！",
            });
            this.zzcAutoSubmit = false;
          }
        })
        .catch((error) => {
          console.log(error);
          this.$Message.destroy();
          this.$Modal.error({
            title: "异常提示",
            content: "发生未知错误，项目初始化失败！",
          });
          this.zzcAutoSubmit = false;
        });
    },
    // 打包项目
    handleBuild(data) {
      this.$Message.destroy();
      this.$Message.loading({
        content: "请勿关闭当前界面，项目打包中，请耐心等待...",
        duration: 0,
      });
      this.$axios
        .post("/api/deploy/auto/build", this.$qs.stringify(data))
        .then((res) => {
          if (res.data.result) {
            this.$Message.destroy();
            this.$Message["success"]({
              background: true,
              content: "项目已打包完成！",
            });
            this.isUpLoader = false;
            this.handleSubmit();
          } else {
            this.$Message.destroy();
            this.$Modal.error({
              title: "异常提示",
              content: "发生未知错误，项目已打包失败！",
            });
            this.zzcAutoSubmit = false;
          }
        })
        .catch(function (error) {
          console.log(error);
          this.$Message.destroy();
          this.$Modal.error({
            title: "异常提示",
            content: "发生未知错误，项目已打包失败！",
          });
          this.zzcAutoSubmit = false;
        });
    },
    handleZzcAutoSubmit() {
      this.$Message["warning"]({
        background: true,
        content: "请勿关闭当前界面，项目正在部署中，请耐心等待~",
      });
    },

    handleBack() {
      this.$router.push({ path: "/" });
    },
    handleCharge(val) {
      this.modeType = val;
    },
    handleHelp() {
      this.isHelp = !this.isHelp;
    },
    handleToLogin() {
      this.isToLogin = false;
    },
    handleLoginModal() {
      window.sessionStorage.clear();
      this.$store.commit("setUser", {});
      this.$router.push({ path: "/login" });
    },
    // -------------------------------------------------------

    // handleAddClear() {
    // 	this.isAddClear = !this.isAddClear;
    // },

    // onEditorChange(quill) {
    // 	// console.log(quill);
    // 	this.explain = quill.html;
    // },
    // handleSuccess(res, file) {
    // 	if (res.result) {
    // 		this.imgSrc = res.fileInfo.path;
    // 		this.$Message["success"]({
    // 			background: true,
    // 			content: "图片上传成功！"
    // 		});
    // 	} else {
    // 		this.$Message["error"]({
    // 			background: true,
    // 			content: "图片上传失败！"
    // 		});
    // 	}
    // }
  },
  beforeDestroy() {
    if (this.isClickBushu) {
      window.location.reload();
    }
    // }
  },
  // beforeDestroy() {
  //   if (this.$socket) {
  //     this.$socket.close();
  //     this.$socket.disconnect();
  //     // this.socket = null;
  //   }
  // }
};
</script>
<style lang="scss" scoped>
@import "./index.scss";
/deep/ .ivu-modal-content {
  margin-bottom: 100px;
}
</style>
