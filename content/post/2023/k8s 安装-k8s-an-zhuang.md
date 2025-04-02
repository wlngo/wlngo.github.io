---
title: k8s 安装
date: 2023-08-18 17:31:15.705
updated: 2023-08-18 17:31:15.705
url: /archives/k8s-an-zhuang
categories: 
tags: 
---

##### keepalived配置
```
cat > /etc/keepalived/keepalived.conf << EOF
! Configuration File for keepalived
 
global_defs {
   router_id LVS_DEVEL
   enable_script_security
   script_user root
}
 
vrrp_script check_haproxy {
    script "killall -0 haproxy"
    interval 3
    weight -2
    fall 10
    rise 2
}
 
vrrp_instance VI_1 {
    state BACKUP
    interface ens18  # 虚拟网卡桥接的真实网卡
    virtual_router_id 80
    # 优先级配置，每台服务器最好都不一样，如100，90，80等，优先级越高越先使用
    priority 80
    advert_int 1
    authentication {
        auth_type PASS
        auth_pass 111
    }
    virtual_ipaddress {
        192.168.123.200 # 对外提供的虚拟IP
    }
    track_script {
        check_haproxy
    }
}
EOF

```
##### 监控keepalived
```
#!/bin/sh

errorExit() {
    echo "*** $*" 1>&2
    exit 1
}

curl --silent --max-time 2 --insecure http://localhost:6443/ -o /dev/null || errorExit "Error GET http://localhost:6443/"
if ip addr | grep -q 192.168.123.200; then
    curl --silent --max-time 2 --insecure http://192.168.123.200:6443/ -o /dev/null || errorExit "Error GET http://192.168.123.200:6443/"
fi
```

##### haproxy配置
```



#---------------------------------------------------------------------
# kubernetes apiserver frontend which proxys to the backends
#---------------------------------------------------------------------
frontend kubernetes-apiserver
    mode                 tcp
    bind                 *:6444  # 对外提供服务的端口，必须和kubernetes一致
    option               tcplog
    default_backend      kubernetes-apiserver #后端服务的名称
 
#---------------------------------------------------------------------
# round robin balancing between the various backends
#---------------------------------------------------------------------
backend kubernetes-apiserver
    mode        tcp
    balance     roundrobin
    server  node-master-1 192.168.123.246:6443 check # 后端服务器hostname和IP
    server  node-master-2 192.168.123.213:6443 check # 后端服务器hostname和IP
    server  node-master-3 192.168.123.154:6443 check # 后端服务器hostname和IP
```

https://developer.volcengine.com/articles/7052310062315864101
/etc/crictl.yaml
https://zhuanlan.zhihu.com/p/585865389
/etc/containerd/config.toml

##### 命令

污点和容忍
```
kubectl get no -o yaml | grep taint -A 5
```

```
kubectl taint nodes node-master-1 node-role.kubernetes.io/control-plane:NoSchedule
```


批量删除容器
```
 crictl ps -a | awk '{print $1}' | xargs crictl rm
```
查看coredns
```
 kubectl get pods --namespace=kube-system -l k8s-app=kube-dns
```