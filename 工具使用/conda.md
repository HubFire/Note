# conda 使用
* 创建环境 conda create -n env_name numpy matplotlib python=2.7
* 删除环境 conda remove -n your_env_name --all
* conda list 查看安装了哪些包。
* conda env list 或 conda info -e 查看当前存在哪些虚拟环境
* conda update conda 检查更新当前conda
* 激活环境 source activate your_env_name
* 退出环境 source deactivate
* 安装python包 conda install -n your_env_name [package]
* 卸载包 conda remove --name your_env_name  package_name
* 设置国内镜像 
conda config --add channels https://mirrors.tuna.tsinghua.edu.cn/anaconda/pkgs/free/  <br>
conda config --set show_channel_urls yes
* 显示镜像 conda config --show
* 删除镜像 conda config --remove channels https://pypi.doubanio.com/simple/
