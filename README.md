# edu-k8s-01
markdown
# k3s와 Traefik을 이용한 Private Cloud 구축 가이드

## Docker 설치 확인
Docker가 설치되어 있는지 확인합니다.
```bash
docker --version
k3s 설치
경량화된 Kubernetes 버전인 k3s를 설치합니다.

bash
curl -sfL https://get.k3s.io | sh -
k3s가 정상적으로 설치되었는지 확인합니다.

bash
sudo k3s kubectl get node
Traefik 설치
헬름 차트를 이용하여 Traefik을 설치합니다.

bash
helm repo add traefik https://containous.github.io/traefik-helm-chart
helm repo update
helm install traefik traefik/traefik
IngressRoute 리소스 생성
IngressRoute 리소스를 생성하여 Traefik이 Ingress Controller로 작동하게 만듭니다.

yaml
apiVersion: traefik.containo.us/v1alpha1
kind: IngressRoute
metadata:
  name: myingressroute
spec:
  entryPoints:
    - web
  routes:
  - match: Host(`mydomain.com`) && PathPrefix(`/`)
    kind: Rule
    services:
    - name: myservice
      port: 80
IngressRoute 리소스 적용
IngressRoute 리소스를 쿠버네티스에 적용합니다.

bash
kubectl apply -f myingressroute.yaml
TLS 설정을 위한 추가 가이드
TLS를 위한 쿠버네티스 Secret 생성
TLS를 위한 쿠버네티스 Secret을 생성합니다.

bash
kubectl create secret tls mysecret --cert=mycert.crt --key=mykey.key
IngressRoute에 TLS 설정 추가
IngressRoute에 TLS 설정을 추가합니다.

yaml
apiVersion: traefik.containo.us/v1alpha1
kind: IngressRoute
metadata:
  name: myingressroute
spec:
  entryPoints:
    - websecure
  routes:
  - match: Host(`mydomain.com`) && PathPrefix(`/`)
    kind: Rule
    services:
    - name: myservice
      port: 443
  tls:
    secretName: mysecret
IngressRoute 리소스 적용
IngressRoute 리소스를 쿠버네티스에 적용합니다.

bash
kubectl apply -f myingressroute.yaml
