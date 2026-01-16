# Flow System - LLM Usage Brief

**Version**: v3.70.3  
**Purpose**: Guide for LLMs and developers on workflow orchestration with Flows and Tasks  
**Location**: `osbot_utils.helpers.flows`  
**Repo**: https://github.com/owasp-sbot/OSBot-Utils  
**Install**: `pip install osbot-utils`

---

## What is the Flow System?

**The Flow System is a lightweight workflow orchestration framework that brings structure, observability, and error handling to Python function execution.** It wraps your code in a rich execution context that provides automatic logging, event emission, statistics collection, dependency injection, and artifact tracking—without requiring external services or infrastructure.

Instead of scattering logging calls, try/except blocks, and timing code throughout your functions, you get a clean separation between business logic and execution concerns.

### The Problem It Solves

When building complex applications, you often need to:
- Track execution across multiple functions
- Share data between processing steps
- Handle errors gracefully without stopping everything
- Measure performance and collect statistics
- Log what's happening in a structured way
- Capture artifacts and results for later analysis

Without a framework, this leads to verbose, tangled code:

```python
# Without Flow System - verbose and scattered
import logging
import time

logger = logging.getLogger(__name__)

def process_data(input_data):
    start_time = time.time()
    results = {}
    
    try:
        logger.info("Starting data fetch")
        fetch_start = time.time()
        raw_data = fetch_data(input_data)
        results['fetch_duration'] = time.time() - fetch_start
        logger.info(f"Fetch completed in {results['fetch_duration']:.2f}s")
    except Exception as e:
        logger.error(f"Fetch failed: {e}")
        raise
    
    try:
        logger.info("Starting transformation")
        transform_start = time.time()
        transformed = transform_data(raw_data)
        results['transform_duration'] = time.time() - transform_start
        logger.info(f"Transform completed in {results['transform_duration']:.2f}s")
    except Exception as e:
        logger.error(f"Transform failed: {e}")
        raise
    
    results['total_duration'] = time.time() - start_time
    return transformed, results
```

**With the Flow System:**

```python
from osbot_utils.helpers.flows.decorators.flow import flow
from osbot_utils.helpers.flows.decorators.task import task

@task()
def fetch_data(input_data):
    return api.get(input_data)

@task()
def transform_data(raw_data):
    return processor.transform(raw_data)

@flow()
def process_data(input_data):
    raw_data = fetch_data(input_data)
    return transform_data(raw_data)

# Execute and get full observability
result = process_data(input_data).execute()
print(result.durations())           # All timing data
print(result.captured_logs())       # All log messages
print(result.flow_return_value)     # The actual result
```

### Design Philosophy

1. **Pythonic and intuitive** — Use decorators for simple cases, classes for complex ones
2. **Zero infrastructure** — No external services, databases, or message queues required
3. **Built on Type_Safe** — Full runtime type checking and serialization support
4. **Automatic context discovery** — Tasks automatically find their parent Flow via stack inspection
5. **Dependency injection** — Request `this_flow`, `flow_data`, etc. as function parameters
6. **Graceful error handling** — Configure whether errors stop execution or get logged and continue
7. **Rich observability** — Events, statistics, artifacts, and structured logging built-in

### Key Capabilities

- **Flow orchestration** — Coordinate multiple tasks with shared context
- **Automatic logging** — All print statements and log calls captured
- **Event system** — Subscribe to flow/task lifecycle events
- **Statistics collection** — Timing and status for every flow and task
- **Dependency injection** — Automatic parameter resolution
- **Error isolation** — Tasks can fail without stopping the flow
- **Artifact tracking** — Store and retrieve data products
- **Async support** — Full support for async/await patterns
- **JSON serialization** — Export complete flow execution data

---

## Quick Start

### Decorator Style (Recommended for Simple Cases)

```python
from osbot_utils.helpers.flows.decorators.flow import flow
from osbot_utils.helpers.flows.decorators.task import task

@task()
def add_numbers(x, y):
    return x + y

@task()
def multiply_result(value, factor):
    return value * factor

@flow()
def calculate(a, b, factor):
    sum_result = add_numbers(a, b)
    return multiply_result(sum_result, factor)

# Execute the flow
flow_instance = calculate(10, 20, 2).execute()

print(flow_instance.flow_return_value)  # 60
print(flow_instance.durations())        # {'flow_name': 'calculate', 'flow_duration': 0.001, ...}
```

