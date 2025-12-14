
## 环境搭建

### 1. 安装 Ruby 和 Bundler

- 首先，安装 [Ruby](https://www.ruby-lang.org/en/documentation/installation/)，建议使用 [RubyInstaller](https://rubyinstaller.org/) 来安装 Ruby。
- 安装好 Ruby 后，打开命令行并安装 Bundler：

```bash
  gem install bundler
```

### 2. 安装依赖

* 克隆或下载你的 Jekyll 项目到本地。
* 在项目目录下，运行以下命令来安装所有依赖：

  ```bash
  bundle install
  ```

  这会根据 `Gemfile` 安装所有必需的 Gem 包。

## 编译和查看网站

### 3. 编译网站并启动本地服务器

* 运行以下命令来启动 Jekyll 本地服务器：

  ```bash
  bundle exec jekyll serve
  ```

* 默认情况下，网站将会在 [http://localhost:4000](http://localhost:4000) 上运行。

## 修改网站内容

### 4. 编辑网站内容

* 你可以修改 `/_config.yml` 文件来配置网站的全局设置。
* 修改网站的页面或文章时，编辑 `_posts` 目录中的 Markdown 文件（例如：`2025-12-14-example-post.md`）。
* 如果需要修改布局，可以编辑 `/_layouts` 目录中的相关文件。

### 5. 删除不需要的文件

* 删除 `_posts` 中不需要的文章，或直接删除不再使用的页面和资源文件。

## 提交到 GitHub

### 6. 提交修改到 GitHub

* 提交你的更改到 GitHub，使用以下命令：

  ```bash
  git add .
  git commit -m "描述你的更改"
  git push origin main
  ```

  确保将 `main` 替换为你的主分支名称。

## 常见问题

### 7. `bundle install` 时遇到问题

* 如果在执行 `bundle install` 时遇到权限问题，尝试使用 `sudo`（Linux/macOS）或以管理员身份运行命令行（Windows）。

### 8. 编译时出错

* 确保你已经安装了所有的依赖。如果缺少某个 gem，运行 `bundle install` 重新安装。

### 9. 如何卸载不需要的依赖

* 编辑 `Gemfile` 移除不需要的 Gem 包，然后运行：

  ```bash
  bundle install
  ```
