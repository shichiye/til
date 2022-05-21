# cURL
## 简介
cURL可以让你不需要浏览器也能作为HTTP客户端发送请求，并且是跨平台的
## 使用cURL发送HTTP常见请求
cURL默认发送GET请求
```
curl httpbin.org/get
```
发送POST请求
```
curl -XPOST httpbin.org/post
```
发送POST请求并携带数据 -d
```
curl -XPOST httpbin.org/post -d '{"name": "shichiye"}'
```
跟随重定向 -L
```
curl https://www.bilibili.com -L
```
调试连接 -v
```
curl 
```
## HTTP首部
发送HTTP请求并带上首部 -H
```
curl -XPOST httpbin.org/post -H 'Content-type: application/json' -d '{"name": "shichiye"}'
```
获取相应的所有首部 -I
```
curl -XPOST httpbin.org/post -I
```
## 文件处理
下载文件 -O
```
curl -O URL
```
下载文件并重命名 -o
```
curl -o 文件名 URL
```
---
[source](https://www.bilibili.com/video/BV1n94y1U7Eu?spm_id_from=333.788.top_right_bar_window_history.content.click)