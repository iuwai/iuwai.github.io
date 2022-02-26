---
title: JumpServer添加访问k8s集群
date: 2022-02-22 22:32:44
categories: Jumpserver
tags: ["jumpserver", "k8s"]
---
Jumpserver中添加k8s，方便直接从Jumpserver管理k8s集群
<!-- more -->

### 一、k8s准备
#### 1.创建对象ClusterRole
此对象定义具体权限
```yaml
apiVersion: rbac.authorization.k8s.io/v1
kind: ClusterRole
metadata:
  name: jmsClusterRole
rules:
- apiGroups:
  - '*'
  resources:
  - '*'
  verbs:
  - create
  - delete
  - deletecollection
  - get
  - list
  - patch
  - update
  - watch
```
#### 2.创建对象ServiceAccount
此对象为创建用户，并自动生成Secret对象，即token
```yaml
apiVersion: v1
kind: ServiceAccount
metadata:
  name: jms-serviceaccount
  namespace: kube-system
```
#### 3.创建对象ClusterRoleBinding
此对象将ClusterRole定义的权限赋给ServiceAccount
```yaml
apiVersion: rbac.authorization.k8s.io/v1
kind: ClusterRoleBinding
metadata:
  name: jms-bind-jmsClusterRole
roleRef:
  apiGroup: rbac.authorization.k8s.io
  kind: ClusterRole
  name: jmsClusterRole
subjects:
- kind: ServiceAccount
  name: jms-serviceaccount
  namespace: kube-system
```
#### 4.获取token值
此步骤也可参考Jumpserver官网：https://docs.jumpserver.org/zh/master/admin-guide/app/kubernetes/
```yaml
执行：
export POD_NAME=`kubectl get secret -n kube-system|grep jms|awk '{print $1}'`
kubectl describe secret ${POD_NAME} -n kube-system|grep 'token:'
输出：
token:      it-is-example-token-pZCI6IlB0MUR2dEdtRW5lN19nQWg1WWdWN2o5U1FicTA2dDYtT2VfMk1ScTd0RG8ifQ.eyJpc3MiOiJrdWJlcm5ldGVzL3NlcnZpY2VhY2NvdW50Iiwia3ViZXJuZXRlcy5pby9zZXJ2aWNlYWNjb3VudC9uYW1lc3BhY2UiOiJrdWJlLXN5c3RlbSIsImt1YmVybmV0ZXMuaW8vc2VydmljZWFjY291bnQvc2VjcmV0Lm5hbWUiOiJqbXMtc2VydmljZWFjY291bnQtdG9rZW4tcnB3d2giLCJrdWJlcm5ldGVzLmlvL3NlcnZpY2VhY2NvdW50L3NlcnZpY2UtYWNjb3VudC5uYW1lIjoiam1zLXNlcnZpY2VhY2NvdW50Iiwia3ViZXJuZXRlcy5pby9zZXJ2aWNlYWNjb3VudC9zZXJ2aWNlLWFjY291bnQudWlkIjoiMGZkYjIwOWUtMTgyYi00M2JmLTgyNDctZTAwN2VkOWI1ZTY4Iiwic3ViIjoic3lzdGVtOnNlcnZpY2VhY2NvdW50Omt1YmUtc3lzdGVtOmptcy1zZXJ2aWNlYWNjb3VudCJ9.o8N_m_QtpLOj5OHxvlnepzqypmx8xfE63_GQnzbSfy4deGR1WY0UElxLZcWrajhzHIOP2cBkzVF-X4K6sZoixV1tI0qevHC-gu2v-VZkzv0h-lwbJfMWNV9fiF2alZ9VCVSIXXUBRJ49Wd6ecn8N5_BAIXZ3rLePYxhJA9t_Vlx8l_8stE-AXn9W5IRRy6bzFCpJqR0Mua8BDvv8UJMPT7PPFp3bmBdTNuFOlwpoUNHNtKERGpN19N9Zg05bNaHZwphABOK2mlHDwERAJ3JDFrC1bztUZCYgxXhDQnCG_EJE54SpoMNVvvRgyTw5BJAZYizEmM2_ljytCgdAEznD5g
```
### 二、配置Jumpserver
#### 1.创建K8S协议系统账户，“令牌”输入框填入上文获取到的token
![输入token](/images/jumpserver-input_token.png)
#### 2.应用管理-kubernetes中创建应用
注：“集群”中填写jms能够访问到的集群地址，此处填写TKE提供的内网ip地址
![输入集群地址](/images/jumpserver-input_cluster_addr.png)
#### 3.赋权后即可访问k8s集群信息，测试
```shell
kubectl get nodes
```