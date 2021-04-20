# SQL进阶2

## 表之间关系

### 一对一

### 一对多关系

- 一个人可以拥有多辆汽车，要求查询某个人拥有的所有车辆。

- 创建Person表

  ```mysql
  CREATE TABLE IF NOT EXISTS person(
  	id INT Primary Key Not Null Auto_Increment,
      name Varchar(50) Unique,
      age Int,
      sex Char(1)
  )Engine=Innodb;
  ```

- 创建Car表(car表的pid外键约束, car表中的pid 必须是在person表中)

  ```mysql
  CREATE TABLE IF NOT EXISTS car(
  	cid Int Primary Key Auto_Increment Comment 'car_id',
      cname Varchar(50) Unique,
      color Varchar(25),
      pid Int Comment 'person_id',
      Constraint fk_pid Foreign Key(pid) References person(id)
  ) Engine=Innodb;
  ```

- 查询
  ![image-20210420113550234](http://qiniuyun.qaqalone.com/imgqiniu/image-20210420113550234.png?alone)

### 多对多关系

- 学生选课，一个学生可以选修多门课程，每门课程可供多个学生选择。

- 一个学生可以有多个老师，而一个老师也可以有多个学生

- 1.创建老师表
  ![image-20210420114655874](http://qiniuyun.qaqalone.com/imgqiniu/image-20210420114655874.png?alone)

- 2.创建学生表
  ![image-20210420114702763](http://qiniuyun.qaqalone.com/imgqiniu/image-20210420114702763.png?alone)

- 3.创建学生与老师关系表
  ![image-20210420114718623](http://qiniuyun.qaqalone.com/imgqiniu/image-20210420114718623.png?alone)

- 4.添加外键

  ```mysql
  Alter Table tea_stu_rel Add Constraint fk_tid Foreign Key(tid) References teacher(tid)
  ```

  ```mysql
  Alter Table tea_stu_rel Add Constraint fk_sid Foreign Key(sid) References student(sid)
  ```

- 查询
  ![image-20210420114756024](http://qiniuyun.qaqalone.com/imgqiniu/image-20210420114756024.png?alone)

为什么要拆分表，避免大量冗余数据的出现

将学生信息和成绩拆分
![image-20210420115325303](http://qiniuyun.qaqalone.com/imgqiniu/image-20210420115325303.png?alone)

拆分后:
![image-20210420115417409](http://qiniuyun.qaqalone.com/imgqiniu/image-20210420115417409.png?alone)

## 多表查询

### 合并结果集

- 什么是合并结果集

  - 合并结果集就是把两个select语句的查询结果合并到一起

- 合并结果集的两种方式

  - UNION

    - 合并时去除重复记录

  - UNION ALL

    - 合并时不去除重复记录

- 格式：

  - UNION

    ```mysql
    #合并时去除重复记录
    SELECT * FROM 表1 UNION SELECT * FROM 表2；
    #合并时不去除重复记录
    SELECT * FROM 表1 UNION ALL SELECT * FROM 表2;
    ```

- 示例

  - 创建表
    ![image-20210420115632757](http://qiniuyun.qaqalone.com/imgqiniu/image-20210420115632757.png?alone)

  - UNION 去除重复数据

    ![image-20210420115643107](http://qiniuyun.qaqalone.com/imgqiniu/image-20210420115643107.png?alone)
    ![image-20210420115659237](http://qiniuyun.qaqalone.com/imgqiniu/image-20210420115659237.png?alone)

    

  - UNION ALL 不去除重复数据

    ![image-20210420115716966](http://qiniuyun.qaqalone.com/imgqiniu/image-20210420115716966.png?alone)
    ![image-20210420115727307](http://qiniuyun.qaqalone.com/imgqiniu/image-20210420115727307.png?alone)

- 注意事项

  - 被合并的两个结果：列数、列类型必须相同。

## 连接查询

- 什么是连接查询

  - 也可以叫跨表查询，需要关联多个表进行查询

- 什么是笛卡尔集

  - 假设集合A={a,b}，集合B={0,1,2}，
  - 则两个集合的笛卡尔积为{(a,0),(a,1),(a,2),(b,0),(b,1),(b,2)}。
  - 可以扩展到多个集合的情况

- 同时查询两个表，出现的就是笛卡尔集结果

  ```mysql
  Select * From stu;
  Select * From score; 
  ```

  查询结果:
  ![image-20210420115925470](http://qiniuyun.qaqalone.com/imgqiniu/image-20210420115925470.png?alone)

  ```mysql
  Select * From stu, score;
  ```

  查询结果是笛卡尔积:
  ![image-20210420120123868](http://qiniuyun.qaqalone.com/imgqiniu/image-20210420120123868.png?alone)

- 查询时给表起别名

  ```mysql
  Select * From stu As st, score As sc;
  ```

- 多表联查，如何保证数据正确

  - 在查询时要把**主键和外键保持一致**

    ```mysql
    #学生表的id = 成绩表的sid
    Select * From stu st, score sc 
    Where st.id=sc.sid;
    ```

    查询结果:
    ![image-20210420120351469](http://qiniuyun.qaqalone.com/imgqiniu/image-20210420120351469.png?alone)

  - 主表当中的数据参照子表当中的数据

  - 原理

    逐行判断，相等的留下，不相等的全不要
    ![image-20210420121850873](http://qiniuyun.qaqalone.com/imgqiniu/image-20210420121850873.png?alone)

  

## 根据连接方式分类

- 内连接 ( [Inner] Join ... On;  [] 表示可以省略不写)

  - 等值连接

  两个表同时出现的id号（值）才显示

  ```mysql
  Select * From stu st 
  Inner Join score sc On st.id = sc.sid;
  ```

  与多表联查约束主外键是一样，只是写法改变了。

  ON后面只写主外键

  - 如果还有条件直接在后面写where

    ```mysql
    Select * From stu st 
    Inner Join score sc On st.id = sc.sid
    Where score>=70;
    ```

  - 多表联查后面还有条件就直接写and

  - 多表连接

    - 建立学生，分数，科目表
      ![image-20210420125537086](http://qiniuyun.qaqalone.com/imgqiniu/image-20210420125537086.png?alone)
    - 使用99连接法(方法一)
      ![image-20210420125543182](http://qiniuyun.qaqalone.com/imgqiniu/image-20210420125543182.png?alone)
    - 使用内联查询(方法二)
      ![image-20210420125559080](http://qiniuyun.qaqalone.com/imgqiniu/image-20210420125559080.png?alone)
    - 查询结果
      ![image-20210420125628924](http://qiniuyun.qaqalone.com/imgqiniu/image-20210420125628924.png?alone)

  - 非等值连接

    - 示例表 (薪资等级表, 部门表, 员工表)
      ![image-20210420140938693](http://qiniuyun.qaqalone.com/imgqiniu/image-20210420140938693.png?alone)

    - 建表语句
      员工表:

      ```mysql
      CREATE TABLE `emp` (
          `empno` int(11) NOT NULL,
          `ename` varchar(255) DEFAULT NULL,
          `job` varchar(255) DEFAULT NULL,
          `mgr` varchar(255) DEFAULT NULL,
          `hiredate` date DEFAULT NULL,
          `salary` decimal(10,0) DEFAULT NULL,
          `comm` double DEFAULT NULL,
          `deptno` int(11) DEFAULT NULL,
          PRIMARY KEY (`empno`)
      ) ENGINE=InnoDB DEFAULT CHARSET=utf8;
      
      INSERT INTO `emp` VALUES (7369, '孙悟空', '职员', '7902', '2010-12-17', 800, NULL, 20);
      INSERT INTO `emp` VALUES (7499, '孙尚香', '销售人员', '7698', '2011-2-20', 1600, 300, 30);
      INSERT INTO `emp` VALUES (7521, '李白', '销售人员', '7698', '2011-2-22', 1250, 500, 30);
      INSERT INTO `emp` VALUES (7566, '程咬金', '经理', '7839', '2011-4-2', 2975, NULL, 20);
      INSERT INTO `emp` VALUES (7654, '妲己', '销售人员', '7698', '2011-9-28', 1250, 1400, 30);
      INSERT INTO `emp` VALUES (7698, '兰陵王', '经理', '7839', '2011-5-1', 2854, NULL, 30);
      INSERT INTO `emp` VALUES (7782, '虞姬', '经理', '7839', '2011-6-9', 2450, NULL, 10);
      INSERT INTO `emp` VALUES (7788, '项羽', '检查员', '7566', '2017-4-19', 3000, NULL, 20);
      INSERT INTO `emp` VALUES (7839, '张飞', '总裁', NULL, '2010-6-12', 5000, NULL, 10);
      INSERT INTO `emp` VALUES (7844, '蔡文姬', '销售人员', '7698', '2011-9-8', 1500, 0, 30);
      INSERT INTO `emp` VALUES (7876, '阿珂', '职员', '7788', '2017-5-23', 1100, NULL, 20);
      INSERT INTO `emp` VALUES (7900, '刘备', '职员', '7698', '2011-12-3', 950, NULL, 30);
      INSERT INTO `emp` VALUES (7902, '诸葛亮', '检查员', '7566', '2011-12-3', 3000, NULL, 20);
      INSERT INTO `emp` VALUES (7934, '鲁班', '职员', '7782', '2012-1-23', 1300, NULL, 10);
      ```

      部门表: 

      ```mysql
      CREATE TABLE `dept` (
          `deptno` bigint(2) NOT NULL AUTO_INCREMENT COMMENT '表示部门编号，由两位数字所组成',
          `dname` varchar(14) DEFAULT NULL COMMENT '部门名称，最多由14个字符所组成',
          `local` varchar(13) DEFAULT NULL COMMENT '部门所在的位置',
          PRIMARY KEY (`deptno`)
      ) ENGINE=InnoDB AUTO_INCREMENT=41 DEFAULT CHARSET=utf8;
      
      INSERT INTO `dept` VALUES (10, '财务部', '北京');
      INSERT INTO `dept` VALUES (20, '调研部', '上海');
      INSERT INTO `dept` VALUES (30, '销售部', '王者峡谷');
      INSERT INTO `dept` VALUES (40, '运营部', '腾讯大楼');
      ```

      薪资表: 

      ```mysql
      CREATE TABLE `salgrade` (
          `grade` bigint(11) NOT NULL AUTO_INCREMENT COMMENT '工资等级',
          `lowSalary` int(11) DEFAULT NULL COMMENT '此等级的最低工资',
          `highSalary` int(11) DEFAULT NULL COMMENT '此等级的最高工资',
          PRIMARY KEY (`grade`)
      ) ENGINE=InnoDB AUTO_INCREMENT=6 DEFAULT CHARSET=utf8;
      
      INSERT INTO `salgrade` VALUES (1, 700, 1200);
      INSERT INTO `salgrade` VALUES (2, 1201, 1400);
      INSERT INTO `salgrade` VALUES (3, 1401, 2000);
      INSERT INTO `salgrade` VALUES (4, 2001, 3000);
      INSERT INTO `salgrade` VALUES (5, 3001, 9999);
      ```

    - 查询所有员工的姓名，工资，所在部门的名称以及工资的等级
      分析过程:

      - 1.查询所有员工的姓名，工资

        ```mysql
        Select ename, salary From emp;
        ```

        查询结果:
        ![image-20210420141001688](http://qiniuyun.qaqalone.com/imgqiniu/image-20210420141001688.png?alone)

      - 2.查询所有员工的姓名，工资和所在部门

        ```mysql
        Select ename, salary, dname
        From emp
        Join dept
        On dept.deptno = emp.deptno;
        ```

        查询结果:
        ![image-20210420141014332](http://qiniuyun.qaqalone.com/imgqiniu/image-20210420141014332.png?alone)

      - 3.查询所有员工的姓名，工资和所在部门及工资等级

        ```mysql
        #方式一
        Select ename, salary, dname, grade
        From emp, dept, salgrade s
        Where emp.deptno=dept.deptno 
        And salary Between s.lowsalary And s.highsalary
        #方式二
        Select ename, salary, dname, grade
        From emp e
        Join dept d On e.deptno=d.deptno
        Join salgrade s On e.salary Between s.lowsalary And s.highsalary
        ```

        查询结果: 
        ![image-20210420143718796](http://qiniuyun.qaqalone.com/imgqiniu/image-20210420143718796.png?alone)

  - 自连接
    当前表与自身的连接查询，关键点在于虚拟化出一张表给一个别名

- 外连接

  - 左外连接（左连接）

    - 两表满足条件相同的数据查出来，如果左边表当中有不相同的数据，也把左边表当中的数据查出来。

    - 左边表当中的数据全部查出，右边表当中，只查出满足条件的内容
      ![image-20210420145458652](http://qiniuyun.qaqalone.com/imgqiniu/image-20210420145458652.png?alone)

    - 使用内连接时，周七不会查出来，没有成绩，缺考了。把所有考过试的学生分数查出来。
      内连接:方式一

      ![image-20210420145514595](http://qiniuyun.qaqalone.com/imgqiniu/image-20210420145514595.png?alone)

      内连接: 方式二
      ![image-20210420145523846](http://qiniuyun.qaqalone.com/imgqiniu/image-20210420145523846.png?alone)

      查询结果: 没有周七
      ![image-20210420145532945](http://qiniuyun.qaqalone.com/imgqiniu/image-20210420145532945.png?alone)

    - 使用左连接查询所有学生及学生的考试分数

      - 左连接会把左表当中的数据全部查出，右表当中只查出满足条件的数据
        ![image-20210420145550043](http://qiniuyun.qaqalone.com/imgqiniu/image-20210420145550043.png?alone)
        ![image-20210420145558636](http://qiniuyun.qaqalone.com/imgqiniu/image-20210420145558636.png?alone)
      - 可以省略outer不写
      - 查询时，两个表可以不需要建立主外键约束

  - 右外连接（右连接）

    - 右连接会把右当中的数据全部查出，左表当中只查出满足条件的数据
      ![image-20210420145728271](http://qiniuyun.qaqalone.com/imgqiniu/image-20210420145728271.png?alone)

    - 右边表当中 的所有数据全部查出，左边表只查出满足条件的记录
    - 站在表的角度去看，使用左连接就把左边表当中的内容全部查出，右边查出满足条件的。
    - 使用右连接，就把右边表当中的数据全部查出。左边查出满足条件的。

- 自然连接(一般使用的都是自然连接)
  排除多次出现的列, 使每个列只返回一次

  - 连接查询会产生无用笛卡尔集，我们通常使用主外键关系等式来去除它。
  - 而自然连接无需你去给出主外键等式，它会自动找到这一等式
  - 也就是说不用去写条件
  - 要求

    - 两张连接的表中列名称和类型完全一致的列作为条件
    - 会去除相同的列

### 子查询

- 什么是子查询

  - 一个select语句中包含另一个完整的select语句。
  - 或两个以上SELECT，那么就是子查询语句了。

- 子查询出现的位置

  - where后，把select查询出的结果当作另一个select的条件值
  - from后，把查询出的结果当作一个新表；

- 示例表  (薪资等级表, 部门表, 员工表)
  ![image-20210420164458595](http://qiniuyun.qaqalone.com/imgqiniu/image-20210420164458595.png?alone)

- 使用

  - 查询与项羽同一个部门人员工

    - 1.先查出项羽所在的部门编号
      ![image-20210420164542102](http://qiniuyun.qaqalone.com/imgqiniu/image-20210420164542102.png?alone)

    - 2.再根据编号查同一部门的员工
      ![image-20210420164555281](http://qiniuyun.qaqalone.com/imgqiniu/image-20210420164555281.png?alone)

      <p style="color:red;">把第1条查出来的结果当第2天语句的条件</p>

  - 查询工资高于程咬金的员工

    - 1.查出程咬金的工资
      ![image-20210420164719599](http://qiniuyun.qaqalone.com/imgqiniu/image-20210420164719599.png?alone)

    - 2.再去根据查出的结果查询出大于该值的记录员工名称
      ![image-20210420164729734](http://qiniuyun.qaqalone.com/imgqiniu/image-20210420164729734.png?alone)

  - 工资高于30号部门 所有人的员工信息(在整个员工表中, 员工工资高于30号部门中最高工资的一群人)

    - 1.先查出30号部门工资最高的那个人
      ![image-20210420164807740](http://qiniuyun.qaqalone.com/imgqiniu/image-20210420164807740.png?alone)

    - 2.再到整个表中查询大于30号部门工资最高的那个人
      ![image-20210420164814352](http://qiniuyun.qaqalone.com/imgqiniu/image-20210420164814352.png?alone)

  - 查询工作和工资与妲己完全相同的员工信息

    - 1.先查出妲已的工作和工资
      ![image-20210420165137942](http://qiniuyun.qaqalone.com/imgqiniu/image-20210420165137942.png?alone)

    - 2.根据查询结果当作条件再去查询工作和工资相同的员工
    - 由于是两个条件，使用 IN进行判断
      ![image-20210420165144055](http://qiniuyun.qaqalone.com/imgqiniu/image-20210420165144055.png?alone)

  - 有2个以上直接下属的员工信息

    - 1.对所有的上级编号进行分组
      ![image-20210420165328356](http://qiniuyun.qaqalone.com/imgqiniu/image-20210420165328356.png?alone)

    - 2.找出大于2个的，大于2个说明有两个下属
      ![image-20210420165333843](http://qiniuyun.qaqalone.com/imgqiniu/image-20210420165333843.png?alone)

    - 3.把上条的结果当作员工编号时行查询
      ![image-20210420165340043](http://qiniuyun.qaqalone.com/imgqiniu/image-20210420165340043.png?alone)

  - 查询员工编号为7788的员工名称、员工工资、部门名称、部门地址
    ![image-20210420165346306](http://qiniuyun.qaqalone.com/imgqiniu/image-20210420165346306.png?alone)

### 自连接

- 求7369员工编号、姓名、经理编号和经理姓名
  ![image-20210420165356500](http://qiniuyun.qaqalone.com/imgqiniu/image-20210420165356500.png?alone)

- 以上这种方法只能查询出一个经理的名称
- 自连接：自己连接自己，起别名
  ![image-20210420165401007](http://qiniuyun.qaqalone.com/imgqiniu/image-20210420165401007.png?alone)

