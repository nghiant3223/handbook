Memory Layouts
==============

This articles will introduce type alignment and size guarantees in Go. It is essential to know the guarantees to estimate the sizes of struct types and properly use the 64-bit functions in `sync/atomic` standard package.

Go is a C family language, so many concepts talked in this article are shared with C language.

### Type Alignment Guarantees in Go

To fully utilize CPU directives and get the best performance, the (starting) addresses of the memory blocks allocated for values of a specified type must be aligned as multiples of an integer N, then N is called the value address alignment guarantee of the type, or simply the alignment guarantee of the type. We can also say the addresses of addressable values of the type are guaranteed to be N-byte aligned.

In fact, each type has two alignment guarantees, one is for when it is used as field types of other (struct) types, the other is for other cases (when it is used for a variable declaration, array element type, etc). We call the former one the field alignment guarantee of that type, and call the latter one the general alignment guarantee of that type.

For a type `T`, we can call `unsafe.Alignof(t)` to get its general alignment guarantee, where `t` is a non-field value of type `T`, and call `unsafe.Alignof(x.t)` to get its field alignment guarantee, where `x` is a struct value and `t` is a field value of type `T`.

Calls to the functions in the `unsafe` standard code packages are always evaluated at compile time.

At run time, for a value `t` of type `T`, we can call `reflect.TypeOf(t).Align()` to get the general alignment guarantee of type `T`, and call `reflect.TypeOf(t).FieldAlign()` to get the field alignment guarantee of type `T`.

For the current standard Go compiler (version 1.18), the field alignment guarantee and the general alignment guarantee of a type are always equal. For gccgo compiler, the statement is false.

