## JuiceFS 简介
JuiceFS 是一款面向云原生设计的高性能分布式文件系统，在 Apache 2.0 开源协议下发布。提供完备的 POSIX 兼容性，可将几乎所有对象存储接入本地作为海量本地磁盘使用，亦可同时在跨平台、跨地区的不同主机上挂载读写。  
  
JuiceFS 采用「数据」与「元数据」分离存储的架构，从而实现文件系统的分布式设计。文件数据本身会被切分保存在对象存储（例如 Amazon S3），而元数据则可以保存在 Redis、MySQL、TiKV、SQLite 等多种数据库中，你可以根据场景与性能要求进行选择。  
  
JuiceFS 提供了丰富的 API，适用于各种形式数据的管理、分析、归档、备份，可以在不修改代码的前提下无缝对接大数据、机器学习、人工智能等应用平台，为其提供海量、弹性、低价的高性能存储。运维人员不用再为可用性、灾难恢复、监控、扩容等工作烦恼，专注于业务开发，提升研发效率。同时运维细节的简化，对 DevOps 极其友好。  

# k8s集群部署
|k8s版本|架构|
|:---:|:---:|
|v1.28|arm|

参考链接: https://juicefs.com/docs/zh/csi/getting_started  
1. 检查 kubelet 根目录  
在 Kubernetes 集群中任意一个非 Master 节点上执行以下命令：  
ps -ef | grep kubelet | grep root-dir  
2. 部署（Kubernetes 版本 >= v1.18）  
如果上一步检查命令返回的结果不为空或者 /var/lib/kubelet，则代表该集群 kubelet 定制了根目录（--root-dir），因此需要在 CSI 驱动的部署文件中更新 kubelet 根目录路径：  
sed -i 's@/var/lib/kubelet@{{KUBELET_DIR}}@g' deployment.yaml  
kustomize build . | kubectl apply -f -  
3. 准备secret.yaml配置  
name: 文件系统名称  
metaurl: 用于元数据存储的数据库URL,redis://xxxx:6379/1  
storage: 对象存储类型,华为云填写obs  
bucket: 桶路径  
access-key: <ACCESS_KEY>  
secret-key: <SECRET_KEY>  
 
kubectl apply -f secret.yaml  
说明: secret.yaml中的value不能转成base64加密,需要明文写入value  
  
4. 创建storageclass  
kubectl apply -f storageclass.yaml



