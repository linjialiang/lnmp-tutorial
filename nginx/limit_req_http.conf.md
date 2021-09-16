```conf
# == http 区块定义限制，需要配合server区块
# 限制请求：针对同ip不同请求地址，每秒可发起5条，生成1个标识为 with_ip ，容量为10M的内存区域，用来存储访问的频次信息
limit_req_zone $binary_remote_addr zone=with_ip:10m rate=5r/s;
# 限制请求：标识符为 with_ip_request , 针对同ip同请求地址，每秒可发起1条，生成1个标识为 with_ip_request ，容量为10M的内存区域，用来存储访问的频次信息
limit_req_zone $binary_remote_addr$uri zone=with_ip_request:10m rate=1r/s;
```
