---
title: Ubuntu中hexo搭建博客
date: 2016-07-28 14:59:15
tags:
---

# 一.安装Node.js

        1.系统更新： apt-get update
        
        2.安装依赖：#apt-get install python gcc  make g++  wget   缺什么安装什么
        
        3.下载最新版的Node JS 
        
            https://nodejs.org/en/download/current/       
            
            node-v6.3.1.tar.gz
            
            解压：tar -zxvf  node-v6.3.1.tar.gz
            
            cd node-v6.3.1  
            
            ./configure
            
            make install 
            
            (此处若缺少2中的依赖，重新安装依赖)
# 二.安装git

        sudo apt-get install git
        
# 三.安装Hexo

        1.创建文件夹blog，进入安装Hexo：sudo  npm install -g hex
![ ](../image/hexo_1.png)             
        
        2.安装完成后执行如下命令，Hexo自动生成需要的文件夹,在blog文件夹（当前工作的文件夹）下运行

             hexo init
             npm install 

        3. 本地预览
            hexo server       
![ ](../image/hexo_2.png)      

            默认端口4000
            自定义端口 hexo s  -p   xxxx
            清除缓存 hexo clean  （页面不正常时可以使用）
# 四.创建Repository
    1.登录github点击右上角“+”，一直继续
![ ](../image/hexo_3.png)      
  
    2.复制https://github.com/zqturtle/zqturtle.github 
     打开hexo文件夹（bolg）下的_config.yml，设置deploy信息：
     
```     
deploy:

type:git

respository: https://github.com/zqturtle/zqturtle.github

branch:master
```
   ---
![ ](../image/hexo_4.png)       
---
    
# 五.设置SSH key

### 1. 执行 cd ~        cd  .ssh

     执行ls查看是否存在_id_rsa.pub和id_dsa.pub,若没有key什么都不会显示

### 2若没有则添加SSH key

       $ ssh-keygen -t rsa -C "github邮箱"

      ﻿##  t 指定密钥类型，默认是 rsa ，可以省略。 -C 设置注释文字，比如邮箱或其他。

        然后会提示你 Enter Enter file in which to save the key (/c/Users/you/.ssh/id_rsa): [Press enter,这里是输入一个文件名用来保存ssh key,也可以什么都不输,会使用默认的id_rsa.pub 和 id_dsa.pub回车之后,需要输入两次密码(该密码是你push文件的时候要输入的密码，而不是github的密码)输入密码之后,看见如下显示信息,添加SSH key成功.
    
![ ](../image/hexo_5.png)      
    
### 3.Github设置SSH key

    登录github,点击Settings,然后点击 SSH keys ,在这个页面你可以管理你所有的ssh keys然后点击Add SSH key用文本编辑器打开刚刚添加的key文件id_rsa.pub,复制里面的所有的内容回到github页面,将复制的内容粘贴到刚刚那个页面的key对应的文本框里面,title 可以随便填写
![ ](../image/hexo_6.png)      

![ ](../image/hexo_7.png)      
### 4测试ssh key是否添加成功

         $ ssh -T git@github.com

会出现一段警告代码,输入yes回车,然后会要求你输入刚刚设置的push  file密码
![ ](../image/hexo_8.png)      

注：
重新生成ssh key肯能由于没添加到ssh-agent报错
![ ](../image/hexo_9.png)      

发现fingerprint和github上的不一样

![ ](../image/hexo_10.png)      
   
        确保ssh-agent开启
```      
$eval "$(ssh-agent -s)"
```
        添加  
        
$ssh-add ~/.ssh/id_rsa
    
        
#  六.部署到Github

###  1.执行 hexo generate

        报错
![ ](../image/hexo_11.png)      

解决：blog目录下的_config.yml 文件中添加的respository: 后面多了空格
###  2.执行 hexo deploy

#  七.markdown编辑器ReText
    使用sudo apt-get install retext命令就可以安装了。打开编辑器，若无法即时预览，提示Could not parse file contents, check if you have the necessary module installed！的话，

```
sudo apt-get install python3-docutils python3-markdown
```
    
    
    
    
    
    
    
    
    
    
    
    
    
    
    
    
    
    
    
    
    
    
    
    
    
    
    
    
    
    
    