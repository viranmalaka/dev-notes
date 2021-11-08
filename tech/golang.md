# GoLang Tute
- `go build`: create an executeable file from the go code
- `go run`: build and immediately execute the go code

- package names
  - `main` creates an executable application
    - this file should have `func main()` inside it
  - anything else creates an reusable  package

- variable delcare
  - `var card string = "something"`
  - `card:= "something"`
- function
  - `func newCard() string { return "something" }`
- arrays
  - two type of arrays
    - array: fixed length
    - slice: can be add or delete elements
  - `card := []string{"card1", "card2"}`
  - append()
    - `card = append(card, "new card")`
    - append will create a new one instead of modifing the existing slice.
- for loops
```go
  for i, card := range cards {
    fmt.Println(i, card)
  }
```
- Receiver Functions
```go
 type deck []string // deck is extended version of string array

func (d deck) print () {    
  for i, card := range d {
    fmt.Println(i, card)
  }
}
card := []string{"card1", "card2"}
card.print();
```
  - Receiver functions are defined on top of any types
  - this is similar to javascript `this` keyward
  - usually we use single charactor convention for the type variable name. eg: `d`