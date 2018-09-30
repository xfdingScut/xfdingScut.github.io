---

title: SQL练习
toc: true
date: 2018-09-28 15:54:51
tags: [SQL]
categories: [程序语言]
---

# 题目

## 创建

```sql
use dxf;

# 创建学生表
create table `Student`(
`s_id` VARCHAR(20),
`s_name` VARCHAR(20) NOT NULL DEFAULT '',
`s_birth` VARCHAR(20) NOT NULL DEFAULT '',
`s_sex` VARCHAR(10) NOT NULL DEFAULT '',
PRIMARY KEY(`s_id`)
);
# 创建课程表
create table `Course`(
`c_id` VARCHAR(20),
`c_name` VARCHAR(20) NOT NULL DEFAULT '',
`t_id` VARCHAR(20) NOT NULL,
PRIMARY KEY(`c_id`)
);
# 创建教师表
create table `Teacher`(
`t_id` VARCHAR(20),
`t_name` VARCHAR(20) NOT NULL DEFAULT '',
PRIMARY KEY(`t_id`)
);
# 创建成绩表
create table `Score`(
`s_id` VARCHAR(20),
`c_id` VARCHAR(20),
`s_score` INT(3),
PRIMARY KEY(`s_id`,`c_id`)
);

# 插入学生表测试数据
insert into Student values('01' , '赵雷' , '1990-01-01' , '男');
insert into Student values('02' , '钱电' , '1990-12-21' , '男');
insert into Student values('03' , '孙风' , '1990-05-20' , '男');
insert into Student values('04' , '李云' , '1990-08-06' , '男');
insert into Student values('05' , '周梅' , '1991-12-01' , '女');
insert into Student values('06' , '吴兰' , '1992-03-01' , '女');
insert into Student values('07' , '郑竹' , '1989-07-01' , '女');
insert into Student values('08' , '王菊' , '1990-01-20' , '女');
# 课程表测试数据
insert into Course values('01' , '语文' , '02');
insert into Course values('02' , '数学' , '01');
insert into Course values('03' , '英语' , '03');
# 教师表测试数据
insert into Teacher values('01' , '张三');
insert into Teacher values('02' , '李四');
insert into Teacher values('03' , '王五');
# 成绩表测试数据
insert into Score values('01' , '01' , 80);
insert into Score values('01' , '02' , 90);
insert into Score values('01' , '03' , 99);
insert into Score values('02' , '01' , 70);
insert into Score values('02' , '02' , 60);
insert into Score values('02' , '03' , 80);
insert into Score values('03' , '01' , 80);
insert into Score values('03' , '02' , 80);
insert into Score values('03' , '03' , 80);
insert into Score values('04' , '01' , 50);
insert into Score values('04' , '02' , 30);
insert into Score values('04' , '03' , 20);
insert into Score values('05' , '01' , 76);
insert into Score values('05' , '02' , 87);
insert into Score values('06' , '01' , 31);
insert into Score values('06' , '03' , 34);
insert into Score values('07' , '02' , 89);
insert into Score values('07' , '03' , 98);
```



## 查询

1. **查询"01"课程比"02"课程成绩高的学生的信息及课程分数（左连接）**

   ```sql
   select Student.* , Score.c_id, Score.s_score 
   from Student join Score 
   on Student.s_id = Score.s_id
   where Student.s_id in (
   	select a.s_id from 
   		(select * from Score where Score.c_id = '01') as a left join 
           (select * from Score where Score.c_id = '02') as b 
           on a.s_id = b.s_id
   		where a.s_score > b.s_score or b.s_score is null
   );
   ```

2. **查询"01"课程比"02"课程成绩低的学生的信息及课程分数（右连接）**

   ```sql
   select Student.* , Score.c_id, Score.s_score 
   from Student join Score 
   on Student.s_id = Score.s_id
   where Student.s_id in (
   	select b.s_id from 
   		(select * from Score where Score.c_id = '01') as a right join 
           (select * from Score where Score.c_id = '02') as b 
           on a.s_id = b.s_id
   		where a.s_score < b.s_score or a.s_score is null
   );
   ```

3. **查询平均成绩大于等于60分的同学的学生编号和学生姓名和平均成绩（汇总函数：avg，分组函数）**

   ```sql
   select Student.s_id, Student.s_name, avg(Score.s_score) as a  
   from Student left join Score 
   on  Student.s_id = Score.s_id group by Student.s_id
   having a>=60;
   ```

