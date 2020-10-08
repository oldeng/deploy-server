<template>
  <!-- <div>
    <Modal v-model="model" fullscreen title="Fullscreen Modal">
  <div>This is a fullscreen modal</div>-->
  <div class="shell-box">
    <div class="panel-shell">
      <div class="output-view">☺ Welcome to the web command line tool "quit"：退出、"clear"：清屏".</div>
      <br />
      <div class="shell-view">
        <span class="prompt">></span>
        {{&nbsp;}}
        <span class="input">
          <span class="left"></span>
          <span class="cursor" v-html="html"></span>
          <span class="right"></span>
        </span>
      </div>
    </div>
  </div>
  <!-- </Modal>
  </div>-->
</template>
<script>
import $ from "jquery";
import _ from "underscore";
export default {
  data() {
    return {
      // theme: "dark",
      // inputVal: "",
      html: "&nbsp;",
      isOk: false,
      // model: true,
      socket: null,
      timer: null,
      mas: ""
    };
  },
  // watch: {
  //   model(val) {
  //     this.$event.emit("isShell", false);
  //   }
  // },
  mounted() {
    // this.user = this.$store.state.variable.info;
    // this.$event.on("inputClear", val => {
    //   this.inputVal = val;
    // });
    this.$nextTick(() => {
      this.handleInit();
    });
  },
  methods: {
    template_output(obj) {
      return `<div class="output-view ${
        this.isOk ? "" : "pass"
      }"><span class="${obj.classVal}">${
        obj.separate
      }</span>&nbsp;<span class="${obj.classVal}">${obj.value}</span></div>`;
    },
    handleInit() {
      let _this = this;
      //连接聊天室的io服务器 io服务器的根地址
      // if (this.socket) {
      //   this.socket.close();
      //   this.socket.disconnect();
      //   // this.socket = null;
      // }

      // this.socket = io(this.$url + "/news");
      // this.socket.off("message");
      //当服务器广播消息时，触发message事件，消息内容在回调函数中
      // socket.on("connect", function() {
      //  console.log();

      // });
      // console.log(socket);
      // socket.close()
      // socket.on("disconnect", function() {
      //  socket.reconnect();
      // });
      let cmd_cache = [];
      let cmd_pos = 0;

      let $left = $(".shell-box .left");
      let $right = $(".shell-box .right");
      let $cursor = $(".shell-box .cursor");
      let $shell = $(".shell-box .shell-view");
      let $input = $(".shell-box .input .left");

      let str_left = "";
      let str_cursor = "";
      let str_right = "";
      let str_tmp_cursor = "";
      let flag_end = false;

      // 光标闪烁效果
      this.timer = setInterval(function() {
        $cursor.toggleClass("blink");
      }, 1000);

      // keypress 按下字符键时触发
      // keydown 按下任意键触发

      // 获取键盘输入 (keydown 与 keypress 获取的 keyCode 值不一样, 其中keydown不区分大小写)
      $(document).keypress(function(e) {
        // jQuery 标准化了 event.keyCode(IE) event.which(W3C) event.charCode(事件为keypress下除IE)
        // console.log(e.which);
        // console.log(String.fromCharCode(e.which));

        if (e.which === 32) {
          // space
          $left.append("&nbsp;");
        } else if (e.which !== 13) {
          // enter
          $left.append(String.fromCharCode(e.which));
        }
      });
      this.addTemplate("Please enter the login password!");
      // 功能键
      $(document).keydown(function(e) {
        // console.log(e.which);

        if (e.which === 13) {
          // enter
          let cmd = $.trim($input.text());
          let val_ouput = "";
          let err_class = "";
          let is_print = true;

          if (cmd !== "") {
            cmd_cache.push(cmd);
            cmd_cache = _.uniq(cmd_cache);
          }
          if (cmd_cache.length > 0) {
            cmd_pos = cmd_cache.length - 1;
          }

          if (cmd === "help") {
            val_ouput =
              'Type JavaScript syntax for interactive console, or type "clear" to clear terminal.';
          } else if (cmd === "clear") {
            $shell.siblings().remove();
            is_print = false;
          } else if (cmd === "quit") {
            // socket.disconnect();
            // socket.close();
            // _this.socket.close();
            // _this.socket.disconnect();
            // this.socket = null;
            _this.handleRouter();
          } else {
            if (!_this.isOk) {
              _this.handleSubmit(cmd);
            } else {
              _this.$socket.send(cmd.replace(" ", " ")); //发送消息到服务器
            }
            // if (_this.isOk) {

            // } else {
            // $shell.before(
            //   _this.template_output({
            //     separate: _this.isOk ? "$" : "&gt;",
            //     value: 'Wrong password, please input again!',
            //     classVal: 'error'
            //   }) + "<br />"
            // );
            // }

            // try {
            //     val_ouput = eval(cmd);

            // } catch (e) {
            //     val_ouput = '\'' + cmd + '\': command not found';
            //     err_class = ' error';
            // }
          }

          $left.text("");
          $cursor.html("&nbsp;");
          $right.text("");

          if (is_print) {
            $shell.before(
              _this.template_output({
                separate: "$",
                value: cmd,
                classVal: "cmd-class"
              })
            );
            // $shell.before(template_output({
            //     separate: '&gt;',
            //     value: val_ouput,
            //     error: err_class
            // }) + '<br />');
          }
        } else if (e.which === 8) {
          // backspace
          e.preventDefault();

          str_left = $left.text();
          if (str_left.length === 0) {
            return;
          }
          str_left = str_left.substring(0, str_left.length - 1);
          $left.text(str_left);
        } else if (e.which === 37) {
          // 向左方向键
          str_left = $left.text();
          str_right = $right.text();
          str_cursor = $cursor.text();
          str_tmp_cursor = "";

          if (str_left.length === 0) {
            return;
          }
          str_tmp_cursor = str_left.substring(
            str_left.length - 1,
            str_left.length
          );
          str_left = str_left.substring(0, str_left.length - 1);
          if (
            !(
              $cursor.html() === "&nbsp;" &&
              str_right.length === 0 &&
              $.trim(str_tmp_cursor) !== ""
            )
          ) {
            str_right = str_cursor + str_right;
          }

          $left.text(str_left);
          $cursor.text(str_tmp_cursor);
          $right.text(str_right);
        } else if (e.which === 39) {
          // 向右方向键
          str_left = $left.text();
          str_right = $right.text();
          str_cursor = $cursor.text();
          flag_end = false;

          if (str_right.length === 0) {
            if ($cursor.html() === "&nbsp;") {
              return;
            }
            flag_end = true;
          }
          str_left += str_cursor;
          if (flag_end) {
            $cursor.html("&nbsp;");
            str_right = "";
          } else {
            $cursor.text(str_right.substring(0, 1));
            str_right = str_right.substring(1);
          }

          $left.text(str_left);
          $right.text(str_right);
        } else if (e.which === 38) {
          // 向上方向键
          if (cmd_pos < 0) {
            return;
          }

          $left.text(cmd_cache[cmd_pos]);
          cmd_pos--;
          $cursor.html("&nbsp;");
          $right.text("");
        } else if (e.which === 40) {
          // 向下方向键
          if (cmd_pos >= cmd_cache.length - 1) {
            $left.text("");
          } else {
            cmd_pos++;
            $left.text(cmd_cache[cmd_pos]);
          }

          $cursor.html("&nbsp;");
          $right.text("");
        } else if (e.which === 46) {
          // delete
          str_right = $right.text();

          if (str_right.length === 0) {
            if ($cursor.html() === "&nbsp;") {
              return;
            }
            flag_end = true;
          }

          if (flag_end) {
            $cursor.html("&nbsp;");
          } else {
            $cursor.text(str_right.substring(0, 1));
            $right.text(str_right.substring(1));
          }
        } else if (e.which === 35) {
          // end
          str_right = $right.text();
          str_cursor = $cursor.text();
          let str_all = $input.text();

          if (str_right.length === 0 && $.trim(str_cursor).length === 0) {
            return;
          }
          $left.text(str_all);
          $cursor.html("&nbsp;");
          $right.text("");
        } else if (e.which === 36) {
          // home
          str_left = $left.text();
          let str_all = $input.text();

          if (str_left.length === 0) {
            return;
          }
          $left.text("");
          $cursor.text(str_all.substring(0, 1));
          $right.text(str_all.substring(1, str_all.length));
        } else if (e.which === 85 && e.ctrlKey) {
          // Ctrl + U
          e.preventDefault();

          $left.text("");
        } else if (e.which === 76 && e.ctrlKey) {
          // Ctrl + L
          e.preventDefault();

          $shell.siblings().remove();
        }
      });
      _this.$socket.on("message", function(mm) {
        // console.log("回调");
        _this.addTemplate(mm);
      });
    },
    addTemplate(mm) {
      // console.log('dcsdcsfvsd33333333333');

      // if (this.mas !== mm) {
      let isWel = mm.indexOf("successfully") > -1;
      let classVal = mm.indexOf("rror") > -1 ? "error" : isWel ? "" : "info";
      $(".shell-box .shell-view").before(
        this.template_output({
          separate: isWel ? "√" : "&gt;",
          value: mm,
          classVal: classVal
        }) + `<br class=${this.isOk ? "" : "pass"}>`
      );
      //   this.mas = mm;
      // }
    },
    handleRouter() {
      // window.sessionStorage.setItem('shell','yes')
      this.$router.push({ path: "/" });
    },
    // 登录
    handleSubmit(pass) {
      let info = this.$store.state.variable.info;
      let data = {
        name: info.name,
        pass: pass,
        shell: "yes"
      };
      // console.log('sdvsdgs111111111');
      this.$axios
        .post("/api/person/user/login", this.$qs.stringify(data))
        .then(res => {
          if (res.data.result) {
            this.isOk = true;
            $(".pass").remove();
            this.addTemplate(
              "You have successfully logged in，Web command line tool connected successfully!"
            );
          } else {
            this.addTemplate("Wrong password, please input again!");
            this.isOk = false;
          }
        })
        .catch(function(error) {
          // this.addTemplate("Wrong password, please input again!");
          this.isOk = false;
        });
    }
  },
  beforeDestroy() {
    window.clearInterval(this.timer); //关闭
    window.location.reload();
    // if (this.$socket) {
    //   this.$socket.close();
    //   this.$socket.disconnect();
    //   // this.socket = null;
    // }
  }
};
</script>

<style lang="scss">
@import "./index.scss";
</style>
