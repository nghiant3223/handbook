# Accept interfaces, return structs
### Preemptive abstractions make systems complex
- Always [abstract] things when you actually need them, never when you just foresee that you need them.
### Need is in the eye of the beholder
- You’re the one writing the function so you know exactly when you need to abstract the return value.
- This imbalance between being able to precisely control the output, but be unable to anticipate the user’s input, creates a stronger bias for abstraction on the input than it does on the output.
### Remove dead code details
- Another aspect of simplification is removing unnecessary details.
- Similar, functions shouldn’t list inputs they don’t need.
- Interfaces allow us to create the function that only depends upon what we need.
```
type DatabaseWriter interface {
  AddUser(string)
}
func NewUser(d DatabaseWriter, firstName string, lastName string) {
  d.AddUser(firstName + lastName)
}
```
### Summarize the reasons and examine exceptions
- Remove unneeded abstractions
- Ambiguity of a user’s need on function input
- Simplify function inputs
### Some exceptions
- If your function actually can return multiple types then obviously return an interface.
- If the function is private then there is no ambiguity on function input since you control that, so bias towards not preemptive abstraction.
- Go has no way to abstract out struct members values. So, if your function needs to access struct members (and not just functions on the struct), then you’re forced to accept the struct directly.
### Ref
- [What “accept interfaces, return structs” means in Go](https://medium.com/@cep21/what-accept-interfaces-return-structs-means-in-go-2fe879e25ee8)