# JavaStreamAPI
Employee Salary based on Department


Java 8 Stream API: Find Second Highest Salary per Department

import java.util.*;
import java.util.stream.*;

class Employee {
    private String name;
    private String department;
    private double salary;

    // Constructor
    public Employee(String name, String department, double salary) {
        this.name = name;
        this.department = department;
        this.salary = salary;
    }

    // Getters
    public String getName() {
        return name;
    }
    public String getDepartment() {
        return department;
    }
    public double getSalary() {
        return salary;
    }
}

public class SecondHighestSalaryByDept {
    public static void main(String[] args) {
        // Sample employee list
        List<Employee> employees = Arrays.asList(
            new Employee("Amit", "HR", 55000),
            new Employee("Priya", "HR", 72000),
            new Employee("Ravi", "HR", 48000),
            new Employee("Sneha", "IT", 82000),
            new Employee("Vikram", "IT", 67000),
            new Employee("Neha", "IT", 90000),
            new Employee("Rahul", "Finance", 60000),
            new Employee("Pooja", "Finance", 75000)
        );

        // Group by department
        Map<String, Optional<Employee>> secondHighestByDept = employees.stream()
            .collect(Collectors.groupingBy(
                Employee::getDepartment,
                Collectors.collectingAndThen(
                    Collectors.toList(),
                    list -> list.stream()
                        .sorted(Comparator.comparingDouble(Employee::getSalary).reversed()) // Sort desc
                        .skip(1) // Skip the highest salary
                        .findFirst() // Pick the second-highest
                )
            ));

        // Print result
        secondHighestByDept.forEach((dept, empOpt) -> {
            if (empOpt.isPresent()) {
                Employee emp = empOpt.get();
                System.out.println("Department: " + dept);
                System.out.println("2nd Highest Paid Employee: " + emp.getName());
                System.out.println("Salary: ₹" + emp.getSalary());
                System.out.println("--------------------------");
            } else {
                System.out.println("Department: " + dept + " → Not enough employees for comparison");
            }
        });
    }
}



Explanation

groupingBy(Employee::getDepartment) → partitions employees by department.

sorted(...reversed()) → sorts each department’s employees from highest → lowest.

skip(1).findFirst() → skips the top earner and picks the second-highest.

collectingAndThen → allows post-processing on the collected list.


Sample Output:

Department: Finance
2nd Highest Paid Employee: Rahul
Salary: ₹60000.0
--------------------------
Department: HR
2nd Highest Paid Employee: Amit
Salary: ₹55000.0
--------------------------
Department: IT
2nd Highest Paid Employee: Sneha
Salary: ₹82000.0
--------------------------





Java 8 Stream API program that shows both the highest and second-highest salary employees side-by-side for each department



import java.util.*;
import java.util.stream.*;

class Employee {
    private String name;
    private String department;
    private double salary;

    public Employee(String name, String department, double salary) {
        this.name = name;
        this.department = department;
        this.salary = salary;
    }

    public String getName() { return name; }
    public String getDepartment() { return department; }
    public double getSalary() { return salary; }
}

public class HighestAndSecondHighestByDept {
    public static void main(String[] args) {
        List<Employee> employees = Arrays.asList(
            new Employee("Amit", "HR", 55000),
            new Employee("Priya", "HR", 72000),
            new Employee("Ravi", "HR", 48000),
            new Employee("Sneha", "IT", 82000),
            new Employee("Vikram", "IT", 67000),
            new Employee("Neha", "IT", 90000),
            new Employee("Rahul", "Finance", 60000),
            new Employee("Pooja", "Finance", 75000)
        );

        // Group by department
        Map<String, List<Employee>> result = employees.stream()
            .collect(Collectors.groupingBy(Employee::getDepartment));

        // Display highest and second-highest salaries
        result.forEach((dept, empList) -> {
            // Sort employees in descending order of salary
            List<Employee> sorted = empList.stream()
                    .sorted(Comparator.comparingDouble(Employee::getSalary).reversed())
                    .collect(Collectors.toList());

            System.out.println("Department: " + dept);
            
            if (!sorted.isEmpty()) {
                Employee highest = sorted.get(0);
                System.out.println("Highest Paid Employee: " + highest.getName() + " | ₹" + highest.getSalary());
            }

            if (sorted.size() > 1) {
                Employee secondHighest = sorted.get(1);
                System.out.println("Second Highest Paid Employee: " + secondHighest.getName() + " | ₹" + secondHighest.getSalary());
            } else {
                System.out.println("Second Highest Paid Employee: Not enough data");
            }

            System.out.println("--------------------------------------");
        });
    }
}