### Context Manager Style

```python
from osbot_utils.helpers.flows.Flow import Flow
from osbot_utils.helpers.flows.Task import Task

def my_workflow():
    with Flow() as flow:
        flow.setup(lambda: None)  # Setup required before execute
        
        with Task() as task:
            task.task_target = lambda: "task completed"
            result = task.execute__sync()
        
        return result

# Or more explicitly
with Flow() as flow:
    flow.flow_id = 'my-custom-id'
    flow.setup(process_function, arg1, arg2)
    flow.execute()
    print(flow.flow_return_value)
```

### Class-Based Style (Recommended for Complex Flows)

```python
from osbot_utils.type_safe.Type_Safe import Type_Safe
from osbot_utils.helpers.flows.decorators.flow import flow
from osbot_utils.helpers.flows.decorators.task import task

class DataPipeline(Type_Safe):
    source_url : str
    batch_size : int = 100
    
    @task()
    def fetch_data(self, flow_data=None):
        data = api.fetch(self.source_url)
        flow_data['raw_data'] = data
        return len(data)
    
    @task()
    def process_batch(self, flow_data=None):
        raw_data = flow_data['raw_data']
        processed = [transform(item) for item in raw_data[:self.batch_size]]
        flow_data['processed'] = processed
        return len(processed)
    
    @flow()
    def run_pipeline(self):
        count = self.fetch_data()
        processed = self.process_batch()
        return f"Fetched {count}, processed {processed}"

# Usage
pipeline = DataPipeline(source_url='https://api.example.com/data')
result = pipeline.run_pipeline().execute()
print(result.flow_return_value)
```

---

## Core Concepts

### Flow Lifecycle

A Flow goes through distinct phases:

```
1. Creation     → Flow() instantiated
2. Setup        → .setup(target, *args, **kwargs) configures the flow
3. Execution    → .execute() runs the target function
4. Completion   → Statistics collected, events fired, results available
```

**Critical**: You must call `setup()` before `execute()`:

```python
# ✓ Correct
flow = Flow()
flow.setup(my_function, arg1, arg2)
flow.execute()

# ✓ Correct - decorator handles setup automatically
@flow()
def my_function():
    pass
my_function().execute()

# ✗ Wrong - will raise ValueError
flow = Flow()
flow.execute()  # Error: setup has not been called
```

### Task Execution Context

Tasks automatically discover their parent Flow by inspecting the call stack:

```python
@flow()
def parent_flow():
    # Task automatically finds this flow
    result = child_task()
    return result

@task()
def child_task(this_flow=None):
    # this_flow is automatically injected
    this_flow.log_info("Task is running")
    return "done"
```

The `find_flow()` method walks the stack frames looking for a `Flow` instance:

```python
def find_flow(self):
    stack = inspect.stack()
    for frame_info in stack:
        frame = frame_info.frame
        for var_name, var_value in frame.f_locals.items():
            if isinstance(var_value, Flow):
                return var_value
    return None
```

### Sync vs Async Execution

The Flow System fully supports async operations:

```python
@task()
async def async_fetch(url):
    async with aiohttp.ClientSession() as session:
        async with session.get(url) as response:
            return await response.json()

@task()
def sync_transform(data):
    return [item.upper() for item in data]

@flow()
async def mixed_flow():
    data = await async_fetch('https://api.example.com')
    return sync_transform(data)

# Async flows are executed in a new event loop
result = mixed_flow().execute()
```

### Dependency Injection

The Flow System automatically injects special parameters into your functions:

| Parameter | Type | Description |
|-----------|------|-------------|
| `this_flow` | `Flow` | The current Flow instance |
| `this_task` | `Task` | The current Task instance (tasks only) |
| `flow_data` | `dict` | Shared dictionary across all tasks in the flow |
| `task_data` | `dict` | Dictionary scoped to the current task |

```python
@task()
def my_task(x, y, this_flow=None, flow_data=None, task_data=None):
    # x, y are your regular parameters
    # this_flow, flow_data, task_data are auto-injected
    
    this_flow.log_info(f"Processing {x} + {y}")
    flow_data['result'] = x + y      # Shared with other tasks
    task_data['local'] = 'private'   # Only this task sees this
    
    return x + y
```

