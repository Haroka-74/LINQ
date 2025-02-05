# LINQ 01

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
Write a LINQ expression to generate a detailed report listing all current employees who are actively working in the organization.
```csharp
var context = new MasterContext();
var employees = context.Employees.ToList();
```

## Question 02
Write a LINQ expression to retrieve the first names of all employees from the `employees` table.
```csharp
var context = new MasterContext();

var method = context
    .Employees
    .Select(e => e.FirstName)
    .ToList();

var query = (from e in context.Employees
            select e.FirstName)
            .ToList();
```

## Question 03
Write a LINQ expression to retrieve the full names of all employees from the `employees` table.
```csharp
var context = new MasterContext();

var method = context
    .Employees
    .Select(e => string.Concat(e.FirstName, ' ', e.LastName))
    .ToList();

var query = (from e in context.Employees
            select string.Concat(e.FirstName, ' ', e.LastName)).
            ToList();
```

## Question 04
Write a LINQ expression to find all unique department and job role pairs in the organization, ensuring that each pair represents a distinct combination of `department_id` and `job_id`.
```csharp
var context = new MasterContext();

var method = context
    .Employees
    .Select(e => new { e.DepartmentId, e.JobId })
    .Distinct()
    .ToList();

var query = (from e in context.Employees
            select new { e.DepartmentId, e.JobId })
            .Distinct()
            .ToList();
```

## Question 05
Write a LINQ expression to retrieve the total number of departments that have at least one employee in the organization.
- `SELECT COUNT(department_id) FROM departments;` returns `27`, indicating that the company has a total of `27` departments, but not all of them have active employees.
- In SQL, `COUNT(department_id)` automatically ignores `NULLs` and only counts non-null department IDs. However, in LINQ, `Count()` includes `NULLs` by default. To ensure the LINQ result matches the SQL behavior, we must first filter out `NULLs` using `.Where(e => e.DepartmentId != null)` before applying `Count()`.
```csharp
var context = new MasterContext();

var method = context
    .Employees
    .Where(e => e.DepartmentId != null)
    .Select(e => e.DepartmentId)
    .Distinct()
    .Count();

var query = (from e in context.Employees
            where e.DepartmentId != null
            select e.DepartmentId)
            .Distinct()
            .Count();
```

## Question 06
Write a LINQ expression to retrieve the first names of all employees working in department `90`.
```csharp
var context = new MasterContext();

var method = context
    .Employees
    .Where(e => e.DepartmentId == 90)
    .Select(e => e.FirstName)
    .ToList();

var query = (from e in context.Employees
            where e.DepartmentId == 90
            select e.FirstName)
            .ToList();
```

## Question 07
Write a LINQ expression to retrieve the first names and salaries of all employees whose salary is not equal to `10,000`.
```csharp
var context = new MasterContext();

var method = context
    .Employees
    .Where(e => e.Salary != 10000)
    .Select(e => new { e.FirstName, e.Salary })
    .ToList();

var query = (from e in context.Employees
            where e.Salary != 10000
            select new { e.FirstName, e.Salary })
            .ToList();
```

## Question 08
Write a LINQ expression to retrieve the first names of all employees who were hired after `March 8, 2000`.
```csharp
var context = new MasterContext();

var method = context
    .Employees
    .Where(e => e.HireDate > new DateTime(2000, 3, 8))
    .Select(e => e.FirstName)
    .ToList();

var query = (from e in context.Employees
            where e.HireDate > new DateTime(2000, 3, 8)
            select e.FirstName)
            .ToList();
```

## Question 09
Write a LINQ expression to retrieve the first names and salaries of all employees who earn less than `$5,000`.
```csharp
var context = new MasterContext();

var method = context
    .Employees
    .Where(e => e.Salary < 5000)
    .Select(e => new { e.FirstName, e.Salary })
    .ToList();

var query = (from e in context.Employees
            where e.Salary < 5000
            select new { e.FirstName, e.Salary })
            .ToList();
```

