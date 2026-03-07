 

1.打开一个终端，输入`dpkg --list` ,按下Enter键，终端输出以下内容，显示的是你电脑上安装的所有软件。  

![](attach/Pasted%20image%2020250211140033.png)

2.在终端中找到你需要卸载的软件的名称，列表是按照首字母排序的。  

![](attach/Pasted%20image%2020250211140047.png)

3.在终端上输入命令`sudo apt-get --purge remove 包名`（`--purge`是可选项，写上这个属性是将软件及其配置文件一并删除，如不需要删除配置文件，可执行`sudo apt-get remove 包名`） ，此处我要删除的是`polipo` ，那么在终端输入`sudo apt-get --purge remove polipo`，按下回车，输入密码，再次回车。  

![](attach/Pasted%20image%2020250211140108.png)


4.执行过程中，会提示你是否真的要删除（继续执行删除命令），在终端输入`y` ，然后回车，删除程序继续执行。 

![](attach/Pasted%20image%2020250211140125.png)

5.正常情况下，再次出现输入命令行删除成功。  

![](attach/Pasted%20image%2020250211140132.png)













