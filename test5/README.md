## 实验五 ##
*2016级软工三班* 
*陈荣杰* 
*201610414302*   

####  声明包，查询函数以及存储过程
<pre>
create or replace PACKAGE MyPack IS
  /*
  声明包MyPack中有：
  一个函数:Get_SaleAmount(V_DEPARTMENT_ID NUMBER)，
  一个过程:Get_Employees(V_EMPLOYEE_ID NUMBER)
  */
  FUNCTION Get_SaleAmount(V_DEPARTMENT_ID NUMBER) RETURN NUMBER;
  PROCEDURE Get_Employees(V_EMPLOYEE_ID NUMBER);
END MyPack;
</pre>  
#### 创建包，函数以及存储过程
<pre>
create or replace PACKAGE BODY MyPack 
 IS -- 创建MyPack 包
  FUNCTION Get_SaleAmount(V_DEPARTMENT_ID NUMBER) RETURN NUMBER-- 定义Get_SaleAmount函数
  AS
    N NUMBER(20,2); 
    BEGIN
      SELECT SUM(O.TRADE_RECEIVABLE) into N  FROM ORDERS O,EMPLOYEES E
      WHERE O.EMPLOYEE_ID=E.EMPLOYEE_ID AND E.DEPARTMENT_ID =V_DEPARTMENT_ID;
      RETURN N;
    END;

  PROCEDURE GET_EMPLOYEES(V_EMPLOYEE_ID NUMBER)-- 创建存储过程
  AS
    LEFTSPACE VARCHAR(2000);
    begin
      --通过LEVEL判断递归的级别
      LEFTSPACE:=' ';
      --使用游标
      for v in
      (SELECT LEVEL,EMPLOYEE_ID,NAME,MANAGER_ID FROM employees
      START WITH EMPLOYEE_ID = V_EMPLOYEE_ID
      CONNECT BY PRIOR EMPLOYEE_ID = MANAGER_ID)
      LOOP
        DBMS_OUTPUT.PUT_LINE(LPAD(LEFTSPACE,(V.LEVEL-1)*4,' ')|| V.EMPLOYEE_ID||' '||v.NAME);
      END LOOP;
    END;
END MyPack;

Package Body MYPACK 已编译
</pre>  
#### 进行测试  
Get_SaleAmount()测试
<pre>
select count(*) from orders;
select MyPack.Get_SaleAmount(11) AS 部门11应收金额,MyPack.Get_SaleAmount(12) AS 部门12应收金额 from dual;
</pre>
<pre>
Count
1   1000

     部门11应收金额      部门12应收金额
1    70028304.26	      69994476.88
</pre>  
Get_Employess()测试
<pre>
set serveroutput on
DECLARE
  V_EMPLOYEE_ID NUMBER;    
BEGIN
  V_EMPLOYEE_ID := 1;
  MYPACK.Get_Employees (  V_EMPLOYEE_ID => V_EMPLOYEE_ID) ;  
  V_EMPLOYEE_ID := 11;
  MYPACK.Get_Employees (  V_EMPLOYEE_ID => V_EMPLOYEE_ID) ;    
END;
/  
</pre>  
<pre>
1 李董事长
    11 张总
        111 吴经理
        112 白经理
    12 王总
        121 赵经理
        122 刘经理
11 张总
    111 吴经理
    112 白经理

PL/SQL 过程已成功完成。
</pre>  