## Question 10
Write a LINQ expression to retrieve the first names and salaries of all employees whose salary is between `$2,500` and `$3,500`.
```csharp
var context = new MasterContext();

var method = context
    .Employees
    .Where(e => e.Salary >= 2500 && e.Salary <= 3500)
    .Select(e => new { e.FirstName, e.Salary })
    .ToList();

var query = (from e in context.Employees
            where e.Salary >= 2500 && e.Salary <= 3500
            select new { e.FirstName, e.Salary })
            .ToList();
```

## Question 11
Write a LINQ expression to retrieve the first names and salaries of all employees whose salary is not between `$2,500` and `$3,500`.
```csharp
var context = new MasterContext();

var method = context
    .Employees
    .Where(e => !(e.Salary >= 2500 && e.Salary <= 3500))
    .Select(e => new { e.FirstName, e.Salary })
    .ToList();

var query = (from e in context.Employees
            where !(e.Salary >= 2500 && e.Salary <= 3500)
            select new { e.FirstName, e.Salary })
            .ToList();
```

## Question 12
Write a LINQ expression to retrieve the first names and job IDs of all employees who hold one of the following job titles: `AD_PRES`, `IT_PROG`, or `FI_ACCOUNT`.
```csharp
var context = new MasterContext();

var method = context
    .Employees
    .Where(e => e.JobId == "AD_PRES" || e.JobId == "IT_PROG" || e.JobId == "FI_ACCOUNT")
    .Select(e => new { e.FirstName, e.JobId })
    .ToList();

var query = (from e in context.Employees
            where e.JobId == "AD_PRES" || e.JobId == "IT_PROG" || e.JobId == "FI_ACCOUNT"
            select new { e.FirstName, e.JobId })
            .ToList();
```

## Question 13
Write a LINQ expression to retrieve the first names and job IDs of all employees who don't hold the job titles: `AD_PRES`, `IT_PROG`, and `FI_ACCOUNT`.
```csharp
var context = new MasterContext();

var method = context
    .Employees
    .Where(e => !(e.JobId == "AD_PRES" || e.JobId == "IT_PROG" || e.JobId == "FI_ACCOUNT"))
    .Select(e => new { e.FirstName, e.JobId })
    .ToList();

var query = (from e in context.Employees
            where !(e.JobId == "AD_PRES" || e.JobId == "IT_PROG" || e.JobId == "FI_ACCOUNT")
            select new { e.FirstName, e.JobId })
            .ToList();
```

## Question 14
Write a LINQ expression to retrieve the first names, department IDs, and salaries of all employees who either work in department `90` or have a salary greater than `$6,000`.
```csharp
var context = new MasterContext();

var method = context
    .Employees
    .Where(e => e.DepartmentId == 90 || e.Salary > 6000)
    .Select(e => new { e.FirstName, e.DepartmentId, e.Salary })
    .ToList();

var query = (from e in context.Employees
            where e.DepartmentId == 90 || e.Salary > 6000
            select new { e.FirstName, e.DepartmentId, e.Salary })
            .ToList();
```

## Question 15
Write a LINQ expression to retrieve the first names of all employees who are not assigned to any department.
```csharp
var context = new MasterContext();

var method = context
    .Employees
    .Where(e => e.DepartmentId == null)
    .Select(e => e.FirstName)
    .ToList();

var query = (from e in context.Employees
            where e.DepartmentId == null
            select e.FirstName)
            .ToList();
```

## Question 16
Write a LINQ expression to retrieve the first names of all employees who are assigned to a department.
```csharp
var context = new MasterContext();

var method = context
    .Employees
    .Where(e => e.DepartmentId != null)
    .Select(e => e.FirstName)
    .ToList();

var query = (from e in context.Employees
            where e.DepartmentId != null
            select e.FirstName)
            .ToList();
```