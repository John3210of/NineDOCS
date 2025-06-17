## Install ArgoCD

ArgoCD를 설치하고 네임스페이스를 생성합니다.

```sh
kubectl create namespace argocd
kubectl get namespace
kubectl get namespace argocd --show-labels
```

ArgoCD를 적용합니다.

```sh
kubectl apply -n argocd -f https://raw.githubusercontent.com/argoproj/argo-cd/stable/manifests/install.yaml
curl https://raw.githubusercontent.com/argoproj/argo-cd/stable/manifests/install.yaml > install.yaml
```

---

## Get Started with ArgoCD

ArgoCD 서버에 접근하려면 포트포워딩을 사용하거나 NodePort 설정을 추가해야 합니다.

```sh
kubectl port-forward svc/argocd-server -n argocd 8080:80
kubectl get secret argocd-initial-admin-secret -n argocd -o yaml
```

ArgoCD 기본 설정 값 및 아키텍처별 차이점(AMD, ARM 등)에 대한 자세한 정보는 [공식 문서](https://argo-cd.readthedocs.io/en/stable/getting_started/)를 참고하세요.

---

## Install ArgoCD CLI

ArgoCD CLI를 설치합니다.

```sh
curl -sSL -o argocd-linux-amd64 https://github.com/argoproj/argo-cd/releases/latest/download/argocd-linux-amd64
sudo install -m 555 argocd-linux-amd64 /usr/local/bin/argocd
rm argocd-linux-amd64
```

---

## Login to ArgoCD

CLI를 사용하여 ArgoCD에 로그인합니다.

```sh
kubectl get svc -n argocd
argocd login <argocd server ip> (10.110.16.205)
# 만약 NodePort로 열려있다면 localhost:<NodePort> 로 로그인하면 됩니다.
argocd repo list
```

### 초기 관리자 비밀번호 확인 및 변경

```sh
# ArgoCD 초기 관리자 비밀번호 확인 (Base64 디코딩 필요)
kubectl get secret argocd-initial-admin-secret -n argocd -o jsonpath="{.data.password}" | base64 --decode

# ArgoCD 비밀번호 변경
argocd account update-password --account admin --current-password <current-password> --new-password <new-password>
```

---

## Repository 등록

ArgoCD에서 Private Repository를 등록하는 방법은 [공식 문서](https://argo-cd.readthedocs.io/en/stable/user-guide/private-repositories/)를 참고하세요.

---

## 배포

### services 폴더 내의 모든 YAML 파일에 따라 ArgoCD 애플리케이션을 개별적으로 생성하기
```sh
# NOTE: 최초의 배포는 ArgoCD UI를 이용하는 것이 편합니다.
for app in services/*.yaml; do
    argocd app create -f "$app"
done
```

---

## Sync

```sh
# NOTE: Sync Policy는 각 App의 설정을 따르며, 수동일 경우에도 역시나 ArgoCD UI를 이용하는 것이 편합니다.
argocd app sync --all
```

---

이 가이드는 ArgoCD를 설치하고 초기 설정을 진행하는 순서를 따릅니다. 보다 자세한 설정과 활용법은 공식 문서를 참고하세요.
- [ArgoCD 공식 문서](https://argo-cd.readthedocs.io/en/stable/getting_started/)
- [Private Repository 설정](https://argo-cd.readthedocs.io/en/stable/user-guide/private-repositories/)

