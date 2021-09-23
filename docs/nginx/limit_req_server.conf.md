```conf
# == server 区块执行限制，需要配合 http 区块
# 针对同ip不同请求地址，每秒可同时发起5条，超过5条后使用延迟加载，超过15(rate+burst)条后，直接返回503
limit_req zone=with_ip burst=10 nodelay;
# 针对同ip不同请求地址，每秒只允许发起1条，超过1条后使用延迟加载，超过6(rate+burst)条后，直接返回503
limit_req zone=with_ip_request burst=5 nodelay;
```
