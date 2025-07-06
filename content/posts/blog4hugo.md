---
title: 'Hugo+PaperMod+Github创建自己的博客网站'  # 标题
date: '2025-07-06T23:49:15+08:00'  # 只需要修改前面的日期
draft: false  # 是否为草稿，是草稿在hugo server时不会显示
toc: true  # 是否显示大纲
tags: ["Odds and Ends"]  # 用于显示标签
---
1. 安装hugo
2. 新建aaronpan.github.io仓库
3. clone 到本地
4. hugo new site aaronpan.github.io --format yaml --force
5. cd aaronpan.github.io
6. git add .
7. git commit -m "Build with hugo"
8. git push
9. git submodule add --depth=1 https://github.com/adityatelange/hugo-PaperMod.git themes/PaperMod
10. git submodule update --init --recursive
11. git submodule update --remote --merge
12. 配置hugo.yaml

```yaml
baseURL: https://aaronpan.github.io/
languageCode: en-us
title: Aaron-Pan's Log
theme: ["PaperMod"]

menu:
  main:
    - identifier: Posts
      name: Posts
      url: /posts/
      weight: 10
    - identifier: Tags
      name: Tags
      url: /tags/
      weight: 20
    - identifier: Search
      name: Search
      url: /search/
      weight: 30
    - identifier: Archives
      name: Archives
      url: /archives/
      weight: 40

params:
  assets:
    css:
      - css/custom.css
  title: Aaron-Pan's Blog
  description: My personal blog using Hugo and PaperMod
  author: Aaron-Pan
  profileMode:
    enabled: false
  showTags: true
  showToc: true
  showReadingTime: true
  showPostNavLinks: true
  homeInfoParams:
    Title: 🐕Welcome to Aaron-Pan's Log
    Content: |
      Hi, I am a student from the College of Computer Science and Technology, Zhejiang University. I will share some of my learning experiences and thoughts here. Hope you like it!😊
  socialIcons:
    - name: github
      url: https://github.com/Master-Pan
    - name: csdn
      url: https://blog.csdn.net/plc19981102?type=blog
  defaultTheme: light
  mainSections:
    - posts
  fuseOpts:
    isCaseSensitive: false
    shouldSort: true
    location: 0
    distance: 1000
    threshold: 0.4
    minMatchCharLength: 0
    # limit: 10 # refer: https://www.fusejs.io/api/methods.html#search
    keys: ["title", "permalink", "summary", "content"]

outputs:
  home:
    - HTML
    - RSS
    - JSON

```

13. 创建新博客hugo new posts/blog4hugo.md
14. 添加标题

```markdown
---
title: 'Hugo+PaperMod+Github创建自己的博客网站'  # 标题
date: '2025-07-06T23:49:15+08:00'  # 只需要修改前面的日期
draft: false  # 是否为草稿，是草稿在hugo server时不会显示
toc: true  # 是否显示大纲
tags: ["Odds and Ends"]  # 用于显示标签
---
```

15. 添加归档，在content目录下创建archives.md

```markdown
---
title: "Archives"
layout: "archives"
url: "/archives/"
summary: archives
---
```

16. 添加搜索，在content目录下创建search.md

```markdown
---
title: "Search"  # in any language you want
layout: "search"  # necessary for search
# url: "/archive"
# description: "Description for Search"
summary: "search"
placeholder: "Keywords for Searching"
---
```

17. 运行hugo server
18. 设置github.io的自动推送，在aaronpan.github.io的settings的pages下面，将Build and deployment中Source修改为Github Actions
19. 在本地的.github目录下创建一个workflows文件夹，在 workflows下创建hugo.yaml文件

```yaml
# Sample workflow for building and deploying a Hugo site to GitHub Pages
name: Deploy Hugo site to Pages

on:
  # Runs on pushes targeting the default branch
  push:
    branches:
      - main

  # Allows you to run this workflow manually from the Actions tab
  workflow_dispatch:

# Sets permissions of the GITHUB_TOKEN to allow deployment to GitHub Pages
permissions:
  contents: read
  pages: write
  id-token: write

# Allow only one concurrent deployment, skipping runs queued between the run in-progress and latest queued.
# However, do NOT cancel in-progress runs as we want to allow these production deployments to complete.
concurrency:
  group: "pages"
  cancel-in-progress: false

# Default to bash
defaults:
  run:
    shell: bash

jobs:
  # Build job
  build:
    runs-on: ubuntu-latest
    env:
      HUGO_VERSION: 0.141.0
    steps:
      - name: Install Hugo CLI
        run: |
          wget -O ${{ runner.temp }}/hugo.deb https://github.com/gohugoio/hugo/releases/download/v${HUGO_VERSION}/hugo_extended_${HUGO_VERSION}_linux-amd64.deb \
          && sudo dpkg -i ${{ runner.temp }}/hugo.deb
      - name: Install Dart Sass
        run: sudo snap install dart-sass
      - name: Checkout
        uses: actions/checkout@v4
        with:
          submodules: recursive
          fetch-depth: 0
      - name: Setup Pages
        id: pages
        uses: actions/configure-pages@v5
      - name: Install Node.js dependencies
        run: "[[ -f package-lock.json || -f npm-shrinkwrap.json ]] && npm ci || true"
      - name: Build with Hugo
        env:
          HUGO_CACHEDIR: ${{ runner.temp }}/hugo_cache
          HUGO_ENVIRONMENT: production
          TZ: America/Los_Angeles
        run: |
          hugo \
            --gc \
            --minify \
            --baseURL "${{ steps.pages.outputs.base_url }}/"
      - name: Upload artifact
        uses: actions/upload-pages-artifact@v3
        with:
          path: ./public

  # Deployment job
  deploy:
    environment:
      name: github-pages
      url: ${{ steps.deployment.outputs.page_url }}
    runs-on: ubuntu-latest
    needs: build
    steps:
      - name: Deploy to GitHub Pages
        id: deployment
        uses: actions/deploy-pages@v4

```

20. git add .
21. git commit -m "Deploy hugo"
22. git push
