# 소마 기술 멘토링 정리
https://mumomu.notion.site/11d80c0ab1064f8696ecd81c49b8d5fd

# 소마 기술 멘토링 정리

## **22.05.13 김준범 멘토님 DB 모델링**

#1 SQL = SELECT = 개발
#2 MODELING = 설계
#3 PL/SQL = 개발 : 복합 SQL

**#2 MODELING = 설계**

타 SW 설계와 동일하게, 복잡한 현실 세계특지를 쉽게 이해하도록 개념화, 단순화하여 표현

### Identifier

- 개념
    
    튜플간 구별이 가능한 값 ( 튜플 = 레코드 = row )
    엔티티 내의 튜플간 구별이 가능한 기준 또는 항목 **ex) 학번, 주민, 환자**
    
- 특징
    
    단일 또는 다수의 속성조합으로 구성가능
    
    - 유일성 : 타 엔티티와 식별이 가능, Not Null
    - 단순성 : 가능한 최소한의 속성, 최소길이 만족(고정길이일수록 유리)
    - 활용성 : 속성이나 값이 변경되지 않는 항목, 업무적 활용성 높은 항목
- 유형
    - 주 식별자 Pk : 기본키(줄로 나누어 표현), 주 식별자는 두개 이상도 가능(슈퍼키?)
        
        NOT NULL을 만족하는 유일한 값
        
    - 고유 식별자 Uk : 유일성을 만족하는 속성 중 선택된 값(NULL 허용{두개x}, Pk 보조)
    - 외래 식별자 Fk : 다른 엔티티를 참조하기 위해 사용되는 속성
- Meta vs Master
    - Meta
        - Master를 포함한 전체 데이터 속성에 대한 의미
            
            (어떤 값이 들어가야되는지에 대한 의미)
            
    - Master
        - Master Data는 Legacy Data에 대한 기준
            
            (성별이 M,F로 한다면 다른 알파벳은 올 수 없다는 기준)
            
- Data Dictionary
    - DBMS에서 사용되는 용어를 정리, 관리하는 집합
    - 값, 규격 등의 표현 총칭하여 관리
    - 논리 > 물리 변환 시 사용
    - Data Profiling, Cleansing등 메타데이터로 화용
    - DBMS뿐 아니라 Web 등 FrontEnd 개발 시에도 통일된 용어활용(Metaphor)
    
    | 구분 | 명칭 | 물리명 | 적용 | 동의어 | 규격 |
    | --- | --- | --- | --- | --- | --- |
    | 단어 | 학생 | STUDENT | STDNT |  |  |
    | 단어 | 교수 | PROFESSOR | PRFSR |  |  |
    | 단어 | 구매 | PURCHASE | PRCHS | BUY |  |
    | 도메인 | 성명 | NAME | NM | 성함,존함 | STRING(50) |
    | 도메인 | 수량 | QUANTITY | QTY | 수 | NUMBER(12) |
    
    | 논리명 | 물리명 | 속성 |
    | --- | --- | --- |
    | 학생성명 | STDNT_NM | STRING(50) |
    | 구매수량 | PRCHS_QTY | NUMBER(12) |
    | 주소 | ADDR | STRING(200) |
- Relation
    - 엔티티간 상호 소통하는 포함관계의 표현
    - 데이터는 한 군데에서만 관리하는 RDBMS 핵심
        - 한 관계에서 부모테이블, 자식테이블이 생긴다
        - 식별관계 : 부모의 Pk가 자식의 Pk로 구현(실선)
        - 비식별관계 : 부모의 Pk가 자식의 Pk가 아님(점선)
        - 어떻게 보면 상속이라고 볼 수 있다. (has-a)
- ERD
    - DB Modeling의 목표는 해당 시스템의 ERD를 작성하는 것
    
    - 엔티티간 상관관계를 표현하는 도식
    - 엔티티, 속성, 관계로 표현
- 정규화
    - 정규화가 불 필요하도록 ERD를 작성하는 것이 최선
    - ERD내에서 중복을 찾아서 제거하는 과정
    - 속성을 제자리에 위치시키는 절차
    - 더 이상 분해 불가능한 엔티티로 재구성
    - 속성간의 부정확한 종속성의 삭제
- 테이블 구성 Process
    1. Entity 검토
    2. 컬럼 정리 및 Pk 선정
        - Pk선정 시 어떤 기준으로 정할 지
            1. 기수별 1~100, 101~200 이런식으로
            2. 그냥 순서대로
    3. Relation
- 반정규화
    - 여러가지 상황 및 이해관계 등에 의해서 정규화를 역변환하는 것
    - 정규화를 하지 않은게 아니라 정규화 이후 성능향상을 위해 역변환을 하는 절차
    - 조회 성능 향상이 주목적
    - 실무에서는 관련 resource 최소화를 위해 다양한 방법으로 적용하기도 한다.
        
        (관리 목적의 테이블 수, 컬럼 수 감소)
        
        | 환자성명 | 생년월일 | 연령(일반) | 연령(만) | 성별 |
        | --- | --- | --- | --- | --- |
        | 홍길동 | 1990.12.03 | 32 | 30 | 남 |
        
        나이는 가지고 있지 않고, 생년월일과 오늘의 차이로 구분하는게 좋다. (이것이 정규화)
        
        연령(일반)은 1년에 한 번 변경된다. 그러나 만나이는 매일 변경이 되야하므로 
        
        반정규화 시에는 연령(일반)을 하는게 낫다.
        
        - 정규화
            - 연령속성 미생성, 조회 시마다 생년월일 가지고 계산
        - 응용(반정규화)
            - 연령 속성 생성, 년 배치 실행하여 일괄처리(일년에 1번)
        
- 작업절차
    
    개념 → 논리 → 물리
    
    - 개념, 논리 모델링
        - 실질적 DB 모델링은 논리 모델링까지 수립하는 단계이며,  이후는 Data Dictionary에 의해 진행
    - 물리 모델링
        - DB별로 명칭이 다 다르다며 Data Dictionary에 의해 진행한다
- 모델링 검토
    - 많이 하는 실수
        - Tuple간 식별 불가
        - Pk 구성시 Biz 로직 넣는 부분 부족(운영관점)
        - Data Contents(Value)에 대해 모호
        - Tuple의 생애주기에 대한 비인식
- 공통 코드
    - 단순 구조의 다량의 테이블을 감소시켜 많은 작업을 효율적으로 진행
    - key/value 기반의 단순성 코드만 별도 관리하는 엔티티 및 그에 포함된 데이터 집합
        
        분류와 분류별 상세항목 (엔티티 2개)
        
        - 단순성 테이블 생성 방지
        - 하드코딩 최소화
        - 단순변수, 상수관리 등의 방법에 효율적
        
         
        
        | 학과CD | 학과명 |
        | --- | --- |
        | COM | 컴퓨터공학과 |
        | ELTR | 전자공학과 |
        
        | 코드 | DTL_CD | 상세명 | 필드1 |
        | --- | --- | --- | --- |
        | GNDR | F | 여성 | 여자 |
        | GNDR | M | 남성 | 남자 |
        | SCLS | A | 전액장학금 | 100 |
        | SCLS | B | 성적우수장학금 | 50 |
        
        join 등을 통해 사용 (where조건을 반드시 사용해서 코드 구별)
        
- CRUD Matrix(최종문서, 중간발표에는 넣자)
    - 항목 : 멘티정보등록, 수정, 등
    - 시나리오 : 항목에 관한 상세 설명
    - 멘티, 팀, 구성, 분과 : CRUD 중 어떠한 것이 일어나는지 정의

## **22.05.15 남상수 멘토님 애자일, 스크럼, 칸반**

<남상수> : 풀스택, MS 인턴, 숨고, Tapas, 3Soft, **Spotify**

**핵심 : 애자일은 지향점이며 스크럼과 칸반은 프로세스 도구이다. 그리고 완벽한 도구는 없다.**

**squad** : team, **chapter** : 같은 챕터가 코드리뷰, 

**guild** : 같은 관심사를 가진 사람(동아리), **tribe** : 같은 목적을 가진 조직

- 애자일
    - 사전상 날렵한, 민첩한, 재빠른이라는 뜻
    - **폭포수 모델** (요구사항 분석→설계→구현→검증)
        - 새로운 가치까지 필요한 시간이 얼마나 걸리는가?
        - 폭포수가 더 나은 상황? 하드웨어, 건축(폭포수가 아니면 거의 불가능)
        - 소프트웨어에서도 이게 잘 맞는가? 그래서 나온게 애자일 방법론
        - 이 방법은 정확하고 완벽한 문서화를 필요로 함
        - 분리된 책임 / 의사소통 한계
        - 빠른 소프트웨어 시장에 대응하지 못함
    - 소프트웨어 개발이 예측하기 어렵고, 복잡하며, 변경될 수 있다
        
        는 관찰을 기반으로 한 일련의 원칙 및 관행
        
    - 애자일 소프트웨어 개발 선언(왼쪽보다 오른쪽에 더 높은 가치를 둠)
        - 공정과 도구보다 **개인과 상호작용**
        - 포괄적인 문서보다 작동하는 **소프트웨어**
        - 계약 협상보다 **고객과의 협력**
        - 계획을 따르기보다 **변화에 대응하기**
    
    ![Untitled](%E1%84%89%E1%85%A9%E1%84%86%E1%85%A1%20%E1%84%80%E1%85%B5%E1%84%89%E1%85%AE%E1%86%AF%20%E1%84%86%E1%85%A6%E1%86%AB%E1%84%90%E1%85%A9%E1%84%85%E1%85%B5%E1%86%BC%20%E1%84%8C%E1%85%A5%E1%86%BC%E1%84%85%E1%85%B5%2011d80c0ab1064f8696ecd81c49b8d5fd/Untitled.png)
    
    - 빠른 피드백 주기
    
    ![Untitled](%E1%84%89%E1%85%A9%E1%84%86%E1%85%A1%20%E1%84%80%E1%85%B5%E1%84%89%E1%85%AE%E1%86%AF%20%E1%84%86%E1%85%A6%E1%86%AB%E1%84%90%E1%85%A9%E1%84%85%E1%85%B5%E1%86%BC%20%E1%84%8C%E1%85%A5%E1%86%BC%E1%84%85%E1%85%B5%2011d80c0ab1064f8696ecd81c49b8d5fd/Untitled%201.png)
    
    - 하나를 목표로 하는 것이 아닌 사용자의 피드백으로 발전하는 과정
        
        (위 폭포수, 아래 애자일)
        
- 스크럼(how to 방법론)
    - 작고, 교차 기능적 팀 구성
    - 테스트는 팀별로 자동화?
    - 사용자의 요구사항을 사용자 관점에서 이야기하는 것(가장 작은 비즈니스 가치) As a <user/who> I want <action/what> So that <purpose/why>
    - 좋은 사용자 스토리
        - 독립성 : 각각의 스토리는 어떤 순서로든 작업할 수 있어야 함
        - 협상/변경가능 : 목표는 고객의 요구를 충족시키는 것, 고객과 개발팀의 대화에 따라 변경 가능
        - 가치있음 : 식별 가능한 비즈니스 가치가 있어야 함
        - 추정 가능 : 우선 순위를 적절하게 지정할 수 있도록 스토리의 크기를 추정할 수 있어야 함(Ex) small : 1일, medium : 2~3일, large : 7일)
        - 작은 : 한 스프린트 안에 스토리를 완료 상태로 할 수 있어야 함
        - 테스트 가능 : 스토리가 완료되기 위해 테스트 가능해야 함
    - 프로덕트 백로그 → 스프린트 백로그 → 1~4주 주기 →
        
        **일일 회의**(어제 무엇을 했는지, 오늘 무엇을 할지, 진행에 문제가 되는 부분이 있는지?)
        
        - 프로덕트 백로그에는 지금까지 나온 모든 기능 나열
        - 스프린트 백로그에는 스프린트 기간동안 볼 수 있는 모든 기능 나열
        - figma
    
    ![Untitled](%E1%84%89%E1%85%A9%E1%84%86%E1%85%A1%20%E1%84%80%E1%85%B5%E1%84%89%E1%85%AE%E1%86%AF%20%E1%84%86%E1%85%A6%E1%86%AB%E1%84%90%E1%85%A9%E1%84%85%E1%85%B5%E1%86%BC%20%E1%84%8C%E1%85%A5%E1%86%BC%E1%84%85%E1%85%B5%2011d80c0ab1064f8696ecd81c49b8d5fd/Untitled%202.png)
    
    - 역할
        - 제품 책임자 : 제품 전략 및 로드맵 수립, 백로그 설명 및 우선순위 관리, 결정 검증
        - 스크럼 마스터 : 팀의 작업 환경 확인 및 장애 요소 해결, 일일 스크럼 회의, 의사결정 도움
    - 하나의 기능
    
    ![Untitled](%E1%84%89%E1%85%A9%E1%84%86%E1%85%A1%20%E1%84%80%E1%85%B5%E1%84%89%E1%85%AE%E1%86%AF%20%E1%84%86%E1%85%A6%E1%86%AB%E1%84%90%E1%85%A9%E1%84%85%E1%85%B5%E1%86%BC%20%E1%84%8C%E1%85%A5%E1%86%BC%E1%84%85%E1%85%B5%2011d80c0ab1064f8696ecd81c49b8d5fd/Untitled%203.png)
    
- 칸반(how to 방법론)
    - 스크럼보다 훨씬 요구하는 사항이 적다
    - 스프린트가 없고 작업 흐름의 시각화만 한다.
    - 여기 있는 일들이 완료되면 새로운 일을 추가하면 된다.
    - 프로젝트가 종료될 때까지 있는 보드

## **22.05.18 강상진 멘토님 AWS 클라우드 컴퓨팅(초급)**

- 클라우드 컴퓨팅이란?
    - 컴퓨팅 파워, 데이터베이스, 스토리지, 애플리케이션, 기타 다양한 IT 리소스들은 온-디맨드(필요한 만큼 그때그때) 형태로 제공
    - 클라우드 서비스 플랫폼은 종량 요금제(쓴만큼 내라) 적용
    - 사용자가 필요로 하는 리소스의 유형 및 크기를 정확하게 프로비저닝 가능
    - 필요한 만큼의 리소스에 거의 바로 액세스 가능
    - 서버, 스토리지, 데이터베이스 및 광범위한 애플리케이션 서비스에 간단하게 액세스 할 수 있는 방법을 제공
    - 아마존 웹서비스(AWS)는 이러한 애플리케이션 서비스에 필요한 네트워크 연결 하드웨어를 소유 및 유지 관리하는 한편, 사용자는 웹 애플리케이션을 통해 필요한 것을 프로비저닝하고 사용하는 방식
    - 아마존이 가장 점유율이 높기때문에 aws를 배워두는 편이 좋다
- 클라우드를 통해 해결된 문제
    - 유연성 : 필요에 따라 리소스 타입 변경 가능
    - 비용효율성 : 사용량만큼 과금
    - **확장성 : 실제로 측정된 사용량에 따라 자동으로 확장(Auto-Scaling)**
    - **탄력성 : 필요에 따라 스케일 아웃과 스케일 인 가능(Auto-Scaling)**
    - 고가용성과 내결함성 : 전세계에 걸쳐 데이터센터 구축
        
        (내결함성 : 오류가 생기더라도 스스로 복구)
        
    - 민첩성 : 민첩하고 신속하게 소프트웨어 애플리케이션을 개발, 테스트 후 시작
- laaS vs PaaS vs Saas
    
    ![Untitled](%E1%84%89%E1%85%A9%E1%84%86%E1%85%A1%20%E1%84%80%E1%85%B5%E1%84%89%E1%85%AE%E1%86%AF%20%E1%84%86%E1%85%A6%E1%86%AB%E1%84%90%E1%85%A9%E1%84%85%E1%85%B5%E1%86%BC%20%E1%84%8C%E1%85%A5%E1%86%BC%E1%84%85%E1%85%B5%2011d80c0ab1064f8696ecd81c49b8d5fd/Untitled%204.png)
    
    - On-Premises - 모두 혼자 처리해야한다.
    - IaaS - AWS EC2(Elastic Computer Cloud)
        - 인프라 제공
        - Networking : 스스로 학습
        - Storage : S3(Simple Storage Service)
        - Virtualization
        - Servers
    - PaaS - Elastic BeanStalk, GCP, Windows,Azure
        - 일부를 아마존이 또 다시 결정해준다
        - OS 설치 + middle ware + runtime
        - Linux, MySQL, apache 등 이미 설치가 되어있다.
    - SaaS - amazon recognition, dropbox, zoom
        - 모든게 다 되어있고, 사용만 하면 된다.
        - GoogleDrive같은 것들도 SaaS라고 할 수 있다.
- AWS EC2 (Elastic Cloud Computing)
    - IaaS, 하나의 서비스가 아니며 높은 수준의 서비스들로 구성
    - 가상화 머신 렌탈(EC2), 가상 드라이브에 데이터 저장(EBS), 시스템간의 로드 분산(ELB), 자동 확장 그룹
