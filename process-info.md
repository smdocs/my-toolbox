#Process Information and Debugging

> For every resource, check utilization, saturation, and errors. (Brendan Gregg)

#### Process Management

<b> strace </b>
  When to run: Any time a detailed process trace is required, especially when system call-level tracing is required. Very useful to see calls to system calls such as open(), read(), and write().

Caution: Avoid running it in PROD since it can slow down or even crash the process being traced.


#### References

- [USE method checklist](http://www.brendangregg.com/USEmethod/use-linux.html)
