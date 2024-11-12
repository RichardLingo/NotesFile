   
#解决 

[[Subjects/日常管理工具/电脑系统/MacOS/MacOS]]
  

备份和还原Mac系统app的布局的教程

备份与还原步骤：

备份：

查找所在文件夹：LaunchPad 的布局数据库位于 /private/var/folders 下的某个文件夹内，具体位置可以在终端中输入以下命令查找：

cd /private/var/folders

sudo find ./ -name 'com.apple.dock.launchpad'

备份其下文件夹所有子项即可。

还原：

将备份的文件夹还原覆盖回原来的文件夹即可。