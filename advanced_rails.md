# 进阶知识

1. Gemfile的版本号要标准精确
2. 本地和服务器的环境要完全一致： ruby version, rails version, mysql version
3. 代码风格： 宁可啰嗦，不要简写

4. 优化： 每个column 都要加上index
5. 使用 bullet 减少 n + 1 请求
6. 关键的地方多使用logger。
7. 优化数据库的知识
8. 发起的http 请求，务必要加上timeout , 1~2s以内
9. 要打开数据库的slow query。这样数据库中哪个查询慢了可以瞬间定位到。
10. 单元测试