---

## Import Reference

### Core Classes

```python
# Flow and Task
from osbot_utils.helpers.flows.Flow import Flow
from osbot_utils.helpers.flows.Task import Task

# Decorators
from osbot_utils.helpers.flows.decorators.flow import flow
from osbot_utils.helpers.flows.decorators.task import task
```

### Configuration

```python
# Flow configuration
from osbot_utils.helpers.flows.models.Flow_Run__Config import Flow_Run__Config
```

### Events

```python
# Event system
from osbot_utils.helpers.flows.actions.Flow__Events import Flow_Events, flow_events
from osbot_utils.helpers.flows.models.Flow_Run__Event import Flow_Run__Event
from osbot_utils.helpers.flows.models.Flow_Run__Event_Data import Flow_Run__Event_Data
from osbot_utils.helpers.flows.models.Flow_Run__Event_Type import Flow_Run__Event_Type
```

### Data and Statistics

```python
# Flow data management
from osbot_utils.helpers.flows.actions.Flow__Data import Flow__Data
from osbot_utils.helpers.flows.actions.Flow__Stats__Collector import Flow__Stats__Collector
from osbot_utils.helpers.flows.actions.Task__Stats__Collector import Task__Stats__Collector

# Schemas
from osbot_utils.helpers.flows.schemas.Schema__Flow import Schema__Flow
from osbot_utils.helpers.flows.schemas.Schema__Flow__Data import Schema__Flow__Data
from osbot_utils.helpers.flows.schemas.Schema__Flow__Stats import Schema__Flow__Stats
from osbot_utils.helpers.flows.schemas.Schema__Flow__Status import Schema__Flow__Status
from osbot_utils.helpers.flows.schemas.Schema__Task__Stats import Schema__Task__Stats

# Models
from osbot_utils.helpers.flows.models.Schema__Flow__Artifact import Schema__Flow__Artifact
from osbot_utils.helpers.flows.models.Schema__Flow__Result import Schema__Flow__Result
```

---

## API Reference

### Flow Class

#### Setup and Execution

| Method | Description | Returns |
|--------|-------------|---------|
| `setup(target, *args, **kwargs)` | Configure flow with target function and arguments | `self` |
| `execute()` | Execute the configured flow | `self` |
| `execute_flow(flow_run_params=None)` | Execute with optional runtime parameters | `self` |

#### Logging

| Method | Description |
|--------|-------------|
| `log_debug(message)` | Log debug-level message |
| `log_info(message)` | Log info-level message |
| `log_error(message)` | Log error-level message |
| `log_messages()` | Get all log messages (without ANSI colors) |
| `captured_logs()` | Get captured execution logs |
| `print_log_messages(use_colors=True)` | Print all log messages |

#### Artifacts and Results

| Method | Description |
|--------|-------------|
| `add_flow_artifact(description, key, data, artifact_type)` | Store an artifact |
| `add_flow_result(key, description)` | Record a result |

#### Statistics and Output

| Method | Description | Returns |
|--------|-------------|---------|
| `durations()` | Get timing data for flow and tasks | `dict` |
| `durations__with_tasks_status()` | Get timing with status info | `dict` |
| `flow_output()` | Get return value and durations | `dict` |
| `json()` | Serialize flow data to dict | `dict` |

#### Key Attributes

| Attribute | Type | Description |
|-----------|------|-------------|
| `flow_id` | `str` | Unique identifier for this flow run |
| `flow_name` | `str` | Name of the flow |
| `flow_return_value` | `Any` | Return value from the target function |
| `flow_error` | `Exception` | Exception if flow failed, else None |
| `flow_config` | `Flow_Run__Config` | Configuration options |
| `data` | `dict` | Shared data dictionary |
| `executed_tasks` | `List[Task]` | Tasks executed in this flow |

### Task Class

#### Execution

| Method | Description | Returns |
|--------|-------------|---------|
| `execute__sync()` | Execute task synchronously | Return value |
| `execute__async()` | Execute task asynchronously | Return value |
| `find_flow()` | Find parent Flow in call stack | `Flow` or `None` |

#### Logging

| Method | Description |
|--------|-------------|
| `log_debug(message)` | Log debug-level message |
| `log_info(message)` | Log info-level message |
| `log_error(message)` | Log error-level message |

