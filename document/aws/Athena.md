## AWS Athena

표준 SQL을 사용해 S3에 저장된 데이터를 간편하게 분석할 수 있는 대화식 쿼리 서비스.
athena는 **서버리스** 서비스이므로 관리할 인프라가 없으며 실행한 쿼리에 대해서만 비용을 지불하면 됩니다.
S3에 저장된 데이터를 가리키고 스키마를 정의한 후 표준 SQL을 사용하여 쿼리를 시작하면 된다.
Athena에서는 데이터 분석을 준비하기 위한 복잡한 ETL 작업이 필요 없습니다.
SQL은 ANSI SQL을 지원하는 Presto 를 사용.

**SQL 표준에서는 WHERE and HAVING은 output columns에 access 할 수 없고 단지 ORDER BY에만 가능하다. → Presto도 동일함.**

Athena에서 테이블과 데이터베이스는 기본 소스 데이터에 대한 스키마를 정의하는 메타데이터 정의를 위한 컨테이너이다.
각 데이터 집합에 대한 테이블이 Athena에 있어야 한다. 테이블의 메타데이터는 Amazon S3의 데이터 위치를 Athena에 알려주고, 데이터의 구조를 지정한다.
데이터베이스는 테이블의 논리적 그룹이며 데이터 세트에 대한 메타데이터와 스키마 정보만 보유한다.

테이블 생성 방법에 관계없이 테이블 생성 프로세스는 Athena를 통해 데이터 세트를 등록합니다.
AWS Glue Data Catalog에서 이 등록이 이루어지며, 등록하면 Athena가 데이터에 대해 쿼리를 실행할 수 있게 됩니다.

쿼리 결과에서 테이블 생성(CTAS(Create Table As Select))

CTAS쿼리는 다른 쿼리의 SELECT 문 결과로부터 Athena의 새 테이블을 만듭니다.
Athena는 CTAS문에서 생성한 데이터 파일을 S3의 지정된 위치에 저장합니다.

Create Table As
Select id
From sample_table

