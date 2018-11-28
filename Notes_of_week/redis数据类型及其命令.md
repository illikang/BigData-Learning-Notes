<!-- GFM-TOC -->
* [Redis数据类型](#Redis数据类型)
    * [概述](#概述)
    * [key](#key)
      * [key键](#key键)
      * [key命令](#key命令)
    * [String](#String)
      * [String字符串](#String字符串)
      * [String值命令](#key命令)
    * [hash](#hash)
      * [hash哈希](#hash哈希)
      * [hash值命令](#快速选择)
    * [list](#list)
      * [list列表](#list列表)
      * [list值命令](#list值命令)
    * [set](#set)
      * [set集合](#set集合)
      * [set值命令](#set值命令)
    * [zset](#set)
      * [zset有序集合](#zset有序集合)
      * [zset值命令](#zset值命令)

# Redis数据类型
## 概述
Redis是基于key-value的存储系统。
它的值（value）支持五种数据类型：String(字符串),hash（哈希）,list（列表）,set（集合）和zset(sorted set：有序集合)。
## key
### key键

### key命令

## String
### String字符串
1. String是redis最基本的类型，其他集合类型都是基于String构建的。
2. String类型是二进制安全的，即redis的string可以包含任何数据，比如jpg图片或者序列化对象。
3. String类型的值最大能存储512MB.
### String值命令
1. SET key value ：设定指定key的值。
```
set name "Leon"
```
2. GET key:获取指定key的值。
```
get name
>"Leon"
```
3. ETRANGE key start end :返回 key 中字符串值的子字符。
```
getrange name 1 2
//注意下标索引是从0开始的，所以这里1 2返回的是字符串“Leon”的的第2、3个字母。
>""eo
```
4. GETSET key value：将给定 key 的值设为 value ，并返回 key 的旧值(old value)。
```
getset name illikang
>"Leon"
```
5.SETBIT key offset:对 key 所储存的字符串值，设置或清除指定偏移量上的位(bit)。
6.GETBIT key offset：对 key 所储存的字符串值，获取指定偏移量上的位(bit)。
```
//bit 默认被初始化为0
set bitmap "test"
getbit bitmap 20
//在没有设置过bitmap偏移量20位上值时，取出的是0
>(integer) 0
//设置位值以后，再取出的值为设置值。
setbit bitmap 20 1
getbit bitmap 20
>(integer) 1
```
补充：bit位存储是个很强大的功能。bit位只能存储0或1，可以用户很多场景，比如性别，商品是否有库存，座位是否已经预定等等。
```
MGET key1 [key2..]：获取所有(一个或多个)给定 key 的值。
```
mget name bitmap
>"illikang"
>"te{t"
```
补充：这里bitmap取出的值变为“te{t”是因为之前的位操作，改变了字符串的内容
```
```
```
```
```
```
## hash
### hash哈希
### hash值命令

## list
### list列表
### list值命令

## set
### set集合
### set值命令

## zset
### zset有序集合
### zset值命令

