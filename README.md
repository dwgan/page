# 甘东文个人学术主页

基于 Jekyll + academicpages 的静态网站，托管在 GitHub Pages，域名 `dwgan.top`。

## 环境要求

- Ruby 3.1
- Bundler

> 本项目依赖的 `github-pages`/Jekyll 旧版本在 Ruby 3.2+ 上会因移除 `String#tainted?` 而无法运行，请使用 Ruby 3.1。

推荐使用 Homebrew 安装 Ruby 3.1：

```bash
brew install ruby@3.1
export PATH="/opt/homebrew/opt/ruby@3.1/bin:$PATH"
```

## 安装依赖

```bash
bundle install
```

依赖会安装到项目内的 `vendor/bundle`。

## 本地启动

```bash
bundle exec jekyll serve --config _config.yml,_config.dev.yml --host 127.0.0.1 --port 4000
```

打开 http://127.0.0.1:4000

## 目录结构

- `_config.yml` 生产配置；`_config.dev.yml` 本地开发覆盖
- `_pages/` 普通页面（英文在根路径，中文在 `zh-cn/`）
- `_research/` 研究方向集合（英文 + `zh-cn/` 中文）
- `_data/` 数据源：`authors.yml`、`home.yml`、`research.yml`、`navigation.yml`、`ui-text.yml`
- `_layouts/` 布局；`_includes/` 局部模板；`_sass/` 样式源码
- `assets/` 编译后/静态资源；`images/` 图片

## 修改内容

- 页面内容在 `_pages/` 和 `_research/` 下编辑 Markdown。
- 全局配置在 `_config.yml`。
- 修改布局或样式时分别编辑 `_layouts/`、`_sass/`。
- 中文 UI 文案在 `_data/ui-text.yml` 的 `zh-CN` 区块。

## 提交

```bash
git add .
git commit -m "描述你的更改"
git push origin main
```
