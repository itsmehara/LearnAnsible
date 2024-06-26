### 20. Error Handling in Playbook

Error handling is a crucial part of writing reliable and robust Ansible playbooks. 
Ansible provides several mechanisms to handle errors, such as `ignore_errors`, `block`, `rescue`, `always`, and retry mechanisms with `retries` and `until`. 

This section covers these techniques in detail with examples.

#### Using `ignore_errors`

The `ignore_errors` directive allows you to ignore errors on a particular task. 
When `ignore_errors` is set to `yes`, the playbook will continue executing subsequent tasks even if the current task fails.

**Example: Using `ignore_errors`**

Create a playbook named `ignore_errors.yml`:

```yaml
---
- name: Demonstrate ignore_errors
  hosts: localhost
  tasks:
    - name: This will fail but will be ignored
      command: /bin/false
      ignore_errors: yes

    - name: This will still run
      command: /bin/echo "This task runs even if the previous one fails"
```

In this example, the first task is designed to fail, but the `ignore_errors: yes` directive ensures that the second task will still execute.

#### Handling Errors with `block`, `rescue`, and `always`

The `block`, `rescue`, and `always` directives provide a way to group tasks and define error handling and cleanup steps.

- `block`: A group of tasks to be executed.
- `rescue`: Tasks to be executed if any task in the block fails.
- `always`: Tasks to be executed regardless of the success or failure of the block.

**Example: Using `block`, `rescue`, and `always`**

Create a playbook named `block_rescue_always.yml`:

```yaml
---
- name: Demonstrate block, rescue, and always
  hosts: localhost
  tasks:
    - name: Block demo
      block:
        - name: This will fail
          command: /bin/false

        - name: This will not run because the previous task failed
          command: /bin/echo "This will not run"

      rescue:
        - name: This will run because the block failed
          command: /bin/echo "Error handling task"

      always:
        - name: This will always run
          command: /bin/echo "Cleanup task"
```

In this example, the task inside the `block` will fail, triggering the tasks in the `rescue` section. 
The tasks in the `always` section will run regardless of the failure in the `block`.

#### Retrying Tasks with `retries` and `until`

The `retries` and `until` directives allow you to retry a task until a condition is met or a maximum number of retries is reached.

- `retries`: Specifies the number of times to retry a task.
- `until`: A condition that must be met for the task to succeed.

**Example: Retrying a Task**

Create a playbook named `retry_task.yml`:

```yaml
---
- name: Demonstrate retries and until
  hosts: localhost
  tasks:
    - name: Retry until success
      command: /bin/false
      register: result
      retries: 5
      delay: 2
      until: result.rc == 0
      ignore_errors: yes

    - name: This will run after retries
      debug:
        msg: "Task completed after retries"
```

In this example, the task will be retried up to five times with a two-second delay between each attempt. 
The `until` condition checks the return code (`result.rc == 0`). If the condition is not met after the specified retries, the task will fail.

#### Combined Example

**Example: Combining All Error Handling Techniques**

Create a playbook named `combined_error_handling.yml`:

```yaml
---
- name: Combined Error Handling
  hosts: localhost
  tasks:
    - name: Block demo with retry
      block:
        - name: This will fail and be retried
          command: /bin/false
          register: result
          retries: 3
          delay: 2
          until: result.rc == 0
          ignore_errors: yes

        - name: This will not run because the previous task failed
          command: /bin/echo "This will not run"

      rescue:
        - name: This will run because the block failed
          command: /bin/echo "Error handling task"

      always:
        - name: This will always run
          command: /bin/echo "Cleanup task"
```

In this combined example, the first task within the block will fail and be retried three times. If it still fails, the `rescue` section will execute, followed by the `always` section.

### Conclusion

Effective error handling in Ansible playbooks is essential for creating reliable automation workflows. 
By using directives such as `ignore_errors`, `block`, `rescue`, `always`, and retry mechanisms with `retries` and `until`, 
you can control how your playbooks respond to errors and ensure that necessary cleanup or error-handling steps are always executed. 
The provided examples should help you understand and implement these techniques in your Ansible projects.