How It Works

Group employees by department.

Inside each group, sort by salary in descending order.

Pick:

sorted.get(0) → highest salary employee

sorted.get(1) → second-highest salary employee

Print them neatly.


Department: Finance
Highest Paid Employee: Pooja | ₹75000.0
Second Highest Paid Employee: Rahul | ₹60000.0
--------------------------------------
Department: HR
Highest Paid Employee: Priya | ₹72000.0
Second Highest Paid Employee: Amit | ₹55000.0
--------------------------------------
Department: IT
Highest Paid Employee: Neha | ₹90000.0
Second Highest Paid Employee: Sneha | ₹82000.0
--------------------------------------


To find the second highest salary by department in a company, the approach depends on the language or system you’re using — e.g., SQL, Python (pandas), or another programming language.


1. SQL Query

| emp_id | emp_name | department | salary |
| ------ | -------- | ---------- | ------ |
| 1      | Alice    | HR         | 50000  |
| 2      | Bob      | HR         | 70000  |
| 3      | Carol    | IT         | 60000  |
| 4      | Dave     | IT         | 90000  |
| 5      | Eve      | IT         | 85000  |


You can find the second highest salary per department using window functions:

Using DENSE_RANK():

SELECT department, emp_name, salary
FROM (
    SELECT 
        department,
        emp_name,
        salary,
        DENSE_RANK() OVER (PARTITION BY department ORDER BY salary DESC) AS rank
    FROM employees
) ranked
WHERE rank = 2;


Explanation:
PARTITION BY department → groups by department
ORDER BY salary DESC → ranks salaries from highest to lowest
DENSE_RANK() → assigns ranks (1 for highest, 2 for second-highest, etc.)
The outer query filters rank = 2 → second-highest salaries
Output:

| department | emp_name | salary |
| ---------- | -------- | ------ |
| HR         | Alice    | 50000  |
| IT         | Eve      | 85000  |


2. Python (Pandas)
If you have employee data in a DataFrame df:

import pandas as pd

df = pd.DataFrame({
    'emp_name': ['Alice', 'Bob', 'Carol', 'Dave', 'Eve'],
    'department': ['HR', 'HR', 'IT', 'IT', 'IT'],
    'salary': [50000, 70000, 60000, 90000, 85000]
})


Using groupby() + nlargest():

second_highest = (
    df.sort_values(['department', 'salary'], ascending=[True, False])
      .groupby('department')
      .nth(1)  # 0 = highest, 1 = second highest
      .reset_index()
)

print(second_highest)


Output:

  department emp_name  salary
0         HR    Alice   50000
1          IT      Eve   85000



3. Logic Explanation (Algorithmically)
If you were to write the logic manually (in pseudocode):

for each department in departments:
    salaries = list of salaries of employees in that department
    unique_salaries = remove duplicates from salaries
    sort unique_salaries in descending order
    if length(unique_salaries) >= 2:
        second_highest = unique_salaries[1]
    else:
        second_highest = None




here’s the pure logic (algorithmic) version to find the highest salary per department in a company:


Logic to Find the Highest Salary by Department

1. Create a data structure (like a dictionary or map) to group employees by department.

2. For each employee in the company:
      a. Get the employee’s department.
      b. Get the employee’s salary.
      c. Compare this salary with the current highest salary stored for that department.
         - If it’s higher, update the highest salary for that department.

3. After processing all employees:
      - The map will contain each department as a key
      - And the corresponding highest salary as the value.



Pseudocode Example

initialize department_highest_salary = empty map

for each employee in employee_list:
    dept = employee.department
    sal = employee.salary

    if dept not in department_highest_salary:
        department_highest_salary[dept] = sal
    else if sal > department_highest_salary[dept]:
        department_highest_salary[dept] = sal

return department_highest_salary


Result Example
If you have:

HR: [50000, 70000]
IT: [60000, 90000, 85000]


Then output would be:

HR → 70000
IT → 90000









 
