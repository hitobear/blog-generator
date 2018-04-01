
---
title: 搭建我的hexo博客
---


我的hexo博客终于搭建完成了，虽然用基础工具都是前人的分享，各种高大上的hexo博客教程也有很多了，但这里还是想分享一下自己博客能够在浏览器中看到的喜悦，所以来做一下博客搭建的小总结(本文适用于windows下的搭建)~

# 安装git

因为目标是基于github.io来实现博客的访问，当然离不开git仓库了，后续hexo的发布也会和github绑定，除此之外，关键的一点是，gitbash安装后不仅可以执行git相关命令，它相当于一个windows上的linux操作系统，可以执行大部分linux命令，所以这里首要的就是安装git了~
首先去[git官网](https://git-scm.com/)下载gitbash(只需右击git bash here即可进行命令行操作)

# 安装node

之所以安装node有两个原因：
> 1. node是一个JS执行环境，之后可以基于node进行js相关的服务端开发
> 2. 很多node相关的程序包都可以通过npm来进行管理，包括上传，下载，管理依赖，可以说npm是一个nodejs的程序仓库,有了npm，就可以获得很多已有的成熟的nodejs程序，所以我们需要npm，包括之后会用到的hexo也可以通过npm获得，而这里只要安装了node，就会同时自动安装npm，供我们以后使用

## 安装配置

> 1. 从[官网](https://nodejs.org/en/)下载安装
> 2. 执行 `node -v` `npm -v`命令，能正确的输出版本信息，说明安装成功 
> 3. 右击打开git bash，依次执行如下命令，对npm进行配置
```
npm config set registry https://registry.npm.taobao.org/
npm config set loglevel http
npm config set progress false
```
npm的配置文件是~/.npmrc,可以编辑该文件进行配置的变更

# hexo安装和使用

> 1. 右击进入 gitbash
> 2. 安装hexo命令行工具 npm install -g hexo-cli
> 3. 新建一个空目录，并进入该目录，在该目录下进行操作 `mkdir hexo && cd ..`
> 4. 初始化本地的hexo目录，`hexo init myBlog`
> 5. `cd myBlog && npm i` myBlog目录下的package.json进行依赖的安装
> 6. `hexo new firstblog` 此时第一篇博客文档(`firstblog.md`)会在`source/_posts`目录下生成
> 7. start 文档路径,开始编辑博客内容
> 8. start _config.yml,编辑博客的配置
`把第 6 行的 title 改成你想要的名字
把第 9 行的 author 改成你的大名
把最后一行的 type 改成 type: git
在最后一行后面新增一行，左边与 type 平齐，
加上一行 repo:  仓库地址 （仓库地址以 git@github.com: 开头）`
> 9. 这里说到hexo的发布要和github的仓库关联，所以要配置repo为github的仓库地址,如果还没有gigpage仓库，请参考下一节 gitPage仓库的配置。
> 10. `npm install hexo-deployer-git --save`，安装 git 部署插件,使得自动部署到github仓库
> 11. `hexo deploy` 发布当前的md博客
> 12. 进入`username.github.io`仓库，可以看到当前仓库有了新的commit，多了很多文件，就是hexo自动push的
> 12. 浏览器输入username.github.io可以预览生成的博客首页了

# 博客更新
> 1. 在本地的博客目录下编辑内容(可以通过`hexo new`或直接编辑新建)
> 2. 执行 `hexo generate`
> 执行 `hexo deploy`即可刷新你的gitpage页面了

# 博客主题的变更

有很多开源的博客主题,这里有一些[主题推荐](https://www.zhihu.com/question/24422335)，可找到合适自己的主题应用到自己的博客上，省时省力又漂亮(感兴趣的话以后有能力可以制作自己的博客主题分享给大家哈哈)，我这里选择了`iissnan/hexo-theme-next`这个主题，所以以它为例进行下配置
```
cd themes
git clone git@github.com:iissnan/hexo-theme-next.git
cd ..
将 _config.yml 的第 75 行改为 theme: hexo-theme-next
hexo generate
hexo deploy
```

# 博客源码的保存

进入你的gitpage仓库会发现，仓库上的代码好像都不是我认识的，我写的markdown文件怎么不见了呢？确实这些代码都是由hexo生成后的博客代码，可以说是经过了hexo的编译转化的，我当然不认识了:>，那怎么样把我的博客源码推上仓库呢？也简单，新建一个github仓库就可以了，如新建`blog-generator`,把本地的hexo博客所在目录和`blog-generator`绑定就可以了，具体步骤如下:
> 1. 在 GitHub 创建 blog-generator `空仓库`
> 2. 在本地的hexo博客代码根目录如myBlog下执行如下命令
```
echo "hello" > README.md
git init
git add README.md
git commit -m "first commit"
git add origin git@github.com:hitobear/blog-generator.git
git push -u origin master
```
执行完这几个命令后，博客的源码，就会出现在`blog-generator`仓库了，以后每次变更博客内容后，在执行`hexo generator && hexo deploy`命令之后，再执行 `git add ,git commit,git push`即可将最新的博客内容包括原始的markdown文件提交到`blog-generator`仓库了~

# gitPage仓库的配置

> 1. 首先由一个github账号
> 2. 新建一个名为 username.github.io的仓库，该仓库默认是开启gitPage功能的，如果仓库名称是其他，则需要另行配置以开启gitPage功能。
> 3. 只有本地git配置了以后，才可以将本地仓库push到远端的github仓库，所以还需要打开gitbash执行以下步骤进行git配置(已经绑定过github并能正常push代码的可以跳过4-8步)
> 4. 生成本地的sshkey, `ssh-keygen -t rsa -b 4096 -C 邮箱名称`,此时命令行会和你交互，这期间连按三次回车即可
> 5. 执行完4之后查看`~/.ssh/`目录下会有`id_rsa.push`文件,复制该文件的全部内容
> 6. 进入 [github配置页面](https://github.com/settings/keys)，单机New SSH Key，需要输入Title和Key，Title可以随意填写一个标识名称，此时的Key则是刚刚第5步复制的内容，直接粘贴即可，点击 Add SSH key
> 7. 回到gitBash,输入`ssh -T git@github.com`命令，与控制台的交互过程中输入yes即可完成和github的绑定配置
> 8. 接下来，依次执行以下几个命令进行git本地仓库的配置
```
git config --global user.name 你的英文名
git config --global user.email 你的邮箱
git config --global push.default matching
git config --global core.quotepath false
git config --global core.editor "vim"
```
