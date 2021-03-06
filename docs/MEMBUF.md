# MemBufs #
At its base, the `membuf` struct is a wrapper for a `void *, size_t` pair that designates a memory buffer.

On the surface it seems like there's no advantage of using a `membuf` over passing the actual pair to a method, but using a `membuf` allows for greater flexibility.

I do not expect anyone to use the `membuf` manually, but implicitly through one of the of the supplied constructors.

## Array conversion ##
`membuf` has the following constructor, which allows for implicit conversion from arrays:
```
template <typename T, size_t N>
membuf(T (&array)[N]);
```
Example:
```
void foo(membuf s);


uint8_t buffer[32];
foo(buffer);
```

## membuf_adapter ##
I am not blind for the need of higher level collections, and I am sure everyone either uses `std::array` or some similar implementation,
so there's a way to extend third_party and add an implicit cast to a membuf:
```
class MY_CLASS { ... };

template<>
struct membuf_adapter<MY_CLASS>
{
    static constexpr void *addr(MY_CLASS &o) { return &o; }
    static constexpr const void *addr(const MY_CLASS &o) { return &o; }
    static constexpr size_t size(const MY_CLASS &o) { return sizeof(MY_CLASS); }
}
```

I won't lie to you, this solution is ugly as fuck. 
Your best bet is probably to never ever use it, but I needed am easily compile-time-optimized solution. 