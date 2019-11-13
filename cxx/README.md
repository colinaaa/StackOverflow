## CXX problems

### `unique_ptr`

- [passing `unique_ptr` as argument](https://stackoverflow.com/questions/8114276/how-do-i-pass-a-unique-ptr-argument-to-a-constructor-or-a-function) 

```cpp
// declearation
Base(std::unique_ptr<Base> n): next(std::move(n)) {}

// usage
auto nextBase = std::make_unique<Base>();
Base newBase(std::move(nextBase));
Base fromTemp(std::unique_ptr<Base>(new Base(...));
```

> To take a unique pointer by value means that you are transferring ownership of the pointer to the function/object/etc in question. After `newBase` is constructed, `nextBase` is guaranteed to be empty. You don't *own* the object, and you don't even have a pointer to it anymore. It's **gone**.

> This is ensured because we take the parameter by value. `std::move` doesn't actually move anything; it's just a fancy cast. `std::move(nextBase)` returns a `Base&&` that is an r-value reference to `nextBase`. That's all it does.

> Because `Base::Base(std::unique_ptr<Base> n)` takes its argument by value rather than by r-value reference, C++ will automatically construct a temporary for us. It creates a `std::unique_ptr<Base>` from the `Base&&` that we gave the function via `std::move(nextBase)`. It is the construction of this temporary that actually moves the value from `nextBase` into the function argument n.

> *should* perfer passing **By Value** if you mean for a function to claim *ownership* of a `unique_ptr`
>
> should *avoid* passing **By rvalue reference**

related code:

- [DS lab](https://github.com/colinaaa/hello-ds/blob/fb9676b13926c324be59b3accf54c2e836e8e2fb/src/lab/3/Tree.cc#L104)

- [deleting the object `unique_ptr` managed](https://stackoverflow.com/questions/25609457/does-unique-ptrrelease-call-the-destructor/25609507)

> `release` is used to release ownership of the managed object without deleting it

```cpp
auto v = make_unique<int>(12);  // manages the object
int * raw = v.release();        // pointer to no-longer-managed object
delete raw;                     // needs manual deletion
```

> To delete the object, use `reset`.

```cpp
auto v = make_unique<int>(12);  // manages the object
v.reset();                      // delete the object, leaving v empty
```
