---
layout: post
title: Kubernetes에 Weave Scope 설치하기
comments: true
---

Weave Scope는 Docker 및 Kubernetes의 시각화 및 모니터링 도구다. Weave Scope는 프로세스, 컨테이너 및 호스트의 맵을 생성하므로 애플리케이션을 이해, 모니터링 및 제어 할 수 있다. Weave Scope을 사용하면 단일 UI에서 클러스터 호스트 전체의 컨테이너 수명주기 전체를 ​​제어 할 수 있다. 세부 정보 패널에서 컨테이너를 시작, 중지, 일시 중지 및 다시 시작하고 컨테이너보기에서 중지 된 컨테이너에 대한 필터를 토글 할 수 있다.

​

**[참고][Weave Scope](https://www.weave.works/docs/scope/latest/introducing/)**

~~~
$ kubectl create -f 'https://cloud.weave.works/launch/k8s/weavescope.yaml'
~~~
~~~
$ kubectl get pods -n weave
NAME                                         READY   STATUS    RESTARTS   AGE
weave-scope-agent-khwfx                      1/1     Running   0          3m52s
weave-scope-agent-wqk72                      1/1     Running   0          3m52s
weave-scope-agent-xqnc8                      1/1     Running   0          3m52s
weave-scope-app-6cbf5dbc45-l7zz9             1/1     Running   0          3m52s
weave-scope-cluster-agent-5d7f64677b-vfrxx   1/1     Running   0          3m52s
~~~
~~~
$ pod=$(kubectl get pod -n weave --selector=name=weave-scope-app -o jsonpath={.items..metadata.name})
~~~
~~~
$ kubectl expose pod $pod -n weave --external-ip="172.17.0.71" --port=4040 --target-port=4040 --type=NodePort
~~~
~~~
$ kubectl get services --all-namespaces
...
weave         weave-scope-app                    ClusterIP      10.110.184.207   <none>        80/TCP                                                                         4m45s
weave         weave-scope-app-6cbf5dbc45-l7zz9   NodePort       10.99.108.240    172.17.0.71   4040:31937/TCP                                                                 3m25s
~~~
~~~
$ kubectl describe services -n weave
Name:              weave-scope-app
Namespace:         weave
Labels:            app=weave-scope
                   name=weave-scope-app
                   weave-cloud-component=scope
                   weave-scope-component=app
Annotations:       cloud.weave.works/launcher-info:
                     {
                       "original-request": {
                         "url": "/k8s/v1.8/scope.yaml",
                         "date": "Tue Apr 02 2019 06:23:04 GMT+0000 (UTC)"
                       },
                       "email-address": "support@weave.works"
                     }
Selector:          app=weave-scope,name=weave-scope-app,weave-cloud-component=scope,weave-scope-component=app
Type:              ClusterIP
IP:                10.110.184.207
Port:              app  80/TCP
TargetPort:        4040/TCP
Endpoints:         10.244.1.87:4040
Session Affinity:  None
Events:            <none>


Name:                     weave-scope-app-6cbf5dbc45-l7zz9
Namespace:                weave
Labels:                   app=weave-scope
                          name=weave-scope-app
                          pod-template-hash=6cbf5dbc45
                          weave-cloud-component=scope
                          weave-scope-component=app
Annotations:              <none>
Selector:                 app=weave-scope,name=weave-scope-app,pod-template-hash=6cbf5dbc45,weave-cloud-component=scope,weave-scope-component=app
Type:                     NodePort
IP:                       10.99.108.240
External IPs:             172.17.0.71
Port:                     <unset>  4040/TCP
TargetPort:               4040/TCP
NodePort:                 <unset>  31937/TCP
Endpoints:                10.244.1.87:4040
Session Affinity:         None
External Traffic Policy:  Cluster
Events:                   <none>
~~~
 ​

웹 브라우저를 실행하여 접속해보자. NodePort 31937

http://weave-scope:31937

​

![Weave Scope](/img/weavescope.png)
![Weave Scope](/img/weavescope1.png)
![Weave Scope](/img/weavescope2.png)
![Weave Scope](/img/weavescope3.png)



