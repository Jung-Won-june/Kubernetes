# Kubernetes

# Kubernetes

쿠버네티스 이름짓기 어플리케이션 배포에 대해 연구하다가 나온 정보 정리용

문제 상황: kubectl get pods에 대해 

No resources found in computer-system-team-21 namespace.

이렇게 뜸. 이것은 권한 문제까지는 아니고, 일반적인 애플리케이션을 쿠버네티스 클러스터에 배포하는 과정을 봐야 배포자가 파드를 만들어야 하는지 알 수 있을 것 같다.

파드(POD)란? 애플리케이션을 배포하는 쿠버네티스의 단위. 하나 이상의 컨테이너의 그룹으로, 해당 컨테이너를 구동하는 방식에 대한 명세를 갖는다. (대부분은 단일 컨테이너로 이루어짐. 이번 배포 케이스가 그런 것 같음.)

일반적으로 파드는 직접 생성하지 않고, 대신 워크로드 리소스를 사용하여 생성한다.

파드는 일회용 엔티티로 설계된다. 파드는 프로세스 개념이 아니라 컨테이너를 실행하기 위한 환경으로, 삭제될 때까지 유지된다고 한다. 

파드 배포 실습

kubectl run nginx —image=nginx

명령어를 사용하면 nginx파드가 생성된다.

kubectl get pod

명령어로 파드 생성이 되었는지 확인한다. 

kubectl get pod -o wide

이 명령어로 파드의 ip를 확인할 수 있다.

curl <ip>

홈페이지에서 처음 나오는 메시지를 확인 가능.

아직 파드가 외부에서 접속되지는 않음.

이유? 터미널에서 ping, curl로 접근을 해보면 알음. 응답이 없기 때문에 안됨.

쿠버네티스 클러스터가 외부에서 접근이 가능하려면 문을 통과해야 한다. 

그러기 위해 서비스에서 노드 포트에 접속해 파드가 위치한 곳에 접근해야 한다. 그리고 각 노드 포드(?)들이 통신을 하면서 파드들이 위치한 곳을 찾아가는 구조이다. 파드가 직접 연결되는 것은 아니다.

kubectl expose pod nginx —type=NodePort —port=80

이 명령어로 서비스를 배포한다.

kubectl get service

로 확인하면 NodePort가 밖으로 외부로 어떤 포트가 노출되어 있어야 하는지 알 수 있다.

kubectl get nodes -o wide

명령어로 가상환경에서 돌고 있는 노드들의 ip정보를 가져와 <ip>:port 로 접속해 봤을 때 화면이 보이면 성공.

파드가 여러개 있을 수 있도록 준비한 단위인 디플로이먼트 개념이 나옴.

워커 노드의 파드를 디플로이먼트라는 단위로 묶음. 이 디플로이먼트를 배포하는 방법도 존재한다.

kubectl run은 파드 배포 가능, 디플로이먼트 배포 불가능

kubectl create은 파드와 디플로이먼트 배포가 가능하다.

kubectl apply는 파드와 디플로이먼트 배포가 가능하지만, 파일(?)이 필요하다.

그럼 그냥

kubectl create deployment deploy-nginx —image=nginx

이 명령어로 디플로이먼트를 배포할 수 있다.

kubectl get pods

명령어로 디플로이먼트가 생성되었나 확인. 

kubectl get pods -o wide

curl <ip>

똑같이 실행해본다.

(출처) [https://velog.io/@daehoon12/kubernetes-배포를-통한-쿠버네티스-체험](https://velog.io/@daehoon12/kubernetes-%EB%B0%B0%ED%8F%AC%EB%A5%BC-%ED%86%B5%ED%95%9C-%EC%BF%A0%EB%B2%84%EB%84%A4%ED%8B%B0%EC%8A%A4-%EC%B2%B4%ED%97%98)

결국 백앤드 도커, 프론트엔드 도커를 image파일에 넣어 apply하면 파드가 생성되어 배포되는 것. 

백앤드, llm까지 도커 받으면 따라해보고 해보자!

---

image파일에 넣고 apply까지 했는데 파드가 생성되지도 않고, 할당된 네임스페이스가 서비스 실행 불가하다는 문장이 떴음. 따라서 sudo로도 명령을 실행해보기도 했지만 정확히 어떤 과정을 거쳐서 다른 결과가 나왔는지는 모르겠음.

디렉션이 localhost로 되어서 config파일의 설정이 잘못되었나 했지만, 결과적으로 root계정으로 실행했을 때 run상태가 되었음. 하지만 아직 service생성 불가하다는 오류는 유지.

이후에 외부 접근 url로 했을 때 DNS서버가 nslookup했을 때 찾지를 못함. ping도 그렇고 curl도 통하지 않음.

의문점 메일로 제기
