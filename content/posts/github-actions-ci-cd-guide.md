---
title: "GitHub Actions CI/CD 实战：让你的项目自动构建与部署"
date: 2026-04-01
draft: false
description: "手把手教你用GitHub Actions搭建CI/CD流水线，支持GitHub Pages自动部署。"
tags: ["GitHub Actions", "CI/CD", "DevOps", "自动化部署", "GitHub"]
categories: ["开发工具"]
slug: "github-actions-cicd"
showToc: true
author: "Aitoolhub"
---

GitHub Actions 是 GitHub 官方提供的自动化工具，可以帮你实现代码提交后自动测试、构建、部署。本文用实际案例演示完整流程。

<!--more-->

## GitHub Actions 核心概念

| 概念 | 说明 |
|------|------|
| Workflow | 自动化流程（YAML文件） |
| Job | 工作单元（可并行） |
| Step | 具体步骤（执行命令） |
| Action | 可复用动作 |

## 最小可用的 Workflow

创建一个 `.github/workflows/deploy.yml`：

```yaml
name: Deploy to GitHub Pages

on:
  push:
    branches: [main]

jobs:
  build-and-deploy:
    runs-on: ubuntu-latest
    steps:
      - name: Checkout
        uses: actions/checkout@v4

      - name: Setup Hugo
        uses: peaceiris/actions-hugo@v3

      - name: Build
        run: hugo --minify

      - name: Deploy
        uses: peaceiris/actions-gh-pages@v4
        with:
          github_token: ${{ secrets.GITHUB_TOKEN }}
          publish_dir: ./public
```

保存后推送到 main 分支，每次 push 都会自动触发构建和部署。

## 进阶：多环境部署

```yaml
jobs:
  test:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - name: Run tests
        run: npm test

  deploy-staging:
    needs: test
    if: github.ref == 'refs/heads/develop'
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - name: Deploy to Staging
        run: ./deploy.sh staging

  deploy-prod:
    needs: test
    if: github.ref == 'refs/heads/main'
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - name: Deploy to Production
        run: ./deploy.sh production
```

## 常用 Action 推荐

| Action | 用途 |
|--------|------|
| actions/checkout | 代码检出 |
| peaceiris/actions-hugo | Hugo静态站构建 |
| peaceiris/actions-gh-pages | 部署到GitHub Pages |
| actions/setup-node | Node环境 |
| appleboy/ssh-action | SSH远程执行 |

## 常见问题

**Q: GitHub Token 怎么使用？**
A: `secrets.GITHUB_TOKEN` 是 GitHub 自动提供的，不需要手动创建。

**Q: 部署失败怎么办？**
A: 查看 Actions 日志，检查权限设置或依赖安装是否正常。

**Q: 如何调试 Workflow？**
A: 在本地使用 `act` 工具模拟运行，或添加 ` Debug` step 输出中间变量。

## 总结

GitHub Actions 功能强大，上述只是冰山一角。善用 CI/CD 能大大提升开发效率，专注于写代码而非部署操作。

*关注我，获取更多 DevOps 实战技巧。*