- AWS 글로벌 Infra Structure
    - AWS 리전
        - 국가별로 코드 존재
        - 데이터 센터의 클러스토로 여러 개의 데이터 센터 존재
        - AWS 서비스는 특정 지역으로 연결, 범위 지정
    - AWS 가용 영역
    - AWS 데이터센터
    - AWS 엣지 로케이션
        - 최대한 짧은 지연시간으로 최종 사용자에게 콘텐츠를 제공할 때 유용
- EC2 설정옵션
    - 주요 기
        - 가상화 머신 렌탈
        - 가상 드라이브에 데이터 저장
        - 시스템간의 로드 분산
        - 자동 확장 그룹을 사용하여 용량의 확장 및 축소 가능
    - 운영체제 : Linux, Windows, Mac
    - Compute Power & cores 성능
    - random-access memory 사이즈
    - 저장공간 용량
    - network card : 카드속도, public ip/private ip 주소 제공
    - 방화벽 규칙 : 보안 그룹(방화벽)
    - EC2 bootstrap 인스턴스를 처음 구성할 때) : EC2 사용자 데이터
- 초기설정(콘솔)
    - 지역을 서울로
    - 서비스 > EC2
    - ssh 비대칭 키
    - EC2는 대칭 키 (private key)
    - 프라이빗 키 파일 형식(맥북은 .pem, 윈도우는 .ppk가 편하다)
    - 네트워크 설정 : 웹서버 Https 트래픽허용, Http 트래픽 허용
        - 위치 무관 : 접속하는 위치를 어디만 받아줄 것인가?
        - 내 IP만 넣으면 나만 접속 가능하다
    - 사용자 데이터
        - EC2를 깔자마자 무언가 동작을 원할 때
        
        #!/bin/bash
        
        yum update -y
        
        yum install -y httpd.x86_64
        
        systemctl start httpd.service
        
        systemctl enable httpd.service
        
        - httpd는 아파치 경량 http
            
            설치가 완료되었으면 시동시켜라
            
            혹시 이 서버가 멈췄다 다시 켜지면 enable시켜라
            
- 생성!
    - public ip vs private ip
        - what is my ip? 사이트 들어가면 나오는 ip가 public ip
        - 내가 내부 자원에서 통신할 때 사용하는 ip가 private ip - 127.0.0.1
        
        private은 대역이 3개뿐이다. 192.x.x.x 172.x.x.x 10.x.x.x
        
    - public ip의 주소가 나의 도메인
- 도메인 연결
    - godaddy접속 후 도메인 등록(cname)
    - 보안그룹 - 인바운드 규칙 편집 : 들어오는 규칙 추가 / 아웃바운드 규칙 편집 : 나가는 규칙 추가(방화벽 규칙 - 서버를 지켜준다)
- S3
    - 무한한 확장성을 지닌 스토리지 대표
    - 개발 프로젝트에서는 정적 파일, 대용량 파일, 문서 등의 저장 서비스로 활용
    - 이미지나 영상, 문서 파일은 스토리지에 저장하고 꺼내와야한다.
    - 활용 사례
        - backbone과 스토리지
        - 재해복구
        - 아카이빙
        - 하이브리드 클라우드 스토리지
        - 애플리케이션 호스팅 : 정적 콘텐츠에 대한 웹 사이트 운용
        - 미디어 호스팅
        - 데이터 레이크와 빅데이터 분석
        - 소프트웨어 전송
        - 정적 웹 사이트
    - 요약
        - 버킷 : 유니크(중복X)
