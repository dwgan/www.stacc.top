## 一、克隆仓库到本地

   ```shell
   git clone git@github.com:dwgan/www.stacc.top.git
   ```

   or pull update to local repository.

   ```shell
   git pull
   ```

## 二、修改对应文件

You can build the website on local machine or push them to Github directly if you are sure of no gramma error.

<details><summary>点这里查看如何在本地编译网站</summary>

### 1. 安装 Ruby 环境

首先，你需要在 Windows 上安装 Ruby。推荐使用 [RubyInstaller](https://rubyinstaller.org/) 来安装。

1. 下载并安装 **Ruby+Devkit**（选择 2.x 版本）。
2. 在安装过程中，选择“Add Ruby executables to your PATH”。
3. 安装完成后，打开命令行（cmd 或 PowerShell），输入 `ruby -v` 和 `gem -v` 来检查 Ruby 是否成功安装。

### 2. 安装 Jekyll 和 Bundler

打开命令行工具，输入以下命令来安装 **Jekyll** 和 **Bundler**：

```bash
gem install jekyll bundler
```

### 3. 克隆 Minimal Mistakes 仓库

选择一个文件夹来存放你的网站，然后在命令行中执行：

```bash
git clone https://github.com/mmistakes/minimal-mistakes.git .
```

如果你没有安装 Git，可以从 [Git 官网](https://git-scm.com/) 下载并安装。

### 4. 安装依赖

在命令行中，进入到你的项目目录并安装依赖：

```bash
bundle install
```

### 5. 启动本地服务器

安装完依赖后，你可以使用以下命令启动本地服务器：

```bash
bundle exec jekyll serve --incremental
```

网站将在 `http://localhost:4000` 上运行。

</details>

## 三、删除本地多余文件

   ```
   rm -r _site
   ```

## 四、添加到本地暂存区

   ```shell
   git add .
   ```

## 五、提交到本地仓库

   ```shell
   git commit -m 'descrip what have been modified'
   ```

## 六、推送到远程仓库

   ```shell
   git push origin main
   ```

## 七、等待远程仓库编译约1分钟，到网站查看更改
