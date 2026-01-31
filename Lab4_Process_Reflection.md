# Lab 4 – Process Management Reflection

### What this lab does

This lab explores how processes are created, executed, and managed in a UNIX-based operating system using system calls such as `fork()`, `execvp()`, `wait()`, and `sleep()`. Through a series of small C programs (`p1`, `p2`, and `p3`), the lab demonstrates how parent and child processes interact, how execution paths diverge after a `fork()`, and how the operating system schedules processes during runtime. Instead of focusing on large programs, the lab uses short, targeted examples to isolate specific behaviors and make them easier to observe.

The lab also introduces more advanced process behaviors, including orphan processes, zombie processes, and output redirection. By modifying the timing and structure of parent and child execution, the programs make it possible to observe how the operating system reassigns parent process IDs, how zombie processes occur when exit statuses are not collected, and how output can be redirected away from the terminal using file descriptors. These exercises helped bridge the gap between theoretical operating system concepts discussed in lecture and real system behavior that can be verified using tools like `ps`.

---

### Explanation of each program

The first program (`p1`) focuses on basic process creation using the `fork()` system call. After `fork()` is called, both the parent and child processes continue executing the same code, but take different paths depending on the return value of `fork()`. The child receives a return value of `0`, while the parent receives the child’s process ID. This program highlights how a single line of code can result in two independent execution flows that run concurrently.

By adding `sleep()` calls and selectively omitting `wait()`, `p1` was used to demonstrate orphan and zombie processes. When the parent process terminates before the child, the child continues running and becomes an orphan, which can be verified when its PPID changes to `1`. When the child terminates first and the parent remains alive without calling `wait()`, the child becomes a zombie process. Observing these states using `ps -l` made the process lifecycle much more concrete.

The second program (`p2`) introduces the `execvp()` system call. In this program, the child process replaces its current program image with a new program (`wc`) using `execvp()`. This demonstrates that `execvp()` does not return on success, because the original program code is completely replaced. Any code written after `execvp()` in the child process is never executed, which reinforces the idea that `exec()` fundamentally transforms the process rather than creating a new one.

The parent process in `p2` uses `wait()` to pause execution until the child finishes. This ensures predictable behavior and allows the parent to retrieve the child’s exit status. Together, `fork()`, `execvp()`, and `wait()` demonstrate the standard UNIX model for creating and managing new programs.

The third program (`p3`) demonstrates output redirection at the file descriptor level. In the child process, standard output is explicitly closed and then redirected to a file before calling `execvp()`. As a result, the output of the `wc` program does not appear in the terminal and is instead written to a file (`p3.output`). This program illustrates how output redirection works internally and why separating `fork()` and `exec()` is essential for flexible process control in UNIX systems.

---

### What I learned

Through this lab, I gained a much deeper understanding of how process creation works in practice. I learned that calling `fork()` does not simply “split” a program in a conceptual sense, but instead creates two fully independent processes with their own memory spaces. This explains how the same variable can satisfy different conditions in different processes at the same time, and why both parent and child code paths execute after a single `fork()` call.

I also learned how important synchronization is in process management. Without using `wait()`, the operating system scheduler determines whether the parent or child runs first, which makes output order unpredictable. Adding or removing `wait()` immediately changes program behavior, demonstrating how much control the developer has over process execution order. Seeing this behavior change in real time helped clarify concepts that initially felt abstract.

Additionally, I learned how orphan and zombie processes form and how to identify them using system tools. Watching a child process get adopted by the init process (PPID = 1) and observing zombie processes marked as `Z` or `<defunct>` made these concepts far more intuitive than simply reading about them. This lab emphasized that timing plays a critical role in process state transitions.

Finally, I developed a clearer understanding of output redirection at a low level. By closing `STDOUT` and opening a file before calling `execvp()`, I saw how file descriptors are inherited across program execution. This helped explain how shells are able to redirect output without modifying the underlying programs, and why UNIX process design is both flexible and powerful.

---

### What was confusing at first

At the beginning of this lab, it was confusing why both the parent and child processes appeared to execute the same code after a `fork()` call, and how a single variable could appear to hold two different values simultaneously. It was also unclear why the order of printed output changed between runs and why some programs appeared to produce no output in the terminal even though they were functioning correctly.

Understanding orphan and zombie processes was especially challenging at first, since these states are short-lived and highly dependent on timing. It took multiple attempts with different `sleep()` durations and careful use of `ps -l` to reliably observe these behaviors. Similarly, output redirection initially felt like “missing output” until I understood how file descriptors were being reassigned.

Overall, this lab required more experimentation and observation than previous assignments. Rather than focusing solely on writing correct code, I had to pay close attention to system behavior and process state. By the end of the lab, I felt much more confident in my understanding of how UNIX processes are created, scheduled, synchronized, and terminated, and how these low-level mechanisms support higher-level system functionality.

---

Delali Doe  
Computer Science (Kean University)  
Published: 1/31/2026  

