## AWS Athena

표준 SQL을 사용해 S3에 저장된 데이터를 간편하게 분석할 수 있는 대화식 쿼리 서비스.
athena는 서버리스 서비스이므로 관리할 인프라가 없으며 실행한 쿼리에 대해서만 비용을 지불하면 됩니다.
S3에 저장된 데이터를 가리키고 스키마를 정의한 후 표준 SQL을 사용하여 쿼리를 시작하면 된다.
Athena에서는 데이터 분석을 준비하기 위한 복잡한 ETL 작업이 필요 없습니다.
SQL은 ANSI SQL을 지원하는 Presto 를 사용.

**SQL 표준에서는 WHERE and HAVING은 output columns에 access 할 수 없고 단지 ORDER BY에만 가능하다. → Presto도 동일함.**