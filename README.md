# Spider

### （一）爬虫的概念
`网络爬虫`（又称为网页蜘蛛，网络机器人），是一种按照一定的规则，自动地抓取互联网信息的程序或者脚本。它为搜索引擎从互联网上下载网页，是`搜索引擎`的重要组成。

如我们想从一个网页获得`整齐完整的数据`，但手动一句一句地复制太麻烦，便可以使用爬虫自动帮我们完成任务。以下是一些效果图。

##### 一些效果图

足球赛事的爬取

!img

糗事百科的爬取

!img


是不是看起来很舒服？接下来我们就学习如何用python3完成这个任务。

### （二）爬虫的组成部件

#### Requests库 基本介绍

我们平时浏览网页的步骤是输入一个地址，回车。我们以打开懂球帝网页为例，在 Request库中它是这样呈现的：

	import requests
 
 	url = 'https://www.dongqiudi.com/'
  	header = {"User-Agent": "Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/120.0.0.0 Safari/537.36"}
   	r = requests.get(url, headers=header) 

  	print(r.text)
   
这里的header是`请求标头`，更逼真地模拟浏览器登录，可以通过浏览器—检查—网络—随便点一个文件—标头—请求标头—"Uers-Agent"查看。没有也影响不大。
   
输出结果如下

!img

这样我们就得到了这个网站的所有源码

### 解析源码 BeautifulSoup库

我们得到了关于这个网页的所有消息，那我们怎么获取我们想要的信息呢？

下面看一个简单的例子，首先建立一个简单的网页example.html

!img

例如我们要获取主科man-subjects里的项目

	from bs4 import BeautifulSoup
 
 	soup = BeautifulSoup(open('example.html',encoding='utf8'), 'html.parser') #打开文件
	ul_MainSubjects = soup.find('ul', class_="main-subjects") #找到种类为"main-subjects"的ul
 	print(ul_MainSubjects)

输出结果

!img

这样我们就得到了主科的ul

	lis_MainSujects = ul_MainSubjects.find_all('li') #这里得到的是一个列表
 	print(lis_MainSujects)

  	for i in lis_MainSujects:
    	print(i.text)
输出结果

!img

这样我们就成功获得了主科的信息

搜索文档树还有很多方法，想学习更多的话可以上官网https://beautifulsoup.readthedocs.io/zh-cn/v4.4.0



	




   










 
