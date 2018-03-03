
##项目简介
设计大数据集成分析平台，主要功能是多种数据库及文件数据；访问；采集；解析，清洗，ETL，同时可以编写模型支持后台统计分析算法。设计数据可视化平台 ，应用于大数据的可视化和互动操作。为此，根据“先进实用、稳定可靠”的原则设计本数据处理平台。
* 系统高可用、高性能、易扩展，安全稳定，实时可靠，满足用户的需要。
* 系统可以进行扩展，增加数据的种类和数量。
* 系统可以复用别的软件和算法。
* 与其他大数据处理系统兼容性良好。
* 系统使用c#开发,以服务或是中间件方式部署。

##数据存储方案

![数据存储方案](/doc/datastoragescheme.jpg)

数据可以同时存入多种sql数据库服务器里面，每个数据库服务器中包含多个数据库，每个数据库中包含多张数据表。
* 服务器配置见服务器配置存储空间配置。
* 数据库配置见服务器配置数据库配置。
* 数据表配置见服务器配置数据表配置。

##逻辑架构

![逻辑架构](/doc/logicalarchitecture.jpg)

##服务器配置
基于数据存放状态,配合算法实现快速定位数据所在区间。

##配置同步规则
多个代理访问点的情况下防止数据配置文件差异化造成数据不准确性。
* 存储空间配置
	* 某种数据存入数据库时根据此配置文件申请和使用资源，对此数据库服务器下的数据库和数据表作相应限制。
	* 常用配置属性有唯一编号，数据存放位置，数据存储空间大小，是否自动创建库，数据库服务器连接参数，数据库类型，数据库连接状态，存储数据结构标识，记录数据索引字段等。
* 数据库配置
	* 常用配置属性有存储数据结构编号，数据库服务器编号，数据库唯一编号，数据库名，数据库使用空间大小，数据库存放位置，是否自动创建表，数据库是否可用，数据库是否可写入数据等。
* 数据表配置
	* 常用配置属性有存储数据结构编号，数据库服务器编号，数据库编号，数据表唯一编号，表名，表使用空间大小，表存放记录总数，是否自动创建表，数据库是否可用，数据库是否可写入数据等。
* 数据写入规则
	* 定位写入表位置
	* 根据服务器配置定位可写入表位置返回。
* 服务器配置规则更新
	* 定位写入表位置时检测服务器，数据库，数据表可用状态，写入状态，存储空间，发现状态不一致时自动更新配置。
	* 数据写入表后更新服务器配置中数据表字段索引状态配置。
	* 数据存储状态记录
	* 更新数据存储记录
	* 更新数据库数据后，更新服务器配置中数据表字段索引状态配置，检查服务器、数据库、数据表存储空间是否占满，占满后更新可用状态为不可写入。
	* 定位数据存位置
	* 根据查询条件去服务器配置数据表字段索引状态配置中逐步缩小查询区间，同时满足所有条件的表即为数据存放位置。

##部署方式
* 统一服务代理
	* 以服务的方式部署一个访问点，所有需要操作数据库的请求，都由部署的这个访问点代理发起数据库操作。
* 分布式代理
	* 部署多个数据访问点，每个访问点之间不冲突，所有的服务器配置更新都实时更新到多个访问点。

##数据库访问方式
* 为了更好的兼容消息中间件，实时操作，非实时操作等，提供以下几种操作数据库方式。
	* 单线程同步
	* 单线程异步
	* 多线程同步
	* 多线程异步

##数据操作
* 数据写入
	* 根据数据写入规则定位可写入位置写入数据库后，更新服务器配置。
	
![数据写入](/doc/datawrite.jpg)

* 数据查询
	* 根据数据存储状态记录定位数据位置，然后查询数据，满足查询条件即返回。如前500条，查询的数据记录满500立即返回。
	
![数据查询](/doc/datasearch.jpg)

* 数据删除
	* 根据数据库主键ID去数据存储状态记录定位数据位置，然后执行记录删除操作，执行成功后更新数据表字段索引状态配置。
	
![数据删除](/doc/datadel.jpg)

* 数据修改
	* 根据数据库主键ID去数据存储状态记录定位数据位置，然后执行记录修改操作，执行成功后更新数据表字段索引状态配置。
	
![数据修改](/doc/dataupdate.jpg)

##数据库管理
* 回收机制
	* 增加数据库回收缓存和垃圾数据清理操作，当应用场景会消耗非常大的资源时数据库服务器自动的回收机制无法实时回收导致操作运行缓慢前，可以主动发起回收操作。
	
![回收机制](/doc/datarecover.jpg)

* 数据迁移
	* 数据频繁的增删改导致系统表中的数据存在空白空间，和数据表记录的存放顺序变的混乱，增加数据迁移操作。
	
![数据迁移](/doc/datamove.jpg)

##数据可靠性保证
 数据安全性要求非常高时，对每个数据库服务器进行容灾备份设置；数据访问量非常大时可以设置主从分离；与此系统不会有任何冲突。
* 缓存交互
	* 减少数据库查询负担，增加频繁访问数据写入第三方缓存，增加缓存数据定时更新操作。

##系统特点
与其他大数据处理软件兼容性良好，在现有系统上增加此架构做数据操作简单，快速上手。