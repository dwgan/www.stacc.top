## How to use

1. Clone this repository to local folder

   ```shell
   git clone git@github.com:dwgan/www.stacc.top.git
   ```

   or pull update to local repository.

   ```shell
   git pull
   ```

2. Edit `.md` file as your need.

3. Add modified file to local 

   ```shell
   git add .
   ```

4. Commit to local repository

   ```shell
   git commit -m 'descrip what have been modified'
   ```

5. Push to remote repository and build automatically.

   ```shell
   git push origin main
   ```

   
## How to build website on local machine


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
bundle exec jekyll serve
```

网站将在 `http://localhost:4000` 上运行。

### 6. 编辑和定制网站

- 配置文件：修改 `_config.yml` 来定制网站的基本信息。
- 页面内容：编辑 `_pages/` 中的页面，修改 `index.md` 来更新首页内容。
- 部署：完成修改后，可以将代码推送到 GitHub，并通过 GitHub Pages 部署你的站点。

### 注意事项

- Windows 系统上可能遇到一些兼容性问题，特别是在使用 Ruby 的扩展库时。如果遇到问题，可以考虑使用 Windows Subsystem for Linux (WSL)，它提供了一个更接近 Linux 的环境，有时能更顺畅地运行 Jekyll。

希望这些步骤能帮助你在 Windows 上成功启动并运行 **Minimal Mistakes** 主题！