# 杂记
## 数据库三大范式
### 第一范式
原子性，每一列都是原子的，不能重复出现，且不可分割。
```
- 学生ID（StudentID）
- 学生姓名（StudentName）
- 手机号1(phone1)
- 手机号2(phone2)
在这个表中，手机号出现了2个列，在特定情况下可以提高查询速度，避免了链接操作。
```
### 第二范式
基于第一范式，所有列都依赖于主键，避免部分依赖。
```
- 学生ID（StudentID）
- 课程ID（CourseID）
- 选课时间（EnrollmentDate）
- 学生姓名（StudentName）

在这个表中，复合主键是由 `StudentID` 和 `CourseID` 组成的，因为它们两个一起可以唯一标识每一条选课记录。现在，如果 `StudentName` 只依赖于 `StudentID`（因为每个学生只有一个名字），而不是依赖于整个复合主键（`StudentID` 和 `CourseID`），那么 `StudentName` 就存在部分依赖。
```
### 第三范式
基于第二范式，表中的所有非主键列都应该直接依赖于主键，而不是间接通过另一个非主键列依赖。
```
- 员工ID（EmployeeID） - 主键
- 员工姓名（EmployeeName）
- 部门ID（DepartmentID）
- 部门名称（DepartmentName）

在这个表中，`DepartmentName` 依赖于 `DepartmentID`（因为每个部门有一个名称），而 `DepartmentID` 依赖于 `EmployeeID`（因为每个员工属于一个部门）。这里 `DepartmentName` 对 `EmployeeID` 的依赖是传递的：`EmployeeID` -> `DepartmentID` -> `DepartmentName`。
```
## mysq中的join
### LeftJoin
以左表为主表，右表为待合并的表，左有右无则补充null，左无右有则不显示。
### RightJoin
以右表为主表，左表为待合并的表，右有左无则补充null，右无左有则不显示。
### InnerJoin
获取左右表的交集，有缺失则不现实。
