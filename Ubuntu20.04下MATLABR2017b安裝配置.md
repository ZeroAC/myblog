[toc]

# MATLAB R2017b for Ubuntu 20.04安装

`MATLAB R2017b Linux` 安装包下载地址：

**链接: https://pan.baidu.com/s/1zM6y_LBdyMP9YAfujcOw5A  密码: m7lq**

## 1.解压

```shell
unzip R2017b_glnxa64.zip
```

**会报错!**

原因是zip文件是分割了好几部分 .zip .z01 .z02 因此需要合并后再解压

执行

```shell
cat R2017b_glnxa64.* > R2017b_glnxa64_all.zip 
unzip R2017b_glnxa64_all.zip  
```

即可

然后顺便把破解文件也解压一下

```shell
sudo apt-get update
sudo apt-get install unrar
sudo rar x MATLABR2017b_Linux_Crack.rar
```

## 2.安装

挂载解压后的iso文件 例如把iso文件内的内容放到 `/home/zeroac/matlabR2017`中

```shell
mkdir /home/zeroac/matlabR2017
sudo mount -t auto -o loop R2017b_glnxa64.iso /home/zeroac/matlabR2017
```

然后进入该文件夹下 执行安装文件即可

```shell
cd /home/zeroac/matlabR2017
./install
```

选择文件密匙安装    为:  `09806-07443-53955-64350-21751-41297`

安装过程就一路默认安装就行了

若出现安装路径无法创建读写的情况 就自己去文件管理器手动创建 然后把创建的文件夹更改下权限

例如对于安装路径为`/usr/local/MATLAB/R2017b`

手动创建文件夹后 执行

```shell
chmod 777 /usr/local/MATLAB/R2017b
```

然后再继续安装即可

## 3.破解

进入之前`MATLABR2017b_Linux_Crack.rar`解压后的文件夹然后执行以下命令即可完成破解
```shell
sudo cp license_standalone.lic /usr/local/MATLAB/R2017b/licenses/ 
sudo cp libmwservices.so /usr/local/MATLAB/R2017b/bin/glnxa64/
```

## 4.创建快捷方式

运行`matlab `则需执行

```shell
sh /usr/local/MATLAB/R2017b/bin/matlab
```

但这样很不方便 然而又没有现成的快捷方式 应用里也搜不到 故而只能自己创建！

***创建matlab的快捷方式***

```shell
sudo gedit /usr/share/applications/Matlab.desktop
```

再该文件中写入以下内容

```shell
[Desktop Entry]
Categories=Application;
Comment=Matlab:科学计算的语言
Encoding=UTF-8
Exec=/usr/local/MATLAB/R2017b/bin/matlab -desktop -prefersoftwareopengl
Icon=/usr/local/MATLAB/R2017b/toolbox/shared/dastudio/resources/MatlabIcon.png
Name=MATLAB
StartupNotify=true
Terminal=false #在启动matlab时不启动终端
Type=Application
```

保存退出 在应用中搜索matlab即可添加到快捷方式

## 5.【美化】MATLAB字体更改

默认字体下的matlab丑的连他亲妈都不认识了...

我们可以考虑换个好看点的字体

下载字体

**链接: https://pan.baidu.com/s/1Y6cbiNbYW-OXKWsHvIjrTA  密码: tr4n**

然后配置安装即可

```shell
cd /usr/share/fonts
sudo mkdir myfonts
sudo mv ~/新字体的位置/新字体.ttf ./myfonts

sudo mkfontscale
sudo mkfontdir
sudo fc-cache -fv  
```

然后打开`matlab`

点击菜单上的设置按钮`Preferences`然后点击左侧栏的`Fonts` 切换为新下载的字体即可 `YaHei Consolas Hybrid`

## 参考资料

[Ubuntu18.04安装MatlabR2017b](https://blog.csdn.net/beizhengren/article/details/100514590?utm_medium=distribute.pc_relevant.none-task-blog-title-3&spm=1001.2101.3001.4242#commentBox)

[Ubuntu16.04/18.04安装MATLAB2018b](https://blog.csdn.net/m0_38087936/article/details/103342731)

[Ubuntu18.04MATLAB安装及配置](https://www.cnblogs.com/MingruiYu/p/12367846.html)