- SSL 인증키 생성 → ACM (amazon certificied manager)
    - 도메인 생성 → 도메인 해당 된 값으로 뒤에서 .metakorea. 한두칸 빼고 cname cvalue 집어넣는다
    - [goDaddy.com](http://goDaddy.com) 이용
    - 배포되면 s3어쩌구로 해놓은 도메인을 ~~.cloudfront.net으로 바꾼다.
- RDS
    - 데이터베이스 생성
    - 접속 방법 : 엔드포인트&포트
    - 퍼블릭 액세스 : 예 → 아니요로 하는 경우는 aws끼리만 사용하는 것 → ec2에서 디비접속 ec2는 퍼블릭 키
    - 삭제 시 비활성화 후 기다렸다가 삭제

## **22.05.19 변재영 멘토님 Docker 강의 3회차**

- docker의 취약점 : root를 사용하는 것
    - 어떤 파일을 안전하게 떨굴까? log파일
        
        user 설정 시 해당 유저로 CMD 실행
        
- 명령어 정리
    - VOLUME : 컨테이너 안에 있는 데이터는 컨테이너를 삭제하면 모든 데이터가 같이 삭제. 따라서, 데이터를 보존하기 위해 VOLUME 사용, VOLUME 명령은 설정한 컨테이너의 데이터를 호스트 OS에 저장하거나, 컨테이너들간의 데이터를 공유 가능
        
        #docker run -it -v /log/docker/log/:/var/log test/vol /bin/bash
        
        위와 같이 생성한 볼륨은 호스트 OS의 /var/lib/docker/volumes/에 생성되며, Docker에서 자동 생성한 hash값으로 디렉토리를 생성
        
- 배포시 장점
    - 레이어는 읽기 전용, 이미지 다운로드 시 기존 레이어를 재활용, 변경 부분만 다운로드
    - 네트워크 대역폭, 디스크 저장크기, 이미지 처리시간 단축
    
    멘토_변재영 님이 모두에게:    오후 7:09
    FROM centos:7 RUN touch /etc/yum.repos.d/nginx.repo && echo -e '[nginx]\nname=nage repo\nbaseurl=http://nginx.org/packages/centos/7/$basearch/\ngpgcheck=0\nenabled=1' > /etc/yum.repos.d/nginx.repo RUN yum -y install nginx curl HEALTHCHECK --interval=10s --timeout=3s CMD curl -f [http://127.0.0.1/](http://127.0.0.1/) || exit 1
    CMD ["nginx", "-g", "daemon off;"]
    
    멘토_변재영 님이 모두에게:    오후 7:16
    watch -n 0.5 docker ps
    멘토_변재영 님이 모두에게:    오후 7:32
    dockerignore 파일 사용
    이미지 생성 작업 중에 불필요한 파일 및 디렉토리를 로드하지 않는다. (.gitignore파일과 유사)
    dockerfile을 빈 디렉토리에 저장하고, 빌드할때 필요한 파일만 해당 디렉토리에 추가한다.
    빌드 성능과 이미지 용량을 줄이기 위해 .dockerignore파일을 활용한다.
    이미지는 가능한 적은 크기로 구성
    불필요한 패키지를 설치하지 않는다.
    데이터베이스 이미지에 텍스트 편집기를 포함시킨다던지…
    한 컨테이너 당 한 어플리케이션을 구성(어플리케이션 상태 저장X)
    애플리케이션을 여러 컨테이너로 분리하면 컨테이너를 확장하고 재사용하는 것이 훨씬 쉬워진다.예를 들어, 일반적인 어플리케이션은 웹 어플리케이션, 데이터베이스, 인메모리-캐시와 같이 세 개의 컨테이너로 구성 될 수 있다.
    이미지 레이어 수를 최소화
    RUN, COPY, ADD 명령어는 새로운 레이어를 만든다.
    
    멘토_변재영 님이 모두에게:    오후 8:26
    ...
    멘토_변재영 님이 모두에게:    오후 8:26
    mkdir -p /usr/share/nginx/html
    echo "Web Server running: YES" > /usr/share/nginx/html/index.html
    docker run -d -p 80:80 --name=MyWebServer nginx
    docker cp /usr/share/nginx/html/index.html MyWebServer:/usr/share/nginx/html/index.html
    curl [http://localhost:80](http://localhost/)
    Web Server running: YES
    
    성능 테스트한게 있어야한다(value같은게) → 엄청난 포트폴리오적 value가 된다
    

## **22.05.21 강상진 멘토님 모던 웹 엔지니어링**

- whois : 도메인 주인
- **dig** domain information groper 도메인 정보 쿼리
    - 각 쿼리에 여러 도메인이 있다면 해당 도메인은 라운드 로빈으로 각 사용자마다 다른 도메인 제공, 고가용성을 위해
- cname 재귀로 해당 위치로 옮겨줌
- 캐시를 사용하는 이유
    - 이미 사용한 값을 다시 사용함으로 써 성능이 좋아진다(캐시 주기 동안)
    
    [blog.naver.com](http://blog.naver.com) → [blog.naver.com.nheos.com](http://blog.naver.com.nheos.com) → ip x.x.x.x 재귀 형태로 호출
    
- elb 로드밸런싱 부하를 나누는 AWS 기능
    - 해당 ip는 로드밸런싱 된 ip
    - 로드 밸런스 뒤에는 많은 웹 서버가 있다
- swmaestro
    
    ![Untitled](%E1%84%89%E1%85%A9%E1%84%86%E1%85%A1%20%E1%84%80%E1%85%B5%E1%84%89%E1%85%AE%E1%86%AF%20%E1%84%86%E1%85%A6%E1%86%AB%E1%84%90%E1%85%A9%E1%84%85%E1%85%B5%E1%86%BC%20%E1%84%8C%E1%85%A5%E1%86%BC%E1%84%85%E1%85%B5%2011d80c0ab1064f8696ecd81c49b8d5fd/Untitled%205.png)
    
    - 300초동안 캐시, 부하 나누는 ip없음
    - 왜 고가용성 아키텍처를 사용하지 않았을까?
        - 사용자가 많지 않아서
- kakao
    - 메인 홈페이지가 분할을 안 하고 있다 이유가 뭘까?
        - 면접에서 이런걸 물어보면 장점이 될 수 있다.
- curl -v “www.swmaestro.org”
    - http로 접속시 302 not found가 뜬다
    
    ![Untitled](%E1%84%89%E1%85%A9%E1%84%86%E1%85%A1%20%E1%84%80%E1%85%B5%E1%84%89%E1%85%AE%E1%86%AF%20%E1%84%86%E1%85%A6%E1%86%AB%E1%84%90%E1%85%A9%E1%84%85%E1%85%B5%E1%86%BC%20%E1%84%8C%E1%85%A5%E1%86%BC%E1%84%85%E1%85%B5%2011d80c0ab1064f8696ecd81c49b8d5fd/Untitled%206.png)
    
    curl -I “https://www.swmaestro.org/sw/main/main.do” → 헤더만
    
    - http header 정보는 웹 개발자라면 다 알아두는 편이 좋다
        - General
        - Request Headers
            - method : 요청방식
            - path : 접속 url(필요한거)
            - schema : http/https
            - accept : 받을 수 있는 컨텐츠 사전 기술
            (헤더를 자기정보 흘려줄 때 사용)
            - accept-encoding : 지원하는 압축(gzip, deflate, br), 다운받자마자 다시 압축해제
            - accept-language : 지원하는 언어
            - cache-control : 캐시 지원 여부
            - cookie : 쿠키 정보
            - referer : 어디서 js를 호출하는지 경로를 알려준다(값이 없는 경우 정상적인 접속경로가 아니라고 판단에 접속을 차단할 수 있다.)
            - user-agent : 내가 사용하는 브라우저 정보를 웹 서버에 알려줌
        - Response Headers
            - content-encoding : 압축 방식
            - content-type : 지원하는 컨텐츠 타입
            - date : 시간
            - etag : 유일한 구분코드
            - strict-transport-security : https를 사용하겠다는 뜻
    - HTTP status도 웹 개발자면 모두 알아야한다
        - 200은 get만이 아니라 post에서도 나온다
- 웹 최적화를 하고 싶으면 js minify를 하면된다 → 디버깅이 어려워진다.
    - 네이버가 js 최적화를 잘해두었다. (카카오는 5줄, 네이버는 1줄)
    - js 캐시를 안하면 필요할 때마다 js를 불러오므로 느려진다.
- dns checker : 각 나라별 도메인을 알 수 있다.
- 로드밸런서 : dns에서 자신의 서버 여러 개 중 라운드로빈으로 호출해준다. 특정 서버가 죽을 경우 그 서버가 라운드로빈으로 나올 수 있으므로, 특정 시간마다 서버가 죽었는지 확인
- 각 복제된 서버는 다른 위치에 존재해야한다. 한 지역에 홍수가 나거나 하면, 같이 다운되면 안되므로
- devops = 개발자가 시스템 운영까지 할 줄 알아야한다
- 자바 개발자도 아키텍처를 그리고 아마존을 구성할 줄 알아야한다 + CI/CD + Infra
- **도메인 샤딩** : 도메인을 여러개 사용하는 웹
    - 도메인을 분리하지 않으면 폭포수 형의 웹 성능이 만들어진다. 호출-응답 호출-응답
    - 분리하면 병렬 호출함으로써 페이지가 빨리 로딩이 된다.
    - 일반적으로 도메인 샤딩은 7개까지 사용한다.
- **gzip하고 cache하면 웹 페이지 성능이 올라간다, css-js도 minify**
    - 와일드카드 인증서 : 뭐든지 관련 도메인들이 이 도메인으로 불려짐
    - apache content-encoding 이런식으로 검색하면 압축하는 법이 다 나온다.
- HTTP 헤더 / HTTP 페이로드(바디)
- **프록시 서버**
    
    ![Untitled](%E1%84%89%E1%85%A9%E1%84%86%E1%85%A1%20%E1%84%80%E1%85%B5%E1%84%89%E1%85%AE%E1%86%AF%20%E1%84%86%E1%85%A6%E1%86%AB%E1%84%90%E1%85%A9%E1%84%85%E1%85%B5%E1%86%BC%20%E1%84%8C%E1%85%A5%E1%86%BC%E1%84%85%E1%85%B5%2011d80c0ab1064f8696ecd81c49b8d5fd/Untitled%207.png)
    
    - 클라이언트 - 프록시서버 or cdn(content delivery network - 캐시) - 웹 서버
    - 먼 거리에 있는 정보를 계속 가져오면 오래 걸린다
    - 캐시에 저장하는 정보는 바뀌지 않는 정보들
    - 응답이 빨라진다는 얘기는 고객의 불만이 줄어든다는 것이므로 좋다.
    - 월마트에서 웹 사이트가 0.5초 빨라졌더니 매출이 30% 증가했다고 한다. ~카더라
- HTTP
    - HTTP 성장
        - http0.9 1991년
        - http1.0 1995년
        - http1.1 1997년
        - SPDY 2012년
        - http2.0 multiflexing
        - http3.0 2020년(강상진 멘토님 강의)
            - youtube, cloudflare 등
            - [https://www.slideshare.net/BrandonK/web-performance-optimization-with-http3](https://www.slideshare.net/BrandonK/web-performance-optimization-with-http3)
    - Http가 어느정도 통신이 없으면 session time out이 지나고 통신이 끊겨서 필요 시 재 연결해야한다
    - Http 1.1부터 persistance connection을 진행하였고, 그 전에는 unpersistance connection으로 1회 데이터 통신마다 http를 끊는다.
    
    ![Untitled](%E1%84%89%E1%85%A9%E1%84%86%E1%85%A1%20%E1%84%80%E1%85%B5%E1%84%89%E1%85%AE%E1%86%AF%20%E1%84%86%E1%85%A6%E1%86%AB%E1%84%90%E1%85%A9%E1%84%85%E1%85%B5%E1%86%BC%20%E1%84%8C%E1%85%A5%E1%86%BC%E1%84%85%E1%85%B5%2011d80c0ab1064f8696ecd81c49b8d5fd/Untitled%208.png)
    
    - 웹페이지 로딩이 오래 걸리면 어떻게 할 건가? → 프론트의 시간을 줄여야한다. → 이 다음이 백엔드(쿼리튜닝)같은걸 진행하는 것이다. →특히 이미지와 같은 것이 많을 수록 최적화가 더 필요하다. (11번가)
    - **사이트 객관적 평가 파이트 - webpagetest.org(외국을 가지 않아도 외국에서의 접속을 테스트할 수 있다.)**
        - cache control
        
        ![Untitled](%E1%84%89%E1%85%A9%E1%84%86%E1%85%A1%20%E1%84%80%E1%85%B5%E1%84%89%E1%85%AE%E1%86%AF%20%E1%84%86%E1%85%A6%E1%86%AB%E1%84%90%E1%85%A9%E1%84%85%E1%85%B5%E1%86%BC%20%E1%84%8C%E1%85%A5%E1%86%BC%E1%84%85%E1%85%B5%2011d80c0ab1064f8696ecd81c49b8d5fd/Untitled%209.png)
        
        - cdn 전세계 곳곳에 서버 배포, 각 위치에 맞는 서버에서 전송(캐시가 한 번 되면)
        - browser control, internet control
        - minify script 줄이기
        - gzip으로 줄이기(이미지압축)
        - 꼭 개발하면 minify해서 줄이자
        - 도메인 샤딩
    - **chrome lighthouse**를 이용하면 확장으로 가능
    - nginx가 apache보다 훨씬 많은 기능을 제공함으로 이 참에 써보는 것도 좋다.
        - 웹 방화벽과 같은 기능ㅇ이 추가되었다.
- 이미지 최적화
    - 이미지의 로딩 속도가 엄청 길다.
    - 이미지 디스플레이의 원리를 알아야한다.
    - 해상도가 증가 → PPI(pixel per inch)가 증가
    - 이 딜레마를 해결하는 법
        - [https://tinypng.com/](https://tinypng.com/)
        - 거의66%의 효율을 보인다
        - 원본 이미지는 백터 이미지로 한칸한칸 들어있는데 공통부분은(같은 색인 부분은) 데이터를 한칸한칸 저장하지 말고 묶어서 처리해서 압축하는 알고리즘 사용
        - 사람의 눈이 인식 못할 정도로만 압축
        - 이미지는 이미 압축되어있는 형태 (jpeg)
        - 이런 경우에는 다시 압축하지말고 브라우저 캐싱을 사용하자
        - image compression algorithm(이런 부분도 쉽게 사용할 수 있다.)
- 모바일 환경 최적화
    - 인터넷 사용자의 6-70%는 모바일 사용자
    - 앱이든 웹이든 이러한 해상도를 지원해주어야한다. → 반응형 웹디자인(RWD) → WURFL.js
    - css를 사용해서 bootstrap을 사용하는 방식
    - responsive web design를 반드시 사용
    - 안 보일 정보는 css에서 숨기기
    - shrink : pc버전, 모바일버전일 때 모바일버전의 디자인을 미리 해놓는 것
    - single page application : page가 link가 있는게 아니라 스크롤로 무한 이동이 되는 어플리케이션
        - 이러한 친구들은 처음부터 모두 띄울 필요가 없고 스크롤 이벤트에 의해서 보이기 직전에 불러오면 된다.
    - 반응형 웹
        - 반응형 웹 - css
        - 서버측 반응형 웹 - 디바이스별로 요청이 들어오면 어떤 디바이스인지 확인해서 그에 맞게 요청을 들어주는 것
        - 둘 다 혼용해서 쓴다

## **22.05.21 박주형 멘토님 AWS**

****프로그래머스 AWS 데브코스 매우 좋다 추천***

- Cloud Service
    - 클라우드를 사용하지 않으면 별도의 시스템실을 준비해야한다. 클라우드 서비스를 이용하면 필요한 만큼 자원을 사용하는 온디멘드 속성, laaS Paas Saas 나눠서 이용가능
    - 클라우드 서비스 중에서는 점유율은 AWS가 가장 높다
    - 네이버 NCP 클라우드 사업 → 정부가 밀어주는 사업(국산을 미뤄줘서)
- AWS 시작 전 알아야되는 사실
    - 전 세계 데이터센터(region)가 곳곳에 있다 → 지역설정가능
    - region마다 제공되는 서비스와 비용이 차이가 난다.
    - instance 만들 떄마다 region변경이 필요하다.
    - 계정부여
        - IAM → 사용자에 대한 권한 관리, instance에 띄우는 자원에 대해서 역할 관리
            - 사용자 세부 정보 수정
                - 액세스 키 : api, cli, sdk로 접속 시 액세스 키가 필요
                - 암호 : 패스워드
            - 권한 부여
                - FullAccess : 권한 전체 부여
                - 이외의 것은 일부 권한만 부여
            - 역할 부여
                - 어떤 인스턴스에 대한 역할
                - 그 역할에서 정책을 가져다 쓴다.
    - EC2 : 인스턴스를 확보하는 가장 기본적인 것
        - 솔직히 EC2만 쓸 수 있으면 된다.
        - 서버 제작
            - AMI 가상화된 이미지 : 원하는 이미지로 생성 가능
            - 이미지로 원하는 걸 이용할 수 있다.
                
                (ex) jenkins가 설치된 상태로 서버를 실행할 수 있다
                
                 apache kafka → 이걸 이용하거나 MSK)
                
            - t2.small정도는 되야 실제 서비스 가능
            - key pair가 있어야 내 서버에 접속 가능(공개키 개인키)
            - 속성
                - 퍼블릭 DNS : 접속할 수 있는 도메인
                - 퍼블릭 IP : 접속할 수 있는 IP
                - 인바운드 규칙 편집
                    - ssh 22
                    - HTTP 8080
                    - 바운더리 설정
                - 실행 설정
                    - chmod 400 {key}
                    - ssh -i {key} ec2-user@{public domain}
            - Elastic Beanstalk → 웹서비스 백엔드구성할 때 대부분 만들어준다.
                - 포괄적인 웹 어플리케이션 관리
                - 완전관리형
                - 자동 어플리케이션 상태 모니터링
                - EC2와 요금은 같다.
                - 구성 → 편집 → 인스턴스 유형 편집 가능
                - 키 → 비어있으므로 세팅해야 한다.
                - 기본적인 서비스 role도 설정되어있다 → 변경 가능
                - 스프링 파일 띄우려면 java설정 후 jar 업로드
            - CDN이나 ELB에서 SSL인증을 할 수 있다 http → https
            - beanstalk는 ELB를 보유 중
            - SSL인증은 총 세군데에서 가능(CDN, ELB, Elastic Beanstalk)
            - CDN은 유일하다.
                - 그래서 SSL 인증서는 CDN 본부가 있는 곳에 있어야 한다
            - Lambda - Serverless
                - 별도의 서버가 있는 것이 아닌 이벤트가 있을 때만 동작하는 것
                - 함수 하나만 등록해 놓으면서 사용
                - ex) 크롤러를 만든다 → 함수를 만들어놓고 필요할 때만 사용
                - 트리거 구성 → api gateway같은 것
                    - api 생성 → VPC 내부 or 외부에서 사용가능
                    - rest api → api 이름, 설명
                    - 리소스 생성 → member
                    - post → lambda
                    - 쉽게 백엔드 구현 가능
                - S3 - 파일 등을 저장하는 스토리지
                    - 파일 등록 캐치를 인식해 람다 이용 가능
                    - bucket에 put이 일어났을 때 해당 코드 실행
            - DB(돈이 제일 많이 나간다)
                - RDS (RDBMS)
                    - 데이터베이스 선택
                    - 퍼블릭 액서스 : 아니요로 만들면 jdbc로 접속을 못한다.
                    - EC2에 만들어도 되나 고생 좀 한다.
                    - 인스턴스 구성 : t클래스 포함으로 구성(더 싼거)
                    - 스냅샷 가능(특정시점으로 복원), 리더보드 가능
                    - default는 라지로 되기때문에 잘 관리해야한다, 삭제방지 없애야 삭제 가능
                    - 경고 생성을 통해 db 상태 확인 후 스케일링 변경 가능 (CloudWatch)
                - NoSQL
                    - DocumentDB(MongoDB)
                        - 클러스터를 얼마나 쪼갤건지 정도만 학습하면 된다.
                    - DynamoDB(자체)
                        - 바로 nosql 서비스를 이용하고 싶다면 다이나모 디비 사용
                    - Redis용 Amazon MemoryDB
                    - rabbitMQ : 메세징 서비스 이용할 때 편하다.
            - 프로그램 테스트할 때 로컬에서 DB 테스트하는지 아니면 테스트 서버에 디비를 띄워놓고 DB 테스트하는지 → 여러 사람이 사용할 경우 로컬에서 각자의 db로 테스트 후 로드
            - S3
                - 데이터 로그같은 것이 모두 여기 남아서 이걸로 로그풀처럼 사용해도 된다.
                - 스토리지처럼 사용 가능
                - 프론트엔드 단에서 사용
                - 정적 웹 사이트 호스팅 : html을 올려두면 다이렉트로 해당 정적 웹 사이트 접근 가능
                - 기본적으로 S3는 권한이 막혀있어서 모든 퍼블릭 어세스 차단을 열어야 한다.
                - 그리고 해당 버킷에 대해서 json으로 정책을 추가해주어야한다. (S3 → 정책설정 → S3 bucket → 버킷 ARN)
            - 도메인을 사면 Route 53에 등록
                - 제일 쉬운건 aws에서 도메인
                - 이렇게 등록한 도메인은 가비아에서 샀다면 가비아 네임 서버가 등록이 되어있으니, 그 네임 서버를 원하는 도메인으로 연동
                - 단순 레코드 정의 → 빈스톡 도메인을 가져다 오면 해당 도메인으로 api를 쏴준다.
            - Certificate Manager(SSL인증)
                - 가비아에서 받았다면 내 인증서랑 키 본문 달라고 해야한다.
                - 인증서 본문, 프라이빗 키, 체인
                - cdn
                - 퍼블릭 인증서 요청 → Route53에서 등록한 도메인 등록 → *.mumomu.com 도 등록
            - CloudFront(CDN)
                - 배포 생성 → 원본 도메인, 거기에다 cdn 서비스를 먹일거다 라고 하면, SSL 인증서를 여기에 먹여야한다.
                - 뷰어 프로토콜 정책을 HTTPS로 설정, SSL 인증서를 설정해야 하는데 여기에서 위에서 등록한 SSL인증서를 설정
                    - 무조건 같은 지역에 있어야만 한다.
                - AWS WAF
                    - 관리자 서버라던가, ip제한을 둬야한다면 WAF에서 ip세팅을 하면 폐쇄적인 CDN을 만들 수 있다.
            - 로드 밸런서
                - 로드 밸런서 유형 → 구성 → HTTPS로 해서 다음에 어떤 대상으로 가게할건지, 어떻게 묶을건지 대상그룹에서 설정 가능하다.
            - CI/CD
                - CodeCommit(GitHub와 매우 유사, 단순)
                    - mumomu(프로젝트명)  → Github사용 시 사용 X
                - CodeBuild → CodePipeline에서 엮어서 설명
                - CodePipeline → 소스, 빌드, 배포, 파이프라인
                    - 파이프라인 생성 → 개발, 운영에 따라 다름
                        - mumomu-dev-pipeline
                            - github연결 후 원하는 프로젝트 연결
                            - branch 연결 (develop 머지하는 순간)
                            - Build 추가 AWS codereview/Jenkins
                        - 파이프라인 까다로운거
                            - buildscript 짜는 것(언어마다 다름)
                            - 기본 템플릿은 제공한다.
                        - 배포 선택 사항 beanstalk 사용 시 편하다.
                        - 어플리케이션 이름에 사용 → beanstalk
                        - 10분 내에 완료
                        - 전환 중지 가능하고, 롤백 가능하다 → beanstalk에서 자체적으로 버전관리를 해준다 → 이전 버전으로 되돌릴 수 있다.
            - 서비스 이용하려면 vpc를 구성하여야 한다.
            - vpc 구성 된 예
                - ec2 beanstalk로 하면 자동으로 vpc 내로 갇힌다

## **22.05.27 김용웅 멘토님 아키텍처**

- **강의 내용**
    
    1. 웹서비스, RESTful API 구현을 위한 기본 아키텍처
    
    2. 데이터 저장 아키텍처
    
    3. 병목과 응답 속도 개선을 위한 기술들
    
    4. 서버리스 아키텍처
    
    5. 마이크로 서비스 아키텍처
    
    6. 아키텍처 설계시 참조할 만한 레퍼런스 아키텍처
    
    - 조정환 : AI 영어 서비스
- B2B SaaS 강점 : 정부지원, 성장률 높음
- 아키텍처 종류
    - HTTP 프로토콜 기반의 웹 서비스
    - Restful API, GraphQL
        - RESTful API
            - **API 명세는 Swagger? 스웨거로 주는게 좋다.**
            - 정적 → 동적으로 받기 위해 GraphQL 사용
        - GraphQL
            - 보내는 사람이 자신의 폼을 보내면 원하는 대로 변경되어 나온다.
    - HTTPS 적용
        - SSL인증서 : 1년마다 얼마, 람다 serverless, **Let’s Encrypt** 무료 발급(3개월 짜리)
    - Load Balancer
        - 무중단 서비스(이중화), 부하 감소
        - DB 분리 - 다른 애도 접속, 초기화 방지(persistance layer, application layer
    - 
- AWS 자격증, 쿠버네티스 자격증(인프라가 호황이다)
- HTTP 프로토콜 보완
    - 소켓(웹소켓)
        - stateless한 http로는 부족한 방식을 도와준다 (ACK)
        - 바로 구독한 유저들에게 뿌려진다.
        - 스톰프(gradle로 추가)? 언더토우 - 스프링 내 웹소켓 내장(설정에서 톰켓 제거)
        - 사용자가 많아지면 서버가 죽는데 이건 eventful방식이라 사용자가 많아져도
            
            nginx나 언더토우는 유지할 수 있다. 
            
    - CDN(Content Delivery Network)
        - 동영상과 같은 큰 파일을 서비스하는 방법
        - 여러개 있어도 된다..?(다른 멘토님들은 안된다했는데..)
        - purge : 디렉토리를 퍼지시키면 다시 캐싱된다. 그러나 약간의 시차가 있다.
            
            그래서 수정이 바로되진 않는다.
            
            특정 지역의 Accelaration
            
        - nginx에 ssl설치 후 80,443 포트 등을 다음에 port를 리버스프록시 해서 바꾸어준다.
- 병목 해결(사용자가 너무 많아 클라우드 이용료가 감당이 안될 때)
    - Message Queue(A)
        - insert 상황 트래픽이 몰릴 때
        - 카프카, 레비덴큐?를 따로 컨테이너로 여러개 만들어 db에 들어갈 데이터를 줄 세움
        - Consumer / Broaker
        - rdb가 주로 조회가 빠르고 (쓰기보다) 의외로 많이 썼다
    - In-memory Cache(A)
        - 조회를 많이 하면 똑같은 정적인 정보를 계속 주니 내부 메모리에 캐시를 해놓고 사용(key : object)
        - 길어야 좋다. application딴에 있으면 (Redis사용)
    - Http Service의 속도 개선(*Google Page Speed)
        - 어디서 느린지 찾는다(로그)
        - Scouter(분석프로그램, 제니퍼소프트 APM application performance management - jvm 분석)
        - 분석 기능으로 로그에서 어디가 느린지 확인 가능
        - WAS가 되게 많을 때 로그를 한 곳에서 볼 수 있다.
        - 수집 서버 1개(S3..?)
        - batch(batch를 주기적으로 실행)
        - google page speed
- 데이터 저장 아키텍처
    - 컨테이너 만들어서 mysql 이런거 하지마라
    - mariadb 같은 걸 컨테이너 만들어서 배포하면 백업을 못한다.
    - 관리형 서비스를 이용해야한다.
    - RDS 같은 건 백업도 해준다.
    - 데이터를 한 바구니에 담으면 안된다.
        - 내 서버에 맞는 DB를 골라야한다.
        - DB도 종류가 많다. (중요 데이터)
            - RDBMS : 정형화된 데이터
            - NoSQL : insert가 굉장히 많은 데이터 (mongoDB)
            - GraphDB : 관계
            - TimeSeriesDB : 시간이 기본적으로 들어가면 시간 단위로 분석
        - Storage (일반 로그는 DB에 저장하지 마라)
            - 로그도 일단 spring 등의 파일에 보관하다가 한달마다 삭제되도록 옵션 설정
            - 웹 서버 단에 파일로 보관하다가
            - 기록해야되는 로그는 따로 DB를 만들어 보관
            - SSD가 NAS보다 빠르다 (비용도 높다)
            - S3 - URL 매핑하면 바로 다운로드 가능
            - 어떤 스토리지 형태가 필요할 지 판단해서 선택한다.
- 실시간 분석
    - Kafka, Spark
- Serverless 아키텍처
    - 스프링부트보다 편하구나, URL Function별로 배포 가능
    - 내부는 컨테이너, AWS Lambda - Java보단 파이썬이 편하다
    - 스케줄링 돌려서 사용하는데 Request가 많이 않을 땐 거의 무료에 가깝다.
    - AWS는 10만 request까진 무료
    - AWS Lambda, Firebase Function, appwrite.io(로컬에서 사용)
    - server is function(메소드단위로 내가 배포를 할 수 있다), 서버가 있다는 걸 잘 모르겠다.
- 네트워크
    - 방화벽
        - Application 전에 방화벽이 있는 것이 원칙
    - Zone
        - public, private 나누어 구분
- 글로벌 서비스
    - Region
        - 이 지역을 아시아, 서울로 만들고 글로벌은 그 쪽으로 옮기면 된다
        - Zone간의 네트워크 비용은 별도
        - 서버가 같은 존의 옆 서버에 호출하여 응답받을 때 url을 이용해서 호출하는건 밖으로 나갔다와서 호출하므로 오래걸린다. 내부 url을 가지고 처리해야한다.
- Micro Service Architecture
    
    ![Untitled](%E1%84%89%E1%85%A9%E1%84%86%E1%85%A1%20%E1%84%80%E1%85%B5%E1%84%89%E1%85%AE%E1%86%AF%20%E1%84%86%E1%85%A6%E1%86%AB%E1%84%90%E1%85%A9%E1%84%85%E1%85%B5%E1%86%BC%20%E1%84%8C%E1%85%A5%E1%86%BC%E1%84%85%E1%85%B5%2011d80c0ab1064f8696ecd81c49b8d5fd/Untitled%2010.png)
    
    - 유레카, 관리까지 해야 MSA라고 할 수 있다.
- 서비스별 참조 아키텍처(참고하면서 개발)
    - AWS Reference Architecture Diagrams(필요한 솔루션 제공)
    - NCP 레퍼런스 아키텍처(맨 밑에 소개 두번째)
- VM vs Container

## **22.05.28 이건재 멘토님 스프링 부트 기초**

- 요즘 서버사이드 튜닝
    - 쿠버네티스 기반 컨테이너로 오토 스케일링
    - 로드밸런서
    - 면접 볼 때 물어보는 질문 : 프로세스 레벨 에서 동시접속 처리
        - **블럭킹IO(tomcat 0 multi thread, CPU), 논블럭킹IO → 검색해보기(면접단골)**
        - 논 블럭킹 → 뒤로 보낸걸 들어올 때 sync, async 구분해야한다.
        - 요즘은 대부분 논블럭킹 IO에 async 방식이다.
        - 쓰레드 방식은 블럭킹 IO
        - node는 앞단에서 블럭 체크하는 오버헤드가 없다.
        - node 자체의 event io?가 싱글 쓰레드라서 내부는 멀티이다?
        - 다른걸 처리하는 동안 나머지를 기다리게 하는 것이 블럭킹 IO
        - 소켓 디스크립터가 뒤로 넘겨서 계속 받을 수 있고 처리가 끝나면 콜백으로 받는 형태 (논블럭킹 IO)
        - 멀티 쓰레드 → 하나의 톰켓에서 여러개의 쓰레드를 띄워 request를 받는 것
        - 하나를 사용하고 다른 것을 사용하기전까지 blocking이 되있는 것??
        - Socket descriptor
        - 웹서버 vs WAS brower request
            - static 단의 빠르게 처리되야하는 것 : 웹 서버
            - java같은 프로그래밍에 관련되는 것 : WAS
            - 두 방화벽 사이에 두는 DMZ존에 nginx같은 것을 둔다
- 스프링 vs 노드 vs 장고 vs flask
    - 성격에 따라서 선택을 해야한다(성능비교)
    - MSA 도커가 나왔기 때문에 전반적인 서버 아키텍처가 바뀌고 있다.
    - 도커로 프로세스들을 관리하는데 관리하기 어려워서 쿠버네티스 사용
- spring에서 msa → spring cloud
    - 줄 라이브러리?
- Spring 특징
    - @SpringBootConfiguration
    - @EnableAutoConfiguration
    - @ComponentScan
        - basiclink가 있어 하위 객체만 컴포넌트 스캔이 된다.
        - 디렉토리 단위로 설정하므로, 하위에 있어야 한다.
    - 서비스 단은 서비스 인터페이스와 구현단으로 나누어 구분(BoardSevice, BoardServiceImpl)
    - 재사용성을 높이기 위한 의존성을 제거한 형태로 구현
    - 팩토리 매서드 패턴
- Spring MVC
    - model, view, controller
    - 프로젝트에서는 보라색만 구현(Controller, Service, Respository)
        
        ![Untitled](%E1%84%89%E1%85%A9%E1%84%86%E1%85%A1%20%E1%84%80%E1%85%B5%E1%84%89%E1%85%AE%E1%86%AF%20%E1%84%86%E1%85%A6%E1%86%AB%E1%84%90%E1%85%A9%E1%84%85%E1%85%B5%E1%86%BC%20%E1%84%8C%E1%85%A5%E1%86%BC%E1%84%85%E1%85%B5%2011d80c0ab1064f8696ecd81c49b8d5fd/Untitled%2011.png)
        
    - 파란색 - 스프링 부트가 처리
    - 인터셉터라는 걸 이용해서 앞의 부분 처리 가능
    - 밖의 웹 부분은 필터로 처리
- react(3000) ← json → spring root — jpa
    - restful api → HTTP message json → json rpc ← → 압축 메세지(proto buffer, thrift) → gRPC
        - Create → Post
        - Read → Get
        - Update → Put
        - Delete → Delete
        - URL 규칙
            1. 명사
            2. 구조적
            3. 하이픈 대신 언더바
            4. 끝은 /로 끝내지 말자
            5. ?
- 로그인 유지
    - 한 네트워크에서 jsession으로 로그인 유지 → 다른 로드밸런서에서는 유지 안됨 → 레디스로 관리 → 디비라 날라갈 가능성 있음
    - oauth2로 구성 → jwt형태로 확장해서 구현
    - jwt와 session 차이 찾아볼 것
- 유닛테스트
    - unit test4 - RunWith
    - unit test5 - ExtentWith

## **22.05.28 김용웅 멘토님 스프링 CoC 강의**

- 개발 시대가 아니라 프레임워크의 시대
    - 시작이 Convention of configuration
    - 시작은 Ruby, david heinemeier hansson : 프레임워크의 시작
- 관례 : 이미 사람들이 정해놓은 것 (for문 안은 i,j)
    - 좋은 코드를 보면 좋은 관례, 나쁜 코드를 보면 나쁜 관례를 배운다.
    - maven 구조 등과 같은 관례를 미리 적용?(스프링부트?)
- 서비스 구성 과정
    1. 서비스 키워드 지정
    2. Controller → CRUD 담당 → create, read, update, delete 붙이기
    3. baseModel에 createAt, creator, updateAt, updater를  추가해서 모든 entity에 추가하여 관리, used도 추가해서 실제로 삭제하지 말고 false로 해서 관리
    4. Controller 통일 기능 필요할 시 base controller를 만들어 protected로 필요한 기능 넣는다
    5. Interceptor - 보안취약점 점검, 로그인 점검
        1. 방어코드를 인터셉터에 넣는다.
        2. XSS를 나갈 때 나가는 인터셉터에서 감지한다
    6. controller method 호출시 
        1. validation(나중에 꼭 만들어야 한다.) 
            1. if(name ≠ null) 이렇게 짜지 마라(유지보수나쁨) → if(name == null) throw error;
            2. 이런식으로 중괄호가 길지 않도록 해야 유지보수에 편하다.
        2. business logic
        3. make response
    7. if문 반복하지 말고, Map에 key:value형태로 보관한다.
        1. 진정한 OOP는 if문을 사용하지 않는다.
- MyBatis : XML 조작하는 파일 만들 때, 한 개 테이블마다 한 개씩 넣을 때 관리
- APM : Scouter 저번 강의와 같다.
    - 켜놓고 작업하는게 좋다. 스카우터 라이브러리를 프로젝트에 넣은 다음에 안고 실행하게끔 한다.
- upsert : create + update를 동시에 처리하는 것
- log(slf4j)
    - 들어오는 값과 나가는 값은 info
    - 중간 값 비교는 debug
    - try, catch, exception → exception은 log.error, 알고 있는 오류다 그러면 warn
    - 운영 서버는 error,warn만 나오게 설정, 테스트 서버에서는 info, debug까지
    - 근데 발견 못하면 운영 서버 단계를 낮춤
    - 나가는 리스트인 경우 info를 출력하지 말고, 사이즈만 출력하면 된다.
    - 하루에 한 개의 로그 파일 생기게, 한 달된 것은 사라지도록
    - 한 번 관리하면 이후 관리가 필요 없어진다.
- 운영서버에는 swagger가 보이지 않게 하는 법
    - @Profile(”local”,”dev”)를 사용하는 방법
    - application-prod.yml에서 swagger.enable=false로 지정하는 방법
- undertow써라!!!!
    - exclude group: ‘org.springframework.boot’, module: ‘spring-boot’starter-tomcat’
    - 대신 undertow implement
    - tomcat는 request 하나 당 하나의 쓰레드를 준비하여 준비가 되면 응답
    - 100개면 100개
    - undertow는 event풀 방식? 단일 쓰레드라 느리다? → 실제로는 별로 다르지 않다.
        
        하나하나씩 처리하는게 훨씬 빠르다? 기업에서 nodejs를 안 쓰는 이유는 시스템을 만들고 나면 관리하고 모니터링 해야하는 툴들이 있는데, java계열이 많아서 java를 사용하는 것. 옛날 언어들은 cpu 하나일 때 기준으로 만들어져 병렬처리가 힘듬, 그걸 대체해서 나오는게 새로운 언어들(C → Go, Java → Scalar, Kotlin)
        
        자바를 배우는 이유는 전자정부프레임워크가 자바가 크기 때문이다.
        
    - 단일쓰레드가 생각보다 느리지 않다? 언더토우는 여러 사용자에 대해 하나의 쓰레드로 관리하는 것.
    - 이벤트 풀을 구성하는 쓰레드만 단일이고, 로그 등과 같은 것이 일어나면 쓰레드를 추가로 사용한다(이벤트 풀만 사용하는 것이므로)
- 프론트단은 수정이 많으므로 그들의 요청에 맞춰주는게 좋다
    
    (백엔드의 고객은 프론트엔드, 웹개발자)
    
    [https://www.notion.so](https://www.notion.so)
    

## **22.05.29 배성민 멘토님 웹 표준, 웹 접근성**

- 웹 표준 : 웹의 사용성 및 접근성 보장
    - HTML, CSS 등에 대한 WC3규격 준수 등
    - 웹 표준 기술이란?
        - 표준 문법을 지켜라 WC3에 맞춰서~~
        - <br>은 닫는 태그를 없게하는 등
    - 해야하는 이유?
        - 웹 접근성 수준 향상(스크린 리더 - SENSE READER)
            - 눈이 안보이든 어떤 사람이든 사용할 수 있어야 한다.
        - 검색엔진 최적화
            - 구글이 웹 사이트의 표준을 지키는 사이트를 검색하여 찾아온다.
        - 코드의 엄격한 분리 가능 → 손쉬운 유지보수
        - 웹 호환성 확보
            - Cross Browsing
    - DTD - <!DOCTYPE html> 적는것
        - 이 문서를 html5 문법으로 읽겠다는 뜻
    - 시맨틱 마크업
        - NAV,SECTION,ARTICLE,ASIDE,HEADER,FOOTER
            - article안에 section이 있으면 안된다.
            - section, article 안에는 h 태그를 하나씩 반드시 넣어야한다.
            - 무조건 어느 영역 안에 있어야만 한다. 아니면 스크린 리더에 버려지는 영역이 되버린다.
        - main,div,p,span,address,ul,ol,block,quote
            - header태그안에 div들 넣지 마라
            - br 태그 남용 금지
            - h1은 하나만 있어야한다
        - 구조태그를 의미한다
        - DIV로만 하면 스크린 리더가 읽지 못한다.
        - 메타데이터 태그
            - alt속성 : 눈이 안보이는 사용자에게 읽히는 이미지 설명
        - label안에 anchors, buttons같은 인터렉티브한 요소 배치 X, 입력하기 어려워진다.
- 웹 호환성 : 웹 브라우저 버전, 종류와 관계없는 웹 사이트 접근
    - 웹 표준 준수를 통한 브라우저 호환성 확보
- 웹 접근성 : 인적, 환경적 요인에 제약없는 웹 정보 접근
    - W3C 웹 접근성 이니셔티브(WAI)
    - 한국형 웹 콘텐츠 접근성 지침 2.0
    - 인식의 용이성, 운용의 용이성, 이해의 용이성 등
    - 쓰는 이유
        - 사용자 층 확대
        - 개발 및 운영의 효율성
        - 기업 이미지 상승
        - ☆법 준수 - 지키지 않으면 사업이 망할 수 있다.
            - 장애인 차별 금지 및 권리 구제 등에 관한 법률 등이 있다.
    - 웹 콘텐츠 접근성 지침(WCAG 2.0)
    - <html lang=”ko”>
        - 스크린 리더에게 이 문서가 한국어로 이루어짐을 알려줌 → 언어셋 지정
            - 의미적인 태그를 작성해야 한다.
            - 선형적으로 작성해야 한다.
                
                그래야 tab키를 통해 페이지를 논리적으로 이동 가능하니까
                
            - 트리구조를 명확히 해야 한다.
    - IR 기법
        - 화면에서는 안 보이고, 스크린 리더에서는 읽히게 하는 기법
    - 웹 접근성 케이스 살펴보기
        - a태그 : 새로운 리소스를 가져오거나 새로운 페이지를 불러오기 위한 것
        - button태그 : 데이터의 업데이트나 변경, 레이어 이동 등을 구현하기 위한 것
        - 색맹을 위해 현재 페이지 표시 + border-line 표시
        - 현재 페이지 표시를 통해 어디 페이지인지 알 수 있게 해야한다
        - 레이어 팝업 호출 시 포커스 레이어 팝업으로 이동 → 이후 기존의 위치로 포커스 이동
        - focus가 있다고 hover가 있어야하는건 아니지만, hover가 있으면 focus가 있어야 한다.
        - 특수문자의 경우 의도된 바와 다르게 해석될 수 있다.
        - aria-hidden : IR기법과 반대되는 기능(UI로는 보이는데, 스크린 리더로는 읽히지 않게하는 기능)
        - for로 연결하여야 한다 (input같은 경우)
        - aria-label을 입력하면 placeholder처럼 읽게할 수 있다.
        - aria-live
            - assertive : 급하게 반응
            - polite : 조용하게 진행
        
        ![Untitled](%E1%84%89%E1%85%A9%E1%84%86%E1%85%A1%20%E1%84%80%E1%85%B5%E1%84%89%E1%85%AE%E1%86%AF%20%E1%84%86%E1%85%A6%E1%86%AB%E1%84%90%E1%85%A9%E1%84%85%E1%85%B5%E1%86%BC%20%E1%84%8C%E1%85%A5%E1%86%BC%E1%84%85%E1%85%B5%2011d80c0ab1064f8696ecd81c49b8d5fd/Untitled%2012.png)
        

## **22.06.04 김건우 익스퍼트**

spring jpa + baekjoon platinum + 앱 런칭/서비스 + 

코딩테스트 회사오는거랑 소마가는거랑 크게 다르지 않다.

스프링 부트 기술 스택으로 실제 서비스를 만들어봤냐? 이정도일거같다.

스타트업을 가려면 코딩테스트보단 실제 개발 경험(교육기관없이 바로 펖초먼스를내야하다보니까)

대기업같은 곳들은 잠재력을 보는데, 스타트업은 개발력을 본다.

1. 네카쿠라배 대기업을 갈 것이냐
    1. 우테코 - 연수 - 정석
    2. 네이버, 카카오 - 코딩테스트, 대외활동(소마 등), 서비스 직접 런칭해보고 문제해결을 했던 경험
2. 전통대기업
3. 은행공기업
4. 스타트업

로컬에서 서비스 만들어보고, aws ec2올려서 직접 nginx올려서 스프링부트

jpa를 써도 직접한 쿼리는 직접 짜야하므로 마이바티스를 해보는게 중요하다.

기획을 2~3개월을 했다. 인간의 심리를 정의하기 애매하다. 의료법하고 걸려서

디자인을 입히고, api를 프론트에서 받아다가 기능을 제공하는 것

디자인 외주(지원금), api 개발 후 프론트엔드 구현, 출시구현, 최종 발표

TOPCIT 좋은 사람만 인증받고, 책에서 안나오는게 많아서 실무에서 잘 찾아서 하는게 많다. 

대화의 단계, 이야기의 시작 씨앗→나무가 되서 나무들이 자라나고 숲이 되는 것

이런것도 Gamification 요소

선생님의 수업 만족도 + 학생들의 수업 만족도

처음했는데 평균 집중도가 올라갔을 때 수업에 도움이 된 것이다

유사한 프로젝트 Look At me 

5초에 한 번씩 학생의 화면을 찍어 화면을 보고있는지 뭘하고있는지 판단을 해서

집중도 점수를 매기는 것

80점에서 새겨서 집중하는 모습을 매칭해서 딴짓하고 있으면 집중도를 다르게 해서 시간별로 그래플ㄹ 그려준다

이런식으로 가도 긴장감이 있다. 우측 메뉴바에는 집중도 점수를 보여주면 되지 않을까?

## **22.06.18 강상진 멘토님 AWS 중급**

- REST API
    - rest api란 represetational state transfer의 준말로 상징상태들로 소통하는 웹 개발용 API
    - 3rd party services(구글, 네이버 등의 API)를 적용하는데 사용
    - rest api가 사람이 알아보기는 편하나 graphql이 훨씬 유용하다 → 나중에 쓸 일이 생긴다
- 일반 API와 REST API의 차이
    - REST is a type of API especially for building web-based systems.
    Not all APIs are REST, but all REST APIs are APIs.
    - REST is a set of rules, standards and guidelines for how to build a
    web API.
    - REST mostly just refers to using the HTTP(S) protocol the way it
    was intended.
    - In general, Customers' API and API related products usually use
    the term "API" to represent "REST API" in short
    - REST는 특히 웹 기반 시스템 구축을 위한 API 유형입니다.
    모든 API가 REST는 아니지만 모든 REST API는 API입니다.
    - REST는 빌드 방법에 대한 일련의 규칙, 표준 및 지침입니다.
    웹 API.
    - REST는 대부분 HTTP(S) 프로토콜을 사용하는 방식을 말합니다.
    의도했다.
    - 일반적으로 고객의 API 및 API 관련 제품은 일반적으로 사용
    간단히 "REST API"를 나타내는 "API"라는 용어
- REST API 설계 규칙
    - 정해진 규칙에 따라 작성한다
    - Stateless 클라이언트의 상태를 서버에 저장하지 않는다
        
        API 서버는 단순히 요청된 내용에 대해서만 응답한다
        
    - 가능하다면 API 응답은 인터넷(CDN,Proxy) 혹은 클라이언트(브라우저)에 캐싱한다 → 그래야 느려지지 않는다
        - 캐싱 불가능한 API
            - 실시간 조회 가능해야한 응답(환율, 가상화폐)
            - 개인 정보(법적, 사용자 동의 없이 캐싱할 수 없다)
            - 민감한 정보 (금융거래)
        - 캐싱 가능한 API
            - 상품 정보같이 변화가 적은 정보 (캐시 주기를 하루로?)
                - mutant manager
                - database update → trigger 설정해놔서 API 호출
                    - cdn 캐싱 삭제 API
    - swagger 사용
        - [https://petstore.swagger.io/](https://petstore.swagger.io/)
        - 간단한 테스트 및 규약 확인 가능
        - api key가 있어야 db에 접근가능토록 구현
        - json online viewer로 보면 json파일을 깔끔하게 볼 수 있다
- AWS API GATEWAY에서 swagger를 지원해주기 때문에 그것을 이용해서 export한 json형태의 api 문서를 가져오기 버튼을 누르면 가져올 수 있다
    - (curl -v -o out “[https://petstore.swagger.io/v2/swagger.json](https://petstore.swagger.io/v2/swagger.json)”)
    - timeout이 중요하다(API응답대기)
- API 제작 가장 빠른 방법
    - swagger로 API 제작
    - json추출 후 AWS API GATEWAY에 넣음
        - rest api의 end point 역할
        - rest api 요청을 분석하여 올바른 백엔드 프로세스를 수행
        - api버저닝
        - api인증
        - 보안
            - cors(cross-origin-resource sharing)
        - api인가
        - api캐싱
        - api 트래픽 관리
        - api req/res 변환
        - openapi spec 지원
        - cors 설정 지원
    - AWS 람다로 실행
        - serverless method
        - 여러 언어 사용 가능
        - ram → 128MB ~ 10GB
        - cpu → ram과 연동, 자동으로 늘어남
        - 실행 15분 후 time-out
            
            ![Untitled](%E1%84%89%E1%85%A9%E1%84%86%E1%85%A1%20%E1%84%80%E1%85%B5%E1%84%89%E1%85%AE%E1%86%AF%20%E1%84%86%E1%85%A6%E1%86%AB%E1%84%90%E1%85%A9%E1%84%85%E1%85%B5%E1%86%BC%20%E1%84%8C%E1%85%A5%E1%86%BC%E1%84%85%E1%85%B5%2011d80c0ab1064f8696ecd81c49b8d5fd/Untitled%2013.png)
            
- MSA란 서로가 서로를 호출할 수 있다
- 재귀 / 람다 / 또 다른 API 도메인 호출도 가능하다
- 실습
    - 람다 → 함수 생성 → 함수 URL 활성화 + none + cors 생성 → trigger(호출 트리거), 대상(호출할 트리거) → 수정 후 Deploy(배포)
    - 실제 서비스에서는 사용할 수 없다(도메인이 많이 필요하므로) → API gateway 사용
    - 리소스 - url
    - 메소드 - get post 등
    - 테스트 후 API 배포
    - 스테이지 설정
    - 도메인 구매 후 도메인 연결

## **22.06.22 배성민 멘토님 IT 금융권 취업**

- 금융업이란?
    - 은행을 대표적으로 생각하나 종류가 많다
    - 4대 금융업
        
        ![Untitled](%E1%84%89%E1%85%A9%E1%84%86%E1%85%A1%20%E1%84%80%E1%85%B5%E1%84%89%E1%85%AE%E1%86%AF%20%E1%84%86%E1%85%A6%E1%86%AB%E1%84%90%E1%85%A9%E1%84%85%E1%85%B5%E1%86%BC%20%E1%84%8C%E1%85%A5%E1%86%BC%E1%84%85%E1%85%B5%2011d80c0ab1064f8696ecd81c49b8d5fd/Untitled%2014.png)
        

- 왜 금융권을 선호할까?
    - 문과입장에서는 TOP TIER다
    - 안정적인 급여
        
        ![Untitled](%E1%84%89%E1%85%A9%E1%84%86%E1%85%A1%20%E1%84%80%E1%85%B5%E1%84%89%E1%85%AE%E1%86%AF%20%E1%84%86%E1%85%A6%E1%86%AB%E1%84%90%E1%85%A9%E1%84%85%E1%85%B5%E1%86%BC%20%E1%84%8C%E1%85%A5%E1%86%BC%E1%84%85%E1%85%B5%2011d80c0ab1064f8696ecd81c49b8d5fd/Untitled%2015.png)
        
    - 호봉제의 장점
        - 선형적인 상승
        - 연봉제는 협상을 하여 오를수도, 떨어질 수 있다
    - 안정성이 되게 좋다
        - 평균 근속 연수 16.9년
        - 국민,신한,하나,우리는 근속연수가 낮은편인데도 16년정도 된다
        - 퇴직금 - 누진제 퇴직금
            - 3개월 평균 월급 * (2 * 근속연수 - 2)
- 왜 금융권을 비선호할까?
    - 조직문화
        - 9시 출근인데 8시반까지 출근
        - 정장 출근
        - 상명하복
    - 스톡옵션 X
    - 행원 리스크, 잡무 리스크
    - 개발을 직접적으로 하지 않고, 3년정도 은행원 시키기도 한다
    - 하도급 문화
        - 금융권을 가면 위치는 갑의 위치가 된다
        - SI 업체를 많이 상대하다보니 기분이 좋지 않다(갑을 계속하다보면)
- 금융업 종류
    
    ![Untitled](%E1%84%89%E1%85%A9%E1%84%86%E1%85%A1%20%E1%84%80%E1%85%B5%E1%84%89%E1%85%AE%E1%86%AF%20%E1%84%86%E1%85%A6%E1%86%AB%E1%84%90%E1%85%A9%E1%84%85%E1%85%B5%E1%86%BC%20%E1%84%8C%E1%85%A5%E1%86%BC%E1%84%85%E1%85%B5%2011d80c0ab1064f8696ecd81c49b8d5fd/Untitled%2016.png)
    
- 핀테크 vs 테크핀
    - 핀테크 : 카카오뱅크, 토스뱅크
        - 핀테크는 금융이 주체이기 때문에 돈을 많이 받는다
    - 테크핀 : 카카오페이, 뱅크샐러드, 보맵 등
        - 핀테크는 it가 주체이기 때문에 돈을 상대적으론 적게 받는다
        - 네이버 파이낸셜은 테크핀이나 곧 핀테크로 될 가능성이 있다
- 금융권IT를 가면 경력이 단절된다? 이직이 어렵다?
    - 맞다
    - 개발 능력보다 해당 금융 도메인 지식을 더 요구 받는다(PM)
    - 처음부터 직개발하는 경우는 매우 드물다
    - 개발자로 이직은 거의 불가능하다
    - 이직을 하더라도 현재 받는 급여를 맞춰줄 수 있는 마땅한 회사가 없다
    - PM, PO 커리어를 쌓을 수 있다
    - 이직을 하게 되면 지속적인 높은 연봉으로 점프업이 가능하다
    - 도메인 지식을 바탕으로 개발 외의 분야로 확장이 가능하다
    - 정부주도 신사업을 기획해볼 수 있다
    - PM, PO, DT운용 엔지니어(클라우드 아키텍처), 컴플라이언스 담당자, 개인정보보호 담당자 등으로 이직한다
- 금융권 준비
    
    ![Untitled](%E1%84%89%E1%85%A9%E1%84%86%E1%85%A1%20%E1%84%80%E1%85%B5%E1%84%89%E1%85%AE%E1%86%AF%20%E1%84%86%E1%85%A6%E1%86%AB%E1%84%90%E1%85%A9%E1%84%85%E1%85%B5%E1%86%BC%20%E1%84%8C%E1%85%A5%E1%86%BC%E1%84%85%E1%85%B5%2011d80c0ab1064f8696ecd81c49b8d5fd/Untitled%2017.png)
    
    ![Untitled](%E1%84%89%E1%85%A9%E1%84%86%E1%85%A1%20%E1%84%80%E1%85%B5%E1%84%89%E1%85%AE%E1%86%AF%20%E1%84%86%E1%85%A6%E1%86%AB%E1%84%90%E1%85%A9%E1%84%85%E1%85%B5%E1%86%BC%20%E1%84%8C%E1%85%A5%E1%86%BC%E1%84%85%E1%85%B5%2011d80c0ab1064f8696ecd81c49b8d5fd/Untitled%2018.png)
    
- 면접준비

![Untitled](%E1%84%89%E1%85%A9%E1%84%86%E1%85%A1%20%E1%84%80%E1%85%B5%E1%84%89%E1%85%AE%E1%86%AF%20%E1%84%86%E1%85%A6%E1%86%AB%E1%84%90%E1%85%A9%E1%84%85%E1%85%B5%E1%86%BC%20%E1%84%8C%E1%85%A5%E1%86%BC%E1%84%85%E1%85%B5%2011d80c0ab1064f8696ecd81c49b8d5fd/Untitled%2019.png)

- 금융권 시스템의 특징
    - 대규모 트래픽
        - 망분리 규제
        - 엄격한 컴플라이언스(법에 의해 진행되는 규제)
        - 트랜잭션에 민감 → 금융공동망 존재
            - 대한민국의 돈은 한정적이라고 가정, 총액은 언제나 같아야한다
    - 금융공동망
        - 은행은 트랜잭션이 중요해서 은행 사이에서 이용되는 공통 DB
            - 두나콜?
        - 액수가 크면 한국은행의 BOK-Wire 사용
        - 소액은 금융결제원의 은행공동망 사용
        - 블록체인이 공동망을 사용하지 않기 위해서 나온 전략
    - 계정계, 대외계, 정보계
        
        ![Untitled](%E1%84%89%E1%85%A9%E1%84%86%E1%85%A1%20%E1%84%80%E1%85%B5%E1%84%89%E1%85%AE%E1%86%AF%20%E1%84%86%E1%85%A6%E1%86%AB%E1%84%90%E1%85%A9%E1%84%85%E1%85%B5%E1%86%BC%20%E1%84%8C%E1%85%A5%E1%86%BC%E1%84%85%E1%85%B5%2011d80c0ab1064f8696ecd81c49b8d5fd/Untitled%2020.png)
        
        - 채널계 : end user가 사용하는 공간(인터넷뱅킹, 폰뱅킹 등)
            - MCA라는 아키텍처로 통합
        - 계정계 : 실제 돈이 있는 DB(원장) > 통장 → 계좌 → 계정
            - Core Banking, 대부분 Java 사용
        - 정보계 : 거래 데이터를 바탕으로 통계 및 분석하는 시스템
            - 빅데이터가 적극 도입되고 있는 곳
        - 대외계 : 금융공동망
        - 기간계 : legacy 시스템을 금융계라고 한다
    - 전자지급결제대행
        - 카카오페이, 티몬페이, 엘페이, 페이코, 네이버페이 등
        - 특정 조건이 되야만 시작할 수 있는 사업
        - 얘네 때문에 신용카드 사들이 실적이 나오지 않는다
        - 그래서 경쟁사들 중 하나이다
    - 규제샌드박스
        - 금융은 규제산업인데 네이버페이한테 후불결제를 허락해줬다
            - 이게 신용카드랑 다른게 뭐지? 이렇게 허용해주는 것이 규제샌드박스
    - 신용카드 한도는 어떻게 측정될까?
        - 신용평가(CSS - credit scoring system)를 통해 결정
        - 빅데이터 기반으로 디지털 심사
        - 비금융정보 전문으로 신용조회를 하기도 한다
            - 상품약관을 오랜시간 읽은 뒤 확인 버튼을 누르는 경우 대출 상환의지가 높은 것으로 판단되어 더 큰 신용등급을 준다
    - 데이터3법, K익명성
        - 데이터 3법 = 개인정보보호법, 정보통신망법, 신용정보법
            - 개정을 왜 했을까?
                - 유럽의 개인정보법 적정성 심사 통과 (GDPR이 허용해주면 예외로 해준다)
                - 유럽 GDPR을 준수하기 위해 데이터3법을 개정하였다
                - **마이데이터** 도입
                    - 라이선스인데 특정조건 만족 시 금융위원회가 라이선스를 부여하는 것
                    - 전 기관들의 신용정보, 거래정보 등을 API로 받을 수 있는 것
                    - 마이데이터를 비롯 NFT, 메타버스, CBDC등은 면접시에 자주 나오는 화제거리
    - 망분리
        - 망분리는 왜 할까? → 법 때문에
        - 망분리 고려사항이 있다
        - 금융공동망은 어떻게 할까?
            - 전금법에 예외 분리가 있다~~ 라고 할줄 알아야한다
    - 공동인증서
        - 공동인증서는 20년 5월에 폐지되었다
        - 전자서명제도의 폐지를 의미하는 것이 아닌 공인인증서의 독점적 기능을 폐지하는 것이다
        - 공동인증서만 사용해서 인증하던 것을 폐지한 것이지 공동인증서를 없앤 것이 아니다
    - 비대면 실명확인
        
        ![Untitled](%E1%84%89%E1%85%A9%E1%84%86%E1%85%A1%20%E1%84%80%E1%85%B5%E1%84%89%E1%85%AE%E1%86%AF%20%E1%84%86%E1%85%A6%E1%86%AB%E1%84%90%E1%85%A9%E1%84%85%E1%85%B5%E1%86%BC%20%E1%84%8C%E1%85%A5%E1%86%BC%E1%84%85%E1%85%B5%2011d80c0ab1064f8696ecd81c49b8d5fd/Untitled%2021.png)
        
    - FIDO
        - FIDO 프레임워크
        - 지문등록방법
        - FIDO는 이 기술의 주체가 우리나라에서 선두해서 사용하고 있기 때문에 관심을 가지고 있다
        - 지문, 홍채 등 생체인증을 위해 사용된다
    - 모바일 신분증
        
        ![Untitled](%E1%84%89%E1%85%A9%E1%84%86%E1%85%A1%20%E1%84%80%E1%85%B5%E1%84%89%E1%85%AE%E1%86%AF%20%E1%84%86%E1%85%A6%E1%86%AB%E1%84%90%E1%85%A9%E1%84%85%E1%85%B5%E1%86%BC%20%E1%84%8C%E1%85%A5%E1%86%BC%E1%84%85%E1%85%B5%2011d80c0ab1064f8696ecd81c49b8d5fd/Untitled%2022.png)
        
    - RPA
        - 매크로, 테스트를 자동으로 해주는 매크로이다
        - RPA 기획능력을 많이 본다
    - FDS
        - 감시 시스템, 자동으로 잘못된 사용을 진단
        
        ![Untitled](%E1%84%89%E1%85%A9%E1%84%86%E1%85%A1%20%E1%84%80%E1%85%B5%E1%84%89%E1%85%AE%E1%86%AF%20%E1%84%86%E1%85%A6%E1%86%AB%E1%84%90%E1%85%A9%E1%84%85%E1%85%B5%E1%86%BC%20%E1%84%8C%E1%85%A5%E1%86%BC%E1%84%85%E1%85%B5%2011d80c0ab1064f8696ecd81c49b8d5fd/Untitled%2023.png)
        
        - 부정사용의 방지(빅데이터와 엮어서 이야기 할 수 있어야 한다)
        - 어떠한 IT 기술이 들어가는지
    - 그 외 먹히는 키워드들(이런 용어를 섞어서 말해라)
        - 빅 블러 : 금융과 비금융의 경계가 모호해지는 것
        - 언 번들링 : 금융어플 쪼개기(구 토스 모델)
            - 국민은행도 어플 많았다가 이제 다시 번들링해서 개수가 줄어들고 있다
        - 열거주의/포괄주의 : 열거주의는 열거된 것만 가능한 것, 포괄주의는 포괄해서 가능하게 하는 것
            - 법적인 용어
            - 포괄주의가 좋은 것, 뭉틍그레 써놓고 다 된다고 하는 것
            - 열거주의는 일부만 적어두고 이외는 안된다고 하는 것
    - ESG
        - 회계 용어
        - 환경, 사회도 생각하는 용어
        - 디지털로 할 수 있는 방법을 기획해보아야 한다
- 학점, 어학성적
    - 블라인드 기관은 상관이 없으나 아닌 기관은 학점은 금융결제원,POSCO? 이런데는 본다
        - 시중은행은 전부 블라인드, 금융공기업
        - 블라인드란 학교, 학점을 보지 않는다는 것
        - 육안?
    - 어학성적은 토익 800이상이면 좋을 것 같다
- 지원동기
    - 안정적인 직장을 원한다 → 근속연수를 높게 할 수 있다
    - 기획능력이 높다는 것을 어필한다
    - 정보보호병같은건 메리트가 있다
- 멘토님 회사명
    - 금융협회
- 한국증권금융 → 돈을 제일 많이 준다 (2년차에 1.2억 준다)
    - 상방 : 최대 올라갈 수 있는 연봉 → 상방이 되게 높다
    - 금결원, 포스콤 ㄱㅊ
- 전공필기가 개어렵다(CS지식, 행정고시 전산직으로 준비 많이 한다)
    - red black tree 왼쪽 세번 회전하면 나오는 결과 이런거 나온다

## **22.06.24 강상진 멘토님 IT 보안 기술**

- **네트워크 보안(클라우드)**
    - 퍼블릭 서브넷 : 외부와 인터넷 통신이 필요한 서비스들을 퍼블릭 서브넷에 배치
    - 프라이빗 서브넷 : 외부와 인터넷 통신의 격리가 필요한 서비스들을 배치하여 보안 강화
        - DB가 외부와 통신이 필요없다? 프라이빗 서브넷에 생성
    - VPC : 가상 사설 클라우드
    
    ![Untitled](%E1%84%89%E1%85%A9%E1%84%86%E1%85%A1%20%E1%84%80%E1%85%B5%E1%84%89%E1%85%AE%E1%86%AF%20%E1%84%86%E1%85%A6%E1%86%AB%E1%84%90%E1%85%A9%E1%84%85%E1%85%B5%E1%86%BC%20%E1%84%8C%E1%85%A5%E1%86%BC%E1%84%85%E1%85%B5%2011d80c0ab1064f8696ecd81c49b8d5fd/Untitled%2024.png)
    
    - bastion host를 모두에게 열어주고, bastion host ip만 security group에 넣어준다
    - 자기들의 중요한 시스템을 bastion host를 통해 재택근무 등에서 사용할 수 있게 한다
        - jump server
    - vpn은 폐쇄 네트워크를 이런식으로 이용한다 (virtual private network)
    
    ![Untitled](%E1%84%89%E1%85%A9%E1%84%86%E1%85%A1%20%E1%84%80%E1%85%B5%E1%84%89%E1%85%AE%E1%86%AF%20%E1%84%86%E1%85%A6%E1%86%AB%E1%84%90%E1%85%A9%E1%84%85%E1%85%B5%E1%86%BC%20%E1%84%8C%E1%85%A5%E1%86%BC%E1%84%85%E1%85%B5%2011d80c0ab1064f8696ecd81c49b8d5fd/Untitled%2025.png)
    
- 네트워크 계층 보안
    
    ![Untitled](%E1%84%89%E1%85%A9%E1%84%86%E1%85%A1%20%E1%84%80%E1%85%B5%E1%84%89%E1%85%AE%E1%86%AF%20%E1%84%86%E1%85%A6%E1%86%AB%E1%84%90%E1%85%A9%E1%84%85%E1%85%B5%E1%86%BC%20%E1%84%8C%E1%85%A5%E1%86%BC%E1%84%85%E1%85%B5%2011d80c0ab1064f8696ecd81c49b8d5fd/Untitled%2026.png)
    
    - IPS
        - 침입 방지 시스템
        - 방어 대상 네트워크 트래픽을 스캐닝하여 악성 공격을 감지하고
        방어하는 역할을 수행합니다. 방어의 방식은 악성 트래픽을 차단(block)
        할지 교정(redediate) 할지를 결정
        - 도스 / 디도스
    - IDS
        - 침입 탐지 시스템
    - 방화벽(firewall)
        - 외부에서 어떠한 공격이나 잘못된 접근이 들어왔을 때 차단해주는 것
    - DMZ
        - 웹서버, 이메일서버, FTP서버 등 외내부 서버의 통신을 돕는다
    - NAT
        - 사설(내부)망에서 공인망으로 나갈때 ip를 교환해주는 것
        - 사설 IP는 대역이 정해져있다 10, 172, 192
        - 주로 기업이나 기관에서 내부망을 사용하는 PC 에 사설 IP 를 제공하고
        외부 인터넷에 연결 시엔 공인 IP 하나를 같이 사용하는 형태로
        운영합니다. NAT 는 다수의 주소 변환 정보에 대해 IP 주소와 Port
        번호로 구성된 NAT Forwarding Table 을 보관하고 있고 이에 맞게
        주소 변환 서비스를 제공
- HTTPS
    - 원리가 무엇이냐?
        - HTTPS 는 SSL 혹은 TLS 전송 기술을 사용합니다. TCP, UDP 와 같은
        일반적인 인터넷 통신에 안전한 계층(layer)을 추가하는 방식
    - SSL 인증서와 SSL 핸드셰이크 탑재 기술
        - 하나의 키로 암호화/복호화를 수행하는 대칭 키 암호화 방식
        - 한 쌍의 키페어로 암호화/복호화를 수행하는 공개 키 암호화 방식
        - 통신 대상을 서로 확인하는 신분 확인
        - 믿을 수 있는 SSL  인증서를 위한 디지털 서명
        - 디지털 서명을 해주는 인증기관
        - 공개 키를 안전하게 전달하고 공유하기 위한 프로토콜
        - 암호화된 메시지의 변조 여부를 확인하는 메시지 무결성 알고리즘
    - 금융권은 해킹 당하면 안되기 때문에 비싼 인증서를 사용한다
        - 좀 더 스트롱한 알고리즘을 사용하기 때문에 10배정도 비싸다
- DNS
    
    ![Untitled](%E1%84%89%E1%85%A9%E1%84%86%E1%85%A1%20%E1%84%80%E1%85%B5%E1%84%89%E1%85%AE%E1%86%AF%20%E1%84%86%E1%85%A6%E1%86%AB%E1%84%90%E1%85%A9%E1%84%85%E1%85%B5%E1%86%BC%20%E1%84%8C%E1%85%A5%E1%86%BC%E1%84%85%E1%85%B5%2011d80c0ab1064f8696ecd81c49b8d5fd/Untitled%2027.png)
    
    - DNS resolver : dns를 resolve해서 찾아내는 서비스
        - 클라이언트의 요청을 받고 해당 도메인의 정보를 요청 및 캐시 하는
        서버
        - kt를 사용하면 kt dns resolver, 통신사마다 다름
        - 안전한 dns 서버를 위해서 보조 dns 서버까지 총 2개의 ip를 제공한다
            - ip가 한 개여도 dns는 더욱 더 많다
            - 어떻게 여러가지로 나눈 ip가 하나의 ip로 모이는가? → anycasting(같은 ip를 전세계에서 사용하도록 할 수 있는 기술)
            - cast 종류
                - unicast는 단일(1:1)
                - anicast는 접속 위치에 따라서 다른 곳에 접속하게끔 한다
                - multicast는 동시에 여러곳에 접속하는 방식(카톡방 채팅같은 것), 제한된 공간
                - broadcast는 유투브 방송처럼 전세계에 뿌릴때
- API 공격
    - api가 DB, 웹서버, 웹 어플리케이션 서버, 메시지큐, 스토리지 등 중요한 데이터가 저장되고 처리되는 시스템과 통신하여 공격 시 위험하다
    - 그래서 API를 사용할 수 있는 인증 권한 부여
        - API 키 사용(토큰?)
            - 고유 키를 받아 모든 api키에 포함되어야 한다
            - 평문으로 되면 안되고 url가 조합된 값이어야한다
        - JWT(json web token) 형식으로 만든 웹 인증 용도의 토큰 사용
- MSA
    - 작은(micro) 다수의 서비스(services)를 하나씩 개발하고
    연결하여 전체 애플리케이션을 만들어 나가는 방식
    - HTTP(S) 상에서의 REST
    API 같은 가벼운 통신 방식을 사용하는 아키텍처
    
    ![Untitled](%E1%84%89%E1%85%A9%E1%84%86%E1%85%A1%20%E1%84%80%E1%85%B5%E1%84%89%E1%85%AE%E1%86%AF%20%E1%84%86%E1%85%A6%E1%86%AB%E1%84%90%E1%85%A9%E1%84%85%E1%85%B5%E1%86%BC%20%E1%84%8C%E1%85%A5%E1%86%BC%E1%84%85%E1%85%B5%2011d80c0ab1064f8696ecd81c49b8d5fd/Untitled%2028.png)
    
    - 주요기능
        - API 인증
        - API 사용 빈도 제한
        - API 캐싱
        - API 등록
        - API 분기(라우팅)
- 암호화와 해싱의 차이점
    - 암호화 : 암호화한 문자열을 복호화가 가능하다
    - 해싱 : 원본 문자열을 알아볼 수 없는 난해한 문자열로 바꾸고 복호화가 불가능
        - 중간 변조 감지를 위해 사용된다(한끝차이로도 아예 다른 문자열이 되기 때문)
- 정보보안의 3요소
    - 기밀성
    - 무결성
    - 가용성
- 오토스케일링
    - **스케일 아웃**
        - **개수가 늘어남**
    - **스케일 업**
        - **크기가 커짐**

## **22.07.01 박수현 멘토님 GIT LAB**

- Git vs GitHub vs GitLab
    - git - tool(도구)
        - 소스 코드 버전 관리 시스템으로 로컬에서 변경 사항을 추적하고 원격 리소스에서 변경 사항을 푸시하거나 가져올 수 있다
    - github - 협업 도구
        - 공개적으로 사용 가능한 무료 서비스로 모든 코드를 공개해야 한다
    - gitlab - A~Z까지 지원
        - 개인 또는 조직이 Git repository 의 내부 관리를 제공하는데 상용할 수 있는 Github 으로 즉 비공개된 Github라고 할 수 있다
    
    ![Untitled](%E1%84%89%E1%85%A9%E1%84%86%E1%85%A1%20%E1%84%80%E1%85%B5%E1%84%89%E1%85%AE%E1%86%AF%20%E1%84%86%E1%85%A6%E1%86%AB%E1%84%90%E1%85%A9%E1%84%85%E1%85%B5%E1%86%BC%20%E1%84%8C%E1%85%A5%E1%86%BC%E1%84%85%E1%85%B5%2011d80c0ab1064f8696ecd81c49b8d5fd/Untitled%2029.png)
    
- 유닛테스트

## **22.07.02 정철웅 멘토님 클린코딩**

- 가치 있는 소프트웨어를 만들어 내기 위한 기술
- Any fool can write code that a computer can understand, Good programmers write code that humans can understand. - Martin Fowler -
- 80%가 유지보수에 사용되며
    - 기능성
    - 신뢰성
    - 사용성
    - 효율성
    - 유지보수성
    - 이식성

이 요구된다.

- 1000명의 사람 중 300명이 초기개발, 400이 유지보수자, 300명이 특수직군이다.
    - 특수 직군 : 100 관리자, 50 기획자, 80 마케터
- **소프트웨어가 망가지는 주요 원인들**
    
    ![Untitled](%E1%84%89%E1%85%A9%E1%84%86%E1%85%A1%20%E1%84%80%E1%85%B5%E1%84%89%E1%85%AE%E1%86%AF%20%E1%84%86%E1%85%A6%E1%86%AB%E1%84%90%E1%85%A9%E1%84%85%E1%85%B5%E1%86%BC%20%E1%84%8C%E1%85%A5%E1%86%BC%E1%84%85%E1%85%B5%2011d80c0ab1064f8696ecd81c49b8d5fd/Untitled%2030.png)
    
- 누구를 위해서 깨끗한 코드를 만들어야 하는가?
    - 나의 코드를 볼 다른 사람을 위해서
    - 코드의 깨끗함의 기준은 타인이 기준이다
- 깨끗한 코드와 깨끗한 설계의 관계는?
    - 깨끗한 설계가 반드시 필요한가? - 그렇진 않을 수 있다, 규모에 따라 다름
    - 깨끗한 설계가 있으면 깨끗한 코드가 나올 확률이 높다
- 코딩 노하우를 알려줄 수 있냐? 했을 때 알려줄 수 있어야 한다
- 코드에 의미부여
    - 변수에 의미를 부여한다
    - 함수와 메서드에 의미를 부여한다
    - 고유성을 부여한다
        - 건전지를 가져와라 → 어디 있는지 몰라서 오래걸린다
        - 식칼을 가져와라 → 부엌에 있다는 것을 알아 금방 가져온다
    - 유사한 명칭을 사용하지 않는다
        - 대문자 O와 L 회피
        - 클래스 명칭
            - 명사와 명사구 적합
        - 메서드/함수 명칭
            - get,. set, is 접근자 추가
            - 동사/동사구 적합
        - 독특한 어휘 사용 지양, 일관성 있는 어휘를 사용
            - kickFile, fileCut, dbtouch
        - Less C/C
            - int cnt, days, mins
        - More C/C
            - int studentCnt, daysSinceRegister, minutesSinceLogin
- 함수 메서드 만들기
    - 함수, 메서드는 작게 만든다 (10줄)
    - 함수, 메서드는 한 가지를 잘 해야한다
    - 블록과 들여쓰기가 중첩구조가 생기지 않도록 한다(이중이내로)
    - 위에서 아래로 읽기 편하게 작성한다
    - 인자는 0개가 가장 좋으나 최대 3개 이내가 좋다
- 주석은 예시가 있어야 상대방이 내 코드를 이해할 수 있다(가이드 템플릿 예시)
    
    ![Untitled](%E1%84%89%E1%85%A9%E1%84%86%E1%85%A1%20%E1%84%80%E1%85%B5%E1%84%89%E1%85%AE%E1%86%AF%20%E1%84%86%E1%85%A6%E1%86%AB%E1%84%90%E1%85%A9%E1%84%85%E1%85%B5%E1%86%BC%20%E1%84%8C%E1%85%A5%E1%86%BC%E1%84%85%E1%85%B5%2011d80c0ab1064f8696ecd81c49b8d5fd/Untitled%2031.png)
    
- 주석
    
    ![Untitled](%E1%84%89%E1%85%A9%E1%84%86%E1%85%A1%20%E1%84%80%E1%85%B5%E1%84%89%E1%85%AE%E1%86%AF%20%E1%84%86%E1%85%A6%E1%86%AB%E1%84%90%E1%85%A9%E1%84%85%E1%85%B5%E1%86%BC%20%E1%84%8C%E1%85%A5%E1%86%BC%E1%84%85%E1%85%B5%2011d80c0ab1064f8696ecd81c49b8d5fd/Untitled%2032.png)
    
- 좋은 코드 예시
    - 라인당 변수가 하나여야 주석을 추가하기 용이하다
    - 상위 레벨 변수명과 동일한 변수명을 사용하는 것은 좋지 않다
    - 상수는 define하는게 좋다 - 수정에 용이
    - 함수는 한가지의 기능만 하여야한다
    - 연산자의 우선순위를 고려하여 코드를 작성
    - 로깅 : 서버로깅과 클라이언트 로깅 구분
    - 공통오류처리/개별오류처리 : 오류코드 Book 작성
    - 오류처리/예외처리 Try / Catch / Finally 적극적사용
    - null을 반환 및 전달하지 않도록 함
    - 변수의 범위구분 사용 // const, let, var
- 애자일 코딩 구현
    - 테스트 통과 (최소한의 핵심적인 기능)
    - 의도표현 (읽기 쉽고, 따로 설명이 필요없는 수준으로 리팩토링)
    - 중복제거 / 구성요소 최소화
    - 최종 소스배포
- 깨끗한 코딩을 위한 습관
    - 클린 코딩의 수칙이 지켜졌는지 코딩을 마무리하기전에 읽는다
    - 동료에게 코드검토를 의뢰한다
    - 주석이 적절한지 확인한다
    - 배포/커밋하기 전에 숫자를 1 ~ 10 혹은 1 ~ 5 센다

## **22.07.17 김용웅 멘토님 웹소켓 Spring Stomp**

[SpringBoot + STOMP 로 WebSocket 기반 채팅 개발 실습.pdf](%E1%84%89%E1%85%A9%E1%84%86%E1%85%A1%20%E1%84%80%E1%85%B5%E1%84%89%E1%85%AE%E1%86%AF%20%E1%84%86%E1%85%A6%E1%86%AB%E1%84%90%E1%85%A9%E1%84%85%E1%85%B5%E1%86%BC%20%E1%84%8C%E1%85%A5%E1%86%BC%E1%84%85%E1%85%B5%2011d80c0ab1064f8696ecd81c49b8d5fd/SpringBoot__STOMP_%EB%A1%9C_WebSocket_%EA%B8%B0%EB%B0%98_%EC%B1%84%ED%8C%85_%EA%B0%9C%EB%B0%9C_%EC%8B%A4%EC%8A%B5.pdf)

- **Copilot 사용 권유**
    - 주석과 함수명을 작성하면 코드를 작성해주는 ai
- WebSocket이란
    - HTTP는 클라이언트와 서버가 request와 response로 이루어진 것
    - 헤더가 계속 동일하고 비슷한 정보를 주고받는데, 낭비가 심한 형태이다
    - 구현하기 어렵다, 카트라이더 예시로 차의 변화상태를 다른 이들한테 모두 알려주려면 주기적으로 클라이언트가 서버를 계속 호출해야되서 문제가 생긴다
    - 이런 방식을 **Polling**이라고 한다
        - 게시판 처럼 실시간이 필요없는 곳에서 사용
    - 그래서 나온게 **Long Polling**
        - 일단 request 후 서버가 사용되면 그때서야 response
        - 그리고 바로 클라이언트가 바로 request하여 다시 대기
    - **Web Socket(Streaming)**
        - Long Polling을 양방향으로 주고 받을 수 있도록 설계
        - HandShake후 websocket full duplex persitent 진행
            - 양방향 통신을 의미
        - 특정 토픽을 구독하는 형태
        - 그때부터 변동사항을 계속 받을 수 있도록 해준다(broadcast)
        - publish, subscribe
        - 역사
            - 2011년에 W3C에 의해 표준화
            - 80과 443에서 동작(웹 브라우저에서 소켓을 통신하기 위함)
    - **Web RTC**
        - 화상 통화를 지원하는 형태
- 구현 도구
    - [Socket.IO](http://Socket.IO) - Node.js / JS
    - STOMP - Simple or Streaming Text Oriented Message Protocol, Spring Boot
    - Undertow 내장 WebSocket - Java의 또 다른 내장, redhat의 구독 WAS
    - Firebase Realtime Database : DB + WebSocket(언어상관 X)
        - 구글에서 인수
        - DB, 호스팅 서비스 등 여러가지 보유
        - 저장하고 메시징을 바로 전달해주어 IOT에서 많이 사용
- 개발 범위
    - Server : SpringBoot(STOMP)
    - Client : Frontend(web), Server(SpringBoot)
- 테스트 도구
    - Simple WebSocket Client
- 프론트 역할
    - 데이터 송신 / 수신을 알리는 동작 개발
- build.gradle
    
    ```
    //websocket
    implementation 'org.springframework.boot:spring-boot-starter-websocket'
    implementation 'org.webjars:sockjs-client:1.1.2'
    implementation 'org.webjars:stomp-websocket:2.3.3-1'
    ```
    
- ChatConfig 필요
    - WebSocketMessageBrokerConfigurer
    
    ```
    @Override
    public void registerStompEndpoints(StompEndpointRegistry registry) {
        registry.addEndpoint("/ws/chat").setAllowedOriginPatterns("*").withSockJS();
    }
    ```
    
- MessageMapping
    - 메시지를 보내면 해당 메시지를 WebSocket으로 보내는 것=

## **22.07.23 박수현 멘토님 리눅스 초급반**

- 실습은 AWS에서 EC2-Ubuntu22.04 만들어서 사용
- 이론
    - 역사
        - 리누스 토발즈에 의해 개발됨
        - 유닉스란, AT&T 사에 의해 1960년대 개발되어 지금까지 사용되고 있는 운영체제
        - 메인프레임, 중형, 소형 컴퓨터 시스템용 운영체제로 고전적으로 사용, PC용 운영체제로도 개발
        - 유닉스 계보
            - BSD 계열 유닉스
            - 시스템 V 계열 유닉스
            - POSIX 유닉스
        - http://futurist.se/gldt/
        - 배포 형태는 이제 너무 많은 갈래로 나뉘어 셀 수 없다
    - 리눅스 배포판
        - 슬랙웨어 계열
            - Suse리눅스, etc
        - 데비안 계열
            - Ubuntu, Konoppix, Lindows, Mint, etc
        - 레드헷 계열
            - CentOs, etc
    - 리눅스 배포판 구성 요소
        - Linux Kernel
        - Desktop UI
        - Utilities
    - 배포판 선택 방법(**일반적 - 내가 쓰면 Ubuntu, 회사에선 Redhet**)
        - 상업적 vs 비 상업적
        - 기업용, 개인용
        - 하드웨어 지원 및 특정 하드웨어 벤더의 인증
        - 서버군, 데스크탑군, 임베디드군, 특수목적(방화벽, 라우터, 클러스터)
        - 특정 산업군(교육적, 과학적, 음악적, 생물학적 등)
        - 보안성, 안정성, 사용성, 이식성
        - 지리적 위치(현재 국가에서 서포트 하는지)
    - 리눅스를 위한 데스크톱 환경(X11)
        - GNOME  - 손쉬운 사용
        - KDE - 기능과 확장성
        - XFce - 2D lightweight
        - LXDE - Minimalistic Desktop Environment
    - OS란
        - 시스템 하드웨어를 관리할 뿐 아니라 응용 소프트웨어를 실행하기 위하여 하드웨어 추상화 플랫폼과 공통 시스템 서비스를 제공하는 시스템 소프트웨어이다. 최근에는 가상화 기술의 발전에 힘입어 실제 하드웨어가 아닌 하이퍼바이저 위에서 실행되기도 한다
    - Kernel이란
        - 컴퓨터의 운영 체제의 핵심이 되는 컴퓨터 프로그램의 하나로, 시스템의 모든 것을 완전히 통제.
        - 운영 체제의 다른 부분 및 응용 프로그램 수행에 필요한 여러 가지 서비스를 제공
        - monoritic kernel vs micro kernel
            - monoritic kernel(단일 커널)
                - Unix, Linux, MS-DOS 등(현실적으로는 Layerd)
            - micro kernel
                - windows
    - 운영체제의 부팅 과정
        - BIOS → MBR → GRUB → Kernel → Init → Runlevel
            - Init : /etc /bin과 같은 기본형태 구성
            - Run level
                - 0: halt(시스템 종료)
                - 1: Single user mode(복원모드)
                - 2: Multiuser mode without NFS
                - 3: Full multiuser mode(텍스트유저모드)
                - 4: unused(커스텀)
                - 5: X11(그래픽 유저모드)
                - 6: Reboot(재부팅)
            - systemd에서 숫자가 아닌 자연어로 변경됨
    - 표준 파일 시스템 구조
        
        ![Untitled](%E1%84%89%E1%85%A9%E1%84%86%E1%85%A1%20%E1%84%80%E1%85%B5%E1%84%89%E1%85%AE%E1%86%AF%20%E1%84%86%E1%85%A6%E1%86%AB%E1%84%90%E1%85%A9%E1%84%85%E1%85%B5%E1%86%BC%20%E1%84%8C%E1%85%A5%E1%86%BC%E1%84%85%E1%85%B5%2011d80c0ab1064f8696ecd81c49b8d5fd/Untitled%2033.png)
        
    - 노란색 - 하드웨어 / 초록색 - sudo
    - 다중 사용자 개념
        - Window → PC(Personal Computer) 태생 자체가 개인을 위함
            - 관리자, 사용자 - 본인
        - UNIX → 6~70 서버, 태생 자체가 multi user를 위함(SSH)
            - 관리자와 사용자, 그룹이 구분되어 있음
            - 나(owner), 그룹(group), 남(other)에 대한 권한을 다르게 부여
            - root는 내가 아니다
- 실습
    - cat /etc/*-release
        - etc아래 release 제외하고 출력
        - a → all
        - l → long
        - ls → list
        - . 으로 만들면 히든파일로 생성
    - cat /etc/passwd
        - 사용자 계정 id/pw(해쉬)
    - cat /etc/shadow → 암호
        - 권한 부족으로 보이지 않는다
    - sudo cat /etc/shadow
        - 권한이 있는 관리자만 가져야한다
    - sudo passwd ubuntu
        - ubuntu 비밀번호 변경
    - sudo ln -s /usr/share/zoneinfo/Asia/Seoul /etc/localtime
        - 시간대 변경
        - 심볼릭 링크(소프트 링크) 방식
        - 원본이 삭제되면 조회가 불가능해진다(바로가기 느낌?)
    - ln hello.txt hello4.txt
        - 하드링크, 서로 다른 녀석이 되서 원본이 삭제되도 영향이 없다
    - inode OS
        - 리눅스 내에서 파일을 관리하는 체계 inode
        - inode = 파일 메타 데이터 + 데이터의 위치
        - 파일의 크기 시간, 접근, 권한 등등
        - 하드 링크로 걸었을 때는 inode가 같다
        - 하드 링크는 같은 inode를 바라보기 때문에 용량이 2배가 되지 않는다(백업용도)
        
        ![Untitled](%E1%84%89%E1%85%A9%E1%84%86%E1%85%A1%20%E1%84%80%E1%85%B5%E1%84%89%E1%85%AE%E1%86%AF%20%E1%84%86%E1%85%A6%E1%86%AB%E1%84%90%E1%85%A9%E1%84%85%E1%85%B5%E1%86%BC%20%E1%84%8C%E1%85%A5%E1%86%BC%E1%84%85%E1%85%B5%2011d80c0ab1064f8696ecd81c49b8d5fd/Untitled%2034.png)
        
    - whoami
        - 자신이 누군지
    - adduser
        - 유저 등록
    - sudo lastb
        - 사용자 접속 로그가 보인다
        - 해킹 로그 등
        - 그래서 key 기반의 인증을 한다
    - sshd 설정 바꿔주면 타 커널에서 접속 가능
        - /etc/ssh/ssh_config → ssh 클라이언트 설정
        - /etc/ssh/sshd_config → ssh 서버 설정
            - password notification = no라서 안되는것
        - 이후 cygwin같은 걸로 ssh ubuntu@”ec2 public ip” 로 접속
    - 사용자마다 pem키를 줄 수 없으므로 PKI 기반의 비대칭키 암호화 알고리즘으로 사용
    - 개인키/공개키(모두가 알아도 되는 키)
        - EC2마다 키가 다 다르게 할 순 있지만 그렇게 하지 않아도 된다
        - 어느 서버가 됐던지.. 나의 PC에 있는 공개키를 추가해서 인증하도록 만듬
        - 키가 없으면 openssl
        - 개인키는 /.ssh/id_rsa
        - 공개키는 /.ssh/.id_rsa.pub
            - mkdir .ssh
            - cd .ssh
            - touch authorized_keys
            - vi authorized_keys
            - cd ..
            - chmod 700 .ssh
            - chmod 600 .ssh/authorized_keys
            - sshd_config 설정 다시 변경
            - 공개/개인키 설정
        - 팀이 동시작업을 위해 접근 권한 차단을 하면 공유가 불가능해짐
            - 그래서 그룹권한과 그룹 폴더가 필요해진다

## **22.07.23 배성민 멘토님 개발자도구 제대로 사용하기**

- Chrome 개발자 도구 제대로 활용하기
    - F12를 누르면 나오게 되는 것
    - AhnLab Safe Transaction이 개발자 도구를 못 열게 만든다(은행에서 사용)
- 개발자도구를 활용하는 이유
    - 디버깅
    - 취약점 점검
    - 웹 접근성 분석
    - 개발 생산성 극대화
    - 자원 모니터링 가능
    - 테스트 환경 제공
- 활용방안
    - More Tools
        - Coverage
            - 사용을 안하고 있는 코드들의 비율을 보여준다(빨간색)
            - 이를 이용해 웹 페이지 최적화 가능
        - Network conditions
            - Disable cache
                - 강세를 자동으로 실행
        - Animation
            - 애니메이션 속도 변경으로 프레임 분석을 위해 사용
        - Network request blocking
            - 원하는 특정 코드를 제외하고 실행 가능
        - Rendering
            - Layer borders
                - 보더를 다 없애준다
            - **Highlight ad frames**
                - **광고제거**
            - Emulate vision deficiencies
                - 장애인들이 어떤 시점으로 보이는지 보여준다
    - Settings
        - Disable JS
            - 복사 막는 것 등을 없앨 수 있다
            - 개꿀 ㅋㅋ

## **22.07.24 박주람 멘토님 개발자 성장 + Mock Interview**

- Eager to be great engineers
    - 멘토님 소개
        - parkjuram@gmail.com → 나중에 Mock인터뷰 모니터링 필요하면 연락
        - 팔라 라는 회사 크립토 회사
        - 초6 정보올림피아드
        - 대학생 acm-icpc 금상
        - 스타트업 → 구글코리아 → 스타트업
    - 동기부여
        
        ![Untitled](%E1%84%89%E1%85%A9%E1%84%86%E1%85%A1%20%E1%84%80%E1%85%B5%E1%84%89%E1%85%AE%E1%86%AF%20%E1%84%86%E1%85%A6%E1%86%AB%E1%84%90%E1%85%A9%E1%84%85%E1%85%B5%E1%86%BC%20%E1%84%8C%E1%85%A5%E1%86%BC%E1%84%85%E1%85%B5%2011d80c0ab1064f8696ecd81c49b8d5fd/Untitled%2035.png)
        
        - 예시
            - Play : 재미
            - Purpose : 유저에게 더 나은 web3 경험을 위해
            - Potential : 커리어를 위해
            - Emotional Pressure : 부모님을 위해?
            - Economic pressure : 높은 연봉을 위해
            - Inertia : Zappos
    - 좋은 엔지니어란?
        - skills
        - communication
        - problem solving
        - logical thinking
        - continous learning
        - what do you think?
    - Mock Interview Session
        - 1번째 질문
            - [https://www.algoexpert.io/questions/river-sizes](https://www.algoexpert.io/questions/river-sizes)
            
            ```cpp
            #include <iostream>
            #include <vector>
            #include <algorithm>
            
            using namespace std;
            
            #define W 1005
            #define H 1005
            
            bool visited[W][H];
            
            int dy[4] = {-1,0,1,0};
            int dx[4] = {0,-1,0,1};
            
            bool Safe(int y, int x){
              if(y > -1 && x > -1 && y < W && x < H){
                return true;
              }
              return false;
            }
            
            int find_flood_fill(const vector<vector<int>> & matrix, int y, int x){
            	int ret = 1;
            	visited[y][x] = true;
            	
            	for(int d = 0; d < 4; d++){
            		int dY = y + dy[d];
            		int dX = x + dx[d];
            
            		if(Safe(dY,dX) && matrix[dY][dX] == 1 && !visited[dY][dX]){
            			ret += find_flood_fill(matrix, dY,dX);
            		}
            	}
            
            	return ret;
            }
            
            vector<int> riverSizes(vector<vector<int>> matrix) {
            	vector<int> result;
            	for(int i = 0; i < matrix.size(); i++){
            		for(int j = 0; j < matrix[i].size(); j++){
            			if(matrix[i][j] == 1 && !visited[i][j]){
            				result.push_back(find_flood_fill(matrix,i,j));
            			}
            	}	 
            }
            
              sort(result.begin(), result.end());
              return result;
            }
            ```
            
        - 2번째 질문
            - [https://www.algoexpert.io/questions/max-profit-with-k-transactions](https://www.algoexpert.io/questions/max-profit-with-k-transactions)
            
            ![Untitled](%E1%84%89%E1%85%A9%E1%84%86%E1%85%A1%20%E1%84%80%E1%85%B5%E1%84%89%E1%85%AE%E1%86%AF%20%E1%84%86%E1%85%A6%E1%86%AB%E1%84%90%E1%85%A9%E1%84%85%E1%85%B5%E1%86%BC%20%E1%84%8C%E1%85%A5%E1%86%BC%E1%84%85%E1%85%B5%2011d80c0ab1064f8696ecd81c49b8d5fd/Untitled%2036.png)
            
            ![Untitled](%E1%84%89%E1%85%A9%E1%84%86%E1%85%A1%20%E1%84%80%E1%85%B5%E1%84%89%E1%85%AE%E1%86%AF%20%E1%84%86%E1%85%A6%E1%86%AB%E1%84%90%E1%85%A9%E1%84%85%E1%85%B5%E1%86%BC%20%E1%84%8C%E1%85%A5%E1%86%BC%E1%84%85%E1%85%B5%2011d80c0ab1064f8696ecd81c49b8d5fd/Untitled%2037.png)
            
        - 풀이과정
            
            ```cpp
            #include <vector>
            #include <algorithm>
            using namespace std;
            
            #define MAX_TRANSACTION 1000
            #define MAX_DAY 100
            
            int dp[MAX_TRANSACTION][MAX_DAY];
            
            int maxProfitWithKTransactions(vector<int> prices, int k) {
              for(int i = 1; i <= k; i++){
                  for(int j = 0; j < prices.size(); j++){
                      dp[i][j] = (j == 0) ? dp[i-1][j] : max(dp[i][j-1], dp[i-1][j]);
                      
                      for(int k = j - 1; k >= 0; k--){
                          if(prices[j] > prices[k]){
                              int compare = (k == 0) ? 0 : dp[i-1][k-1];
                              dp[i][j] = max(dp[i][j], compare + (prices[j] - prices[k]));
                          }
                          else
                              break;
                      }
                  }
              }
              
              return dp[k][prices.size() - 1];
            }
            ```
            
        - 평가표
        
        [https://www.algoexpert.io/mock-interviews/feedback?id=fake_feedback_form](https://www.algoexpert.io/mock-interviews/feedback?id=fake_feedback_form)
        
        ![Untitled](%E1%84%89%E1%85%A9%E1%84%86%E1%85%A1%20%E1%84%80%E1%85%B5%E1%84%89%E1%85%AE%E1%86%AF%20%E1%84%86%E1%85%A6%E1%86%AB%E1%84%90%E1%85%A9%E1%84%85%E1%85%B5%E1%86%BC%20%E1%84%8C%E1%85%A5%E1%86%BC%E1%84%85%E1%85%B5%2011d80c0ab1064f8696ecd81c49b8d5fd/Untitled%2038.png)
        
        - 팁
            - 다 됐다고 하기 전에 테스트코드 돌려보기
            - dy, dx 만드는 거
            - 문제에 대한 질문을 많이 해야한다
            - edge케이스를 찾아내는걸 좋아한다. N,M을 주어지지 않는다
            - 끝났다고 말하는 걸 신중하게 해야한다
                - 오류가 있어도 끝났다고 하면 bac signal
                - 꼭 text case를 좀 돌려본다고 말하고 간단한 에제를 돌려보는 성의
            - 한번에 끝까지 풀지말고 천천히 올라가야한다
                - 테스트 케이스?
            - Step을 분류해서 Mock 인터뷰를 낸다
                1. 시작점에서 끝점까지 도달가능한가?
                2. 그 경로를 나타낼 수 있는가?
                3. weight가 있다면 최단경로는?
            - 로직이 전혀 생각나지 않으면 브루트포스로라도 풀어야한다
                - 안 풀면 no hire
            - input condition checks, edge cases가 있을 수 있는지 여쭤봐야한다
            - 작성하려고 하는 solution을 대략적으로 설명
            - coding 시작 전 좋은 sample cases 넣어보기
            - 한 문제에 너무 많은 시간을 쓰지 말아라
            - 너무 쉽게 기출유형, 다빈도 제출문제들이 있습니다.
            - 테스트코드는 실제로 코드를 짜는게 아닌 손과 텍스트를 이용해서 입증하는 것을 좋게 본다
    
    ![Untitled](%E1%84%89%E1%85%A9%E1%84%86%E1%85%A1%20%E1%84%80%E1%85%B5%E1%84%89%E1%85%AE%E1%86%AF%20%E1%84%86%E1%85%A6%E1%86%AB%E1%84%90%E1%85%A9%E1%84%85%E1%85%B5%E1%86%BC%20%E1%84%8C%E1%85%A5%E1%86%BC%E1%84%85%E1%85%B5%2011d80c0ab1064f8696ecd81c49b8d5fd/Untitled%2039.png)
    
    - 인터뷰 추가팁
        - 회사에 대한 질문?
            - 시간을 많이 할애는 X, 회사 내부 로직?
        - 포트폴리오에 꼭 들어가야 하는 정보와 들어가면 좋은 정보
            - 스타트업에서는 포트폴리오를 좀 보는거 같다
            - 구글 같은 경우에는 포트폴리오를 거의 보지 않고 대부분 인터뷰까지는 붙여준다
            - 
    - **성공하는 스타트업 만들기**
        - PO/PM? Engineering?
        - 둘 다 중요하다.
        - 프로세스
            - 아이디어를 구체화 하기
                - PO가 PRD를 통해서 스펙화하기
            - 구체화 된 아이디어를 만들기
                - Design Review - 삽질 방지를 위해
                    - 집단지성, Clean Architecture
            - ETC
                - 디자인, Figma
                    - PRD를 보고 Figma를 통해 전달
                
            - 측정하기
                - Product Analytics tools
                    - Google Analytics
                    - Mixpanel
                    - Amplitude
                    - Hackle
            - 측정된 데이터로 학습
                - Metrics
                - Funnel
                - A/B Testing
                - Cohort
    - 더 가파르게 성장하는 법
        - 좋은 회사 가기(인구밀도 높은)
        - 피드백 요청하고 받기
            - 1:1 Meetings
        - 회고 하기
            - 혼자서도 회사에서도
            - Planning하기
        - Mentor 찾기
            - software maestro
            - events - aws summit / google io
            - group studies - silicon valley
        - Mentee 찾기
            - 생각보다 좋은 경험
            - 남에게 알려주려면 더욱 공부하게 된다
    - 추천 회사(엔지니어링 헤드가 좋은 회사, 코드리뷰 하는 회사)
        - 오늘의 집
        - 시리즈 D 정도 받은 유니콘 스타트업
        - 모로코
        - 토스

- 정보처리기사 등 관련 자격증 공부하는게 좋을지?
    - 정보처리기사 같은 경우에 관련 기업에서 우대하는 경우
- 블로그 TID
    - 자신이 개발한 리뷰를 하거나 기술에 대한 섬세한 리뷰?
- 현직에서 사용하는 신입에게 원하는 기술
    - 채용 홈페이지를 가면 필요로하는 기술 스택을 알 수 있다
    - 많은 것을 해보면 안 좋게 보는 경향이 있는 것 같다
    - 꼬리 질문을 하다가 안 좋은 인상을 심어주게 된다
    - 하나의 기술을 좁더라도 깊게 팔 수 있는 자세가 좋다
    - 코딩테스트 준비
- 개발을 하면서 새로운 기술을 습득할 때 습득하는 방법
    - 실습을 먼저 해보고 만드는 과정에서 필요한 것을 공부하게 되면은 좋을 듯 하다
- 공부 로드맵
    - 공부할 때 관련된 키워드를 먼저 찾아서 영어로 찾고, 직접했던 프로젝트에 적용해보는 과정을 통해 배워본다
- 소마를 통해 얻게 되는 것
    - 협업이 가장 좋은 경험, 깃에 커밋이 많은 오픈 레퍼지토리가 있다는 것 자체가 좋다
    - 인적 네트워킹을 잘해놓는게 되게 중요하다
- 풀스택 개발자
    - 취업을 할 때는 풀스택이 아니라 하나를 잡는 것이 맞는 것 같다
    - 여러 개를 하다보면 위에서 말했던 것처럼 심화 질문에서 까이게 된다
    - 스타트업같은 경우에는 풀스택이 나을 수도 있다.
    - 프로젝트를 처음부터 끝까지 다 만들어봤는지에 대한 경험을 보기도 한다
- 개발 트랜드 접하는 좋은 경로
    - 카카오톡 오픈 채팅
    - 블로그 아카이브 익스탠션
    - 국내에 있는 해외 기업들에서 사용하는 기술들
- 팀장은 개발, 운영 관리, 외부 협력 여러가지 해야하는데 조언
    - 팀장을 하게되면 PO로써의 역할을 수행할 수 있는 기회가 되므로, 한 번 진행해보면서 알게되는 사람들도 알게되므로 좋은 경험이 될 수 있다
- 비전공 AI 취업
    - 연구
        - 논문을 쓰고 싶으면 석사를 준비해야한다
    - 서비스
        - 모델을 응용할 수 있음을 보여야한다
        - 서비스를 만든 프로젝트가 필요
        - 카카오 아레나 같은 대회를 나가서 증명하는 것도 괜찮다
        - 전공은 잘 보지 않고, 블로그를 잘 쓰는 것도 중요
- 클린코드가 중요한가, 개발의 완성이 중요한가?
    - 목적에 따라 다르다
- 계획적이지 못한 팀원과 프로젝트 수행하는 법
    - 팀원과의 합의점이 필요하다
    - 맞춰가는게 제일 중요
    - 계획 완성을 목표로 하는 2~3일전에 프로세스를 시켜 늦어도 되게 만든다
- 반복 작업에 지쳤다면?
    - 사이드 프로젝트를 진행한다
    - 반복 작업의 목적이 중요하다
        - 공부라면, 안 좋은 방향이므로 변경한다
        - 서비스를 위한 것이라면 진행해야한다
- 좋은 투자를 받기 위한 방법
    - 좋은 커리어(카이스트 석박사 ㅋㅋ, 토스 초기 창업자)
    - 결과를 보여줘야한다
    - 믿음을 줄 수 있어야 한다(엔제?)
- MVP 초반 배포를 어느 사이트에서 누구에게 하였는지
    - 학교 사람들에게 처음 배포하는게 좋다
    - 프로젝트 활용비를 잘 써서 마케팅 하는게 좋은 것 같다
- 서비스 운영 시 전체 비용 대비 클라우드 가격 책정 비율
    - $0 - 대부분의 소마 프로덕트는 프리티어로 커버 가능하다
- 커리어 vs 돈
    - 연봉은 유니콘 스타트업과 대기업이 큰 차이가 나지 않는다
    - 일하는 사람이 괜찮은 곳이 좋은데 유니콘 스타트업이 괜찮은 사람이 더 많다
    - 유니콘 스타트업은 스타트업이 아니고 대기업 급이다
    - 스타트업에서 신입을 뽑는 다는 것은 좋은 스타트업이 아닐 가능성이 높다
    - 대기업 3년, 스타트업 3년이 좋은 인재이다
        - 대기업에서 교육, 스타트업에서 실무
        - 제일 좋은 커리어는 성공한 스타트업에서 있던 멤버

## **22.09.13 강성재 멘토님 이력서 보강**

- 원하는 개발자 상
    - 프로젝트 경험
    - 도구/프레임웍
    - 개발 방법론
    - **어떤 책임으로 참여, 개발자, 개발리더**
    - 프로젝트 외에 어떤 기여
    - 발생한 기술적인 문제 해결 방안
    - 리더쉽, 협업, 커뮤니케이션 스킬
    - 성과
        - 효율적인 기술들을 검토해서 스택을 결정하고 팀에 전파
        - 난이도가 높은 기술을 사용했거나 직접 개발 했던 경험
        - 개발 환경이나 구조를 구축
        - 캐시를 사용(Redis, Kafka)
    - 팀탓 금지, 새로운 도전을 원하는 것을 좋아한다
    - 커리어 개발에 도움이 되는 사이클이 있어야 한다
        - 일단 동작하게 만든다
        - 옳게 만든다
        - 더 나은 것으로 만든다
        - 글 작성
- 이력서 내용
    - 규칙
        - 반드시 사실인 정보만 적는다
        - 목적에 부합하지 않는 내용은 과감히 지운다
        - 줄글보다 불렛, 좋은 레이아웃 사용
        - 분량보다 내용
        - 맞춤법 검사기 등을 활용, 오탈자 수정
        - 링크 동작 확인
        - 별도 첨부파일 첨부 X
        - 파일은 절대 금지, 무조건 pdf
    - 자기소개
        - 나에 대해 잘 설명해주는 한 문장의 제목은 강한 인상을 줄 수 있다
        - 어떤 경력/경험을 가졌고, 관심사(일 적인 관심사) 위주의 짧은 글
        - 어떤 개발자인지 2~5줄로
    - 경험
        - 가장 중요한 부분, 지원하는 포지션이 필요로 하는 역량을 그간의 경험을 통해 갖추었음을 증명하는 곳
    - 좋은 인상을 주는 법
        - 과도하게 긴장하지 않고 차분히 자신의 이야기를 하는 사람
        - 입사 후 하고 싶은 일을 구체적으로 적은 사람
        - 오픈 소스 참여나 소스를 직접 볼 수 있는 개인 프로젝트 url을 기재한 사람
        - 면접관과 서로 커뮤니케이션을 진행하는 사람
        - 이 일이 자신이 좋아하고 하고 싶은 일이라는 걸 잘 어필하는 사람
        - 면접 마지막에 자신의 면접에 대해 묻는 사람
    - 광탈 되는 지원자
        - 모르는 걸 순간의 재치로 끼워 맞추려고 무리하는 경우
        - 지원서에 다양한 경험과 다양한 기술을 너무 많고 다양하게 적어서 기술에 대한 이해도가 의심되는 경우

## **22.10.03 나동빈 멘토님 개발형 코딩 테스트(python) 준비하기**

- 기본
    - 개발형 코딩 테스트는 개발 분야마다 상세 요구사항이 다르다
        - 웹 서버 개발 : 스프링, 장고 등의 웹 서버 프레임워크 활용 능력 요구
        - 클라이언트가 요청하면 서버가 응답하는 구조
        - 클라이언트 : 고객
            - 서버로 요청을 보낸 뒤에, 응답이 도착할 때까지 기다린다.
            - 서버로부터 응답을 받으면, 서버의 응답을 처리하여 화면에 출력
        - HTTP
            - 웹 상에서 데이터를 주고받기 위한 프로토콜
            - 웹 문서를 주고받기 위하여 사용
            - GET/POST/PUT/DELETE
            
            ![Untitled](%E1%84%89%E1%85%A9%E1%84%86%E1%85%A1%20%E1%84%80%E1%85%B5%E1%84%89%E1%85%AE%E1%86%AF%20%E1%84%86%E1%85%A6%E1%86%AB%E1%84%90%E1%85%A9%E1%84%85%E1%85%B5%E1%86%BC%20%E1%84%8C%E1%85%A5%E1%86%BC%E1%84%85%E1%85%B5%2011d80c0ab1064f8696ecd81c49b8d5fd/Untitled%2040.png)
            
            ![Untitled](%E1%84%89%E1%85%A9%E1%84%86%E1%85%A1%20%E1%84%80%E1%85%B5%E1%84%89%E1%85%AE%E1%86%AF%20%E1%84%86%E1%85%A6%E1%86%AB%E1%84%90%E1%85%A9%E1%84%85%E1%85%B5%E1%86%BC%20%E1%84%8C%E1%85%A5%E1%86%BC%E1%84%85%E1%85%B5%2011d80c0ab1064f8696ecd81c49b8d5fd/Untitled%2041.png)
            
    - 데이터 형식(JSON)
        - 서버와 클라이언트가 통신하기 위한 방법엔 XML과 JSON이 있다
        - XML은 HTML과 유사한 마크업 언어로, 사람과 기계가 이해하기 쉬운 구조
        - JSON은 XML이 가지는 다양한 단점을 개선, 최근 웹 서비스에서 많이 사용하고 있다
        - 동일한 데이터를 JSON형식으로 만들면 훨씬 짧고 배열도 사용 가능하나, XML보다 안정성이 낮다
        - XML은 HTML 파싱하기 더욱 쉬워서 안정성이 높다
        - JSON(JavaScript Object Notation) 은 데이터를 주고 받기 위해 사용하느 경량 데이터 형식 중 하나
        - JSON 형식에서는 키와 값의 쌍으로 이루어진 데이터 객체
        
        ![Untitled](%E1%84%89%E1%85%A9%E1%84%86%E1%85%A1%20%E1%84%80%E1%85%B5%E1%84%89%E1%85%AE%E1%86%AF%20%E1%84%86%E1%85%A6%E1%86%AB%E1%84%90%E1%85%A9%E1%84%85%E1%85%B5%E1%86%BC%20%E1%84%8C%E1%85%A5%E1%86%BC%E1%84%85%E1%85%B5%2011d80c0ab1064f8696ecd81c49b8d5fd/Untitled%2042.png)
        
    - REST란
        - HTTP는 다양한 HTTP 메서드를 지원
        - 실제로는 서버가 HTTP 메서드를 기존 설명에 맞게 사용하지 않더라도 프로그램 개발은 가능
        - 하지만 각 서비스가 서로 다른 방식으로 개발 시, 개발자 사이 소통 문제 발생
        - 따라서 기준이 되는 아키텍처로 REST 채택
        - Representational State Transfer의 약자
        - 말 그대로 특정한 자원에 대해, 자원의 상태에 대한 정보를 주고받는 개발 방식
        - REST의 구성 요소는 자원, 행위, 표현이다.
            - 자원 : 회원(user)
            - 행위 : 회원등록
            - 표현 : {아이디 : “gildong”, 비밀번호 : “1234”}
    - REST API
        - Application Programming Interface
        - 프로그램이 상호작용하기 위한 인터페이스
        - REST 아키텍처를 따르는 API
        - REST 방식을 따르고 있는 서버에 특정한 요청을 전송하는 행위
        - API 사용자 ← API → API 제공자
    - mocking
        - 어떠한 기능이 있는 것처럼 흉내내어 구현한 것
        - DB 연동 개발은 마치지 못해도, 기능은 조회 가능한 것
    
    ![Untitled](%E1%84%89%E1%85%A9%E1%84%86%E1%85%A1%20%E1%84%80%E1%85%B5%E1%84%89%E1%85%AE%E1%86%AF%20%E1%84%86%E1%85%A6%E1%86%AB%E1%84%90%E1%85%A9%E1%84%85%E1%85%B5%E1%86%BC%20%E1%84%8C%E1%85%A5%E1%86%BC%E1%84%85%E1%85%B5%2011d80c0ab1064f8696ecd81c49b8d5fd/Untitled%2043.png)
    
    - 기본 코드
    
    ```cpp
    import requests, json
    
    """
    API_HOST = '[API_SERVER]'
    headers = {
        'Access-Token': '[YOUR_ACCESS_TOKEN]',
        'Content-Type': 'application/json'
    }
    """
    API_HOST = 'http://localhost:5000'
    headers = {
        'Access-Token': 'ABCDEFGH12345678',
        'Content-Type': 'application/json'
    }
    
    def request(path, method, data={}):
        url = API_HOST + path
        print(f'Request URL: {url}')
        print(f'HTTP Method: {method}')
        print(f'Headers: {headers}')
    
        if method == 'GET':
            return requests.get(url, headers=headers)
        elif method == 'POST':
            print(f'Sended data: {data}')
            return requests.post(url, headers=headers, data=json.dumps(data))
    
    # auth_key를 발급 받고, 문제 풀이 시작
    response = request('/start', 'GET')
    print(f'Response status: {response.status_code}')
    data = response.json()
    print(f'Response: {data}')
    key = data['auth_key']
    ```
