# LINQ 03

The `employees` table stores detailed information about employees in the organization.

| Column Name        | Data Type        | Constraints                           | Description                                      |
|--------------------|------------------|---------------------------------------|--------------------------------------------------|
| `employee_id`      | `INT`            | `PRIMARY KEY`                         | Unique identifier for each employee              |
| `first_name`       | `VARCHAR(20)`    | `DEFAULT NULL`                        | First name of the employee                       |
| `last_name`        | `VARCHAR(25)`    | `NOT NULL`                            | Last name of the employee                        |
| `email`            | `VARCHAR(25)`    | `NOT NULL`                            | Email address of the employee                    |
| `phone_number`     | `VARCHAR(20)`    | `DEFAULT NULL`                        | Contact phone number of the employee             |
| `hire_date`        | `DATE`           | `NOT NULL`                            | Date when the employee was hired                 |
| `job_id`           | `VARCHAR(10)`    | `FOREIGN KEY`, `NOT NULL`             | Foreign key referencing the employee's job role  |
| `salary`           | `DECIMAL(8, 2)`  | `NOT NULL`                            | Salary of the employee                           |
| `commission_pct`   | `DECIMAL(2, 2)`  | `DEFAULT NULL`                        | Commission percentage (if applicable)            |
| `manager_id`       | `INT`            | `FOREIGN KEY`, `DEFAULT NULL`         | Foreign key referencing the employee's manager   |
| `department_id`    | `INT`            | `FOREIGN KEY`, `DEFAULT NULL`         | Foreign key referencing the employee's department|

The `Employee.cs` class serves as the foundational representation of an employee entity within our system.
```csharp
public class Employee {

    public int EmployeeId { get; set; }
    public string? FirstName { get; set; }
    public string? LastName { get; set; }
    public string? Email { get; set; }
    public string? PhoneNumber { get; set; }
    public DateTime HireDate { get; set; }
    public string? JobId { get; set; }
    public decimal Salary { get; set; }
    public decimal? CommissionPercentage { get; set; }
    public int? ManagerId { get; set; }
    public int? DepartmentId { get; set; }

}
```

The `OnModelCreating(modelBuilder)` method is responsible for defining entity relationships, constraints, and configurations within the database model.
```csharp
protected override void OnModelCreating(ModelBuilder modelBuilder) {
    modelBuilder.Entity<Employee>().ToTable("employees", schema: "dbo");
    modelBuilder.Entity<Employee>().HasKey(e => e.EmployeeId);
    modelBuilder.Entity<Employee>().Property(e => e.EmployeeId).HasColumnName("employee_id").HasColumnType("INT");
    modelBuilder.Entity<Employee>().Property(e => e.FirstName).HasColumnName("first_name").HasColumnType("VARCHAR(20)");
    modelBuilder.Entity<Employee>().Property(e => e.LastName).HasColumnName("last_name").HasColumnType("VARCHAR(25)");
    modelBuilder.Entity<Employee>().Property(e => e.Email).HasColumnName("email").HasColumnType("VARCHAR(25)");
    modelBuilder.Entity<Employee>().Property(e => e.PhoneNumber).HasColumnName("phone_number").HasColumnType("VARCHAR(20)");
    modelBuilder.Entity<Employee>().Property(e => e.HireDate).HasColumnName("hire_date").HasColumnType("DATE");
    modelBuilder.Entity<Employee>().Property(e => e.JobId).HasColumnName("job_id").HasColumnType("VARCHAR(10)");
    modelBuilder.Entity<Employee>().Property(e => e.Salary).HasColumnName("salary").HasColumnType("DECIMAL(8, 2)");
    modelBuilder.Entity<Employee>().Property(e => e.CommissionPercentage).HasColumnName("commission_pct").HasColumnType("DECIMAL(2, 2)");;
    modelBuilder.Entity<Employee>().Property(e => e.ManagerId).HasColumnName("manager_id").HasColumnType("INT");
    modelBuilder.Entity<Employee>().Property(e => e.DepartmentId).HasColumnName("department_id").HasColumnType("INT");
}
```

