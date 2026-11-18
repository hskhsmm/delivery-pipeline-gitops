# 새 Pod가 준비되지 않을 때 기존 서비스 확인하기

## 목적

현재 정상 이미지의 readiness 경로만 존재하지 않는 `/readiness-failure-demo`로 변경합니다. 서버는 실행되지만 준비 상태 검사에서 HTTP 404가 발생합니다. 새 이미지의 오류나 JVM 장애를 재현하는 실험과는 원인이 다릅니다.

이 변경은 전용 로컬 delivery-sandbox의 delivery-demo만 대상으로 합니다. 기존 Pod를 유지하도록 maxUnavailable을 0으로 지정하고 추가 Pod를 하나 허용합니다. 60초 동안 진행되지 않으면 Kubernetes의 ProgressDeadlineExceeded 조건과 Argo CD 상태를 관찰합니다. 이 시간 제한 자체가 자동 롤백을 수행하지는 않습니다.

## 적용 전

1. Argo CD가 Healthy이고 기존 Pod가 1/1인지 확인합니다.
2. 서비스의 35000원 계산이 무료배송인지 확인하고 캡처합니다.
3. 복구 시 이 실험 커밋을 revert하는 PR을 병합할 것을 확인합니다.

## 실패 PR 병합 후

1. Argo CD에서 병합 커밋을 감지했는지 확인합니다. Synced여도 새 Pod는 0/1일 수 있습니다.
2. 새 Pod의 Events에서 readiness HTTP 404를 확인합니다.
3. 기존 Pod가 1/1로 남고 Service의 준비된 엔드포인트가 기존 Pod를 가리키는지 확인합니다.
4. Service로 실제 요청을 반복해 기존 배송비 API 응답이 계속 오는지 기록합니다. 새 Pod를 직접 가리키는 port-forward는 이 확인에 사용하지 않습니다.
5. Argo CD 상태와 새 Pod Events, API 응답을 캡처합니다.

## 복구

GitHub에서 실험 PR의 Revert로 복구 PR을 생성합니다. deployment.yaml이 실험 전과 같은 상태가 되는지 확인하고 병합합니다. Argo CD에서 복구 커밋으로 Synced, Healthy를 확인하고 Pod 1/1과 35000원 무료배송 응답을 다시 확인합니다.

## 기록할 값

실험 및 복구 PR, 각 병합 커밋, 이미지 digest, 기존/실패 Pod 이름, readiness 이벤트, 실제 응답, 복구 완료 시간을 기록합니다. 확인한 결과만 검증 완료로 표시합니다.

## 현재 진행 상태

실험용 변경과 절차를 준비한 단계입니다. PR 병합, 실제 장애 주입, 캡처 및 복구 검증은 진행 전입니다. 이미지 digest는 기존 배포 값을 유지합니다.
