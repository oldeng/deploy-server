<template>
  <div>
    <header class="header__content" style="position: relative; overflow: hidden;">
      <Decorate />
      <div class="container">
        <div class="he-row">
          <div class="header_left">
            <div class="logo">
              <Icon type="ios-book" />
            </div>
            <div class="header__info">
              <span>
                <h2>说明文档</h2>
                <p>阅读文档能使你完全掌握本系统的使用。</p>
              </span>
            </div>
          </div>
        </div>
      </div>
    </header>
    <section>
      <div class="docs-page">
        <h2>Web Deploy</h2>
        <p>Web Deploy 简称（WD）是一个前端自动化部署平台，能够快速部署前端界面的自动化服务，相比功能齐全的 Jenkins 来说更简洁、更轻量、部署时间更快速、不需要设置过多的配置项等，另外，此平台中包含配套Vue项目模板，可直接下载开发使用。</p>
        <h2>相关文档</h2>
        <div>
          <p>安装说明：<a href="https://zllugithub.github.io/web-deploy" target="_blank" rel="noopener">https://zllugithub.github.io/web-deploy</a></p>
          <p>Git地址：<a href="https://github.com/zlluGitHub/web-deploy" target="_blank" rel="noopener">https://github.com/zlluGitHub/web-deploy</a></p>
        </div>
        <h2>初识 WD</h2>
        <div>
          <p>1、项目的创建、访问、删除、迭代以及项目的版本回退部署和访问等功能。</p>
          <p>2、部署创建分为静态部署和自动部署这两种部署异同如下：</p>
          <p>3、登录用户信息更新，支持用户名、密码、邮箱地址的修改（暂不支持密码找回）；</p>
        </div>
        <h2>操作方法</h2>
        <div>
          <h4>静态部署</h4>
          <p>这种部署适用于一些静态资源的部署，部署方式如下：</p>
          <div>
            <p>★ 第一步：填写（选择）项目名称；</p>
            <p>★ 第二步：上传你的静态文件夹；</p>
            <p>★ 第三步：点击提交部署即可；</p>
          </div>
          <h4>自动化部署</h4>
          <p>这种部署方式适用于使用脚手架开发的项目并且代码托管到Git上，关联 Git 方式如下：</p>
          <div>
            <p>★ 第一步：在 Git 上配置 webhook 相关钩子链接，例如：打开 git 找到需要部署的项目仓库 -> Settings -> Webhooks -> Add webhook；</p>
            <p>★ 第二步：在 Target URL 中填入 http://127.0.0.1/api/deploy/git 地址；（http://127.0.0.1 为您当前部署平台地址）</p>
            <p>★ 第三步：POST Content Type 选择 apppcation/json；</p>
            <p>★ 第四步：在 Secret 中填入部署成功后返回的 Key 值；</p>
            <p>★ 第五步：Trigger On 选择 Push Events；</p>
          </div>
        </div>
        <h2>配套脚手架</h2>
        <div>
          <p>
            为了简化开发项目与本平台的关联步骤，本平台提供了一款基于 vue-cli3.0 进行包装的
            <a
              href="https://www.npmjs.com/package/swd-cli"
            >swd-cli</a> 脚手架，此脚手架已集成开发中常用的一些包，省去很多不必要的依赖安装过程。
          </p>
          <h4>使用方法</h4>
          <ul class="cli">
            <li>
              <span>1、全局安装 swd-cli 脚手架</span>
              <span class="code">npm install swd-cli -g</span>
            </li>
            <li>
              2、使用 swd-cli 创建项目
              <span class="code">swt init project-name</span>或者
              <span class="code">swt i project-name</span>
            </li>
            <li>说明：project-name 为需要创建的项目名称。</li>
          </ul>
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
  }
};
</script>
<style lang="scss" scoped>
.cli{
  li{
    margin: 10px 0;
  }
}
.code {
  display: block;
  border-radius: 3px;
  border: 1px solid #eee;
  background-color: hsla(0, 0%, 96.5%, 0.49);
  padding: 3px 8px;
  margin: 10px 0;
  color: #07a;
  font-size: 14px;
}
@import "./index.scss";
</style>
