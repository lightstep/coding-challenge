# Introduction

Welcome to the LightStep coding exercise. The goal here is to give you an opportunity to demonstrate your programming 
abilities. Below, we describe a problem that relates to our business. We are looking for a code solution to this problem
that can read in the provided example input and answer some questions about that data.

* We are looking to see how you approach problems, break them down into smaller parts, and come to solutions.
* Choose the language you are most comfortable in. We don’t have any preferences and we want to see how you write code 
in your preferred language.
* There is no time limit, but we expecting this to take somewhere in the realm of 1-2 hours to solve. (If you find 
you’re spending more time, please check in with us. We want to respect your time.)
* Please feel free to use any resources that would be available to you in your day-to-day work (Google, documentation, 
libraries, IDEs, Stackoverflow, etc.).
  * It’s completely appropriate to copy-paste code directly from Stack Overflow or an open source project. When you do, 
  include a comment with a link to the source.

We will evaluate the code using the following criteria (in order of importance):
* _clean code_ - If another engineer had to add a new feature to your code, how easy would it be for them to do so?
* _correctness_ - Does it solve the problem described?
* _tested_ - Does the code feel well tested enough (We are not looking for every single line to be completely tested, 
but what do you feel like is reasonable test coverage?)

We are not necessarily looking for the 100% most performant or ideal solution. Performance is great, but code-clarity is 
more important.

After you complete the exercise, we will schedule some time for you to sit with one of our engineers and talk through 
what you wrote. During that time, we will ask you to:

* Explain the performance of the code.
* Explain some choices you made and why you made them.
* Explain some ways you might improve or extend your solution if you were getting ready to run it in a more 
production-like environment.

If you have any questions, please reach out. 

# Problem

LightStep is built on the idea of tracing data as it moves around the internet and datacenter. One approach to gathering 
together that data is to read and parse logs. In our problem, we will give you a sample log file and ask you to build a 
program that can answer some questions about the system described by the log file. While this problem reflects many of 
the things that LightStep does, we’re not assuming that you know anything about LightStep or how it works.

## Terms
* _services_ are the computers that operate in our system. In this example, all of the services are web servers hosting 
http traffic. Some services will call out to other services (for example the load balancer might send get an HTTP 
request for `/login` and call out to the login service).
* _operations_ a specific piece of work completed by a service. In this example the db_service might perform a lookup of 
a user in a table.
* _transactions_ Transactions represent a single action that is taken that might involve multiple services and 
operations. Each transaction has a unique id (`transaction_id`).

## Log Format
We provide you with an example log file that contains log entries from all of the services in our system. Logs 
files will be a json list containing individual log entries which are dictionaries that look like this:

```
{
    "service": "loadbalancer",
    "level": "INFO",
    "timestamp": "2017-10-17 00:00:00.000000",
    "operation": "POST",
    "message": "START /login requested",
    "transaction_id": "3cf9629d-c05c-4916-a800-f2be630b0bc9"
}
```

## Data Dictionary

| service | A unique name for the service that created this log line. |
| level   | Either DEBUG, INFO, WARNING, or ERROR.                     |
| timestamp | A string representation of the time this log line was printed. |
| operation | A unique name for the piece of work being completed by the service. You can think of this like a method name. |
| message | The operations write a log line at the beginning of their work and also at the end of their work. All messages will begin with either START or END to indicate which they are. |
| transaction_id | A unique id for what transaction that caused this log line to be written. Call chains will share the same transaction_id. So for example if a request to the account_service requires a call to the db_service to complete its work, both of those operations will share the same transaction_id. |

Together, all of the log entries from a single transaction will look like this:

```
[ 
   {
        "service": "loadbalancer",
        "level": "INFO",
        "timestamp": "2017-10-17 00:00:00.000000",
        "operation": "POST",
        "message": "START /login requested",
        "transaction_id": "3cf9629d-c05c-4916-a800-f2be630b0bc9"
    },
    {
        "service": "webserver",
        "level": "DEBUG",
        "timestamp": "2017-10-17 00:00:00.207697",
        "operation": "/login",
        "message": "START Logging in user",
        "transaction_id": "3cf9629d-c05c-4916-a800-f2be630b0bc9"
    },
    {
        "service": "authentication_service",
        "level": "ERROR",
        "timestamp": "2017-10-17 00:00:01.038673",
        "operation": "AuthenticateUser",
        "message": "START Authenticating user",
        "transaction_id": "3cf9629d-c05c-4916-a800-f2be630b0bc9"
    },
    {
        "service": "authentication_service",
        "level": "WARNING",
        "timestamp": "2017-10-17 00:00:01.384007",
        "operation": "AuthenticateUser",
        "message": "END Authenticating user",
        "transaction_id": "3cf9629d-c05c-4916-a800-f2be630b0bc9"
    },
    {
        "service": "webserver",
        "level": "DEBUG",
        "timestamp": "2017-10-17 00:00:02.271526",
        "operation": "/login",
        "message": "END Logging in user",
        "transaction_id": "3cf9629d-c05c-4916-a800-f2be630b0bc9"
    },
    {
        "service": "loadbalancer",
        "level": "WARNING",
        "timestamp": "2017-10-17 00:00:02.637356",
        "operation": "POST",
        "message": "END /login requested",
        "transaction_id": "3cf9629d-c05c-4916-a800-f2be630b0bc9"
    }
]
```

# Task

Write a program to read in a set of log entries (found [in this repository](input.json); warning: it’s a big file!) and 
answer the following questions. The tool can take any form you want (for example, a command-line program is fine). For 
both questions, you’ll first need to collect together all of the log entries for each transaction, then perform an 
analysis of those transactions.

# Questions

**Find the operation that has the highest error count.** We define error count as the number of transactions where that 
operation printed at least one ERROR level log entry.

**Find the longest transaction.** We define the longest transaction to be the transaction where the duration between the 
timestamp of the START of the first operation and the timestamp of the END of the last operation is the greatest.

# Conclusion

When you are done, please send us your code and answers to the questions so we can take a look at in advance of 
discussing it. You are welcome to send us a zipped folder with your code or share it using a repository service (like 
Github).
