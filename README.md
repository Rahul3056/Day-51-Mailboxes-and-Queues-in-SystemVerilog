### **Day 51: Mailboxes and Queues in SystemVerilog**

---

### **Objective:**

Learn how to use **mailboxes** and **queues** in SystemVerilog for communication and synchronization between processes or components in a testbench, especially in constrained-random and transaction-level modeling.

---

## ✅ 1. Queues in SystemVerilog

A **queue** is a variable-size, ordered collection of elements—like a dynamic array with push/pop capability.

### **Syntax:**

```systemverilog
data_type queue_name[$]; // Declares a queue
```

### **Basic Operations:**

```systemverilog
int q[$];        // Declares a queue of ints

q.push_back(10); // Add at end
q.push_front(5); // Add at beginning

int x = q.pop_front();  // Remove from front
int y = q.pop_back();   // Remove from end

int size = q.size();    // Get queue size
```

### **Example:**

```systemverilog
module tb_queue;

    int q[$];

    initial begin
        q.push_back(1);
        q.push_back(2);
        q.push_back(3);
        $display("Queue: %p", q); // prints: {1 2 3}

        int val = q.pop_front();
        $display("Popped: %0d, Remaining: %p", val, q); // {2 3}
    end

endmodule
```

---

## ✅ 2. Mailboxes in SystemVerilog

A **mailbox** is a synchronization and communication mechanism used to safely pass data between concurrent processes (e.g., driver and monitor).

Mailboxes are **blocking** or **non-blocking**.

### **Declaration:**

```systemverilog
mailbox mbox;             // Unbounded
mailbox #(int) mbox2;     // Bounded and typed
```

### **Common Methods:**

| Method              | Description                                |
| ------------------- | ------------------------------------------ |
| `mbox.put(val)`     | Places `val` into the mailbox (blocking)   |
| `mbox.get(var)`     | Retrieves a value (blocking)               |
| `mbox.try_get(var)` | Retrieves if available (non-blocking)      |
| `mbox.peek(var)`    | Reads without removing                     |
| `mbox.num()`        | Number of entries currently in the mailbox |

---

## ✅ 3. Example: Communication Between Two Processes

```systemverilog
module tb_mailbox;

    mailbox mbox = new();

    // Sender process
    initial begin
        for (int i = 0; i < 5; i++) begin
            $display("Sending: %0d", i);
            mbox.put(i);
            #5;
        end
    end

    // Receiver process
    initial begin
        int data;
        repeat (5) begin
            mbox.get(data); // blocks until data is available
            $display("Received: %0d", data);
        end
    end

endmodule
```

### **Output (sample):**

```
Sending: 0
Received: 0
Sending: 1
Received: 1
...
```

---

## ✅ Use Cases

| Scenario                         | Use Feature   |
| -------------------------------- | ------------- |
| Storing transactions in a driver | Queue         |
| Communicating between threads    | Mailbox       |
| Buffering stimuli or responses   | Queue/Mailbox |
| Synchronizing sender/receiver    | Mailbox       |

---

## 🧠 Summary

| Feature  | Queue              | Mailbox                    |
| -------- | ------------------ | -------------------------- |
| Type     | Ordered collection | Blocking FIFO queue        |
| Access   | Indexed, push/pop  | put/get/peek/try\_get      |
| Use Case | Local storage      | Inter-thread communication |
| Blocking | No                 | Yes (unless try\_get)      |


