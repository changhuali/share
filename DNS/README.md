# DNS

全称 Domain Name System，用于将域名解析为ip地址。

## 解析过程（ipv4）

以浏览器访问www.baidu.com为例
 
> 域名服务器忽略缓存，采用迭代查询方式（还有一种递归查询）。

- 查询浏览器本身的DNS缓存
- 查询操作系统DNS缓存
- 向本地DNS服务器请求解析域名 www.baidu.com
- 本地DNS服务器向根DNS服务器请求解析域名 www.baidu.com
- 根DNS服务器向本地DNS服务器返回com.域对应的顶级DNS服务器（type为NS）
- 本地DNS服务器向顶级DNS服务器请求解析域名 www.baidu.com
- 顶级DNS服务器向本地DNS服务器返回baidu.com.域对应的权威DNS服务器（type为NS）
- 本地DNS服务器向权威DNS服务器请求解析域名 www.baidu.com
- 权威DNS服务器向本地DNS服务器返回www.baidu.com.域对应的ip地址（type为A）
- 本地DNS服务器向客户端返回ip地址

**根域名**
根域只有 . ，根DNS服务器一共13台，1台为主根服务器在美国，12台为辅根服务器，其中美国9台，英国、瑞典、日本各1台。

**顶级域名**
又称为一级域名，常见的顶级域有 .com、.org、.net等国际顶级域名，也有 .cn、.fr、.jp等国家级顶级域名。
