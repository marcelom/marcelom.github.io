Title: Go Slice Shuffle
Tags: golang python rand
Author: Marcelo Moreira
Summary: How to perform a shuffle on a slice in Go

I am porting some Python code to [Go](http://golang.org/), and I needed to perform a in-place slice (array) shuffle. I was surprised to discover that there is none !!!

Basically, I wanted something like this in Python:

    a = range(1,11)
    >>> a
    [1, 2, 3, 4, 5, 6, 7, 8, 9, 10]
    >>> random.shuffle(a)
    >>> a
    [8, 9, 2, 1, 4, 10, 7, 5, 3, 6]

I then came across this [post](http://stackoverflow.com/questions/12264789/shuffle-array-in-go).

Basically, I implemented it using [Sattolo's variant](http://en.wikipedia.org/wiki/Fisher%E2%80%93Yates_shuffle#Sattolo.27s_algorithm) of the Fisher–Yates shuffle algorythm.

The code is more or less like this:

    package main
    
    import (
        "fmt"
        "math/rand"
        "time"
    )
    
    func Shuffle(a []int) {
        for i := range a {
            j := rand.Intn(i + 1)
            a[i], a[j] = a[j], a[i]
        }
    }
    
    func main() {
        a := []int{1, 2, 3, 4, 5, 6, 7, 8, 9, 10}
        rand.Seed(time.Now().UnixNano())
        Shuffle(a)
        fmt.Println(a)
    }

Here is a sample output:

    $ go run myshuffle.go
    [7 10 8 2 5 1 4 6 9 3]

And here is the [Gist](https://gist.github.com/marcelom/5732441) and a [Golang Payground](http://play.golang.org/p/M2qPVZvehi).
