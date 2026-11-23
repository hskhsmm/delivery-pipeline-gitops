# Delivery Pipeline GitOps

`delivery-demo`가 Kubernetes에서 어떤 이미지를 실행할지 기록하는 저장소입니다. 로컬 클러스터에 등록한 Argo CD Application은 이 저장소의 `main` 브랜치에서 `apps/delivery-demo`를 읽습니다.

## 배포 설정

- [deployment.yaml](apps/delivery-demo/deployment.yaml)은 Docker Hub 이미지의 정확한 버전을 digest로 지정하고, `/health`로 새 Pod의 준비 상태를 확인합니다.
- [service.yaml](apps/delivery-demo/service.yaml)은 8080 포트로 들어온 요청을 준비된 Pod에 전달합니다.

## 배포 흐름

Jenkins가 새 이미지를 발행하면 Deployment의 이미지 digest를 갱신하는 PR을 엽니다. 사람이 PR을 검토해 병합하면 Argo CD가 변경을 자동으로 적용합니다.

- [#6 주문 화면 v2 배포](https://github.com/hskhsmm/delivery-pipeline-gitops/pull/6)
- [#4 새 Pod의 준비 상태 실패와 기존 서비스 응답 확인](https://github.com/hskhsmm/delivery-pipeline-gitops/pull/4)
- [#5 준비 상태 설정 복구](https://github.com/hskhsmm/delivery-pipeline-gitops/pull/5)

[공유 파이프라인 코드와 사용법](https://github.com/hskhsmm/delivery-pipeline) · [데모 배포 기록과 화면 캡처](https://github.com/hskhsmm/delivery-pipeline-demo/blob/main/docs/evidence/checkout-v2.md)
