
# why Argo CD

如果你沒有 argo, 你要 CD into k8s
![[IMG-Argo CD-k8s原生的持續交付工具 CD tool-20250810210739614.png]]


用了 argo
![[IMG-Argo CD-k8s原生的持續交付工具 CD tool-20250810210739793.png]]






## Argo CD 元件

Argo CD 的架構由多個元件組成，其中主要的四個元件是 Dex、Repo-server、Application-controller 和 Redis。以下是這些元件的簡要介紹：


### Dex: 身份驗證

功能：
- Dex 是一個開源的身份提供者，主要用於提供身份驗證服務。它支持多種身份驗證方法，包括 OAuth2、OpenID Connect 和 SAML。
用途：
- 在 Argo CD 中，Dex 用於管理用戶的身份驗證，允許用戶通過不同的身份提供者（如 GitHub、Google、LDAP 等）登錄 Argo CD。這樣可以簡化用戶管理和安全性。

### Repo-server: 與 Git 存儲庫進行交互

功能：
- Repo-server 是 Argo CD 的一個核心組件，負責與 Git 存儲庫進行交互。
用途：
- 用於拉取應用程序的 Kubernetes 配置文件（如 YAML 文件）和 Helm 圖表，並將這些配置文件轉換為 Kubernetes 資源。
- 還負責處理應用程序的同步和差異檢查，確保 Kubernetes 集群的狀態與 Git 存儲庫中的定義保持一致。


### Application-controller: 監控應用程序的狀態和操作執行
功能：
- Application-controller 是 Argo CD 的主要控制器，負責管理應用程序的生命週期。
用途：
- 它監控應用程序的狀態，並根據 Git 存儲庫中的定義自動執行同步操作。
- 當檢測到應用程序的狀態與 Git 存儲庫中的定義不一致時，Application-controller 會自動進行修復，確保集群的狀態與期望狀態一致。
- 此外，它還負責處理應用程序的健康檢查和回滾操作。

### Redis
功能：
- Redis 是一個高效的鍵值存儲系統，通常用作緩存和消息代理。
用途：
- 在 Argo CD 中，Redis 用於存儲應用程序的狀態和其他元數據，以提高性能和可擴展性。它幫助減少對後端數據庫的直接查詢，從而提高整體系統的響應速度。

總結
這四個元件共同協作，使 Argo CD 能夠實現強大的 GitOps 功能，提供自動化的應用程序部署和管理。Dex 負責身份驗證，Repo-server 負責與 Git 存儲庫的交互，Application-controller 負責應用程序的生命週期管理，而 Redis 則提供高效的數據存儲和緩存支持。這種架構使得 Argo CD 成為一個靈活且強大的持續交付解決方案。

![[IMG-Argo CD-k8s原生的持續交付工具 CD tool-20250810210739974.png]]




--- 


# 上 code 流程


流程 is like: gitlab ci pipeline → build img file → api trigger to tell argo to sync and deploy the new img (replicate set → pod)


code 推到 gitlab

repo 裡面需要有 dockerfile 弄好, 可以讓後面去包

e.g. pricing service
```dockerfile title:Dockerfile fold

FROM node:20-alpine

WORKDIR /usr/src/app

RUN apk add git curl tar

COPY . .

USER root

RUN npm i

RUN npm run build

# Create logs directory and set permissions (as root)
RUN mkdir /usr/src/app/logs && \
    chown -R node:node /usr/src/app/logs

USER node


CMD ["npm", "run", "start:prod"]

```

透過 gitlab CICD 進行 test and buildm depoly
code 會 dockerize, img  放到 gitlab artifact


下面這是 build image yaml, user Wemo-Docker-Cli to build and Tag
```yaml title:build_image.yaml fold

# Common - We user Wemo-Docker-Cli to build and Tag
.wemo_docker_cli:
  stage: build_image
  image: registry.wemoscooter.com/sre/docker-v2:1.0.1
  dependencies: []
  variables:
    DOCKER_DRIVER: 'overlay2'
    DOCKER_TLS_CERTDIR: ''
    DOCKER_HOST: 'tcp://localhost:2375'
  services:
    - docker@sha256:2a7e15ecad4d18a6bb71b1b828dc7bb1b1a6c890e7420a89726a0649e94e8fb1
  tags:
    - kubernetes

build_image:
  extends: .wemo_docker_cli
  script:
    - wemo-docker-cli build-without-latest
  only:
    - qat
    - master
    - merge_requests

build_develop_image:
  extends: .wemo_docker_cli
  script:
    - wemo-docker-cli build
  only:
    - develop

# Tag Version
tag_production_image:
  extends: .wemo_docker_cli
  script:
    - wemo-docker-cli tag
  only:
    - tags


```