Go specification only mentions [a little on type alignment guarantees](https://golang.org/ref/spec#Size_and_alignment_guarantees):

> The following minimal alignment properties are guaranteed:  
> 1\. For a variable `x` of any type: `unsafe.Alignof(x)` is at least `1`.  
> 2\. For a variable `x` of struct type: `unsafe.Alignof(x)` is the largest of all the values `unsafe.Alignof(x.f)` for each field `f` of `x`, but at least `1`.  
> 3\. For a variable `x` of array type: `unsafe.Alignof(x)` is the same as the alignment of a variable of the array's element type.

So Go specification doesn't specify the exact alignment guarantees for any types. It just specifies some minimal requirements.

For the same compiler, the exact type alignment guarantees may be different between different architectures and between different compiler versions. For the current version (1.18) of the standard Go compiler, the alignment guarantees are listed here.

    type                      alignment guarantee
    ------                    ------
    bool, uint8, int8         1
    uint16, int16             2
    uint32, int32             4
    float32, complex64        4
    arrays                    depend on element types
    structs                   depend on field types
    other types               size of a native word
    

Here, the size of a native word (or machine word) is 4-byte on 32-bit architectures and 8-byte on 64-bit architectures.

This means, for the current version of the standard Go compiler, the alignment guarantees of other types may be either `4` or `8`, depends on different build target architectures. This is also true for gccgo.

Generally, we don't need to care about the value address alignments in Go programming, except that we want to optimizing memory consumption, or write portable programs which using the 64-bit functions from `sync/atomic` package. Please read the following two sections for details.

### Type Sizes and Structure Padding

Go specification only makes following [type size guarantees](https://golang.org/ref/spec#Size_and_alignment_guarantees):

    type                    size in bytes
    ------                  ------
    uint8, int8             1
    uint16, int16           2
    uint32, int32, float32  4
    uint64, int64           8
    float64, complex64      8
    complex128              16
    uint, int               implementation-specific,
                            generally 4 on 32-bit
                            architectures, and 8 on
                            64-bit architectures.
    uintptr                 implementation-specific,
                            large enough to store
                            the uninterpreted bits
                            of a pointer value.
    

Go specification doesn't make value size guarantees for other kinds of types. The full list of sizes of different types settled by the standard Go compiler are listed in [value copy costs](value-copy-cost.html#value-sizes).

The standard Go compiler (and gccgo) will ensure the size of values of a type is a multiple of the alignment guarantee of the type.

To satisfy type alignment guarantee rules mentioned previously, Go compilers may pad some bytes between fields of struct values. This makes the value size of a struct type may be not a simple sum of the sizes of all fields of the type.

The following is an example showing how bytes are padded between struct fields. We have already learned that

*   the alignment guarantee and size of the built-in type `int8` are both one byte.
*   the alignment guarantee and size of the built-in type `int16` are both two bytes.
*   the size of the built-in type `int64` is 8 bytes, the alignment guarantee of type `int64` is 4 bytes on 32-bit architectures and 8 bytes on 64-bit architectures.
*   the alignment guarantees of the types `T1` and `T2` are their respective largest field alignment guarantees, a.k.a., the alignment guarantee of the `int64` field. So their alignment guarantees are both 4 bytes on 32-bit architectures and 8 bytes on 64-bit architectures.
*   the sizes of the types `T1` and `T2` must be multiples of their respective alignment guarantees.

```
    type T1 struct {
    	a int8
    
    	// On 64-bit architectures, to make field b
    	// 8-byte aligned, 7 bytes need to be padded
    	// here. On 32-bit architectures, to make
    	// field b 4-byte aligned, 3 bytes need to be
    	// padded here.
    
    	b int64
    	c int16
    
    	// To make the size of type T1 be a multiple
    	// of the alignment guarantee of T1, on 64-bit
    	// architectures, 6 bytes need to be padded
    	// here, and on 32-bit architectures, 2 bytes
    	// need to be padded here.
    }
    // The size of T1 is 24 (= 1 + 7 + 8 + 2 + 6)
    // bytes on 64-bit architectures and is 16
    // (= 1 + 3 + 8 + 2 + 2) on 32-bit architectures.
    
    type T2 struct {
    	a int8
    
    	// To make field c 2-byte aligned, one byte
    	// needs to be padded here on both 64-bit
    	// and 32-bit architectures.
    
    	c int16
    
    	// On 64-bit architectures, to make field b
    	// 8-byte aligned, 4 bytes need to be padded
    	// here. On 32-bit architectures, field b is
    	// already 4-byte aligned, so no bytes need
    	// to be padded here.
    
    	b int64
    }
    // The size of T2 is 16 (= 1 + 1 + 2 + 4 + 8)
    // bytes on 64-bit architectures, and is 12
    // (= 1 + 1 + 2 + 8) on 32-bit architectures.
```    

Although `T1` and `T2` have the same field set, their sizes are different.

One interesting fact for the standard Go compiler is that sometimes zero sized fields may affect structure padding. Please read [this question in the unofficial Go FAQ](unofficial-faq.html#final-zero-size-field) for details.

### The Alignment Requirement for 64-bit Word Atomic Operations

**_64-bit words_** mean values of types whose underlying types are `int64` or `uint64`.

The article [atomic operations](concurrent-atomic-operation.html) mentions a fact that 64-bit atomic operations on a 64-bit word require the address of the 64-bit word must be 8-byte aligned. This is not a problem for the current 64-bit architectures supported by the standard Go compiler, because 64-bit words on these 64-bit architectures are always 8-byte aligned.

However, on 32-bit architectures, the alignment guarantee made by the standard Go compiler for 64-bit words is only 4 bytes. 64-bit atomic operations on a 64-bit word which is not 8-byte aligned will panic at runtime. Worse, on very old CPU architectures, 64-bit atomic functions are not supported.

At the end of the [`sync/atomic` documentation](https://golang.org/pkg/sync/atomic/#pkg-note-BUG), it mentions:

> On x86-32, the 64-bit functions use instructions unavailable before the Pentium MMX.  
>   
> On non-Linux ARM, the 64-bit functions use instructions unavailable before the ARMv6k core.  
>   
> On both ARM and x86-32, it is the caller's responsibility to arrange for 64-bit alignment of 64-bit words accessed atomically. The first word in a variable or in an allocated struct, array, or slice can be relied upon to be 64-bit aligned.

So, things are not very bad for two reasons.

1.  The very old CPU architectures are not mainstream architectures nowadays. If a program needs to do synchronization for 64-bit words on these architectures, there are [other synchronization techniques](concurrent-synchronization-more.html) to rescue.
2.  On other not-very-old 32-bit architectures, there are some ways to ensure some 64-bit words are relied upon to be 64-bit aligned.

The ways are described as **the first (64-bit) word in a variable or in an allocated struct, array, or slice can be relied upon to be 64-bit aligned**. What does the word **_allocated_** mean? We can think an **_allocated value_** as a **_declared_** variable, a value returned by the built-in `make` function, or the value referenced by a value returned by the built-in `new` function. If a slice value derives from an allocated array and the first element of the slice is the first element of the array, then the slice value can also be viewed as an allocated value.

The description of which 64-bit words can be relied upon to be 64-bit aligned on 32-bit architectures is some conservative. There are more 64-bit words which can be relied upon to be 8-byte aligned. In fact, if the first element of an array or slice which element type is a 64-bit word type can be relied upon to be 64-bit aligned, then all elements in the array/slice can also be accessed atomically. It is just some subtle and verbose to make a simple clear description to include all the 64-bit words can be relied upon to be 64-bit aligned on 32-bit architectures, so the official documentation just makes a conservative description.

Here is an example which lists some 64-bit words which are safe or unsafe to be accessed atomically on both 64-bit and 32-bit architectures.

    type (
    	T1 struct {
    		v uint64
    	}
    
    	T2 struct {
    		_ int16
    		x T1
    		y *T1
    	}
    
    	T3 struct {
    		_ int16
    		x [6]int64
    		y *[6]int64
    	}
    )
    
    var a int64    // a is safe
    var b T1       // b.v is safe
    var c [6]int64 // c[0] is safe
    
    var d T2 // d.x.v is unsafe
    var e T3 // e.x[0] is unsafe
    
    func f() {
    	var f int64           // f is safe
    	var g = []int64{5: 0} // g[0] is safe
    
    	var h = e.x[:] // h[0] is unsafe
    
    	// Here, d.y.v and e.y[0] are both safe,
    	// for *d.y and *e.y are both allocated.
    	d.y = new(T1)
    	e.y = &[6]int64{}
    
    	_, _, _ = f, g, h
    }
    
    // In fact, all elements in c, g and e.y.v are
    // safe to be accessed atomically, though Go
    // official documentation never makes the guarantees.
    

If a 64-bit word field (generally the first one) of a struct type will be accessed atomically in code, we should always use allocated values of the struct type to guarantee the atomically accessed fields always can be relied upon to be 8-byte aligned on 32-bit architectures. When this struct type is used as the type of a field of another struct type, we should arrange the field as the first field of the other struct type, and always use allocated values of the other struct type.

Sometimes, if we can't make sure whether or not a 64-bit word can be accessed atomically, we can use a value of type `[15]byte` to determine the address for the 64-bit word at run time. For example,

    package mylib
    
    import (
    	"unsafe"
    	"sync/atomic"
    )
    
    type Counter struct {
    	x [15]byte // instead of "x uint64"
    }
    
    func (c *Counter) xAddr() *uint64 {
    	// The return must be 8-byte aligned.
    	return (*uint64)(unsafe.Pointer(
    		(uintptr(unsafe.Pointer(&c.x)) + 7)/8*8))
    }
    
    func (c *Counter) Add(delta uint64) {
    	p := c.xAddr()
    	atomic.AddUint64(p, delta)
    }
    
    func (c *Counter) Value() uint64 {
    	return atomic.LoadUint64(c.xAddr())
    }
    

By using this solution, the `Counter` type can be embedded in other user types freely and safely, even on 32-bit architectures. The drawback of this solution is there are seven bytes being wasted for every value of `Counter` type and it uses unsafe pointers.

Russ Cox [has proposed that the addresses of 64-bit words should always be 8-byte aligned](https://github.com/golang/go/issues/599), on either 64-bit or 32-bit architectures, to make Go programming simpler. Currently (Go 1.18), this proposal hasn't been adopted yet.

## More examples

Example 1.

```
type T struct {
	a int8
	b int16
	d int32
	c int16
}
```

In 64bit architecture. `a` has alignment gurantee of 1, it must be placed at the address that is multiple of 1. `b` and `c` have alighment gurantee of 2, they must be placed at the address that is multiple of 2. `d` has alignment gurantee of 4, it must be placed at the address that is multiple of 4. The memory layout may look as below:

```
| a | x | b | b | d | d | d | d |
| c | c | - | - |   |   |   |   |
```

Since T must be padded so that its size must be multiple of its largest field alignment gurantee (which is 4 for `d`), 2 bytes must be paded here which makes the size of T is 12.

In 32bit architecture. `a` has alignment gurantee of 1, it must be placed at the address that is multiple of 1. `b` and `c` have alighment gurantee of 2, they must be placed at the address that is multiple of 2. `d` has alignment gurantee of 4, it must be placed at the address that is multiple of 4. The memory layout may look as below:

```
| a | x | b | b | 
| d | d | d | d |
| c | c | - | - |
```

Since T must be padded so that its size must be multiple of its largest field alignment gurantee (which is 4 for `d`), 2 bytes must be paded here which makes the size of T is 12.

Example 2.

```
type T struct {
	a int8
	b int16
	c int16
	d int32
}
```

In 64bit architecture. `a` has alignment gurantee of 1, it must be placed at the address that is multiple of 1. `b` and `c` have alighment gurantee of 2, they must be placed at the address that is multiple of 2. `d` has alignment gurantee of 4, it must be placed at the address that is multiple of 4, which makes 2 bytes padded after `c`. The memory layout may look like below:

```
| a | x | b | b | c | c | - | - |
| d | d | d | d |   |   |   |   |
```

Since T must be padded so that its size must be multiple of its largest field alignment gurantee (which is 4 for `d`), the size of T is 12.

In 32bit architecture. `a` has alignment gurantee of 1, it must be placed at the address that is multiple of 1. `b` and `c` have alighment gurantee of 2, they must be placed at the address that is multiple of 2. `d` has alignment gurantee of 4, it must be placed at the address that is multiple of 4, which makes 2 bytes padded after `c`. The memory layout may look like below:

```
| a | x | b | b | 
| c | c | - | - |
| d | d | d | d |
```

Since T must be padded so that its size must be multiple of its largest field alignment gurantee (which is 4 for `d`), the size of T is 12.

Example 3.

```
type T struct {
    a float64
    b bool
}
```

In 64bit architecture. `a` has alighment gurantee of 8, it must be placed at the address that is multiple of 8. `b` has alighment of 1, it must be placed at the address that is multiple of 1. Since T must be padded so that its size must be multiple of its largest field alignment guratee (which is 8, a native word in 64bit architecture, for `a`), 7 bytes must be padded, which makes size of T is 16. The memory may look like below:

```
| a | a | a | a | a | a | a | a |
| b | x | x | x | x | x | x | x | 
```

In 32bit architecture. `a` has alighment gurantee of 4, it must be placed at the address that is multiple of 4. `b` has alighment of 1, it must be placed at the address that is multiple of 1. Since T must be padded so that its size must be multiple of its largest field alignment guratee (which is 4, a native word in 32bit architecture, for `a`), 3 bytes must be padded, which makes size of T is 12. The memory may look like below:

```
| a | a | a | a |
| a | a | a | a |
| b | x | x | x |
```

## References

- https://go101.org/article/memory-layout.html
- https://itnext.io/structure-size-optimization-in-golang-alignment-padding-more-effective-memory-layout-linters-fffdcba27c61