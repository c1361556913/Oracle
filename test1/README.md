   ## 实验一：分析SQL执行计划，执行SQL语句的优化指导     

*2016级软工三班*    *陈荣杰*       *201610414302*  
   ###查询1：
<pre><code>
SELECT d.department_name，count(e.job_id)as "部门总人数"，
avg(e.salary)as "平均工资"
from hr.departments d，hr.employees e
where d.department_id = e.department_id
and d.department_name in ('IT'，'Sales')
GROUP BY department_name;
</code></pre>

查询结果：  
<pre>
部门总人数   平均工资
---------- ----------
IT
	 5	 5760

Sales
	34 8955.88235
</pre>

   ###查询2：
<pre><code>
SELECT d.department_name，count(e.job_id)as "部门总人数"，
avg(e.salary)as "平均工资"
FROM hr.departments d，hr.employees e
WHERE d.department_id = e.department_id
GROUP BY department_name
HAVING d.department_name in ('IT'，'Sales');
</code></pre>

查询结果： 
<pre>
部门总人数   平均工资
---------- ----------
IT  
	 5	 5760

Sales  
	34 8955.88235
</pre>
比较两次查询，查询1所用时间更少。查询1更优<br>
对查询一使用sqldeveloper的优化指导工具进行优化指导<br>
建议为：考虑运行可以改造物理方案设计的访问编导或者创建推荐的索引。  

设计自己的查询语句：
<pre><code>
SELECT d.department_name，count(e.job_id)as "部门总人数"，
avg(e.salary)as "平均工资"
from hr.departments d，hr.employees e
where d.department_id = e.department_id
and d.department_name in ('IT'，'Sales')
GROUP BY department_name;
</code></pre>  
相比于查询2，该查询使用的分类语句较少，能够一定程度提高效率。
