## ROBUSTA-KRR 最佳实践

Robusta KRR（Kubernetes Resource Recommender）是一个用于优化Kubernetes集群中资源分配的CLI工具。它从 Prometheus 收集 Pod 使用数据，并建议对 CPU 和内存的请求和限制。这降低了成本并提高了性能。

### With brew (MacOS/Linux)
* 安装brew
```
# 使用中科大镜像
export HOMEBREW_BREW_GIT_REMOTE="https://mirrors.ustc.edu.cn/brew.git"
export HOMEBREW_CORE_GIT_REMOTE="https://mirrors.ustc.edu.cn/linuxbrew-core.git"
export HOMEBREW_API_DOMAIN="https://mirrors.ustc.edu.cn/homebrew-bottles/api"
export HOMEBREW_BOTTLE_DOMAIN="https://mirrors.ustc.edu.cn/homebrew-bottles/bottles"
# 执行安装脚本（不能以root进行安装）
/bin/bash -c "$(curl -fsSL https://gitee.com/ineo6/homebrew-install/raw/master/install.sh)"
# 更新brew
$ brew update
```

* 安装 robusta-krr
添加 tap
```
brew tap robusta-dev/homebrew-krr
```
安装 KRR
```
brew install krr
```
检查安装是否成功（首次启动可能需要更长的时间）
```
krr --help
```
### Windows On Install
确保已安装 Python 3.9（或更高版本）

1. 克隆存储库
```
git clone https://github.com/robusta-dev/krr
```
2. 导航到项目目录
```
cd ./krr
```
3. 安装运行依赖
```
pip install -r requirements.txt
```
4. 运行工具
```
python krr.py --help
```

### Flags

* `--cpu-min` 设置建议的最小 CPU 值（以分钟为单位）
* `--mem-min` 设置建议的最小内存值（以 MB 为单位）
* `--memory_buffer_percentage`用于内存建议的峰值内存使用率的缓冲区百分比。默认值为15，表示在峰值内存使用率上增加* 15%的缓冲区
* `--history_duration` 要使用的普罗米修斯历史数据的持续时间（以小时为单位）
* `--timeframe_duration` 历史数据的步长，以分钟为单位。默认值为1.25分钟
* `--prometheus-url` `-P`指定Prometheus的URL，如果没有提供URL，则会尝试在Kubernetes集群中查找
* `--resource` `-r`指定要在哪些资源上运行特定的操作。它接受一个逗号分隔的资源列表作为输入，可以包括Deployment、* StatefulSet、DaemonSet、Job和Rollout等资源。默认情况下，它将在所有资源上运行操作.
* `--namespace` `-n` 指定特定namespace下的资源
* `--selector` `-s` 通过标签选择器来过滤要操作的资源。它接受一个标签查询作为输入，支持使用'='、'=='和'!='来指定* 标签的匹配条件。您可以使用逗号分隔的键值对来指定多个标签和对应的值，例如 -s `key1=value1,key2=value2`
* `--formatter` `-f` 以特定格式输出(json, pprint, table, yaml) [default: table]
* `--points_required` 对于资源进行建议所需的数据点数量。默认值为100，表示至少需要100个数据点才能进行建议

### Usage

给出所有 resource建议
```
python krr.py simple -p http://172.16.10.99:30090
```
只给出特定 Namespace 下resource建议
```
python krr.py simple -n kube-system -p http://172.16.10.99:30090 
```
将查询结果输出到 json文件
```
python krr.py simple -n default -p http://172.16.10.99:30090 --logtostderr -f json > res
ult.json
```

