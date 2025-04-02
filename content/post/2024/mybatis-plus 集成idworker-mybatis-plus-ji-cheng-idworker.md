---
title: mybatis-plus 集成idworker
date: 2024-03-14 18:05:22.447
updated: 2024-03-14 18:06:57.33
url: /archives/mybatis-plus-ji-cheng-idworker
categories: 
tags: 
---


### mybatis-plus v3.4.0
fix: @TableName.autoResultMap=true 情况下, 内置的 selectBody 将不会进行 as ,使用了的需要注意!!!
feat: 新增 mybatis-plus-boot-starter-test 模块
fix: MetaObjectHandler 重载错误(解决办法是参数位置对调),填充值在泛型上支持字段类型的子类
feat: mybatis up to 3.5.5, mybatis-spring up to 2.0.5
feat: jsqlparser up to 3.2
feat: 新增 MybatisParameterHandler, 废弃 MybatisDefaultParameterHandler
feat: 分页插件加入 GBase,ClickHouse,oscar,OceanBase 数据库连接自动识别的支持
feat: Wrapper 新增api not(boolean condition, Consumer consumer)
feat: 新增 MybatisPlusInterceptor 解决 多租户和分页 插件一级和二级缓存不正确问题
feat: 新分页插件优化 size<0 时继续拼接 orderBy
feat: 新增 IdentifierGenerator 一个实现类 **ImadcnIdentifierGenerator**
fix: chainWrapper#func 强转异常
fix(mybatis-plus-generator.main): 重构生成器数据库类型转换器，修复部分支条，提交选择器测试
fix: 修复复杂情况中动态表名替换产生的问题：正则由空白检测转为单词边界检测
refactor: 重构动态表名解析器，去除正则替换程序，改为按表名位置进行替换
refactor: 将表名解析重构为访问者模式，现在不会对原有 SQL 做改动

ImadcnIdentifierGenerator 用于id生成

## 地址
[idwork](https://github.com/imadcn/idworker)
[mybatis-plus](https://github.com/baomidou/mybatis-plus)
## 1.引入maven依赖
```
<dependency>
        <groupId>com.baomidou</groupId>
        <artifactId>mybatis-plus-boot-starter</artifactId>
        <version>3.5.2</version>
    </dependency>
    <dependency>
        <groupId>com.imadcn.framework</groupId>
        <artifactId>idworker</artifactId>
        <version>1.5.0</version>
    </dependency>
```
## 2.添加zookeeper配置
```
mybatis-plus.zookeeper.serverLists=127.0.0.1:2181
```
## 3.指定mybatis-plus的id生成器


@Configuration
public class IdAutoConfig {
    @Value("${mybatis-plus.zookeeper.serverLists}")
    private String zkServerLists;
    @Bean
    public IdentifierGenerator idGenerator() {
        return new ImadcnIdentifierGenerator(zkServerLists);
    }
}
```