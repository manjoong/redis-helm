# redis-cluster


### 1. value값 정리
redis-cluster helm 패키지엔 6가지 입력값(value)가 있습니다.

- **replicaCount** : master 혹은 slave가 될 pod들의 수를 정합니다. (default: 6)
- **fullnameOverride** : 각 요소의 이름을 덮어씁니다. (default: redis-cluster)
- **redisService - type**  : service의 type을 정합니다. (default: NodePort)
- **redisService - port** : service의 port를 정합니다. (default: 6379)
- **exporterService - port** : export service의 prot를 정합니다. (default: 16379)
- **storageClassName - name** : strageclass의 이름을 명시합니다. (default: nfs-client)



### 2. 주의사항
- 꼭 strageclass(dvp)를 만든 후, redis-cluster helm구성을 해야 합니다.
- 6379와 16379 port 사용여부 확인을 해야합니다.
- pv로 활용될 볼륨의 양을 계산하여 resource를 입력 해야 합니다.






### 3. value.yaml 확인
```yaml
# Default values for redis-cluster.
# This is a YAML-formatted file.
# Declare variables to be passed into your templates.

replicaCount: 6

fullnameOverride: "redis-cluster"

redisService:
  type: NodePort
  port: 6379

exporterService:
  port: 16379

storageClassName:
  name: "nfs-client"

resources: {}
  # We usually recommend not to specify default resources and to leave this as a conscious
  # choice for the user. This also increases chances charts run on environments with little
  # resources, such as Minikube. If you do want to specify resources, uncomment the following
  # lines, adjust them as necessary, and remove the curly braces after 'resources:'.
  # limits:
  #  cpu: 500m
  #  memory: 1Gi
  # requests:
  #  cpu: 500m
  #  memory: 1Gi
```
