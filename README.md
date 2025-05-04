# cloud-hw4
雲原生應用程式開發 Assignment 04
工海所 碩二 鄭新曄 r12525072

## Build Docker Image
docker build -t r12525072/2025cloud:v3 .

## Run Docker Container
docker run -p 8888:80 r12525072/2025cloud:v3

Login : http://localhost:8888


---

## 專案自動化產生 Container Image 的邏輯與 Tag 設計

本專案使用 GitHub Actions 自動建構並推送 Docker Image 至 Docker Hub。自動化邏輯與 Tag 命名規則如下：

### 自動化流程設計

```mermaid
    A[Push or PR 事件觸發 GitHub Actions] --> B[檢出原始碼]
    B --> C[登入 Docker Hub]
    C --> D[建構 Docker Image]
    D --> E[推送至 Docker Hub（r12525072/2025cloud）]
```

### Tag 選擇邏輯

當程式碼被 push 到 main 分支時，GitHub Actions 會自動建構並 push 一個標籤為 latest 的正式版 Image，代表主線目前的最新狀態。而當有 Pull Request 發出時，Action 僅會測試 Dockerfile 是否可成功建構，不會產生或推送任何 Image。未來若推出特定版本，可手動建立 Git 標籤，並擴充 GitHub Action 以支援這些條件式的版本化推送流程。

這樣的設計有助於區分「開發中狀態」與「穩定發佈版本」。

### 目前自動觸發條件

- `push` 至 `main`：
  - 自動執行 `docker build`
  - 登入 Docker Hub 並推送 `r12525072/2025cloud:latest`
- `pull_request`：
  - 僅建構，不推送（用於測試 Dockerfile 是否破壞）

### 工作流程檔（workflow）

位於 `.github/workflows/docker-build.yml`，觸發條件如下：

```yaml
on:
  push:
    branches: [ "main" ]
  pull_request:
```

表示只要有 PR 或主線更新，就會觸發 GitHub Actions 進行建構與驗證。
