<template>
  <div>
    <header class="header__content">
      <Decorate />
      <div class="container">
        <div class="he-row">
          <div class="header_left">
            <div class="logo">
              <Icon type="ios-list-box" />
            </div>
            <div class="header__info">
              <span>
                <h2>日志列表</h2>
                <p>这里将展示所有项目的日志信息，可为初步锁定问题提供参考。</p>
              </span>
            </div>
          </div>
        </div>
      </div>
    </header>
    <section>
      <div>
        <div class="query">
          <div>
            <label>请输入信息：</label>
            <Input v-model="value" placeholder="请输入检索信息..." style="width: 200px" />
          </div>
          <div>
            <label>开始时间：</label>
            <DatePicker
              type="datetime"
              v-model="startTime"
              placeholder="请输入开始时间..."
              style="width: 200px"
            ></DatePicker>
          </div>

          <div>
            <label>结束时间：</label>
            <DatePicker
              type="datetime"
              v-model="endTime"
              placeholder="请输入结束时间..."
              style="width: 200px"
            ></DatePicker>
          </div>
          <div>
            <Button type="info" @click="handleGetData(true)">确定</Button>
            <Button type="success" @click="handleClear">重置</Button>
          </div>
        </div>
        <div class="content">
          <ul>
            <li v-for="(item,i) in listData" :key="'we'+i">
              <span :style="'margin-right:'+(item.mark?'10px;':'30px;')">{{item.mark?'>':''}}</span>
              <span :style="{color:item.color}">{{item.text}}</span>
            </li>
          </ul>
          <!-- <List border size="small">
            <ListItem v-for="(item,i) in listData" :key="'we'+i">
            
            </ListItem>
          </List>-->
        </div>
        <div class="page">
          <Page
            class="page-warp"
            prev-text="上一页"
            next-text="下一页"
            show-elevator
            show-total
            :total="total"
            :page-size="20"
            :page-size-opts="pageSizeOption"
            show-sizer
            @on-change="changePage"
            @on-page-size-change="changeSizePage"
          />
        </div>
      </div>
    </section>
  </div>
</template>
<script>
import Decorate from "../header/decorate";
export default {
  components: {
    Decorate
  },
  data() {
    return {
      pageSizeOption: [20, 40, 60, 80, 100],
      pageNo: 1,
      pageSize: 20,
      total: 10,
      listData: [],
      value: "",
      startTime: "",
      endTime: ""
    };
  },
  created() {
    this.handleGetData();
  },
  mounted() {
    // this.handleBuShuRiZhi();
  },
  methods: {
    handleClear() {
      this.pageNo = 1;
      this.pageSize = 20;
      this.value = "";
      this.startTime = "";
      this.endTime = "";
      this.handleGetData();
    },

    // 获取数据
    handleGetData(val) {
      this.$Message.destroy();
      if (val) {
        this.pageNo = 1;
        this.pageSize = 20;
      }
      let data = {
        pageNo: this.pageNo,
        pageSize: this.pageSize
      };
      if (this.value) {
        data.value = this.value;
      }
      if (this.startTime) {
        data.startTime = this.startTime;
      }
      if (this.endTime) {
        data.endTime = this.endTime;
      }

      this.$axios
        .get("/api/logs/run/get", { params: data })
        .then(res => {
          if (res.data.code == 200) {
            let data = res.data.data.list;
            let mark = "";
            data = data.map(item => {
              if (item.indexOf("rror") > -1 || item.indexOf("错误") > -1) {
                mark = "red";
              } else if (
                item.indexOf("uccess") > -1 ||
                item.indexOf("成功") > -1
              ) {
                mark = "#19be6b";
              } else if (
                item.indexOf("WARN") > -1 ||
                item.indexOf("警告") > -1
              ) {
                mark = "#e96900";
              } else if (item.indexOf("Proxy") > -1) {
                mark = "#2d8cf0";
              } else if (item.indexOf("port") > -1) {
                mark = "#DD6DA6";
              }
              //  else if (item.indexOf("GET") > -1) {
              //   mark = "#CA8A89";
              // } else if (item.indexOf("POST") > -1) {
              //   mark = "#E6C993";
              // }
              return {
                text: item,
                color: mark,
                mark: item.indexOf("[") > -1
              };
            });
            this.listData = data;
            this.total = res.data.data.total;
          } else {
            this.$Message["error"]({
              background: true,
              content: "数据请求失败！"
            });
          }
        })
        .catch(function(error) {
          console.log(error);
        });
    },
    changePage(event) {
      this.pageNo = event;
      this.handleGetData();
    },
    changeSizePage(event) {
      this.pageSize = event;
      this.handleGetData();
    }
  }
};
</script>
<style lang="scss" scoped>
section {
  > div {
    background: #fff;
    padding: 20px;
  }
  .query {
    display: flex;
    // justify-content: center;
    // align-items: center;
    margin-bottom: 20px;
    border-bottom: 1px solid #e8eaec;
    padding: 20px;
    > div {
      margin: 0 30px;
      button {
        margin-right: 20px;
      }
    }
    label {
    }
  }
  .content {
    margin-bottom: 20px;
    ul {
      li {
        margin-bottom: 10px;
      }
    }
  }
  /deep/ .ivu-list-bordered {
    border-radius: 0;
  }
}
</style>