4. **查询平均成绩小于60分的同学的学生编号和学生姓名和平均成绩(包括有成绩的和无成绩的)（汇总函数，逻辑操作符）**

   ```sql
   select Student.s_id, Student.s_name, avg(ifnull(Score.s_score,0)) as a  
   from Student left join Score 
   on  Student.s_id = Score.s_id group by Student.s_id
   having a < 60;
   ```

5. **查询所有同学的学生编号、学生姓名、选课总数、所有课程的总成绩（汇总函数：count\sum）**

   ```sql
   select Student.s_id, Student.s_name,count(Score.c_id), sum(ifnull(Score.s_score,0)) 
   from Student left join Score 
   on Student.s_id = Score.s_id 
   group by Student.s_id;
   ```

6. **查询"李"姓老师的数量（逻辑操作符：like）** 

   ```sql
   select count(Teacher.t_name)
   from Teacher
   where Teacher.t_name like '李%';
   ```

7. **查询学过"张三"老师授课的同学的信息（多表查询）** 

   ```sql
   select Student.* 
   from Student
   left join Score
   on Student.s_id = Score.s_id
   where Score.c_id = (
   	select Course.c_id 
   	from Course
       left join Teacher
       on Course.t_id = Teacher.t_id
       where Teacher.t_name = '张三'
   );
   ```

8. **查询没学过"张三"老师授课的同学的信息（逻辑操作符）** 

   ```sql
   select Student.*
   from Student
   where Student.s_id not in(
   	select Student.s_id 
   	from Student
   	join Score
   	on Student.s_id = Score.s_id
   	where Score.c_id in (
   		select Course.c_id 
   		from Course
   		join Teacher
   		on Course.t_id = Teacher.t_id
   		where Teacher.t_name = '张三'
   	)
   );
   ```

9. **查询学过编号为"01"并且也学过编号为"02"的课程的同学的信息（逻辑操作符）**

   ```sql
   select student.* 
   from student 
   where student.s_id in(
   	select score.s_id
       from score
       where score.c_id ='01' and score.s_id in(
   		select score.s_id
           from score
           where score.c_id = '02'
       )
   );
   ```

10. **查询学过编号为"01"但是没有学过编号为"02"的课程的同学的信息（逻辑操作符）**

    ```sql
    select student.* 
    from student 
    where student.s_id in(
    	select score.s_id
        from score
        where score.c_id ='01' and score.s_id not in(
    		select score.s_id
            from score
            where score.c_id = '02'
        )
    );
    ```

11. **查询没有学全所有课程的同学的信息（分组函数和汇总函数）**

    ```sql
    select student.*
    from student
    join score
    on student.s_id = score.s_id
    group by student.s_id
    having count(score.c_id) < (select count(course.c_id) from course);
    ```

12. **查询至少有一门课与学号为"01"的同学所学相同的同学的信息 （逻辑操作符）**

    ```sql
    select student.* 
    from student
    join score
    on student.s_id = score.s_id
    group by student.s_id
    having student.s_id != '01' and exists(
    	select score.c_id 
    	from score
    	where score.c_id in (
    		select score.c_id 
    		from score
    		where score.s_id = '01'
    	)
    );
    ```

13. **查询和"01"号的同学学习的课程完全相同的其他同学的信息 **

    ```sql
    
    ```

14. **查询没学过"张三"老师讲授的任一门课程的学生姓名** 

    ```sql
    
    ```

15. **查询两门及其以上不及格课程的同学的学号，姓名及其平均成绩** 

    ```sql
    
    ```

16. **检索"01"课程分数小于60，按分数降序排列的学生信息**

    ```sql
    
    ```

17. **按平均成绩从高到低显示所有学生的所有课程的成绩以及平均成绩**

    ```sql
    
    ```

18. **查询各科成绩最高分、最低分和平均分：以如下形式显示：课程ID，课程name，最高分，最低分，平均分，及格率，中等率，优良率，优秀率及格为>=60，中等为：70-80，优良为：80-90，优秀为：>=90**

    ```sql
    
    ```

19. **按各科成绩进行排序，并显示排名(实现不完全)**

    ```sql
    
    ```

20. **查询学生的总成绩并进行排名**

    ```sql
    
    ```

# 测试数据库附图

![image-20180928164519967](https://ws2.sinaimg.cn/large/006tNc79ly1fvpckz1if7j30eg0g6q55.jpg)

![image-20180928161612758](https://ws3.sinaimg.cn/large/006tNc79ly1fvpbqnkiwbj30l80fo0tx.jpg)

![image-20180928161633310](https://ws3.sinaimg.cn/large/006tNc79ly1fvpbr24022j30jw0fuq3w.jpg)

![image-20180928161714469](https://ws1.sinaimg.cn/large/006tNc79ly1fvpbrqsycpj30mk0l2776.jpg)

