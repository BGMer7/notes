# MyBatis-Plus

### Github地址

https://github.com/BGMer7/ORM-starter/tree/master/MyBatisPlus-starter

参考：

[多数据源配置MyBatisPlus(十八)下-阿里云开发者社区 (aliyun.com)](https://developer.aliyun.com/article/851653#slide-2)

[mybatis-plus的各种实战demo_@我不是大鹏的博客-CSDN博客_mybatisplusdemo](https://blog.csdn.net/qq_38132995/article/details/113946543)

[关于service层抛出 BindingException: Invalid bound statement (not found)_Gy-1-__的博客-CSDN博客](https://blog.csdn.net/YiQieFuCong/article/details/107520321)

BindingException: Invalid bound statement (not found) 

绑定问题除了可能是Mapper没有和xml对应上之外，也有可能是调用的时候没有选取到合适的Bean。

因为直接调用Mapper的时候是可以正常实现功能的，也就是说从Mapper到xml是正常的，但是出现绑定错误，说明是从Service调用Mapper的时候出现了问题。

