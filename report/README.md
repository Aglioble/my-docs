\# 实践项目2：个人实验报告



\## 1. 基本信息

\- GitHub 登录名：Aglioble

\- 个人仓库地址：https://github.com/Aglioble/my-docs

\- 最终网站地址：https://aglioble.github.io/my-docs/



\## 2. 实践过程与关键截图

\### 2.1 仓库创建

操作：登录 GitHub 网页端，新建公开仓库`my-docs`，初始化仓库，之后在本地使用 Git 将仓库克隆到本地工作目录，作为 MkDocs 文档项目根目录。

结果：成功创建远程仓库，本地和远程仓库建立关联，后续可以执行 git 提交、推送操作。

!\[个人仓库](images/1.png)



\### 2.2 项目结构与 MkDocs 配置

操作：在本地仓库中搭建标准 MkDocs 目录结构，新建`docs`文件夹存放 Markdown 文档，创建`mkdocs.yml`配置文件，配置站点名称、网站地址、仓库地址、material 主题、导航菜单，同时编写`requirements.txt`声明依赖包 mkdocs、mkdocs-material。

结果：项目目录结构完整，mkdocs.yml 配置包含首页、关于、使用指南、API 参考 4 个导航项，配置编码为 UTF-8，缩进规范，YAML 语法校验通过。
(images/2.png)
\### 2.3 本地构建或预览

操作：在本地虚拟环境中安装依赖，执行`mkdocs serve`进行本地预览；执行`mkdocs build --strict`进行严格模式构建。

结果：本地预览服务正常启动，访问`http://127.0.0.1:8000`可以打开文档网站，4 个导航页面均可正常切换；构建命令无报错，自动生成 site 静态文件夹。
(images/3.png)
\### 2.4 Pages 设置

作：进入仓库 Settings → Pages 页面，将 Build and deployment 的 Source 选项修改为`GitHub Actions`，使用 Actions 工作流自动部署，不再使用分支部署方式。

结果：Pages 部署源配置完成，允许工作流将静态产物发布到 GitHub Pages 服务。
(images/4.png)
\### 2.5 Actions 首次构建和部署

操作：在仓库内新建`.github/workflows/deploy.yml`自动化工作流文件，配置触发条件、Python 环境、依赖安装、构建、上传产物、部署 Pages 等步骤；将所有文件提交并推送到 main 分支，触发 Actions。

结果：工作流自动执行，build、deploy 两个任务全部绿色成功，文档站点成功发布到目标 Pages 地址。
(images/5.png)
\### 2.6 网站访问与四页导航检查

操作：部署完成后尝试访问网站地址，检查导航栏四个页面是否都存在、跳转是否正常。

结果：GitHub Actions 侧部署成功，受国内网络限制本机无法直接访问线上地址；本地 mkdocs serve 环境下，首页、关于、使用指南、API 参考 4 个导航页面均可正常加载、切换，导航配置生效。
(images/6.png)
\### 2.7 第二次推送与自动更新验证

操作：修改 docs 目录下任意 md 文档内容，提交新版本并推送到 main 分支，观察 Actions 是否自动重新构建、更新线上站点。
(images/7-a.png)
(images/7-b.png)
\- 对应提交链接：https://github.com/Aglioble/my-docs/commit/bcba73e32f91ecd9e4ac42ed31e775de5aeb7a97

\- 对应 Actions 运行链接：https://github.com/Aglioble/my-docs/actions/runs/43358856495



\## 3. 问题与解决

记录实际现象、关键日志、原因和解决操作。

如没有遇到错误，说明你核对过哪些日志和配置。

1\. 现象：执行`python -m mkdocs build`时报错`Config value 'site\_name': Required configuration not provided.`

日志：`ERROR   -  Config value 'site\_name': Required configuration not provided. Aborted with a configuration error!`

原因：mkdocs.yml 没保存。

解决：保存。

2\. 现象：git push 使用 SSH 方式时报`Permission denied (publickey)`

日志：`git@github.com: Permission denied (publickey).`

原因：本机没有配置并上传 SSH 公钥，SSH 认证失败。

解决：切换远程地址为 HTTPS 方式，使用 Personal access token 完成推送。

3\. 现象：推送成功、Actions 显示绿色成功，但本地浏览器打不开 Pages 网站

日志：无程序报错，部署任务状态 Success

原因：国内网络环境对 GitHub Pages 访问存在限制，不是代码、部署流程错误。

解决：借同学电脑查看网址。



\## 4. 原理理解

1\. docs/ 和 site/ 各保存什么？为什么不用手工修改 site/？

`docs/`：存放原始 Markdown 源码文档，是我们编写、维护的源文件；

`site/`：mkdocs 构建后生成的静态网页文件（html、css、js 等），是最终给浏览器访问的产物。

不能手工修改 site/：site 是 mkdocs 自动编译生成的，每次执行 build 都会清空并重写 site 目录，手动修改的内容会在下次构建时被覆盖，修改源码 docs 下的 md 文件才是正确方式。

2\. 本次工作流在什么情况下自动运行？

当代码推送到 main 分支，并且修改的文件属于配置里指定路径：`docs/\*\*`、`mkdocs.yml`、`requirements.txt`、`.github/workflows/deploy.yml`时自动触发；也可以手动在 Actions 页面点击工作流，使用`workflow\_dispatch`手动触发。

3\. build 与 deploy 两个任务有什么区别？

build 任务：拉取源码、搭建 Python 环境、安装依赖、执行 mkdocs build，把 md 文档编译成静态网页，打包成制品保存；

deploy 任务：依赖 build 任务完成，将 build 产出的静态站点制品上传发布到 GitHub Pages 服务，对外提供网页访问。

build 负责编译生成站点，deploy 负责把站点发布上线。

4\. 只修改 report/README.md 会触发部署吗？为什么？

不会触发。工作流的 paths 配置限定了仅修改 docs/\*\*、mkdocs.yml、requirements.txt、deploy.yml 才触发；report/README.md 不在监听路径列表内，推送后不会启动 Actions。

5\. 本次方案为何无需创建 gh-pages 分支？

当前使用`actions/upload-pages-artifact` + `actions/deploy-pages`这套新的 Pages Action，产物以 artifact 制品的形式临时存储，直接交付给 Pages 服务，不需要单独创建、维护 gh-pages 分支。