## Question 01
Write a LINQ expression to retrieve the number of employees in each department that has at least one employee in the organization.
- `SELECT COUNT(department_id) FROM departments;` returns `27`, indicating that the company has a total of `27` departments, but not all of them have active employees.
- The `into` keyword in LINQ query syntax is used to give a name to a group after a grouping operation or to create intermediate results for further processing.
```csharp
var context = new MasterContext();

var method = context
    .Employees
    .GroupBy(e => e.DepartmentId)
    .Select(deptGroup => new { DepartmentId = deptGroup.Key, EmployeeCount = deptGroup.Count() })
    .ToList();

var query = (from e in context.Employees
            group e by e.DepartmentId
            into deptGroup
            select new { DepartmentId = deptGroup.Key, EmployeeCount = deptGroup.Count() })
            .ToList();
```

## Question 02
Write a LINQ expression to retrieve the total salary, minimum salary, and maximum salary for each department in the organization.
```csharp
var context = new MasterContext();

var method = context
    .Employees
    .GroupBy(e => e.DepartmentId)
    .Select(deptGroup => new
    {
        DepartmentId = deptGroup.Key,
        TotalSalary = deptGroup.Sum(e => e.Salary),
        MinimumSalary = deptGroup.Min(e => e.Salary),
        MaximumSalary = deptGroup.Max(e => e.Salary)
    })
    .ToList();

var query = (from e in context.Employees
            group e by e.DepartmentId
            into deptGroup
            select new
            {
                DepartmentId = deptGroup.Key,
                TotalSalary = deptGroup.Sum(e => e.Salary),
                MinimumSalary = deptGroup.Min(e => e.Salary),
                MaximumSalary = deptGroup.Max(e => e.Salary)
            })
            .ToList();
```

## Question 03
Write a LINQ expression to retrieve the minimum total salary across all departments, where the total salary is calculated as the sum of salaries for each department.
```csharp
var context = new MasterContext();

var method = context
    .Employees
    .GroupBy(e => e.DepartmentId)
    .Select(deptGroup => deptGroup.Sum(e => e.Salary))
    .Min();

var query = (from e in context.Employees
            group e by e.DepartmentId
            into deptGroup
            select deptGroup.Sum(e => e.Salary))
            .Min();
```

The `teachers` table stores information about which teachers are assigned to teach specific subjects in different departments.

| Column Name        | Data Type        | Constraints                           | Description                                                            |
|--------------------|------------------|---------------------------------------|------------------------------------------------------------------------|
| `teacher_id`       | `INT`            | `NOT NULL`                            | Unique identifier for the teacher                                      |
| `subject_id`       | `INT`            | `NOT NULL`                            | Unique identifier for the subject                                      |
| `dept_id`          | `INT`            | `NOT NULL`                            | Unique identifier for the department <br> where the subject is taught  |

- `(subject_id, dept_id)` is the primary key (combinations of columns with unique values) of the `teachers` table.

## Question 04
Write a LINQ expression to calculate the number of unique subjects taught by each teacher.
> A teacher can teach the same subject in different departments.
```csharp
var context = new MasterContext();

var method = context
    .Teachers
    .GroupBy(e => e.TeacherId)
    .Select(deptGroup => new
    {
        TeacherId = deptGroup.Key,
        SubjectCount = deptGroup.Select(t => t.SubjectId).Distinct().Count()
    })
    .ToList();

var query = (from e in context.Teachers
            group e by e.TeacherId
            into deptGroup
            select new
            {
                TeacherId = deptGroup.Key,
                SubjectCount = deptGroup.Select(t => t.SubjectId).Distinct().Count()
            })
            .ToList();
```

## Question 05
Write a LINQ expression to find the number of employees in each department whose salary is greater than `10,000`.
```csharp
var context = new MasterContext();

var method = context
    .Employees
    .Where(e => e.Salary > 10000)
    .GroupBy(e => e.DepartmentId)
    .Select(deptGroup => new { DepartmentId = deptGroup.Key, EmployeeCount = deptGroup.Count() })
    .ToList();

var query = (from e in context.Employees
            where e.Salary > 10000
            group e by e.DepartmentId
            into deptGroup
            select new { DepartmentId = deptGroup.Key, EmployeeCount = deptGroup.Count() })
            .ToList();
```

## Question 06
Write a LINQ expression to find the number of employees in each department, grouped by their respective job roles.
```csharp
var context = new MasterContext();

var method = context
    .Employees
    .GroupBy(e => new { e.DepartmentId, e.JobId })
    .Select(deptGroup => new
    {
        DepartmentId = deptGroup.Key.DepartmentId,
        JobId = deptGroup.Key.JobId,
        EmployeeCount = deptGroup.Count()
    })
    .ToList();

var query = (from e in context.Employees
            group e by new { e.DepartmentId, e.JobId }
            into deptGroup
            select new
            {
                DepartmentId = deptGroup.Key.DepartmentId,
                JobId = deptGroup.Key.JobId,
                EmployeeCount = deptGroup.Count()
            })
            .ToList();
```