會呼叫 deploy script 的 webHook
```sh title:呼叫deployScript fold
#!/bin/bash
set -x
curl -s -o nul -X POST \
  -F token=9155452ab95ed0ba4ada0d86efa387 \
  -F ref=master \
  -F "variables[PROJECT_NAME]=${PROJECT_NAME}" \
  -F "variables[CLUSTER_ENV]=${CLUSTER_ENV}" \
  -F "variables[IMAGE_TAG]=${IMAGE_TAG}" \
  https://athena.wemoscooter.com/api/v4/projects/294/trigger/pipeline

# deploy gitlab
# https://athena.wemoscooter.com/sre/infra/gitlab-ci-deploy-scripts

```
src: /Users/re4388/project/work/pricing-service/scripts/deploy/k8s.sh

deploy script 是參考這邊寫的:
src: /Users/re4388/project/work/gitops-helper/README.md


這個 script 做什麼?
- 會 update argocd k manifest (this repo, https://athena.wemoscooter.com/sre/infra/argocd-kubernetes-manifest)
- 具體來說會做
	- Clone Argocd master/develop branch
	- Checkout to new branch
	- Update `image.tag` in `<env>-values.yaml`
	- Commit changes
	- Push to origin
	- Create MR(merge request)
	- Merge MR when pipeline succeeds

script:
```yaml fold

stages:
  - Create_MR
  - Merge_MR

create_mr:
  image: registry.wemoscooter.com/sre/sre-toolbox:v1.1.0
  tags:
    - kubernetes-light
  stage: Create_MR
  variables:
    # https://athena.wemoscooter.com/sre/infra/argocd-kubernetes-manifest
    GIT_REPO_PROJ_ID: 291
  script:
    # 檢查 Trigger 是否有將參數傳進來
    - |
      if [ -z "$CLUSTER_ENV" ] || [ -z "$PROJECT_NAME" ] || [ -z "$IMAGE_TAG" ]; then
        echo "Missing environment variables"
        exit 1
      fi
    # 根據環境設定取得對應的 config
    - |
      H_VALUE_FILE="$(jq -r ."${CLUSTER_ENV}".helm_value_file config.json)"
      H_CANARY_VALUE_FILE="$(jq -r ."${CLUSTER_ENV}".helm_canary_value_file config.json)"
      TARGET_BRANCH="$(jq -r ."${CLUSTER_ENV}".target_branch config.json)"

      if [ -z "$H_VALUE_FILE" ] || [ -z "$H_CANARY_VALUE_FILE" ] || [ -z "$TARGET_BRANCH" ]; then
        echo "Missing config variables"
        exit 1
      fi
    - git config --global user.email "sre_deploy@wemoscooter.com"
    - git config --global user.name "sre_deploy"
    - git clone https://${GIT_USER}:${GIT_PASS}@${GIT_REPO_URL} -b "${TARGET_BRANCH}"
    - cd argocd-kubernetes-manifest/
    # 設定 branch name
    - BRANCH_NAME="${CLUSTER_ENV}/${PROJECT_NAME}-${IMAGE_TAG}-$(date '+%s')"
    - git checkout -b "${BRANCH_NAME}"
    # 替換 <env>-values.yaml & <env>-canary-values.yaml（如果有找到的話） 的 image tag
    - |
      case "${CANARY_ONLY}" in
        "true" )
          sed -i "s/\(tag:.*\)/tag: \"${IMAGE_TAG}\"/g" "helm/projects/${PROJECT_NAME}/${H_CANARY_VALUE_FILE}"
          git add "helm/projects/${PROJECT_NAME}/${H_CANARY_VALUE_FILE}"
          git commit -m "gitlab-ci: ENV=${CLUSTER_ENV} PROJECT=${PROJECT_NAME} IMAGE_TAG=${IMAGE_TAG} CANARY_ONLY=${CANARY_ONLY}"
          ;;
        "false" )
          sed -i "s/\(tag:.*\)/tag: \"${IMAGE_TAG}\"/g" "helm/projects/${PROJECT_NAME}/${H_VALUE_FILE}"
          git add "helm/projects/${PROJECT_NAME}/${H_VALUE_FILE}"
          # 如果有找到 canary-values.yaml 才更換 image tag
          if [[ ! -z $(find "helm/projects/${PROJECT_NAME}" -name "${H_CANARY_VALUE_FILE}") ]]; then
            sed -i "s/\(tag:.*\)/tag: \"${IMAGE_TAG}\"/g" "helm/projects/${PROJECT_NAME}/${H_CANARY_VALUE_FILE}"
            git add "helm/projects/${PROJECT_NAME}/${H_CANARY_VALUE_FILE}"
          fi
          git commit -m "gitlab-ci: ENV=${CLUSTER_ENV} PROJECT=${PROJECT_NAME} IMAGE_TAG=${IMAGE_TAG}"
          ;;
        "" )
          sed -i "s/\(tag:.*\)/tag: \"${IMAGE_TAG}\"/g" "helm/projects/${PROJECT_NAME}/${H_VALUE_FILE}"
          git add "helm/projects/${PROJECT_NAME}/${H_VALUE_FILE}"
          # 如果有找到 canary-values.yaml 才更換 image tag
          if [[ ! -z $(find "helm/projects/${PROJECT_NAME}" -name "${H_CANARY_VALUE_FILE}") ]]; then
            sed -i "s/\(tag:.*\)/tag: \"${IMAGE_TAG}\"/g" "helm/projects/${PROJECT_NAME}/${H_CANARY_VALUE_FILE}"
            git add "helm/projects/${PROJECT_NAME}/${H_CANARY_VALUE_FILE}"
          fi
          git commit -m "gitlab-ci: ENV=${CLUSTER_ENV} PROJECT=${PROJECT_NAME} IMAGE_TAG=${IMAGE_TAG}"
          ;;
      esac
    - git push -u origin "${BRANCH_NAME}"
    # Create Merge Request by GitLab API
    - |
      BODY="{\"id\": $GIT_REPO_PROJ_ID, \"source_branch\": \"$BRANCH_NAME\", \"target_branch\": \"$TARGET_BRANCH\", \"remove_source_branch\": true, \"title\": \"GITLAB_CI: $BRANCH_NAME\"}"
    - |
      curl -Ss -X POST \
           -H "Content-Type: application/json" \
           -H "PRIVATE-TOKEN: $GIT_API_TOKEN" \
           -d "${BODY}" \
           https://athena.wemoscooter.com/api/v4/projects/$GIT_REPO_PROJ_ID/merge_requests | jq . | tee MR_JSON.tmp
  artifacts:
    paths:
      - argocd-kubernetes-manifest/MR_JSON.tmp
  only:
    - web
    - triggers

merge_mr:
  image: registry.wemoscooter.com/sre/sre-toolbox:v1.1.0
  tags:
    - kubernetes-light
  stage: Merge_MR
  variables:
    # https://athena.wemoscooter.com/sre/infra/argocd-kubernetes-manifest
    GIT_REPO_PROJ_ID: 291
  script:
    # Accept MR
    # https://docs.gitlab.com/ee/api/merge_requests.html#accept-mr
    - |
      sleep 90
      MR_IID="$(jq -r ".iid" argocd-kubernetes-manifest/MR_JSON.tmp)"
      echo "MR_IID=$MR_IID"
      curl --fail -Ss -X PUT \
           -H "Content-Type: application/json" \
           -H "PRIVATE-TOKEN: $GIT_API_TOKEN" \
           https://athena.wemoscooter.com/api/v4/projects/$GIT_REPO_PROJ_ID/merge_requests/$MR_IID/merge?should_remove_source_branch=true | jq . | tee mr_response.json
    - jq --exit-status '.state == "merged"' mr_response.json
  retry:
    max: 2
    when: always
  only:
    - web
    - triggers


```
src: /Users/re4388/project/work/gitops-helper/.gitlab-ci.yml



最後， argo cd 會偵測到改變
會開始 sync (如果是 auto sync, prod 是手動 sync)

ps:
- qat → we use auto-sync
- prod → we sync manually (need to go to argo to manual button)


ps:
這裡放很多 wemo infra 的 helm 檔案
/Users/re4388/project/work/es-kubernetes-manifest

# link
- ref: [Day22 - ArgoCD 建立應用程式 - iT 邦幫忙::一起幫忙解決難題，拯救 IT 人的一天](https://ithelp.ithome.com.tw/articles/10269813)





## Getting Started

### Quick Start

```
kubectl create namespace argocd
kubectl apply -n argocd -f <https://raw.githubusercontent.com/argoproj/argo-cd/stable/manifests/install.yaml>

```

Follow our [getting started guide](https://argo-cd.readthedocs.io/en/stable/getting_started/).
Further user oriented [documentation](https://argo-cd.readthedocs.io/en/stable/user-guide/) is provided for additional features.
If you are looking to upgrade ArgoCD, see the [upgrade guide](https://argo-cd.readthedocs.io/en/stable/operator-manual/upgrading/overview/).
Developer oriented [documentation](https://argo-cd.readthedocs.io/en/stable/developer-guide/) is available for people interested in building third-party integrations.

## How it works

Argo CD use Git repositories as the source of truth for defining the desired application state. 
Kubernetes manifests can be specified in several ways:
- [kustomize](https://kustomize.io/) applications
- [helm](https://helm.sh/) charts
- [jsonnet](https://jsonnet.org/) files
- Plain directory of YAML/json manifests
- Any custom config management tool configured as a config management plugin

Argo CD automates the deployment of the desired application states in the specified target environments. 

Application deployments can track updates to branches, tags, or pinned to a specific version of manifests at a Git commit. 
See [tracking strategies](https://argo-cd.readthedocs.io/en/stable/user-guide/tracking_strategies/) for additional details about the different tracking strategies available.

For a quick 10 minute overview of Argo CD, check out the demo presented to the Sig Apps community meeting: [Kubernetes SIG Apps 20180730 - YouTube](https://www.youtube.com/watch?v=aWDIQMbp1cc&t=64s)



Argo CD is implemented as a **kubernetes controller** which continuously monitors running applications and compares the current, live state against the desired target state (as specified in the Git repo). 

A deployed application whose live state deviates from the target state is considered `OutOfSync`. 

Argo CD reports & visualizes the differences, while providing facilities to automatically or manually sync the live state back to the desired target state. 

Any modifications made to the desired target state in the Git repo can be automatically applied and reflected in the specified target environments.


For additional details, see [architecture overview](https://argo-cd.readthedocs.io/en/stable/operator-manual/architecture/).


## Features

- Automated deployment of applications to specified target environments
- Support for multiple config management/templating tools (Kustomize, Helm, Jsonnet, plain-YAML)
- Ability to manage and deploy to multiple clusters
- SSO Integration (OIDC, OAuth2, LDAP, SAML 2.0, GitHub, GitLab, Microsoft, LinkedIn)
- Multi-tenancy and RBAC policies for authorization
- Rollback/Roll-anywhere to any application configuration committed in Git repository
- Health status analysis of application resources
- Automated configuration drift detection and visualization
- Automated or manual syncing of applications to its desired state
- Web UI which provides real-time view of application activity
- CLI for automation and CI integration
- Webhook integration (GitHub, BitBucket, GitLab)
- Access tokens for automation
- PreSync, Sync, PostSync hooks to support complex application rollouts (e.g.blue/green & canary upgrades)
- Audit trails for application events and API calls
- Prometheus metrics
- Parameter overrides for overriding helm parameters in Git

## Development Status
Argo CD is being actively developed by the community. Our releases can be found [here](https://github.com/argoproj/argo-cd/releases).

## Adoption
Organizations who have officially adopted Argo CD can be found [here](https://github.com/argoproj/argo-cd/blob/master/USERS.md).

