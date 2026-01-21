#  HRM 프로젝트: 하이브리드 클러스터 매니페스트 (GitOps)

본 리포지토리는 **온프레미스(OKD)**와 **AWS 클라우드(K3s)**로 구성된 하이브리드 인프라의 모든 쿠버네티스 설정(Manifest)을 관리합니다. [cite_start]**ArgoCD**와 연동되어 GitOps 방식으로 멀티 클러스터 배포를 자동화합니다. [cite: 1, 2]

---

##  주요 역할 및 특징

### 1. Kustomize 기반 멀티 클러스터 관리
[cite_start]서로 다른 환경(On-prem vs Cloud)의 리소스 차이를 효율적으로 관리하기 위해 **Kustomize Overlay** 구조를 채택했습니다. 

* [cite_start]**`base/`**: 애플리케이션의 공통적인 로직과 기본 리소스 정의 
* [cite_start]**`overlays/on-prem-okd/`**: OKD 전용 Route 설정 및 고성능 컴퓨팅을 위한 설정 적용 
* [cite_start]**`overlays/aws-k3s/`**: 비용 최적화를 위한 Ingress 설정 및 AWS 전용 리소스 패치 

### 2. 하이브리드 데이터 전략 구현
[cite_start]물리적으로 떨어진 두 환경 간의 데이터 일관성과 성능을 위해 아래 설정을 코드화했습니다. 

* [cite_start]**읽기/쓰기 분리**: 클라우드 앱은 가까운 **`Aurora(Read)`**를 조회하고, 데이터 생성은 **`온프레미스 MariaDB(Write)`**로 수행하도록 환경 변수 최적화 
* [cite_start]**이기종 DB 호환성 패치**: MariaDB와 Aurora 간 실시간 복제 시 발생하는 GTID 및 메타데이터 충돌 문제를 해결하기 위한 전용 **ConfigMap** 적용 

### 3. 보안 및 운영 자동화
* [cite_start]**ECR Token Renewer**: AWS ECR 인증 정보(Secret)를 8시간마다 자동으로 갱신하는 CronJob 구현 [cite: 2]
* [cite_start]**Sync-Wave 배포 제어**: DB → Storage → App 순서로 배포 우선순위를 지정하여 안정적인 서비스 기동 보장 [cite: 2]

