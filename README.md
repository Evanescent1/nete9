# nete9
&ensp;&ensp&ensp;&ensp;一对一联系：表和表之间一一对应（例如：员工信息表和工资表）
一对多联系：学生信息表和课程表（一个学生可以报多个课程）     利用外键技术（fk）
被依赖的表称为主键表，依赖的表称为外键表，fk需要作用在外键表中的某个字段，表示所依赖的表的主键（pk）
多对多联系：利用外键表FK把两个主键表（pk)，利用一对多的关系，定义在一张表里，
查select（选择）       曾insert (插入）          delete删除                update（更改）
实体完整性，每一条记录都是唯一的，用主键实现（pk）
列的完整性（定义值来限制）
参考完整性用主外键来实现
冗余越小，访问数据的复杂度越高，违反范式可以提供性能，不违反可以减少冗余，增加空间
第一范式：同一类型，出现了重复的列，一个列不能定义多个值（每个字段只能有一个值）
第二范式：当表中出现复合主键，非主键需要与整个主键有之间相关，如果只与复合主键的一项有关，则违反第二范式。需要把这个字段取出，和相关的主键定义成一个新表，
第三范式：非主属性直接不能产生依赖关系
主键作用在某个字段上，这个字段的值不能为空。
唯一键（uk),但主键作用在某个字段上，而另一字段也需要唯一，加上唯一键（uk),在唯一键上的字段的值都不相同，
但可以做用N个唯一键（允许设置空，null)，
外键表依赖主键或者唯一键
数据存储：二进制方式保存（raw)，裸文件系统，不方便管理，速度快。
Oracle的RAC（二进制存放）
逻辑层：存放的数据信息，表和表之间的关系
试图层：用户访问的数据不一定全部的数据
XML半结构化方式存放，扩展的标记性语言。
实现多实例（测试），基于二进制
创建三个目录（实例），代表三个端口，每个目录放置数据库相应的文件
mkdir /mysql/{3306,3307,3308}/{etc,socket,log,pid} -pv
修改所有者
chown -R mysql.mysql *
需要利用安装的mysql的/usr/local/mysql/scripts/mysql_install_db 下的文件生成各个实例的数据库
./scripts/mysql_install_db --user=mysql --datadir=/mysql/3306
./scripts/mysql_install_db --user=mysql --datadir=/mysql/3307
./scripts/mysql_install_db --user=mysql --datadir=/mysql/3308
准备数据库的相应的配置文件（拷贝rpm包的版本的配置文件到/mysql/3306/etc/下）
cp   /etc/my.cnf  /mysql/3306/etc/
修改文件
[mysqld]
port=3306（指定端口）
datadir=/mysql/3306/设置文件路径）
socket=/mysql/3306/mysql.sock
[mysqld_safe]
log-error=/mysql/3306/log/mariadb.log
pid-file=/mysql/3306/pid/mariadb.pid 
拷贝到其他实例的etc下  
cp /mysql/3306/etc/my.cnf  /mysql/3307/etc/
sed -i 's/3306/3307/'  ../../3307/etc/my.cnf
cp /mysql/3306/etc/my.cnf  /mysql/3308/etc/
sed -i 's/3306/3307/'  ../../3308/etc/my.cnf(修改端口号）
准备服务的启动脚本（拷贝系统脚本\\172.18.0.1\34\files\mysqld到/mysql/3306/目录下）修改权限700
port=3306
mysql_user="root"
#mysql_pwd="centos"                                                         
cmd_path="/usr/local/mysql/bin"指定利用安装好的mysql启动服务的脚本路径
拷贝每个实例的目录下（修改端口）
cp  mysqld  ../3307/
cp  mysqld  ../3308/
加上执行权限chmod +x  ../3308/
./mysqld start启动脚本
mysql -uroot -h127.0.0.1 -P 3308指定端口
安全加固mysql_secure_installation  -S /mysql/3308/socket/mysql.sock
拷贝到init.d目录下
cp mysqld /etc/init.d/mysqld3308
service mysqld3308 stop可以利用服务停止或启动
chkconfig --add  mysqld3308添加到服务列表
 inner join交叉连接
  left左   outer  join
  right  outer join右连接
  select语句的处理过程：先处理from语句（from后面跟表明），
  确定要查询的表，然后where语句从前面定义的表，挑出特定的行，进行group  by分组统计，一旦跟了group by语句，
  在select语句和from之间只能跟两种类型（1.group  by定义的字段2.汇集统计的函数）当group  by对字段分组之后，
  可以用having进行过滤，可以把过滤的内容进行order  by排序，把挑选出的行在进行select选出要显示行用limit选出前
  多少个，得出结果
  视图：虚拟的表，数据来自真实的表里，对真实表的查询结果
查看表的状态
show table status like 'students';
创建视图
create view v_students（视图名） as select stuid,name,age from students;
查看是否是视图         Comment: VIEW
 show table status like 'v_students'\G                 
视图在数据库中只有表结构，数据都来自真实的文件
查看视图的定义
show create view v_students\G
对视图添加就是对真实表的添加
（对视图添加，如果不符合视图定义，不显示，但会添加到真实的表里，对视图操作，就是对真实的表操作）
MYISAM:存储数据大，当数据库达到T级别，会拆分。不支持事物，表级别的锁，不支持数据的缓存，数据和索引是分离的，外键技术
InnoDB:支持事物，行级锁，数据的缓存，数据和索引在一个地方
事物：由一系列动作（命令），组合起来的整体。把多条命令组合起来放到begin里面，一旦执行事物，必须所有的命令都执行（原子性）。如果事物在执行过程中，因为外界因素中断，当恢复之后，因为事物不完整，它会把之前的操作取消（rollback)。
表级别的锁：外人无法修改表操作，数据无法同时做修改，并发访问受限制
行级锁：有MVCC，
MVCC:存储引擎在创建好的表上，添加了两个隐藏字段create创建  delete删除，两个字段添加了每一行记录创建的时间（事物的编号），和删除的时间，一旦行被更改了，会重新刷新时间。当用户执行了事物（事物的编号tran），会在表里找到比它的编号靠前或相等的显示（用户的编号大于或等于表的事物的编号）。用户看到的状态是查询那一刻的表的状态。当数据被删除，会在删除时间上加上时间，但数据不会立即删除，只是查询不到。
Memoty:基于内存的存储引擎，效率高，由于外界因素容易丢数据
单机性能难于扩展，应用集群，搭建多个服务器，数据分流，用户的访问先发送到调度器proxysql，读写操作放置不同的服务器，修改，写，删除（update,delete,insert)放置一个服务器,读select操作放置几个服务器。当写服务器发生修改时，需要同步到读服务器上，实现主从复制，主服务器压力过大，就需要添加一个服务器（BLACKHOLE黑洞)，只当缓存用，不用写磁盘。当主服务器发生变化，只把数据同步给它，由它来实现主从复制。
BLACKHOLE黑洞：基于内存的存储引擎
主键和数据放在一个文件里，称为聚簇索引
在聚簇索引里分主键索引和二级索引
主键索引：主键和数据都放在叶子节点里（一个数据块里），而且 主键就是索引，找到了索引，就相当于找到了主键，也就找到了数据。
二级索引：建立的索引不是主键，索引会和主键放在一起，而主键和数据放在一起。查询数据利用索引找到主键，通过主键索引找到数据。
数据的唯一性决定了主键只能有一个。
稠密索引：每一个索引都对应了一个数据（叶子节点）
稀疏索引：每个索引对应的数据不完整（分支节点）
简单索引：对一个字段创建索引
组合索引：先对一个字段做索引，在前面索引的基础上，在对另一个字段做索引。在后面定义的索引不能跳过前面定义的索引，直接查询。在组合索引了，后面的索引可以单独建立个简单索引
索引
索引类型：
B+ TREE、HASH、R TREE
聚簇（集）索引、非聚簇索引：数据和索引是否存储在一起
主键索引、二级（辅助）索引
稠密索引、稀疏索引：是否索引了每一个数据项
简单索引、组合索引
左前缀索引：取前面的字符做索引，复合索引
覆盖索引：从索引中即可取出要查询的数据，性能高