#### Key Attributes

| Attribute | Type | Description |
|-----------|------|-------------|
| `task_id` | `str` | Unique identifier for this task run |
| `task_name` | `str` | Name of the task |
| `task_target` | `callable` | Function to execute |
| `task_return_value` | `Any` | Return value from the task |
| `task_error` | `Exception` | Exception if task failed, else None |
| `raise_on_error` | `bool` | Whether to raise on error (default: True) |
| `task_flow` | `Flow` | Parent flow instance |
| `data` | `dict` | Task-scoped data dictionary |

### Decorator Parameters

#### @flow() Decorator

```python
@flow(flow_config=None, flow_id=None, flow_name=None, **kwargs)
def my_flow():
    pass
```

| Parameter | Type | Description |
|-----------|------|-------------|
| `flow_config` | `Flow_Run__Config` | Configuration options |
| `flow_id` | `str` | Custom flow ID (auto-generated if not set) |
| `flow_name` | `str` | Custom flow name (uses function name if not set) |

#### @task() Decorator

```python
@task(task_name=None, raise_on_error=True, **kwargs)
def my_task():
    pass
```

| Parameter | Type | Default | Description |
|-----------|------|---------|-------------|
| `task_name` | `str` | Function name | Custom task name |
| `raise_on_error` | `bool` | `True` | Raise exception on task failure |

### Flow_Run__Config Options

```python
from osbot_utils.helpers.flows.models.Flow_Run__Config import Flow_Run__Config

config = Flow_Run__Config(
    add_task_to_self          = True,   # Add executed tasks to flow.executed_tasks
    log_to_console            = False,  # Print logs to console
    log_to_memory             = True,   # Store logs in memory for later retrieval
    logging_enabled           = True,   # Enable/disable all logging
    print_logs                = False,  # Print logs after execution
    print_none_return_value   = False,  # Log when return value is None
    print_finished_message    = False,  # Log "Finished flow run" message
    print_error_stack_trace   = False,  # Print full stack trace on error
    raise_flow_error          = True,   # Raise exception if flow fails
    flow_data__capture_events = False,  # Store events in flow_data
)
```

---

## Events & Observability

### Event Types

```python
from osbot_utils.helpers.flows.models.Flow_Run__Event_Type import Flow_Run__Event_Type

# Available event types
Flow_Run__Event_Type.FLOW_START    # Flow execution started
Flow_Run__Event_Type.FLOW_STOP     # Flow execution completed
Flow_Run__Event_Type.FLOW_MESSAGE  # Log message emitted
Flow_Run__Event_Type.TASK_START    # Task execution started
Flow_Run__Event_Type.TASK_STOP     # Task execution completed
Flow_Run__Event_Type.NEW_ARTIFACT  # Artifact added
Flow_Run__Event_Type.NEW_RESULT    # Result recorded
```

### Registering Event Listeners

```python
from osbot_utils.helpers.flows.actions.Flow__Events import flow_events
from osbot_utils.helpers.flows.models.Flow_Run__Event import Flow_Run__Event
from osbot_utils.helpers.flows.models.Flow_Run__Event_Type import Flow_Run__Event_Type

def my_listener(event: Flow_Run__Event):
    if event.event_type == Flow_Run__Event_Type.TASK_START:
        print(f"Task started: {event.event_data.task_name}")
    elif event.event_type == Flow_Run__Event_Type.TASK_STOP:
        print(f"Task completed: {event.event_data.task_name}")
    elif event.event_type == Flow_Run__Event_Type.FLOW_MESSAGE:
        message = event.event_data.data['message_data']['message']
        print(f"Log: {message}")

# Register listener
flow_events.event_listeners.append(my_listener)

# Execute flow - events will be emitted
result = my_flow().execute()

# Clean up
flow_events.event_listeners.remove(my_listener)
```

### Event Data Structure

```python
# Flow_Run__Event contains:
event.event_id    # Random_Guid - unique event identifier
event.event_type  # Flow_Run__Event_Type enum
event.event_data  # Flow_Run__Event_Data with details
event.timestamp   # Timestamp_Now - when event occurred

# Flow_Run__Event_Data contains:
event_data.flow_name    # Name of the flow
event_data.flow_run_id  # Flow execution ID
event_data.task_name    # Task name (if task event)
event_data.task_run_id  # Task execution ID (if task event)
event_data.log_level    # Log level (for message events)
event_data.data         # Event-specific payload dict
```

