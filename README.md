# SOMA-MENTORING
 소마 멘토링 정리
 
https://mumomu.notion.site/11d80c0ab1064f8696ecd81c49b8d5fd

# 소마 기술 멘토링 정리

- **22.05.xx 변재영 멘토님 Docker 강의 1~2회차**
    
    도커는 서버 자체를 가상화하는게 아니라 필요한 프로세스만 가상화한다.
    runtime 환경만 컨테이너로 구상화하여 만들기 때문에 빠르다
    윈도우 맥은 커널이 없으므로 도커 데스크탑으로 사용한다.
    chroot : 루트를 바꾸어 파일시스템을 격리해준다
    cgroup : 컨테이너별로 자원에 대한 제어를 해주는 것(대표 7개 그룹)
    namespace : chroot와 다르게 네임스페이스는 6가지지원
    mount, pid, network, ipc, hostname, user(uid)
    젠킨스로 타겟 서버 카피하는게 아닌 도커 이미지를 내려받아
    
    도커 사용 예
    
    1. OS container 한 PC 내 같은 OS 여러개, 한 PC내 여러 OS
    image위에 컨테이너를 올리는것 (base image는 jdk처럼 기반)
    image는 수정 못하므로 컨테이너를 올리면 image를 reference한다
    (references parent image)
    
    절대 http로 호출 ㄴㄴ, https로 호출해야한다. (일반 http쓰면 큰일 난다)
    네트워크 설계도도 필요하다.
    
    registry가 겹쳐있다 -> 도커 이미지 관리(Nexus 3 등) -> 이중화 이상 해야한다(장애 시 고장나므로)
    client(api,cli) - server/hjost(containers,images,docker demon) - registry(hub, private)
    
    microservice vs monoliths
    monoliths 모든게 하나에 다 있는 것, 상황에 따라 전체 시스템을 확장
    microservice : micro service architecture 마이크로 서비스마다 db가 따로 있기 때문에
    이것이 어렵다. 무조건 마이크로 서비스가 좋진 않다. 회사의 비즈니스 환경이 변하고
    있는데 빠르게 변할 수 있는 비즈니스에 빠르게 대응할 수 있다.
    
    busybox
    docker container :
    컨테이너 런타임에서 필요한 모둔걸 포함시켜야한다. (그것이 이미지로 있다.)
    일반적으로이미지는 컨테이너의 파일 시스템을 구성하기 위해 계층으로 서로 포개진
    여러 개의 이미지에서 파생 (ReadOnly)
    최상위에 rw 가능한 컨테이너 생성
    
    UnionFileSystem :
    
    - 여러개의 결합된 형태의 이미지
    - 도커를 서버에 설치하면 현재 호스트에서 사용하는 여러 파일시스템에 다
    지원을 한다. Overlay2가 가장 성능이 좋다.
    지원하는 backing file system을 구성하여야한다. -> xfs, ext4 -> xfs가 가장빠르다(네이버 클라우드)
    
    nginx 로그파일
    
    (중요!)컨테이너 구동 시 기존 우분투 파일
    copy on write
    기존 이미지 레이어에 있는 데이터를 지우거나 변경하면 기존 레이어에 덮어쓴다.
    뭔가 변경하거나 삭제하면 writable layer로 먼저 copy후 이를 변경하는 기법
    RO영역의 data는 볼 수 없고, wirtabgle alyer에 저장된 내용을 보여준다.
    
    단점 : data를 먼저 copy후 변경하므로 성능 하락
    하위 layer에 있는 원본 data를 유지하고, 상위 layer에 변경된 data도 유지해야 하므로
    disk space를 많이 사용한다.
    따라서, base image의 변경은 지양하고, 변경사항은 volume을 통해 관리
    
    from : base image 지정
    run : 컨테이너 안에서 실행
    workdir : 작업경로 변경
    copy:  파일명 해당경로 . 현재경로
    항상 왼쪽이 호스트(이 파일이 있는경로) 오른쪽이 컨테이너
    expose 8080
    cmd  ["npm","start"]
    
    이미지는 하위 레이어만 바뀌면 상위 레이어는 바뀌지 않으므로
    그렇기에 도커를 사용한다
    update해야하는 레이어를 최하위로 뺀다.
    그러면 위에는 동일하므로 좋다.
    변경된 부분만 도커에서 내려받는다. 이것이 도커의 핵심이다.
    그래서 도커파일을 만들 때 잘 만들어야한다. 상위에 있는 부분만 바뀌면
    야무져진다.
    
    오픈소스는 업데이트가 빠르므로 한글 블로그를 보면 안된다.
    
    서비스 운영시 루트 사용 x
    
    Use Docker as a non-privileged user, or install in rootless mode?
    
    The installation script requires root or sudo privileges to install and use Docker. If you want to grant non-root users access to Docker, refer to the post-installation steps for Linux. Docker can also be installed without root privileges, or configured to run in rootless mode. For instructions on running Docker in rootless mode, refer to run the Docker daemon as a non-root user (rootless mode).
    
    도커의 취약점 : docker의 모든 파일을 관리하는 디렉토리가 root에서 관리
    root만 접근 가능함, 도커를 쓰려면 일부 루트를 허용해 주어야 한다.
    특정 환경부터 루트리스 모드를 지원한다. (필수!)
    
    오프라인 설치를 꼭 해보아야한다. (그냥 파일을 서비스로 등록해놓고 오프라인으로 설치할 수 있어야한다.)
    ( intstall binaries)
    
    docker정도만 쓰려면 docker compose를 써야한다.
    docker info에 있는건 기록해둘 필요가 있다.
    
    여러서버 운영시 커널버전이 동일해야한다
    
    [https://docs.docker.com/config/daemon/](https://docs.docker.com/config/daemon/)
    docker default 설정 변경
    
    cd /etc/docker
    touch daemon.json
    
    dockerfile 이미지 생성을 위한 핵심 요소 파일
    
    - 인스트럭트(대소문자 구분x, 그러나 가독성을 위해 대문자만 사용)
    - 구성 순서가 중요
    
    FROM os지정
    LABEL 이미지 설명
    RUN 이미지 만들 때 사용할 쉘 명령어
    ENV 어플리케이션별로 설정을 해야하는데 환경변수로 제어하는데 세팅할 때 사용(키, 벨류)
    도커 컨테이너 런타임 시점에 사용
    ARG version=latest
    도커파일을 빌드하는 시점에 사용
    WORKDIR 작업경로지정(도커이미지에대한)
    COPY 호스트에 있는 파일을 도커 이미지로 복사하는 것
    ADD
    RUN
    USER
    EXPOSE
    CMD 이미지 빌드된 이후 컨테이너 구동 시 실행할 명령어
    
    주석 #
    FROM 베이스 이미지 지정
    
    둘다 실행 가능하다.
    
    - RUN 커맨드명령
    - 대괄호를 통해 실행할 명령어와 파라미터 RUN["executable","param1","param2"]
    
    ADD 로컬, 네트워크에서 파일복사, 대상 경로에서 압축 해제
    COPY 로컬 파일 복사만 지원, 압축 해제 없이 복사
    
    CMD
    docker run 명령 시 적용할 command 나열
    cmd 하나만 적용가능, 여러개 입력시 마지막 것
    컨테이너 동작 시 다른 명령으로 전환가능
    ENTRY POINT(이걸 위해서 만들어졌다면)
    cmd와 동일한 목적이나 docker run에서 전환불가
    
    docker build test -t test/wc .
    docker run test/wc
    
    ### 대쉬차이
    
    유닉스 시스템에서 단일옵션을 많이 사용했는데 옵션이 많아져서 구분이 안되어
    사람이 이해할 수 있도록 옵션을 추가하자 그것이 대쉬 두 개(이해할 수 있는 단어로만
    구성한다 대쉬 두개는)
    
    EXPOSE 80 443/tcp
    진짜 도커를 쓰는데 모르는사람 많다
    얘는 포트를 열어주지 않는다.
    몇 번 포트를 사용해야되는지 명시해주는 것
    커뮤니케이션을 위해 사용하는 것
    인작 두개면 포트가 두개인 것
    실제 container run시 -p 옵션을 사용하여야 한다.
    
    USER
    user를 바꿈
    컨테이너를 유저 변경없이 실행 시 루트로 실행된다
    이 유저는 host와 매핑이 된다.
    우리의 도커파일은 이 유저에서 접근할 수 있다.
    
    오늘 한 것은 격리는 되어있지않다.
    격리 세팅을 다 해줘야하는가? 그렇진 않다 cgroup namespace를 보면
    프로세스 동작 공간이랑 통신채널이 독립적으로 생긴다. 일단 리소스에 리밋을 두지않는다
    프로세스끼리 직접 소통은 최소화되어있다.
    
    build run
    
- **22.05.13 김준범 멘토님 DB 모델링**
    
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
- **22.05.15 남상수 멘토님 애자일, 스크럼, 칸반**
    
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
        
        ![Untitled](%E1%84%89%E1%85%A9%E1%84%86%E1%85%A1%20%E1%84%80%E1%85%B5%E1%84%89%E1%85%AE%E1%86%AF%20%E1%84%86%E1%85%A6%E1%86%AB%E1%84%90%E1%85%A9%E1%84%85%E1%85%B5%E1%86%BC%20%E1%84%8C%E1%85%A5%E1%86%BC%E1%84%85%E1%85%B5%208f6abed3a4bb4718bf287f6b877c303b/Untitled.png)
        
        - 빠른 피드백 주기
        
        ![Untitled](%E1%84%89%E1%85%A9%E1%84%86%E1%85%A1%20%E1%84%80%E1%85%B5%E1%84%89%E1%85%AE%E1%86%AF%20%E1%84%86%E1%85%A6%E1%86%AB%E1%84%90%E1%85%A9%E1%84%85%E1%85%B5%E1%86%BC%20%E1%84%8C%E1%85%A5%E1%86%BC%E1%84%85%E1%85%B5%208f6abed3a4bb4718bf287f6b877c303b/Untitled%201.png)
        
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
        
        ![Untitled](%E1%84%89%E1%85%A9%E1%84%86%E1%85%A1%20%E1%84%80%E1%85%B5%E1%84%89%E1%85%AE%E1%86%AF%20%E1%84%86%E1%85%A6%E1%86%AB%E1%84%90%E1%85%A9%E1%84%85%E1%85%B5%E1%86%BC%20%E1%84%8C%E1%85%A5%E1%86%BC%E1%84%85%E1%85%B5%208f6abed3a4bb4718bf287f6b877c303b/Untitled%202.png)
        
        - 역할
            - 제품 책임자 : 제품 전략 및 로드맵 수립, 백로그 설명 및 우선순위 관리, 결정 검증
            - 스크럼 마스터 : 팀의 작업 환경 확인 및 장애 요소 해결, 일일 스크럼 회의, 의사결정 도움
        - 하나의 기능
        
        ![Untitled](%E1%84%89%E1%85%A9%E1%84%86%E1%85%A1%20%E1%84%80%E1%85%B5%E1%84%89%E1%85%AE%E1%86%AF%20%E1%84%86%E1%85%A6%E1%86%AB%E1%84%90%E1%85%A9%E1%84%85%E1%85%B5%E1%86%BC%20%E1%84%8C%E1%85%A5%E1%86%BC%E1%84%85%E1%85%B5%208f6abed3a4bb4718bf287f6b877c303b/Untitled%203.png)
        
    - 칸반(how to 방법론)
        - 스크럼보다 훨씬 요구하는 사항이 적다
        - 스프린트가 없고 작업 흐름의 시각화만 한다.
        - 여기 있는 일들이 완료되면 새로운 일을 추가하면 된다.
        - 프로젝트가 종료될 때까지 있는 보드
- **22.05.18 강상진 멘토님 AWS 클라우드 컴퓨팅(초급)**
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
        
        ![Untitled](%E1%84%89%E1%85%A9%E1%84%86%E1%85%A1%20%E1%84%80%E1%85%B5%E1%84%89%E1%85%AE%E1%86%AF%20%E1%84%86%E1%85%A6%E1%86%AB%E1%84%90%E1%85%A9%E1%84%85%E1%85%B5%E1%86%BC%20%E1%84%8C%E1%85%A5%E1%86%BC%E1%84%85%E1%85%B5%208f6abed3a4bb4718bf287f6b877c303b/Untitled%204.png)
        
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
- **22.05.19 변재영 멘토님 Docker 강의 3회차**
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
        
- **22.05.21 강상진 멘토님 모던 웹 엔지니어링**
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
        
        ![Untitled](%E1%84%89%E1%85%A9%E1%84%86%E1%85%A1%20%E1%84%80%E1%85%B5%E1%84%89%E1%85%AE%E1%86%AF%20%E1%84%86%E1%85%A6%E1%86%AB%E1%84%90%E1%85%A9%E1%84%85%E1%85%B5%E1%86%BC%20%E1%84%8C%E1%85%A5%E1%86%BC%E1%84%85%E1%85%B5%208f6abed3a4bb4718bf287f6b877c303b/Untitled%205.png)
        
        - 300초동안 캐시, 부하 나누는 ip없음
        - 왜 고가용성 아키텍처를 사용하지 않았을까?
            - 사용자가 많지 않아서
    - kakao
        - 메인 홈페이지가 분할을 안 하고 있다 이유가 뭘까?
            - 면접에서 이런걸 물어보면 장점이 될 수 있다.
    - curl -v “www.swmaestro.org”
        - http로 접속시 302 not found가 뜬다
        
        ![Untitled](%E1%84%89%E1%85%A9%E1%84%86%E1%85%A1%20%E1%84%80%E1%85%B5%E1%84%89%E1%85%AE%E1%86%AF%20%E1%84%86%E1%85%A6%E1%86%AB%E1%84%90%E1%85%A9%E1%84%85%E1%85%B5%E1%86%BC%20%E1%84%8C%E1%85%A5%E1%86%BC%E1%84%85%E1%85%B5%208f6abed3a4bb4718bf287f6b877c303b/Untitled%206.png)
        
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
        
        ![Untitled](%E1%84%89%E1%85%A9%E1%84%86%E1%85%A1%20%E1%84%80%E1%85%B5%E1%84%89%E1%85%AE%E1%86%AF%20%E1%84%86%E1%85%A6%E1%86%AB%E1%84%90%E1%85%A9%E1%84%85%E1%85%B5%E1%86%BC%20%E1%84%8C%E1%85%A5%E1%86%BC%E1%84%85%E1%85%B5%208f6abed3a4bb4718bf287f6b877c303b/Untitled%207.png)
        
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
        
        ![Untitled](%E1%84%89%E1%85%A9%E1%84%86%E1%85%A1%20%E1%84%80%E1%85%B5%E1%84%89%E1%85%AE%E1%86%AF%20%E1%84%86%E1%85%A6%E1%86%AB%E1%84%90%E1%85%A9%E1%84%85%E1%85%B5%E1%86%BC%20%E1%84%8C%E1%85%A5%E1%86%BC%E1%84%85%E1%85%B5%208f6abed3a4bb4718bf287f6b877c303b/Untitled%208.png)
        
        - 웹페이지 로딩이 오래 걸리면 어떻게 할 건가? → 프론트의 시간을 줄여야한다. → 이 다음이 백엔드(쿼리튜닝)같은걸 진행하는 것이다. →특히 이미지와 같은 것이 많을 수록 최적화가 더 필요하다. (11번가)
        - **사이트 객관적 평가 파이트 - webpagetest.org(외국을 가지 않아도 외국에서의 접속을 테스트할 수 있다.)**
            - cache control
            
            ![Untitled](%E1%84%89%E1%85%A9%E1%84%86%E1%85%A1%20%E1%84%80%E1%85%B5%E1%84%89%E1%85%AE%E1%86%AF%20%E1%84%86%E1%85%A6%E1%86%AB%E1%84%90%E1%85%A9%E1%84%85%E1%85%B5%E1%86%BC%20%E1%84%8C%E1%85%A5%E1%86%BC%E1%84%85%E1%85%B5%208f6abed3a4bb4718bf287f6b877c303b/Untitled%209.png)
            
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
    
- **22.05.21 박주형 멘토님 AWS**
    
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
- **22.05.21 이광헌 멘토님 Jira 애자일 스크럼 개발**
    
    
    - Start Jira
        - Scrum과 Kanban은 애자일 프레임워크
    - Keep → Problem → Try
    - 대부분 실습으로 진행(Jira)
    - 회의와 회고는 jira에서 template를 사용해서 진행하는게 좋을 듯
    
- **22.05.27 김용웅 멘토님 아키텍처**
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
        
        ![Untitled](%E1%84%89%E1%85%A9%E1%84%86%E1%85%A1%20%E1%84%80%E1%85%B5%E1%84%89%E1%85%AE%E1%86%AF%20%E1%84%86%E1%85%A6%E1%86%AB%E1%84%90%E1%85%A9%E1%84%85%E1%85%B5%E1%86%BC%20%E1%84%8C%E1%85%A5%E1%86%BC%E1%84%85%E1%85%B5%208f6abed3a4bb4718bf287f6b877c303b/Untitled%2010.png)
        
        - 유레카, 관리까지 해야 MSA라고 할 수 있다.
    - 서비스별 참조 아키텍처(참고하면서 개발)
        - AWS Reference Architecture Diagrams(필요한 솔루션 제공)
        - NCP 레퍼런스 아키텍처(맨 밑에 소개 두번째)
    - VM vs Container
    
- **22.05.28 이건재 멘토님 스프링 부트 기초**
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
            
            ![Untitled](%E1%84%89%E1%85%A9%E1%84%86%E1%85%A1%20%E1%84%80%E1%85%B5%E1%84%89%E1%85%AE%E1%86%AF%20%E1%84%86%E1%85%A6%E1%86%AB%E1%84%90%E1%85%A9%E1%84%85%E1%85%B5%E1%86%BC%20%E1%84%8C%E1%85%A5%E1%86%BC%E1%84%85%E1%85%B5%208f6abed3a4bb4718bf287f6b877c303b/Untitled%2011.png)
            
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
- **22.05.28 김용웅 멘토님 스프링 CoC 강의**
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
        
- **22.05.29 배성민 멘토님 웹 표준, 웹 접근성**
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
            
            ![Untitled](%E1%84%89%E1%85%A9%E1%84%86%E1%85%A1%20%E1%84%80%E1%85%B5%E1%84%89%E1%85%AE%E1%86%AF%20%E1%84%86%E1%85%A6%E1%86%AB%E1%84%90%E1%85%A9%E1%84%85%E1%85%B5%E1%86%BC%20%E1%84%8C%E1%85%A5%E1%86%BC%E1%84%85%E1%85%B5%208f6abed3a4bb4718bf287f6b877c303b/Untitled%2012.png)
            
- **22.06.01 박정규 멘토님 DB 모델링**
    - 다대다의 관계는 반드시 쪼개야한다.
    - 부채꼴 함정
        - A,B,C의 엔티티가 N:1, 1:M 관계가 형성되어 있을 때 발생할 수 있음
    - 균열 함정
        - 필수 관계가 아닌 선택 관계가 존재할 때, 정보의 흐름이 끊겨서 원하는 정보를 찾을 수 없는 현상
    - **FK를 자신의 PK로 두면 그 관계는 식별관계(실선), 그렇지 않고 내부 필드로 보유하면 비식별관계(점선)**
    - 주 식별자의 특징
        - 유일성 : 주식별자에 의해 엔티티내에 모든 인스턴스들을 유일하게 구별
        - 최소성 : 주식별자를 구성하는 속성의 수는 유일성을 만족하는 최소의 수가 되어야 함
        - 불변성 : 주식별자가 한 번 특정 엔티티에 지정되면 그 식별자의 값은 변하지 않아야 함
        - 존재성 : 주식별자가 지정되면 반드시 데이터 값이 존재(NOT NULL)
    - 무결성
        - 영역 무결정 : 속성 값은 원자성을 가지며, 해당 도메인에서 정의된 값(타입)
        - 키 무결성 : 테이블의 모든 레코드는 서로 식별 가능
        - 엔티티 무결성 : 기본키는 반드시 유일값, not null
        - 참조 무결성 : 외래키는 null값이거나, 외래키가 참조하는 테이블의 기본키에 존재하는 값이어야 함
        - 사용자 정의 무결성 : 다른 무결성 범주에 속하지 않는 특정 업무 규칙
    - 대부분 관계는 1:N이고, N쪽에 Foregin Key가 생기면 된다.
    - 복합키는 식별관계였으면 다음 관계에서도 식별관계로?
    - erdcloud.com
    - 사업부 - 부문 - 팀 - 등 이런 구조일 경우 모두 테이블로 만드는 것이 아니라
        
        상위 부서 컬럼을 두고 자기 참조 형태(재귀)로 구성한다.
        
    - 이력데이터 모델링
        - 시점이력
            - 발생이력이라고도 하며, 특정한 시점의 데이터에 대해 기록해두는 것(바뀔 때마다 insert 해두는 것)
            - 발생일시를 pk에 포함, 최종여부를 컬럼으로 조회
        - 선분이력
            - 특정 기간에 대한 데이터
            - pk에 시작일자를 포함, 종료일자를 컬럼으로 조회
    - 정규화란
        - 데이터 정확성을 위하여 (+이상현상제거) 시행하는 절차
        - 이상현상
            - 삽입 이상
            - 삭제 이상
                - 유일한 물리학과 학생을 학생테이블에서 삭제하였더니 물리학과의 존재 자체가 사라짐
            - 수정 이상
        - 정규화 과정
            - 1NF
                - 완전함수 종속성 제거
                - X’가 X이고, X’→Y를 만족하는 애트리뷰트 X는 존재하지 않음
                - 한 컬럼에 두 개 이상의 데이터가 들어가면 안됨
            - 2NF
                - 부분함수 종속성 제거
                - X’가 X이고, X’→Y를 만족하는 애트리뷰트 X는 존재함
                - 학번, 과목코드가 복합키인데, 학번으로만 이름이 구분지어지고, 과목코드는 과목명 만을 구분지으므로, 테이블 구분
            - 3NF
                - 이행함수적 종속성 제거
                - A→X이고, X→Y라면 A→Y이다
                - 학과만 알아도 사무실이 정해진다 → 학번,학과 / 학과, 사무실 로 쪼갠다
            - BCNF
                - 함수적 종속이 되는 결정자가 후보키가 아닌 경우
                - 즉 X → Y에서 X가 후보키가 아님
                - 일반 속성이 Pk를 종속시키는 관계
                - 교수랑 과목명에 의해서 교재명이 결정이 되었는데, 교재명에 의해서도 과목명이 결정이 되어버리는 관계
                - 교수, 교재명 / 교재명, 과목명
            - 4NF
    - 반정규화란
        - 정규화된 데이터를 병합/분할하여 정규화를 조금 포기하더라도, 성능/속도 향상을 위해 정규화 규칙을 깬 테이블을 생성하는 것
        - 수평분할 : 똑같은 테이블을 복제하는 것
        - 테이블 추가
        - 중복칼럼 추가 : join을 여러번 해버리면 속도가 느려지니, 같은 컬럼을 가지고 있는 것 → 데이터 정확성, 무결성 고려하여야 한다
        - 파생컬럼 추가
        - 이력 테이블 컬럼 추가
        - PK에 의한 컬럼 추가
        - 응용시스템 오동작을 위한 컬럼 추가
        - 중복 관계 추가
    - Index
        - 인덱스 하나만 설정하면 1,000~10,000배 빨라진다
        - B+ tree
