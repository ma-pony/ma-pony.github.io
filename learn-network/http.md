# 1.1
## 是否可以重用TCP连接
1. 请求不同域名，不重用
2. 请求相同域名，第一个与第二个请求并行发送，不重用
3. 请求相同域名，第一个与第二个请求串行发送，默认不重用
4. 请求相同域名，第一个与第二个请求串行发送，带有connection：keep-alive请求头，重用