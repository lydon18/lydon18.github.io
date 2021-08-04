# MySQL(InnoDB)根据ibd文件恢复数据

### 前提: 

1. 知道表名(如果连表名都不知道那就GG了)

2. 知道丢失数据的表结构(如果不知道表结构可以尝试用frm文件恢复) 

   ### 步骤

1. 创建一张表,表结构与原表结构一致;

   ```sql
   CREATE TABLE `tb_column` (
     `id` bigint(20) NOT NULL AUTO_INCREMENT,
     `del_flag` tinyint(1) NOT NULL DEFAULT '0' COMMENT '逻辑删除字段  0 意为正常(未删除) 1 意为已删除',
     `name` varchar(100) NOT NULL DEFAULT '' COMMENT '栏目名称',
     `pid` bigint(20) NOT NULL DEFAULT '-1' COMMENT '上级ID',
     `type` int(1) NOT NULL DEFAULT '1' COMMENT '类别 1邀标公告 2培训超市',
     `sort` bigint(20) DEFAULT NULL COMMENT '排序',
     PRIMARY KEY (`id`)
   ) ENGINE=InnoDB DEFAULT CHARSET=utf8mb4 COMMENT='栏目类别'
   ```

2. 分离表空间(使当前的.ibd数据文件和.frm分离)

   ``` sql
   ALTER TABLE tb_column DISCARD TABLESPACE;
   ```

3. 复制备份的.ibd文件覆盖新的表数据(覆盖data目录下新建表的.ibd文件)

   如果是linux系统需要修改文件权限

   ```shell
   chown mysql:mysql <table_name>.ibd
   ```

4. 导入表空间

   ```sql
   ALTER TABLE tb_column IMPORT TABLESPACE;
   ```

   ### 注意

   执行第4步时可能出现如下问题:

   ```sql
   Error Code: 1808. Schema mismatch (Table has ROW_TYPE_DYNAMIC row format, <table_name>.ibd file has ROW_TYPE_COMPACT row format.)
   ```

   这是由于mysql 5.6的文件恢复到mysql 5.7版本导致的错误;此时需要

   ```
   DROP TABLE tb_column; 
   ```

   

   需要在建表语句后面添加  

   ```
   ROW_FORMAT=COMPACT
   ```

   删除表然后重新执行上面的步骤. 直到第4步成功 , 此时数据已经可以恢复

   

