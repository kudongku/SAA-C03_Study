### 정리

1. S3 버킷에 최대한 빨리 데이터 집계 ⇒ S3 Transfer acceleration
2. S3에 쿼리 ⇒ Athena
3. S3 버킷 엑세스를 AWS Organizations에게 한해서 ⇒ 
aws PrincipalOrgID 조건 키 정책에 추가
4. EC2에서 인터넷 연결없이 S3 버킷에 엑세스 ⇒
VPC 엔드포인트 생성
5. EBS-EC2 아키텍처 복제해서 다른 AZ에 넣으니 모든 문서 조회 불가능 ⇒
EBS는 AZ안에서만 작동, EFS로 바꿀 것
6. 비디오 파일을 NFS에서 S3로 마이그레이션, 가능한 최소한의 네트워크 대역폭 ⇒
SnowBallEdge를 사용하면 네트워크 대역폭이 충분하지 않을때 마이그레이션에 도움을 줌
7. 작업의 수가 초당 100,000개로 갑자기 증가하는 서비스에서 솔루션을 분리하고 확장성을 높인다. ⇒
SQS를 통해 대기열을 분리
8. aws로 마이그레이션 중 탄력성과 확장성을 극대화하고 싶다 ⇒
확장성 = AutoScaling, SQS를 통해 대기열을 분리해서 탄력적으로 운행
9. 첫 1주일만 엑세스하고 나머지 기간에는 엑세스하지 않는 대용량 파일, 저장공간 늘리고 수명주기 관리하고자 ⇒ 
S3 수명주기 정책으로 7일 뒤 Glacier Deep Archieve, Storage garage는 무제한의 스토리지이다.
10. 주문이 접수된 순서대로 처리되는 기능 ⇒
SQS FIFO 대기열
11. 자격증명관리의 운영 오버헤드 최소화 ⇒ AWS Secret Manager
12. ALB→EC2에서 정적데이터 S3버킷에 저장, 정적 데이터 + 동적데이터 성능개선 ⇒ 
S3버킷과 ALB를 오리진으로 CloudFront 배포를 생성한다. 
13. 여러 리전에서 최소한의 운영 오버헤드로 RDS 자격증명 교체 ⇒
자격증명교체는 SecretManager, 필요한 리전에 대해 다중 리전 비밀 복제
14. 쓰기DB<읽기DB, 데이터베이스의 고가용성을 유지, 데이터베이스를 자동으로 확장 ⇒
읽기 부하 분산을 위해 Aurora  사용
15. 트래픽 보호 솔루션으로 트래픽 흐름 검사 + 필터링 같은 기능을 구현하고자 ⇒
AWS Network FireWall
16. 관리팀만 RDS 시각화에 대한 전체 권한, 나머지는 제한된 권한 ⇒
시각화 = QuickSight, 그룹에게 공유 
17. 2개의 EC2가 S3버킷에 엑세스할 수 있도록 ⇒
EC2 인스턴스에 IAM 역할 부여
18. 이미지를 S3(1)에 저장, lambda로 이미지 처리, 처리된 이미지를 S3(2)에 저장한다. 내구성이 있는 상태 지저장 구성요소 사용, 자동으로 처리하자 ⇒
SQS에 대기열을 생성 S3(1)에 저장될 때 SQS에 알림
SQS를 사용하도록 Lambda 함수 구성
19. 타사 방화벽 ⇒ GateWay Load Balancer를 사용을 관리할 수 있음
20. 테스트 환경 대량 데이터가 EBS에 저장, 이 데이터를 프로덕션에 복제하는데 시간을 최소화 ⇒ EBS 스냅샷 + 빠른 스냅샷 복원기능
21. 피크시간 동안 밀리초안에 수백만개 요청 처리하고자 ⇒ DynamoDB는 RDS와 달리 확장성이 뛰어남
22. S3를 사용해서 가용영역 손실에 대한 복원력, 일부 파일만 엑세스 자주됨, 비용최소화 ⇒ S3 Intelligent-Tiering은 엑세스 빈도를 잘 모를때 사용하기 좋음
23. 첫 1개월만 파일에 자주 엑세스, 그 이후에는 접근안하는 S3 스토리지 솔루션 ⇒ 1개월 후에 S3 Standard 에서 Glacier Deep Archieve로 객체 전환
24. EC2 비용증가 scale-up 관찰, EC2 비용을 비교하는 그래프 생성해서 분석해야한다 ⇒ 비용은 Cost Explorer
25. Lambda에서 Aurora로 이어지는 서비스에서 Lambda 할당량을 늘려야 ⇒ 대용량 데이터 처리는 Lambda + SQS
26. S3 버킷의 무단 구성 변경이 없는지 ⇒ AWS Config는 모니터링 하면서 리소스 구성을 감시한다
27. CloudWatch에 AWS 계정이 없는 제품 관리자에 대한 엑세스 제공 ⇒ 엑세스 = IAM인데 계정이 없다 ⇒ 공유 링크를 제공해야한다.
28. AWS으로 마이그레이션, AWS Organizations를 사용해서 중앙에서 계정 관리, SSO 솔루션과 Microsoft Active Directory 에서 그룹 관리 필요 ⇒
AWS SSO + Microsoft Active Directory용 AWS Directory Service로 양방향 포리스트 트러스트 생성
29. UDP 연결을 사용하는 여러리전에 배포된 EC2 서비스, 가장 짧은 리전으로 사용자 라우팅 + 지역간 자동 장애 조치 필요 ⇒ 
UDP 사용하면 = NLB, 짧은 리전으로 라우팅 = Global Accerlerator
30. RDS 매월 리소스 집약적 테스트 실행, 테스트는 2일동안 지속, 테스트 실행 비용 줄이고파 ⇒
한달에 한번 48시간만 사용하는 가장 비용 적은 방법 = 스냅샷
31. RDS와 Redshift클러스터는 태그로 구성, EC2 인스턴스를 구성하고 운영하고자 ⇒ AWS Config를 사용하면 태그되지 않은 리소스를 감지 가능
32. HTML, CSS, JavaScript 에 대한 컨텐츠를 다른 팀이 엑세스할 웹사이트를 호스팅 ⇒ S33에서 호이스팅
33. 피크시간에 수십만 사용자에게 서비스를 제공, 여러 내부 어플리케이션과 공유하는 확장 가능한 실시간 솔루션, 
빠른 검색을 위해 문서 DB에 저장하기 전 민감한 데이터 제거하기 위해 트랜잭션 처리 ⇒
피크시간 수십만 사용자에게 서비스 제공 = Kinesis,
Lambda에서 민감한 데이터 제거 DB에 저장(확장성)
34. 리소스 구성 변경을 추적, API 호출 기록 ⇒ 리소스 = config, api호출 기록 = CloudTrail
35. ELB-EC2에서 DDOS 공격을 감지하고 보호해야 ⇒ AWS Shield Advanced = DDOS 방어
36. 2개의 리전에서 S3 버킷에 데이터 저장, KMS 고객 관리형 키를 사용해서 S3데이터 암호화, 두 리젼에서 저장되어야 ⇒ 
고객관리형 다중 지역 KMS 키 생성, 각 리전에 S3버킷 생성
37. AWS EC2서비스와 Well-Architected 프레임워크를 따르는 반복 가능 프로세스 ⇒
SSHm AWS Systems Manager Session Manager
38. S3에서 정적 웹 사이트 호스팅, DNS에 R53을 사용, 웹사이트에 엑사이트하는 대기시간을 줄이고자 ⇒
R53으로 CloudFront배포 가리킴
