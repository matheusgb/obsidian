Exemplo de uso:

```
type Animal interface {
	Onomatopeia() string
}

type Urso struct{}
func (u Urso) Onomatopeia() string {
	return "Roarrrrr"
}

type Tucano struct{}
func (t Tucano) Onomatopeia() string {
	return "Tri tri tri"
}

func Play(animal Animal) {
	fmt.Println(animal.Onomatopeia())
}

func main() {
	Play(Urso{})
	Play(Tucano{})
}
```

outro exemplo um pouco mais elaborado:
```
package main

import (
    "fmt"
    "math"
)

type geometry interface {
    area() float64
    perim() float64
}

type rect struct {
    width, height float64
}
type circle struct {
    radius float64
}

func (r rect) area() float64 {
    return r.width * r.height
}
func (r rect) perim() float64 {
    return 2*r.width + 2*r.height
}

func (c circle) area() float64 {
    return math.Pi * c.radius * c.radius
}
func (c circle) perim() float64 {
    return 2 * math.Pi * c.radius
}

func measure(g geometry) {
    fmt.Println(g)
    fmt.Println(g.area())
    fmt.Println(g.perim())
}

func main() {
    r := rect{width: 3, height: 4}
    c := circle{radius: 5}

    measure(r)
    measure(c)
}
```