### Statistics Collection

```python
@flow()
def my_flow():
    task_one()
    task_two()
    return "done"

result = my_flow().execute()

# Get timing information
print(result.durations())
# {
#     'flow_name': 'my_flow',
#     'flow_duration': 0.0234,
#     'flow_status': 'completed',
#     'flow_tasks': {
#         'task_one': 0.0102,
#         'task_two': 0.0128
#     }
# }

# Get timing with status
print(result.durations__with_tasks_status())
# {
#     'flow_name': 'my_flow',
#     'flow_duration': 0.0234,
#     'flow_status': 'completed',
#     'flow_tasks': {
#         1: {'task_name': 'task_one', 'task_duration': 0.0102, 'task_status': 'completed'},
#         2: {'task_name': 'task_two', 'task_duration': 0.0128, 'task_status': 'completed'}
#     }
# }
```

---

## Error Handling

### Task-Level Error Control

```python
# Task raises exception - stops flow (default)
@task(raise_on_error=True)
def critical_task():
    raise ValueError("Critical failure")

# Task catches exception - flow continues
@task(raise_on_error=False)
def optional_task():
    raise ValueError("Non-critical failure")
    # Returns None, error logged, flow continues
```

### Flow-Level Error Control

```python
from osbot_utils.helpers.flows.models.Flow_Run__Config import Flow_Run__Config

# Flow raises exception on any error (default)
config = Flow_Run__Config(raise_flow_error=True)

@flow(flow_config=config)
def strict_flow():
    critical_task()  # Exception propagates

# Flow catches exception - returns with error info
config = Flow_Run__Config(raise_flow_error=False)

@flow(flow_config=config)
def resilient_flow():
    optional_task()  # Error logged, flow completes

result = resilient_flow().execute()
if result.flow_error:
    print(f"Flow had error: {result.flow_error}")
```

### Error Recovery Pattern

```python
@task(raise_on_error=False)
def fetch_with_fallback(this_flow=None):
    try:
        return primary_api.fetch()
    except Exception as error:
        this_flow.log_error(f"Primary API failed: {error}")
        this_flow.add_flow_artifact(
            key="error_details",
            data=str(error),
            artifact_type="error",
            description="Primary API failure"
        )
        return fallback_api.fetch()

@flow(flow_config=Flow_Run__Config(raise_flow_error=False))
def resilient_pipeline():
    data = fetch_with_fallback()
    if data:
        return process_data(data)
    return None
```

### Checking Task/Flow Status

```python
result = my_flow().execute()

# Check flow status
if result.flow_error:
    print(f"Flow failed: {result.flow_error}")
else:
    print(f"Flow succeeded: {result.flow_return_value}")

# Check individual task status via stats
stats = result.durations__with_tasks_status()
for order, task_info in stats['flow_tasks'].items():
    if task_info['task_status'] == 'failed':
        print(f"Task {task_info['task_name']} failed")
```

---

## Common Patterns

### Data Sharing Between Tasks

```python
@task()
def fetch_users(flow_data=None):
    users = api.get_users()
    flow_data['users'] = users        # Store for later tasks
    return len(users)

@task()
def process_users(flow_data=None):
    users = flow_data['users']        # Retrieve from earlier task
    processed = [transform(u) for u in users]
    flow_data['processed'] = processed
    return len(processed)

@task()
def save_results(flow_data=None):
    processed = flow_data['processed']
    return database.save_all(processed)

@flow()
def user_pipeline():
    fetch_count = fetch_users()
    process_count = process_users()
    saved = save_results()
    return {'fetched': fetch_count, 'processed': process_count, 'saved': saved}
```

### Nested Tasks

```python
@task()
def inner_task():
    return 42

@task()
def outer_task():
    # Tasks can call other tasks
    result = inner_task()
    return result + 1

@flow()
def nested_flow():
    return outer_task()  # Returns 43

result = nested_flow().execute()
# Both inner_task and outer_task are tracked
print(len(result.executed_tasks))  # 2
```

### Adding Artifacts and Results

