# Thread

## Composition

- Register set: a set of register state
- Program counter: pointer to next instruction to be executed
- Stack: contains temporary data (such as function parameters, return address and local variables)
- Text, Heap, Data: shared across threads of the same process

<!-- ![Single-threaded process and multithreaded process](images/process_thread.png) -->

## Multithreading Models

- Many to One: N user threads are multiplexed to 1 kernel thread. This has the advantage of being very quick to context switch but cannot take advantage of multi-core systems.
- One to One: 1 user thread is multiplexed to 1 kernel thread. It takes advantage of all of the cores on the machine, but context switching is slow because it has to trap through the OS.
- Many to Many: N user threads are multiplexed to N kernel thread. You get quick context switches and you take advantage of all the cores in your system. The main disadvantage of this approach is the complexity it adds to the scheduler.

In Many to One model, when a user thread making an I/O request, all other threads are also blocked. The reason is that while waiting for I/O, kernel thread is in waiting state and not scheduled any CPU.

## Race Condition and Data Race

A data race occurs when 2 instructions from different threads access the same memory location, at least one of these accesses is a write and there is no synchronization that is mandating any particular order among these accesses.

A race condition is a semantic error. It is a flaw that occurs in the timing or the ordering of events that leads to erroneous program behavior. Many race conditions can be caused by data races, but this is not necessary.

Consider the following example:

```go
package main

type Account struct {
    Balance int
    Activity bool
}

func Transfer(from *Account, to *Account, amount int) bool {
    if from.Balance < amount {
        return false
    }
    from.Balance -= amount
    to.Balance += amount
    return true
}

func main() {
    account1 := &Account{500}
    account2 := &Account{500}

    Transfer(account1, account2, 300) // Transaction 1
    Transfer(account1, account2, 300) // Transaction 2
}
```

Our expectation is that, after two transactions finish, `account1` and `account2` has the balance of **200** and **800** respectively. Unfortunately, data race and race condition occurs and make things not work as expected.

When it comes to concurrency, that is when two transactions are executed concurrently, the snippet has data race because there are two goroutines access `account1` and `account2` that are shared memory and at least one of them has write operation without any synchronization.

The race condition is more subtle. Because we cant guarantee the order of instruction execution of two transaction, let's assume that two transaction are executed in the following order:

| Transaction 1 | Transaction 2 |
| ------------- | ------------- |
| if from.Balance < account <br/> (500 < 300)| |
| | if from.Balance < account <br/> (500 < 300) |
| from.Balance -= amount <br/> (account1 = 200)
| to.Balance += amount <br/> (account2 = 800)
| | from.Balance -= amount <br/> (account1 = -100)
| | to.Balance -= amount <br/> (account2 = 1100)
| | return true |
| return true | |

After transactions finish, `account1` has **-100** and `account2` has **1100** in their balance, which not matches our expectation. This situation is called race condition. One may try to fix the problem using `Mutex.Lock`:

```go
var mu sync.Mutex

func Transfer1(from *Account, to *Account, amount int) bool {
    mu.Lock()    
    balance := from.Balance
    mu.Unlock()

    if balance < amount {
        return false
    }

    mu.Lock()
    from.Balance -= amount
    mu.Unlock()

    mu.Lock()
    to.Balance += amount
    mu.Unlock()

    return true
}
```

Unfortunately, this approach only solves data race, because it excludes goroutines from accessing `from` and `to`. The execution order specified in the above table may still occur, which leads to race condition. The key invariant - convervation of money - is broken.

To preserve the invariant, we have to use a better locking strategy. As long as atomic’s semanatics are to end the atomic section on any exit from the block, the solution can be blunt:

```go
var mu sync.Mutex

func Transfer2(from *Account, to *Account, amount int) bool {
    mu.Lock()    
    defer mu.Unlock()

    if from.Balance < amount {
        return false
    }
    from.Balance -= amount
    to.Balance += amount
    return true
}
```

This function is free of data races and also of race conditions. Can we change it a bit to make an example that has data races but no race conditions? That is simple:

```go
var mu sync.Mutex

func Transfer3(from *Account, to *Account, amount int) bool {
    from.Activity = true
    to.Activity = true

    mu.Lock()    
    defer mu.Unlock()

    if from.Balance < amount {
        return false
    }
    from.Balance -= amount
    to.Balance += amount
    return true
}
```

Here we are setting flags indicating that some sort of activity occurred on the accounts. Are the data races on these flags harmful? Perhaps not. For example, in the evening we might shut down all transaction-processing threads and then select 10 random accounts that are flagged as having had activity for manual auditing. For this purpose, the data races are entirely harmless.

We've ended up covering all possibilities:

| | Data Race | No Data Race |
|-|-----------|----------------|
| Race Condition | Transfer | Transfer 1 |
| No Race Condition | Transfer 3| Transfer 2 |

## Reference

- [Race Condition vs. Data Race](https://blog.regehr.org/archives/490)
- [What is a Race Condition?](https://www.baeldung.com/cs/race-conditions)
- [Race Conditions versus Data Races](https://www.modernescpp.com/index.php/race-condition-versus-data-race)
- [Are “data races” and “race condition” the same thing](https://stackoverflow.com/a/18049303)