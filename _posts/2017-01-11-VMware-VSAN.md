---
title: VMware VSAN
layout: post
category: [tech]
tags: [VMware, VSAN]
---
{% include JB/setup %}
VMware的VSAN,是一种超融合技术. 它在VMware原来的vSphere基础上,集成了存储功能. 可以在降低TCO的同时,增加灵活性,降低维护复杂度.  

VSAN是集成在ESXi的hypervisor中的. 其基本思想是,将本地的存储资源虚拟化, 提供给整个集群,形成一个VSAN datastore, 同时支持vSphere传统的功能,诸如: HA, vMotion, DRS等.  

如同其他vSphere集群一样, 对于形成vSAN的集群, VMware也建议其成员host具有完全一样的存储资源. 这样可以使负载更均衡,调度更灵活. 不过,即使是没有本地存储的host,也可以加入vSAN集群,使用vSAN的datastore; 在这种情况下, 这种host就是一般HCI架构中的计算扩展结点.  

如果要为vSAN提供存储, 则host必须至少提供一个flash作为cache和一个存储设备作为容量存储(也叫数据磁盘).  

host为vSAN提供的存储资源,构成了一个或多个磁盘组.每个磁盘组有1个flash(用于cache)和一至多个容量存储设备(用于持久存储). 每个host都可以配置使用多个磁盘组. 


## VSAN的一些术语
### Disk Group(磁盘组)

    磁盘组,由一个flash作为cache;一个或多个其他磁盘(flash/SAS/NL-SAS)作为容量存储设备. 作为cache的flash盘不能被其他磁盘组共享,也不能作于其他用途.

### Consumed capacity(消耗容量)

    消耗容量是在某个时间点虚机(一个或多个)真实占用的物理存储容量. vmdk,保护复制(protection replica)等都会消耗容量. 计算cache时,不需要考虑保护复制.

### Object-based storage

    由于vSAN主要用于存储虚机,其存储专门为vmdk这种文件做了优化设计. snapshot, vmdk等都是对象; 一个对象有数据,也有元数据. 当部署虚机时, vSAN会为每个虚拟盘创建一系列的对象,同时创建虚机目录作为虚机所有对象的容器. 如果不考虑底层存储的话, 在vSAN中的虚机与使用传统存储的虚机没有什么区别. 如果考虑底层存储的话, vSAN还会根据虚机的存储策略(storage policy)为每个对象创建RAID配置等操作. 有如下一些因素会纳入考虑:
    1. vSAN会验证虚拟磁盘满足虚机的存储策略
    1. vSAN会确保部署时使用了正确的集群资源. 比如,根据保护策略,vSAN会确定创建几份备份. 根据性能策略, 会确定每个备份分配多少flash作为读cache, 以及为每个备份创建多少条带,在集群的什么位置存放这些条带.
    1. vSAN会持续监控并报告虚拟磁盘使用对于策略遵守情况.
    1. vSAN会检查保护措施(比如镜像等)存放在不同的主机或者故障域.

### vSAN datastore
可以在一个已经存在的集群上,启用VSAN. 启用后,集群会看到一个VSAN的datastore, 与其他类型(VMFS/NFS/vVol)的datastore并列存在.  
+ 不论集群中的host是否有存储, VSAN将给集群中的所有host提供一个单一的VSAN datastore. 任何一个集群中的host,还可以mount其他任意类型的datastore.
+ 可以使用Storage vMotion将虚机在各种存储之间迁移.
+ 用于flash cache的设备容量将不计算在VSAN datastore中.
+ 在自动磁盘声明模式下, 当给集群添加host或者给host添加存储时,这些存储会自动被加入vSAN的datastore.

### Objects and components
每个对象由一组组件构成.VSAN会确保保护组件(protection component: 比如replica, witness)部署在不同的host上. 同时根据磁盘条带数配置为2或多个,VSAN会将对象切分为多个条带,每个条带是对象的一个组件,这些条带会跨越多个容量设备. 有时VSAN会将大的object切分为多个组件. VSAN有如下一些对象类型:
+ VM Home Namespace. 虚机主目录,包含虚机的配置文件,包括.vmx, log, vmdk, snapshot delta描述文件等.
+ VMDK
+ VM Swap Object
+ Snapshot Delta VMDKs
+ Memory object

### 虚机遵从状态: compliant/noncompliant
### 组件状态: degreded/absent
### 对象状态: healthy/unhealthy
### witness

### 基于存储策略的管理
### 
### 