## Question 07
Write a LINQ expression to retrieve the department IDs and the number of employees in each department that has more than `5` employees, sorted by the number of employees in ascending order.
- In SQL, the `HAVING` clause is used to filter results after a `GROUP BY` operation. It allows you to filter groups based on aggregate functions, like `COUNT()`, `SUM()`, etc.
However, LINQ does not have a `HAVING` clause. Instead, you can use the `WHERE` clause after performing a grouping operation to filter the groups.

```csharp
var context = new MasterContext();

var method = context
    .Employees
    .GroupBy(e => e.DepartmentId)
    .Select(deptGroup => new { DepartmentId = deptGroup.Key, EmployeeCount = deptGroup.Count() })
    .Where(deptGroup => deptGroup.EmployeeCount > 5)
    .OrderBy(deptGroup => deptGroup.EmployeeCount)
    .ToList();

var query = (from e in context.Employees
            group e by e.DepartmentId
            into deptGroup
            where deptGroup.Count() > 5
            orderby deptGroup.Count()
            select new { DepartmentId = deptGroup.Key, EmployeeCount = deptGroup.Count() })
            .ToList();
```

## Question 08
Write a LINQ expression to retrieve the first names of all employees who hold a managerial position and oversee at least `7` employees.
```csharp
var context = new MasterContext();

var CTE = context
    .Employees
    .GroupBy(e => e.ManagerId)
    .Where(g => g.Count() >= 7)
    .Select(g => g.Key)
    .ToList();

var method = context
    .Employees
    .Where(e => CTE.Contains(e.EmployeeId))
    .Select(e => e.FirstName)
    .ToList();

var query = (from e in context.Employees
            where CTE.Contains(e.EmployeeId)
            select e.FirstName)
            .ToList();
```

The `Employee_Departments` table stores information about employees and the departments they belong to, with an additional flag indicating the primary department for each employee.

| Column Name    | Data Type | Constraints | Description                                                    |
|----------------|-----------|-------------|----------------------------------------------------------------|
| `employee_id`  | `INT`     | `NOT NULL`  | Unique identifier for each employee                            |
| `department_id`| `INT`     | `NOT NULL`  | Identifier for the department where the employee works         |
| `primary_flag` | `VARCHAR` | `NOT NULL`  | Flag `('Y' or 'N')` indicating if the department is primary    |

- `(employee_id, department_id)` is the primary key (combinations of columns with unique values) of the `Employee_Departments` table.
- A flag `('Y' or 'N')` indicating if the department is the primary department for the employee. If `Y`, it is the primary department; if `N`, it is not the primary.
    - Note that when an employee belongs to only one department, their primary column is `N`.

## Question 09
Write a LINQ expression to report all employees with their primary department, and for employees who belong to only one department, report that department as their primary.
```csharp
var context = new MasterContext();

var CTE = context
    .EmployeeDepartments
    .GroupBy(e => e.EmployeeId)
    .Where(g => g.Count() == 1)
    .Select(g => g.Key)
    .ToList();

var method = context
    .EmployeeDepartments
    .Where(e => e.PrimaryFlag == "Y" || CTE.Contains(e.EmployeeId))
    .Select(e => new { e.EmployeeId, e.DepartmentId })
    .ToList();

var query = (from e in context.EmployeeDepartments
            where e.PrimaryFlag == "Y" || CTE.Contains(e.EmployeeId)
            select new { e.EmployeeId, e.DepartmentId })
            .ToList();
```

## Question 10
Write a LINQ expression to find the department ID with the highest total salary, where the total salary is calculated as the sum of salaries for each department.
```csharp
var context = new MasterContext();

var method = context
    .Employees
    .GroupBy(e => e.DepartmentId)
    .OrderByDescending(deptGroup => deptGroup.Sum(e => e.Salary))
    .Select(deptGroup => new { DepartmentId = deptGroup.Key, TotalSalary = deptGroup.Sum(e => e.Salary) })
    .FirstOrDefault();

var query = (from e in context.Employees
            group e by e.DepartmentId
            into deptGroup
            orderby deptGroup.Sum(e => e.Salary) descending
            select new { DepartmentId = deptGroup.Key, TotalSalary = deptGroup.Sum(e => e.Salary) })
            .FirstOrDefault();
```