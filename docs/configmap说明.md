将整个文章从头捋一遍
[文件说明](https://www.cnblogs.com/pu20065226/p/10690628.html)

总结：

**volumeMounts.mountpath**
使用 `mountPath` 将 `ConfigMap` 作为文件或目录挂载，其中每一个 `key-value` 键值对都会生成一个文件，`key` 为文件名，`value` 为内容。

```
kubectl get configmap --namespace=ns-javashop       #查看指定命名空间下的configmap

describe configmap confnginx --namespace=ns-javashop    #查看指定 configmap 下的key-value
get configmap confnginx -o yaml --namespace=ns-javashop     #同样可以查看指定 configmap 的 key-value 键值对
```

#### mountpath与subpath配合使用

```
volumeMounts:
    - name: config-volume
        mountPath: /etc/config/app.properties #此处配合suPath使用时，app.properties为文件名，即pod容器中只生成了/etc/config目录，目录之下 为文件，只有一个名为app.properties的文件（subPath筛选只挂载app.properties文件）
        subPath: app.properties

这里的 subpath 筛选是从对应的 configmap 中的 key 筛选，如果 key 不存在就有问题，如果 configmap 中有多个 key，只会挂载指定的 key
```

subPath筛选范围优先级为pod.spec.volums[0].configMap.items[0].path>pod.spec.volums[0].configMap.items[0].key>configMap.key。

这个优先级我理解，存在 `path` 时就只在 `path` 寻找。如果 `subpath` 对应的 `key` 找到了，则 `mountpath` 的值为目录且目录为空。