What is cloud computing?
it is a way of using IT.

1. on-demand self service: No human intervention needed to get resource
2. Broad network access: Access from anywhere
3. Resource pooling: Provider shares resources to customers
4. Rapid elasticity: Get more resources quickly as needed
5. Measured service: Pay only for what you consume

클라우드 컴퓨팅에 대한 3개의 웨이브가 있는데
1. colocation: 물리적으로 구매를 하여 관리 설정 유지보수까지 했다
2. Virtualized: 가상화된 데이터 센터들의 컴포넌트들은 물리적 빌딩 블록이 맞아야했다. 하지만 개발은 더 빨라지고 여러가지 좋은 이점들을 제공했다.그래도 infra를 유지보수를 해야했다. 그것은 아직도 유저가 컨트롤하고 유저가 환경을 설정했다.
3. Serverless: 

* IaaS: Compute Engine - allocate(할당)받은 만큼 돈을 낸다.
* Hybrid: Kubernetes Engine
* PaaS: App Engine - 사용한 만큼 돈을 낸다.
* Serverless logic: Cloud Funtions
* Automated elastic resources: Managed services

Virtualized data center는 Iaas와 PaaS 제공을 가져다줘.
IaaS offerings 는 너의 작은 컴퓨터, 저장공간 그리고 네트워크 너의 물리적인 모든것들을 virtualized data centers와 연결하는것을 제공해 준다.
PaaS offerings는, on the other hand, 너의 애플리케이션이 필요로 하는 infra에 엑세스하는 것을 제공하는 라이브러리와 너의 코드를 연결해준다.
PaaS(Platform as a Service) - 소프트웨어 서비스를 개발할 때 필요한 플랫폼을 제공하는 서비스. 사용자는 PaaS에서 필요한 서비스를 선택해 애플리케이션을 개발하면 된다. PaaS운영 업체는 개발자가 소프트웨어를 개발할 때 필요한 API를 제공해 개발자가 좀 더 편하게 앱을 개발할 수 있게 돕는다.

## Google Cloud Platform resource hierarchy

Resource hierarchy levels define trust boundaries
-> 폴더로 처리하는듯 하다. 프로젝트 폴더 조직노드를 통해.

프로젝트에 종속되는듯 하다.

IAM는 Who, Can do What - IAM role(delete, stop, start ..) -> Primitive - all resource(Owner, Editor, Viewer, Billing administrator)
on which resource -

## Interacting With Google Cloud Platform

1. Cloud Platform Console
2. Cloud Shell and Cloud SDK
3. Cloud Console Mobile App
4. REST-based API