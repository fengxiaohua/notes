1. 尝试搭建gitlab-runner
2. 编写安全等保测评支持

自己建立测试项目地址

测试环境拉取地址测试

安装gitlab-runner
https://blog.csdn.net/qq_35314093/article/details/123891021

192.168.51.138配置gitlab-runner

git源版本太低，导致第一次运行gitlab-runnner后报错
https://blog.csdn.net/qq_35746739/article/details/126996890

fatal: detected dubious ownership in repository at '/home/clone-platform/Open-Platform'
To add an exception for this directory, call:
	git config --global --add safe.directory /home/clone-platform/Open-Platform
配置git config --global --add safe.directory /home/clone-platform/Open-Platform （由于git升级后带来的安全问题）

chown -R root:root /home/clone-platform/Open-Platform/

设置ssh信息
https://blog.csdn.net/BBEter/article/details/129965393