# Beginner Explanatory Guide: OPS-403: Build Multi-Service Health Check Endpoint

> **Task Type**: Service Task  
> **Domain/Focus**: Observability, Health Checks, Python Fundamentals

---

## 1. The Goal (In-Depth Beginner Explanation)

### The Core Problem
In modern applications, especially those that rely on multiple services, it's crucial to ensure that all components are functioning correctly. This task addresses the need for a health check system that monitors the status of essential dependencies like databases, caches, and external APIs. Currently, there is no mechanism in place to check if these services are operational, which can lead to undetected failures. If a database goes down or an external API becomes unreachable, users may experience errors without any indication of the underlying issue.

Implementing a health check system is vital because it provides real-time insights into the application's operational status. By regularly checking the health of these dependencies, developers can quickly identify and resolve issues, ensuring a smoother user experience. This proactive approach not only enhances reliability but also aids in maintaining user trust and satisfaction.

### Jargon Buster (Key Terms Explained)
* **Health Check**: A health check is a process that verifies whether a service or component is functioning correctly. For example, a health check might ping a database to see if it responds within a certain time frame, indicating that it is operational.
* **Dependency**: In software, a dependency is a component that another component relies on to function. For instance, a web application might depend on a database to store user data. If the database is down, the application cannot retrieve or save data.
* **Response Time**: This refers to the amount of time it takes for a service to respond to a request. For example, if a health check sends a request to an external API and it takes 200 milliseconds to get a response, the response time is 200 ms.
* **Timeout**: A timeout is a predefined period during which a service must respond. If the service does not respond within this time, it is considered to have failed. For instance, if a health check has a timeout of 5 seconds and the service does not respond in that time, the health check will report a failure.

### Expected Outcome
After implementing the health check system, the application should be able to provide a clear status of its dependencies. The expected behavior can be summarized as follows:

- **Before**: The application has no way to monitor the health of its dependencies, leading to potential undetected failures.
- **After**: The application can check the health of the database, cache, and external API, returning an overall status of "healthy," "degraded," or "unhealthy," along with individual results and response times for each check.

---

## 2. Related Coding Concepts & Syntax (50% Theory, 50% Practice)

### Concept 1: Function Registration and Execution
#### 📘 Theoretical Overview (50%)
* **Why it exists**: Function registration allows us to define checks for various dependencies dynamically. This means we can add or remove checks without altering the core logic of our health check system. If we didn't have this mechanism, we would need to hard-code each check, making the system inflexible and difficult to maintain.
* **Key Mechanisms**: The core mechanism involves storing functions (checks) in a dictionary where the key is the name of the check, and the value is the function itself. This allows us to call these functions later based on their names.

#### 💻 Syntax & Practical Examples (50%)
* **Language Syntax**:
  ```python
  def register_check(self, name, check_fn, timeout=5):
      self.checks[name] = (check_fn, timeout)
  ```
  - `def`: This keyword defines a new function.
  - `register_check`: The name of the function that registers a health check.
  - `self.checks`: A dictionary that stores the checks.
  - `name`: The name of the check (e.g., 'database').
  - `check_fn`: The function that performs the health check.
  - `timeout`: An optional parameter that specifies how long to wait before considering the check a failure.

* **Real-World Application**:
  ```python
  hc = HealthChecker()
  hc.register_check('database', check_database)
  hc.register_check('cache', check_cache)
  ```
  In this example, we create an instance of `HealthChecker` and register two checks: one for the database and one for the cache. This allows us to run these checks later using their names.

---

## 3. Step-by-Step Logic & Walkthrough

1. **Step 1: Locate and Analyze the Target File**
   * Navigate to the `healthChecker.py` file in the `s-w12-task-05` folder. This file contains the `HealthChecker` class where we will implement the health check logic.
   * Focus on the `register_check`, `run_check`, and `run_all` methods, as these will be modified to implement the health check functionality.

2. **Step 2: Input Verification & Validation**
   * Ensure that the `check_fn` parameter in `register_check` is a callable function. If it is not, raise an exception to prevent errors during execution.

3. **Step 3: Core Implementation / Modification**
   * Implement the `register_check` method to store the check functions in a dictionary.
   * Implement the `run_check` method to execute the registered check function, measure the response time, and return the result in the specified format.
   * Implement the `run_all` method to iterate through all registered checks, collect their results, and determine the overall health status based on the individual results.

4. **Step 4: Output Verification & Testing**
   * Run the tests defined in `test_health.py` using pytest to ensure that all checks pass and the health check system behaves as expected.

---

## 4. Detailed Walkthrough of Test Cases

### Test Case 1: Standard / Success Case
* **Description**: This test checks the health of the database, which is expected to be operational.
* **Inputs**:
  ```json
  {
    "check": "database"
  }
  ```
* **Step-by-Step Execution Trace**:
  1. The `run_check` method is called with the input 'database'.
  2. The method retrieves the `check_database` function from the `checks` dictionary.
  3. It measures the time taken to execute `check_database`, which simulates a 10 ms latency.
  4. The function returns `True`, indicating the database is healthy.
  5. The `run_check` method constructs and returns the result: `{'name': 'database', 'status': 'healthy', 'response_time_ms': 10, 'error': None}`.
* **Expected Output**: 
  ```json
  {
    "name": "database",
    "status": "healthy",
    "response_time_ms": 10,
    "error": null
  }
  ```

### Test Case 2: Edge Case / Validation Fail
* **Description**: This test checks the health of the external API, which is expected to fail.
* **Inputs**:
  ```json
  {
    "check": "external_api"
  }
  ```
* **Step-by-Step Execution Trace**:
  1. The `run_check` method is called with the input 'external_api'.
  2. The method retrieves the `check_external_api` function from the `checks` dictionary.
  3. The function raises a `ConnectionError`, simulating an unreachable API.
  4. The `run_check` method catches the exception and constructs the result: `{'name': 'external_api', 'status': 'unhealthy', 'response_time_ms': N, 'error': "API unreachable"}` where N is the time taken before the error occurred.
* **Expected Output**: 
  ```json
  {
    "name": "external_api",
    "status": "unhealthy",
    "response_time_ms": N,
    "error": "API unreachable"
  }
  ``` 

This guide provides a comprehensive overview of the task, breaking down the implementation of a health check system into manageable steps while explaining key concepts and expected outcomes.