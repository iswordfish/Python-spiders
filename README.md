# Python-spiders
爬虫笔记
# -*- coding: utf-8 -*-
#HTMLParser是python用来解析html的模块。
#它可以分析出html里面的标签、数据等等，是一种处理html的简便途径。
# HTMLParser采用的是一种事件驱动的模式，当HTMLParser找到一个特定的标记时，它会去调用一个用户定义的函数，以此来通知程序处理。它 主要的用户回调函数的命名都是以handler_开头的，都是HTMLParser的成员函数。当我们使用时，就从HTMLParser派生出新的类，然 后重新定义这几个以handler_开头的函数即可。
import urllib2
import json#encode
from HTMLParser import HTMLParser

class MovieParser(HTMLParser):##继承，定义电影解析器
    def __init__(self):
        HTMLParser.__init__(self)#调用父类的函数
        self.movies =[] #保存

    def handle_starttag(self,tag,attrs):# 这里重新定义了处理开始标签的函数
        def _attr(attrlist,attrname):#解析属性其中attrlist是一个tuple，包含两项
            for attr in attrlist:#他们是tuple的属性，解析出我们想要的
                if  attr[0]==attrname:
                    return attr[1]
            return None

        if  tag =='li' and _attr(attrs,'data-title'):
            movie ={}
            movie['title']=_attr(attrs,'data-title')#解析出来的属性组装成字典
            movie['rate']=_attr(attrs,'data-rate')
            movie['director']=_attr(attrs,'data-director')
            movie['actor']=_attr(attrs,'data-actors')
            self.movies.append(movie)#把字典添加到list中保存
            print('%(title)s||%(rate)s||%(director)s||%(actor)s' %movie)


def  nowplaying_movies(url):
    headers ={'User-Agent':'Mozilla/5.0 (Windows NT 10.0; WOW64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/50.0.2661.102 Safari/537.36'}
    rep =urllib2.Request(url, headers =headers)
    s = urllib2.urlopen(rep)
    parser  =MovieParser()#通过定义解析器找到我们想要的信息
    parser.feed(s.read())#把数据输入
    s.close()
    return parser.movies

if __name__=="__main__":
    url ="https://movie.douban.com/"
    movies = nowplaying_movies(url)
