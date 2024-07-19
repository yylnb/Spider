# Spider

### （一）爬虫的概念
`网络爬虫`（又称为网页蜘蛛，网络机器人），是一种按照一定的规则，自动地抓取互联网信息的程序或者脚本。它为搜索引擎从互联网上下载网页，是`搜索引擎`的重要组成。

如我们想从一个网页获得`整齐完整的数据`，但手动一句一句地复制太麻烦，便可以使用爬虫自动帮我们完成任务。以下是一些效果图。

##### 一些效果图

足球赛事的爬取

![image](https://github.com/yylnb/Spider/assets/119385505/8bc9d59d-16c2-4ad8-8b5e-6d1fa5b053c6)

糗事百科的爬取

![image](https://github.com/yylnb/Spider/assets/119385505/c3473772-8c60-4b68-9f56-9044825cde6b)


是不是看起来很舒服？接下来我们就学习如何用python3完成这个任务。

### （二）爬虫的组成部件

#### 1.Requests库 基本介绍

我们平时浏览网页的步骤是输入一个地址，回车。我们以打开懂球帝网页为例，在 Request库中它是这样呈现的：

	import requests
 
 	url = 'https://www.dongqiudi.com/live/70/'
  	header = {"User-Agent": "Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/120.0.0.0 Safari/537.36"}
   	r = requests.get(url, headers=header) 

  	print(r.text)
   
这里的header是`请求标头`，更逼真地模拟浏览器登录，可以通过浏览器—检查—网络—随便点一个文件—标头—请求标头—"Uers-Agent"查看。没有也影响不大。
   
输出结果如下

![image](https://github.com/yylnb/Spider/assets/119385505/9b3e0abb-e452-493e-ba0e-8c8941eb6dd8)

这样我们就得到了这个网站的所有源码

#### 2.解析源码 BeautifulSoup库

我们得到了关于这个网页的所有消息，那我们怎么获取我们想要的信息呢？

下面看一个简单的例子，首先建立一个简单的网页example.html

![image](https://github.com/yylnb/Spider/assets/119385505/c58f75bc-17d4-4ce0-aa07-dd605c985a07)

例如我们要获取主科man-subjects里的项目

	from bs4 import BeautifulSoup
 
 	soup = BeautifulSoup(open('example.html',encoding='utf8'), 'html.parser') #打开文件
	ul_MainSubjects = soup.find('ul', class_="main-subjects") #找到种类为"main-subjects"的ul
 	print(ul_MainSubjects)

输出结果

![image](https://github.com/yylnb/Spider/assets/119385505/747087cc-e16f-4480-8ad7-630361bab2e0)

这样我们就得到了主科的ul

	lis_MainSubjects = ul_MainSubjects.find_all('li') #这里得到的是一个列表
 	print(lis_MainSubjects)

  	for i in lis_MainSubjects:
    	print(i.text)
输出结果

![image](https://github.com/yylnb/Spider/assets/119385505/81265b83-4110-41f6-bfa9-22e2b7a1cc8f)

这样我们就成功获得了主科的信息

搜索文档树还有很多方法，想学习更多的话可以上官网https://beautifulsoup.readthedocs.io/zh-cn/v4.4.0

#### 3.写入函数

我们可以先创建一个名为dongqiudi.txt的文件，然后用write()函数来完成，这里的*args是`一些`的意思

	def save_txt(*args):
    	for i in args:
        	with open('dongqiudi.txt', 'a', encoding='utf-8') as f:
	            f.write(i)

### （三）完整代码

若我们要把爬虫运用到实战中，则必须熟悉网页的结构，以dongqiudi.com/live/70为例,F12打开检查

![image](https://github.com/yylnb/Spider/assets/119385505/a06b2c56-36a5-436c-a25e-661e045c20f0)

![instruction](https://github.com/yylnb/Spider/assets/119385505/553b6f52-970f-4f12-b6a8-054784e7f2c3)


确定好算法，我们再把所有的部件组合在一起，就得到了完整的代码，代码如下

	import requests
	from bs4 import BeautifulSoup

	#获取网页源码
	def download_page(url):
	    headers = {"User-Agent": "Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/120.0.0.0 Safari/537.36"}
	    r = requests.get(url, headers=headers)
	    return r.text

 	#解析网页源码
	def get_content(html):
	    output = """{} {} 赛事：{} {} {} 比分：{}\n"""
	    soup = BeautifulSoup(html, 'html.parser')
	    con = soup.find('div',class_="list-data")
	    con_list = con.find_all('div', class_="match-list")
	    for lists in con_list:
	        date = lists.find('p',class_="date").text #获取比赛日期
	        boxs = lists.find_all('div',class_="match-item")
	        for items in boxs:
	            time = items.find('span',class_="start-min").text #获取比赛具体时间
	            round = items.find('span',class_="round").text	#获取比赛性质
	            team_a = items.find('p',class_="team-a").text #获取A队队名
	            team_b = items.find('p',class_="team-b").text #获取B队队名
	            scores = items.find('div')
	            if scores.div != None: #获取比分
	                score = scores.div.p.text
	            else:
	                score = "VS"
	            save_txt(output.format(date, time, round, team_a, team_b, score))

 	#储存输出
	def save_txt(*args):
	    for i in args:
	        with open('dongqiudi.txt', 'a', encoding='utf-8') as f:
	            f.write(i)
	def main():
	    url = 'https://www.dongqiudi.com/live/70/'
	    html = download_page(url)
	    get_content(html)
	
	if __name__ == '__main__':
	   main()

 输出的结果如下（2024/2/5）：

![image](https://github.com/yylnb/Spider/assets/119385505/07d86c27-b414-40b7-9349-7a3403634414)

 
	




   










 
