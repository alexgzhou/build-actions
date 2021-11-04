## 本地编译操作步骤
1. 在云服务器上 (如 `oracle` 云服务器) 非 root 用户下 `$ git clone https://github.com/alexgzhou/build-actions.git`  
2. `$ cd build-actions && chmod 755 compile.sh && ./compile.sh` -> 交互选择一些选项, (可选) 进入 `.config - OpenWrt Configuration menu` 进行 `.config` 配置, 退出配置后自动完成编译并显示编译信息  
3. 可以定时同步一下上游 (`upstream https://github.com/281677160/build-actions` 库): `$ git remote add upstream https://github.com/281677160/build-actions && git pull upstream`, 然后 `merge`  

### 本地编译的一些坑
1. `$ make menuconfig` 对 `ssh` 终端显示大小有要求, 必须在 `19 row * 81 col` 以上, 否则会提示终端显示大学不够, 无法进入 `.config - OpenWrt Configuration menu`, 或者出现奇怪的问题, 比如无法回车进入 `menu` 中 `Target System` 等选项的下级菜单  
2. 某些云 (如 `oracle cloud`) 上的 `Ubuntu` 镜像进行了精简, 可能造成编译环境依赖的缺失, 比如 `cmake`, 如果编译日志中出现相关错误提示, 可以手动安装依赖, 如 `$ sudo apt-get install cmake`  
3. 远程云服务器编译时是通过 `ssh` 登陆编译的, 如果 `ssh` 登陆断开, 会造成编译中断, 因此需要将长时间运行的编译等指令改为后台非挂起运行, 如 `$ nohup sh -c 'make -j$(nproc) V=s 2>&1 |tee build.log' &` 或 `$ nohup make -j$(nproc) V=s 2>&1 |tee build.log &`  

### 改造自动编译脚本 `$ bash <(curl -fsSL git.io/pile.sh)` (好像不需要改造, 直接 `make menuconfig` 中修改 `.config` 即可)
* 可下载 `git.io/pile.sh` 到本地, 保存为 `compile.sh`, `$ chmod 755 compile.sh`, 然后 `$ ./compile.sh` 即可本地 (`oracle` 云服务器) 编译  
* **注意** 要修改 `compile.sh` 中的 `make -j$(nproc) V=s 2>&1` 这一行到后台非挂起运行 `nohup sh -c 'make -j$(nproc) V=s 2>&1 |tee build.log' &`, 否则会受到 `ssh` 连接断开影响, 导致编译中断  
* `compile_inProg.sh` 尝试修改增加其他的编译选项, 但是发现没必要, 暂时先搁置  