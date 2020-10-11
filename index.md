## PanicSheep's mill

### C++ generators
There are several ways to generate sequences in C++.

As an example some generators for the fibonacci sequence 0,1,1,2,3,5,8,13,21,34,... are shown.

# Iterator-like
```c++
class Fibonacci
{
  class EndSentinel{};
  class Iterator
  {
    int n{0}, lo{1}, hi{0};
  public:
    Iterator() noexcept = default;
    Iterator(int n) noexcept : n(n) {}
    
    [[nodiscard]] bool operator!=(EndSentinel) const noexcept { return n != 0; }
    
    [[nodiscard]] int operator*() const noexcept { return hi; }
    Iterator& operator++() noexcept {
      lo += hi;
      std::swap(lo, hi);
      n--;
      return *this;
    }
		
  };
  int n;
public:
  Fibonacci(int n) noexcept : n(n) {}
  
  Iterator begin() const { return {n}; }
  static EndSentinel end() { return {}; }
};
```

# Generator-like Lambda-based infinite series
```c++
auto Fibonacci()
{
    return [lo=1, hi=0]() mutable {
    	lo += hi;
	std::swap(lo, hi);
	return lo;
    };
}
```

# Generator-like infinite series
```c++
class Fibonacci
{
	int lo{1}, hi{0};
public:
	Fibonacci() noexcept = default;
	[[nodiscard]] int operator()() noexcept {
		lo += hi;
		std::swap(lo, hi);
		return lo;
	}
};
```

# Generator-like finite series with optional value
```c++
class Fibonacci
{
	int n, lo{1}, hi{0};
public:
	Fibonacci(int n) noexcept : n(n) {}
	[[nodiscard]] std::optional<int> operator()() noexcept {
		lo += hi;
		std::swap(lo, hi);
		if (n-- > 0)
			return lo;
		return std::nullopt;
	}
};
```

# Generator-like finite series with exception
```c++
class Fibonacci
{
	int n, lo{1}, hi{0};
public:
	Fibonacci(int n) noexcept : n(n) {}
	[[nodiscard]] int operator()() {
		lo += hi;
		std::swap(lo, hi);
		if (n-- > 0)
			return lo;
		throw std::out_of_range("");
	}
};
```

# Coroutine-based finite series
```c++
std::experimental::generator<int> fibonacci(int n)
{
	int lo{1}, hi{0};
	while (n-- > 0)
	{
		lo += hi;
		std::swap(lo, hi);
		co_yield lo;
	}
};
```

# Range-based with lambda
```c++
auto fibonacci(int n)
{
	return ranges::views::generate(
		[lo=1, hi=0]() mutable {
        		lo += hi;
			std::swap(lo, hi);
			return lo;
        	}
	) | ranges::views::take(n);
}
```
