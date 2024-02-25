# 2PLSF
河海大学分布式作业
1.项目环境配置
（1）首先对于项目的硬件环境，由于要求cpu线程在64以上，我们团队选择租赁腾讯云服务器64核128G内存服务器来复现项目。另外我们选用centos7.5镜像，并选配了50G的硬盘。（服务器配置详见：软件测试文档-1.1-（1））
（2）对于项目的软件环境，需要安装anaconda、python、g++-7或者更高版本。（安装教程详见：软件测试文档-1.1-（2）（3））
（3）还需要安装一些绘制实验数据图的软件，例如gnuplot、ps2pdf。（安装教程详见：软件测试文档-1.1-(4)、(5)）

2.测试代码运行（运行步骤详见：软件测试文档-1.2-(1)(2)(3)）
主要分为数据结构基准测试和DBx1000基准测试：
（1）克隆项目代码：（运行步骤详见：软件测试文档-1.2-(1)）
	新的机器上没有安装项目所需软件，我们使用ssh连接服务器，通过xshell和xftp工具远程操纵服务器和传输数据。登录root用户后，在root目录新建software和code文件夹。
	mkdir software
	mkdir code
	software文件夹用于存放项目所需要的软件，code文件夹用于存放项目
	下载git拉取github远程仓库项目，有git则不用进行此操作
	yum -y install git
	进入到code文件夹，拉起远程仓库项目
	git clone https://github.com/pramalhe/2PLSF.git
 
（2）数据结构基准测试：（运行步骤详见：软件测试文档-1.2-(2)）
	进入项目目录
	cd /root/code/2PLSF
	执行如下两行指令，构建 TinySTM
	cd stms/tinystm
	make
	执行如下两行指令，构建 TL2
	cd ../tl2-x86
	make
	然后执行下述两行指令，构建数据结构基准
	cd ../../graphs
	make
	运行完这一步，如果我们安装的不是g++-10，可能会出现报错，解决方法也比较简单，我们可以通过修改Makefile文件解决。（详见软件测试文档-1.2-(2)-图13、图14）
	vim Makefile
	将g++-10改成g++
	修改完成之后重新执行，如果本身就安装的g++-10则不会出现上述报错。
	cd ../../graphs
	make
	构建成功之后，即可运行如下指令进行数据结构基准测试
	python2 run-all.py
	该步骤运行时间较长，大概需要执行6小时左右
	这里会显示两个文件找不到，我们就此问题也与作者进行联系，作者回复说明这两个文件不存在是正常现象，并不会影响到实验结果。（详见软件测试文档-1.2-(2)-图17）
	运行成功会将数据结构基准测试的结果保存至graphs/data文件夹下

（3）DBx1000基准测试：（这里文字讲述比较难以理解，软件测试文档-1.2-(3)中有完整运行过程的图文讲解）
	首先进入dbx1000的目录下
	cd /root/code/2PLSF/DBx1000
	然后编辑Makefile文件和上述数据结构基准测试一样将g++-10改成g++
	再修改config.h文件中的CC__ALG，如图20所示。
	这里需要依次将CC_ALG替换为 WAIT_DIE ,  NO_WAIT ,  DL_DETECT ,  TICTOC ,  TWO_PL_SF然后重复执行下述步骤。
	这里我们拿TWO_PL_SF做个例子：
	修改成TWO_PL_SF之后执行下述操作：
	make -j
	这里需要注意的是，在内存小的电脑上DBX1000是无法完成make指令的！
	然后运行
	./run-dbx1000.py
	运行完毕之后会生成4个文件，分别是ycsb-low-result.txt、ycsb-med-result.txt、ycsb-low-result.txt和output.txt。
	这里需要将ycsb-low-result.txt、ycsb-med-result.txt、ycsb-low-result.txt三个文件名中的result全部修改成我们之前替换CC__ALG的名字2plsf（名称修改也可参照graphs/data/cervino路径下的其他文档来进行修改，目的主要是为了覆盖掉该路径下的同名文档）。
	修改完成之后三个文档名称就变为ycsb-low-2plsf.txt、ycsb-med-2plsf.txt、ycsb-low-2plsf.txt。然后用这三个生成的文档替换掉graphs/data/cervino中相同名称的文档。
	DBx1000基准测试需要将CC_ALG 依次更改为WAIT_DIE、NO_WAIT、DL_DETECT、TICTOC、TWO_PL_SF。并且重复上述的make、python运行和覆盖txt动作直至五组全部完成。

3.实验图像绘制（运行步骤详见：软件测试文档-1.2-(4)）
	这里需要用到之前我们安装的gnuplot
	进行完数据结构基准测试和DBx1000基准测试之后进入到graphs/plots 文件夹并运行 ./plot-all.sh即可得到全部实验结果pdf
