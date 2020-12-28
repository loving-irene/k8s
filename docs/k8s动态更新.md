使用 k8s 如何进行服务的动态更新（不影响正常使用）。

关键词：探针，livenessProbe,readinessProbe

在 .yaml 文件中进行配置，如下是管理台的样式

```
"spec": {
    ...
    "template": {
      "metadata": {
        "creationTimestamp": null,
        "labels": {
          "app": "base-api"
        }
      },
      "spec": {
        "containers": [
          {
            "name": "base-api-container",
            "image": "registry.cn-hangzhou.aliyuncs.com/qf_javadev/base-api:7.2.2.dev03",
            "ports": [
              {
                "containerPort": 7000,
                "protocol": "TCP"
              }
            ],
            "resources": {},
            "readinessProbe": {
              "httpGet": {
                "path": "/tests/a",             # 路由
                "port": 7000,                   # 端口
                "scheme": "HTTP"                # 默认 HTTP，请求协议，可以是 https
              },
              "initialDelaySeconds": 350,       # 容器启动后多少秒执行操作，以下时间单位均为秒
              "timeoutSeconds": 1,              # 命令的超时时间
              "periodSeconds": 10,              # 间隔多少秒后执行下一次
              "successThreshold": 1,            # 成功执行一次则认为容器 ready
              "failureThreshold": 3             # 失败3次则认为容器 fail，进入重启程序
            },
          }
        ],
  ...
  },
```

起作用的就是 `readinessProbe` 对应的设置。