```python
@flow()
def analysis_flow(this_flow=None):
    # Process data
    data = fetch_and_process()
    
    # Store artifact (structured data)
    this_flow.add_flow_artifact(
        description="Analysis output data",
        key="analysis-output",
        data={"records": len(data), "summary": summarize(data)},
        artifact_type="json"
    )
    
    # Store result (simple key-value)
    this_flow.add_flow_result(
        key="record-count",
        description=f"Processed {len(data)} records"
    )
    
    return data

# Access artifacts via flow data
result = analysis_flow().execute()
flow_json = result.json()
print(flow_json['flow_data']['artifacts'])
print(flow_json['flow_data']['results'])
```

### Class-Based Flow with Inheritance

```python
from osbot_utils.type_safe.Type_Safe import Type_Safe

class BaseDataFlow(Type_Safe):
    source : str
    
    @task()
    def validate_source(self):
        if not self.source:
            raise ValueError("Source required")
        return True

class CSVDataFlow(BaseDataFlow):
    delimiter : str = ','
    
    @task()
    def read_csv(self, flow_data=None):
        import csv
        with open(self.source) as f:
            reader = csv.reader(f, delimiter=self.delimiter)
            flow_data['rows'] = list(reader)
        return len(flow_data['rows'])
    
    @flow()
    def process(self):
        self.validate_source()
        count = self.read_csv()
        return f"Read {count} rows"

# Usage
csv_flow = CSVDataFlow(source='/data/input.csv')
result = csv_flow.process().execute()
```

### Using Flow ID for Correlation

```python
@flow(flow_id='batch-2024-01-15-001')
def batch_job():
    # All tasks and events use this flow_id
    process_batch()
    return "complete"

# Or set dynamically
@flow()
def dynamic_batch():
    pass

flow_instance = dynamic_batch()
flow_instance.flow_id = f"batch-{datetime.now().strftime('%Y%m%d-%H%M%S')}"
flow_instance.execute()
```

---

## Best Practices

### DO: Always Call setup() Before execute()

```python
# ✓ Correct - decorator handles setup
@flow()
def my_flow():
    return "done"
my_flow().execute()

# ✓ Correct - explicit setup
flow = Flow()
flow.setup(my_function, arg1, arg2)
flow.execute()

# ✗ Wrong - missing setup
flow = Flow()
flow.execute()  # Raises ValueError
```

### DO: Use Decorators for Simple Cases

```python
# ✓ Clean and readable
@task()
def process(data):
    return transform(data)

@flow()
def pipeline(input_data):
    return process(input_data)

# ✗ Verbose for simple cases
def pipeline(input_data):
    with Flow() as flow:
        flow.setup(lambda: None)
        with Task() as task:
            task.task_target = lambda: transform(input_data)
            return task.execute__sync()
```

### DO: Use flow_data for Cross-Task Communication

```python
# ✓ Correct - use flow_data
@task()
def step_one(flow_data=None):
    flow_data['result'] = compute()

@task()  
def step_two(flow_data=None):
    return flow_data['result'] * 2

# ✗ Wrong - global variables
result = None

@task()
def step_one():
    global result
    result = compute()
```

### DO: Configure Error Handling Appropriately

```python
# ✓ Critical tasks should raise
@task(raise_on_error=True)
def must_succeed():
    return critical_operation()

# ✓ Optional tasks can continue on failure
@task(raise_on_error=False)
def nice_to_have():
    return optional_operation()
```

### DON'T: Forget to Clean Up Event Listeners

```python
# ✗ Memory leak - listener never removed
flow_events.event_listeners.append(my_listener)
run_many_flows()

# ✓ Clean up after use
flow_events.event_listeners.append(my_listener)
try:
    run_many_flows()
finally:
    flow_events.event_listeners.remove(my_listener)
```

### DON'T: Use task_data for Cross-Task Communication

```python
# ✗ Wrong - task_data is scoped to single task
@task()
def step_one(task_data=None):
    task_data['value'] = 42  # Lost after task completes

@task()
def step_two(task_data=None):
    return task_data.get('value')  # Returns None!

# ✓ Correct - use flow_data
@task()
def step_one(flow_data=None):
    flow_data['value'] = 42  # Persists across tasks
```

### DON'T: Disable Logging in Production Without Reason

