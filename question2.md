1. 限流算法
    - 时间窗口限流
        - 固定窗口 固定时间段 重置计数器 临界问题
        - 滑动窗口 每次动态往前推算时间段, 计算当前时间段流量
    - 漏桶算法 sentinel使用 控制桶的流出量,进入的流量如果溢出就放弃掉
    - 令牌桶 控制令牌生成,每个请求都需要个令牌才能放行 ,可以做到动态控制生产令牌的速度 但是需要预热
2. druid
3. 联合索引在b+树的实现
4. redis上线新节点,怎么避免缓存命中率下降
 - 一致性hash
5. 多个分布式系统的数据一致性
6. cpu load高怎么排查
7. cpu平均占用率很高怎么排查
8. mysql 查询类型
    - simple 简单查询
    - primary 子查询主查询的类型 union时候内存的类型(和外层没依赖)
    - subquery  子查询 处在 select 和where条件肿
    - drived 被驱动的子查询 被物化的子查询 处在from中
    - union  不可物化的子查询  若第二个select查询语句出现在UNION之后，则被标记为UNION。若UNION包含在FROM子句的子查询中,外层SELECT将被标记为：DERIVED
    - union result 从UNION表获取结果的SELECT。 
9. 消息堆积
10. sql分页太多出现的问题
    - sql会查询很多数据,抛弃之前的
    - 解决方案  
        -   加入id书签
        - 用between
        - 再建一个小的索引表