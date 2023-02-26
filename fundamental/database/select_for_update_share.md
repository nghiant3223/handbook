# SELECT ... FOR UPDATE/SHARE

## How do I lock on an InnoDB row that doesn't exist yet?

> While the answer above is true in that a SELECT ... FOR UPDATE will prevent concurrent sessions / transactions from inserting the same record, that is not the full truth. I am currently fighting with the same problem and have come to the conclusion that the SELECT ... FOR UPDATE is nearly useless in that situation for the following reason:  
> <br/>
> A concurrent transaction / session can also do a SELECT ... FOR UPDATE on the very same record / index value, and MySQL will happily accept that immediately (non-blocking) and without throwing errors. Of course, as soon as the other session has done that, your session as well can't insert the record any more. Nor your nor the other session / transaction get any information about the situation and think they can safely insert the record until they actually try to do so. Trying to insert then either leads to a deadlock or to a duplicate key error, depending on circumstances.  
> <br/>
In other words, SELECT ... FOR UPDATE prevents other sessions from inserting the respective record(s), BUT even if you do a SELECT ... FOR UPDATE and the respective record is not found, chances are that you can't actually insert that record. IMHO, that renders the "first query, then insert" method useless.  
> <br/>
> The cause of the problem is that MySQL does not offer any method to really lock non-existent records. Two concurrent sessions / transactions can lock non-existent records "FOR UPDATE" at the same time, a thing which really should not be possible and which makes development significantly more difficult.  
> <br/>
> The only way to work around this seems to be using semaphore tables or locking the whole table when inserting. Please refer to the MySQL documentation for further reference on locking whole tables or using semaphore tables.  

The following code demonstrates SELECT ... FOR UPDATE doesn't lock non-existed record. There are 20 logs `not found, inserting`. Worsely, deadlocks happen `Error 1213: Deadlock found when trying to get lock; try restarting transaction
`.

```go
package main

type User struct {
	ID       int64  `gorm:"primaryKey"`
	Username string `gorm:"uniqueIndex"`
	Age      int
}

func main() {
	dsn := "root:change_me@tcp(127.0.0.1:3306)/dbname?charset=utf8mb4&parseTime=True&loc=Local"
	db, err := gorm.Open(mysql.Open(dsn), &gorm.Config{})
	if err != nil {
		panic(err)
	}
	var wg sync.WaitGroup
	for i := 0; i < 20; i++ {
		ii := i
		wg.Add(1)
		go func() {
			defer wg.Done()
			insertUser(db, ii)
		}()
	}
	wg.Wait()
}

func insertUser(db *gorm.DB, traceID int) {
	username := "nghiant3223"
	txerr := db.Transaction(func(tx *gorm.DB) error {
		var user User
		err := tx.
			Clauses(clause.Locking{Strength: "UPDATE"}).
			Where("username = ?", username).
			First(&user).Error
		if errors.Is(err, gorm.ErrRecordNotFound) {
			log.Printf("not found, inserting, trace_id=%d\n", traceID)
			newUser := User{
				ID:       1,
				Username: username,
			}
			if err = tx.Create(newUser).Error; err != nil {
				return err
			}
			log.Printf("do something after creation, trace_id=%d\n", traceID)
		}
		if err != nil {
			log.Printf("failed to find user")
			return err
		}
		log.Printf("found, skip inserting, trace_id=%d\n", traceID)
		return nil
	})
	if txerr != nil {
		panic(txerr)
	}
}
```

> Another option, though not necessarily ideal in all circumstances is to skip the SELECT ... FOR UPDATE and just do an INSERT and then handle the resulting duplicate key error (which in my experience is much more consistent when the insert is the first operation performed). I'm sure there is a performance penalty, however, in a lot of cases it is negligible compared to the other operations performed and can save you the trouble of having to create a mutex table.

Instead, we keep on inserting record and handle for duplication as the following code.

```go

func insertUser(db *gorm.DB, traceID int) {
	username := "nghiant3223"
	newUser := User{
		ID:       1,
		Username: username,
	}
	err := db.WithContext(context.Background()).Create(newUser).Error
	if err != nil && strings.Contains(err.Error(), "Duplicate") {
		log.Printf("found, skip inserting, trace_id=%d\n", traceID)
		return
	}
	if err != nil {
		log.Printf("failed to insert db, err: %v\n", err)
		return
	}

	log.Printf("do something after creation, trace_id=%d\n", traceID)
}
```