```python
# ✗ Loses valuable debugging information
config = Flow_Run__Config(logging_enabled=False)

# ✓ Keep logging, control output
config = Flow_Run__Config(
    logging_enabled=True,
    log_to_memory=True,    # Capture for later
    log_to_console=False,  # Don't print during execution
    print_logs=False       # Don't print after execution
)
```

---

## Troubleshooting

### Problem: "Setup has not been called" Error

**Cause**: Calling `execute()` without first calling `setup()`.

```python
# ✗ This fails
flow = Flow()
flow.execute()  # ValueError: setup has not been called

# ✓ Solution
flow = Flow()
flow.setup(my_function)
flow.execute()

# ✓ Or use decorator (handles setup automatically)
@flow()
def my_function():
    pass
my_function().execute()
```

### Problem: Task Cannot Find Flow Context

**Cause**: Task executed outside a Flow context.

```python
# ✗ This fails - no Flow in call stack
@task()
def my_task():
    return "done"

my_task()  # Exception: No Flow found for Task

# ✓ Solution - execute within a flow
@flow()
def my_flow():
    return my_task()

my_flow().execute()
```

### Problem: Events Not Firing

**Cause 1**: Listener not registered before flow execution.

```python
# ✗ Wrong order
result = my_flow().execute()
flow_events.event_listeners.append(my_listener)  # Too late!

# ✓ Correct order
flow_events.event_listeners.append(my_listener)
result = my_flow().execute()
```

**Cause 2**: Logging disabled.

```python
# With logging disabled, FLOW_MESSAGE events won't fire
config = Flow_Run__Config(logging_enabled=False)
```

### Problem: Async Flow Not Executing Properly

**Cause**: Not awaiting async tasks within async flow.

```python
# ✗ Wrong - forgot await
@flow()
async def bad_async_flow():
    async_task()  # Returns coroutine, doesn't execute!
    return "done"

# ✓ Correct
@flow()
async def good_async_flow():
    result = await async_task()
    return result
```

### Problem: flow_data Empty in Later Tasks

**Cause**: Using `task_data` instead of `flow_data`.

```python
# ✗ Wrong - task_data doesn't persist
@task()
def task_one(task_data=None):
    task_data['key'] = 'value'

# ✓ Correct - flow_data persists
@task()
def task_one(flow_data=None):
    flow_data['key'] = 'value'
```

### Problem: Return Value is None

**Cause 1**: Task has `raise_on_error=False` and threw exception.

```python
@task(raise_on_error=False)
def failing_task():
    raise ValueError("oops")
    return "never reached"

# Returns None because exception was caught
```

**Cause 2**: Flow function doesn't return anything.

```python
# ✗ No return
@flow()
def my_flow():
    do_stuff()
    # Missing return!

# ✓ Explicit return
@flow()
def my_flow():
    do_stuff()
    return "completed"
```

### Problem: Logs Not Captured

**Cause**: `log_to_memory` is disabled.

```python
# ✗ Logs not captured
config = Flow_Run__Config(log_to_memory=False)

# ✓ Enable memory logging
config = Flow_Run__Config(log_to_memory=True)

result = my_flow().execute()
print(result.captured_logs())  # Now contains logs
```

---

## Summary Checklist

When working with the Flow System:

- [ ] Import from `osbot_utils.helpers.flows`
- [ ] Use `@flow()` decorator for flow functions
- [ ] Use `@task()` decorator for task functions
- [ ] Always call `setup()` before `execute()` (decorators handle this)
- [ ] Tasks must execute within a Flow context
- [ ] Use `flow_data` for cross-task data sharing
- [ ] Use `task_data` for task-local data only
- [ ] Configure `raise_on_error=False` for non-critical tasks
- [ ] Configure `raise_flow_error=False` for resilient flows
- [ ] Register event listeners before executing flows
- [ ] Clean up event listeners after use
- [ ] Use `durations()` to get timing statistics
- [ ] Use `captured_logs()` to retrieve log messages
- [ ] Use `json()` to serialize complete flow data
- [ ] Access return value via `flow_return_value` attribute
- [ ] Check `flow_error` attribute for exceptions
- [ ] Use `add_flow_artifact()` to store structured data
- [ ] Use `add_flow_result()` to record simple results
- [ ] Remember: `flow_data` persists, `task_data` doesn't
- [ ] Remember: async flows execute in a new event loop
