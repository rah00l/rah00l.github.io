Database normalization is the process of organizing the attributes and tables of a relational database to minimize redundancy and dependency. There are several normal forms (1NF, 2NF, 3NF, BCNF, 4NF, 5NF), each with its own set of rules to achieve progressively higher levels of normalization. Let's go through each one with examples:

1. **First Normal Form (1NF)**:
   - Rule: Eliminate repeating groups and ensure each column contains atomic values.
   - Example: Consider a table `Students` with columns `StudentID`, `Name`, and `Subjects` where `Subjects` is a comma-separated list. To normalize, create a separate table `Student_Subjects` with columns `StudentID` and `Subject`.

2. **Second Normal Form (2NF)**:
   - Rule: Meet the requirements of 1NF and ensure all non-key attributes are fully functional dependent on the primary key.
   - Example: Building on the previous example, if `Subjects` depends only on `StudentID`, it should be moved to the `Student_Subjects` table.

3. **Third Normal Form (3NF)**:
   - Rule: Meet the requirements of 2NF and eliminate transitive dependencies.
   - Example: If there's a table `Books` with columns `ISBN`, `Title`, `Author`, and `Author_Address`, where `Author_Address` depends on `Author`, move `Author_Address` to a new table `Authors` with columns `Author` and `Author_Address`.

4. **Boyce-Codd Normal Form (BCNF)**:
   - Rule: Every determinant must be a candidate key.
   - Example: If we have a table `Orders` with columns `OrderID`, `ProductID`, and `Quantity`, where `ProductID` determines `Quantity`, it violates BCNF. To comply, separate `ProductID` and `Quantity` into a new table where `ProductID` is the primary key.

5. **Fourth Normal Form (4NF)**:
   - Rule: Eliminate multi-valued dependencies.
   - Example: Consider a table `Employees` with columns `EmployeeID`, `ProjectID`, and `Responsibilities`, where `Responsibilities` is multi-valued. To normalize, create a new table `Employee_Projects` with columns `EmployeeID`, `ProjectID`, and `Responsibility`.

6. **Fifth Normal Form (5NF)**:
   - Rule: Decompose tables to eliminate join dependencies.
   - Example: If there's a table `Employee_Projects` with columns `EmployeeID`, `ProjectID`, and `Responsibility`, and `Responsibility` determines `EmployeeID`, split it into two tables: `Employee_Responsibilities` and `Project_Responsibilities`.

In essence, normalization ensures efficient database design by reducing redundancy and dependency, leading to better data integrity and query performance. Each normal form builds upon the previous one, with the ultimate goal of eliminating data anomalies and improving overall database structure.