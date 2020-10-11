## PanicSheep's mill

### C++ generators
There are several ways to generate sequences in C++.
As an example some generators for the fibonacci sequence 0,1,1,2,3,5,8,13,21,34,.. are shown.

# Iterator-like
```c++
class Fibonacci
{
  class Iterator
  {
    int n{0};
    int lo{1}, hi{0};
  public:
    Iterator() noexcept = default;
    Iterator(int n) noexcept : n(n) {}
    
    [[nodiscard]] bool operator!=(const Iterator& o) const noexcept { return n != o.n; }
    
    [[nodiscard]] int operator*() const noexcept { return hi; }
    [[nodiscard]] Iterator& operator++() noexcept {
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
  static Iterator end() { return {}; }
};
```

# Generator-like
```c++
```

# Coroutine-based 
```c++
```
