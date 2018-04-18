#GF安装指南（Ubuntu14.04）
#GF依托于 cabal haskell平台（依托于 GHC）
#现在安装的是cabal-1.22和ghc-7.10.2可根据需要调高版本
sudo add-apt-repository -y ppa:hvr/ghc

sudo apt-get update

sudo apt-get install -y cabal-install-1.22 ghc-7.10.2

cat >> ~/.bashrc <<EOF
export PATH=~/.cabal/bin:/opt/cabal/1.22/bin:/opt/ghc/7.10.2/bin:$PATH
EOF

#更新下bashrc文件
source ~/.bashrc
cabal --version #check you have the correct version
#更新cabal源包
cabal update
#这样安装是没有没有安装alex和happy的需要使用cabal安装alex和happy
#Alex是词法分析器生成器，happy是是语法分析器生成器
cabal install alex happy
还需要另外的一个库
sudo apt-get install libghc-haskeline-dev
#git克隆GF源码
git clone https://github.com/GrammaticalFramework/GF.git
#安装gf server版本 -v查看详细的安装信息
cd GF

cabal install -fserver -v
#可以通过gf指令开启server模式
gf -server
#安装java环境依托于c运行环境，需要先安装c环境
#如果需要使用make install 需要另外的几个包autoconf, automake, libtool, make通过apt-get安装即可
cd /src/runtime/c
#省心的方式
autoreconf -i

./configure

make

make install
#安装完了之后如果发现安装的环境有问题可以使用cabal clean清除编译，并重新开始
#安装c环境
cd到gf文件目录下
cabal install -fserver -fc-runtime
#可以通过gf -crun或者gf -cshell进入gf的shell命令行

#安装GF的java环境需要先安装java环境
#https://www.cnblogs.com/a2211009/p/4265225.html
#我是通过ppa(源) 方式安装oracle的jdk1.8.
sudo add-apt-repository ppa:webupd8team/java

sudo apt-get update

echo oracle-java8-installer shared/accepted-oracle-license-v1-1 select true | sudo /usr/bin/debconf-set-selections

sudo apt-get install oracle-java8-installer

#进入和c同级的java目录
cd ..

cd java
先查找jni.h文件在MakeFile中修改文件位置
find / -name jni.h
#将 makefile中的/usr/lib/jvm/default-java/include/,修改为java的jni.h文件地址例如·/usr/lib/jvm/java-8-oracle/include/ 共计三个地方

make 

make install
#如果源码编译有问题，可以运行make clean执行
#如果make install的过程中出现下面的情况
----------------------------------------------------------------------
Libraries have been installed in:
   /usr/local/lib

If you ever happen to want to link against installed libraries
in a given directory, LIBDIR, you must either use libtool, and
specify the full pathname of the library, or use the `-LLIBDIR'
flag during linking and do at least one of the following:
   - add LIBDIR to the `LD_LIBRARY_PATH' environment variable
     during execution
   - add LIBDIR to the `LD_RUN_PATH' environment variable
     during linking
   - use the `-Wl,-rpath -Wl,LIBDIR' linker flag
   - have your system administrator add LIBDIR to `/etc/ld.so.conf'

See any operating system documentation about shared libraries for
more information, such as the ld(1) and ld.so(8) manual pages.
#在/etc/prfile中添加下面的三句话
export PATH="$PATH:/usr/local/lib"
export LD_LIBRARY_PATH="/usr/local/lib"
export LD_RUN_PATH="/usr/local/lib"
source /etc/profile

备注：
如果上面的apt-get安装的包有问题，可以使用下面的指令卸载包重新安装
# 删除软件及其配置文件
apt-get --purge remove <package>
# 删除没用的依赖包
apt-get autoremove <package>
# 此时dpkg的列表中有“rc”状态的软件包，可以执行如下命令做最后清理：
dpkg -l |grep ^rc|awk '{print $2}' |sudo xargs dpkg -P

