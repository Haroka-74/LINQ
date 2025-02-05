# LINQ 02

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
Write a LINQ expression to retrieve the first names and salaries of all employees sorted by their `salaries` in ascending order.
```csharp
var context = new MasterContext();

var method = context
    .Employees
    .OrderBy(e => e.Salary)
    .Select(e => new { e.FirstName, e.Salary })
    .ToList();

var query = (from e in context.Employees
            orderby e.Salary
            select new { e.FirstName, e.Salary })
            .ToList();
```

## Question 02
Write a LINQ expression to retrieve the first names and hire dates of all employees sorted by their `hire dates` in descending order.
```csharp
var context = new MasterContext();

var method = context
    .Employees
    .OrderByDescending(e => e.HireDate)
    .Select(e => new { e.FirstName, e.HireDate })
    .ToList();

var query = (from e in context.Employees
            orderby e.HireDate descending
            select new { e.FirstName, e.HireDate })
            .ToList();
```

## Question 03
Write a LINQ expression to retrieve the full names of all employees sorted by their `first names` in ascending order, and in case of a tie, sorted by their `last names` in descending order.
```csharp
var context = new MasterContext();

var method = context
    .Employees
    .OrderBy(e => e.FirstName)
    .ThenByDescending(e => e.LastName)
    .Select(e => string.Concat(e.FirstName, ' ', e.LastName))
    .ToList();

var query = (from e in context.Employees
            orderby e.FirstName, e.LastName descending
            select string.Concat(e.FirstName, ' ', e.LastName))
            .ToList();
```

## Question 04
Write a LINQ expression to retrieve the first names of all employees sorted by the length of their `first names` in descending order.
```csharp
var context = new MasterContext();

var method = context
    .Employees
    .OrderByDescending(e => e.FirstName.Length)
    .Select(e => e.FirstName)
    .ToList();

var query = (from e in context.Employees
            orderby e.FirstName.Length descending
            select e.FirstName)
            .ToList();
```