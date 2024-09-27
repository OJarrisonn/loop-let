# `loop let`

This crate provides a macro for a new control flow mechanism in Rust proposal `loop let`. 
Which is a immutable loop pattern that is meant for tail call recursive algorithms earning
tail call optimization for free.

This crate provides the `loop_let!` macro that implements the `loop let` pattern. The syntax
is as follows:

```rs
loop_let!((pattern) = (initial); {
   // code ...
});

loop_let!(fails (pattern) = (initial); {
  // code ...
});
```

The `pattern` is the pattern that will be matched against the input. The `initial` is the
initial value of the input. The code block is the code that will be executed in every iteration
of loop. It's important to notice that the code block must return either a `Continue` or
a `Break`.

`Continue` is used to set the input value for the next iteration of the loop. `Break` is
used to break the loop and return the value.

The `fails` keyword is used before the pattern when using fallible patterns. This way, the loop
breaks once the pattern fails. By using the `fails` keyword, the `loop_let` macro will return
`()` (this means you can only use `Break(())`). So it can't be used as an expression.

# Why?

The main purpose of this crate is to encourage the utilization of tail call recursive algorithms
in Rust rather than the traditional recursion which relies on the compiler to optimize the code.

Some other earnings of using the `loop let` pattern in comparison to other tail call recursive
approaches are:

- Support for pattern matching (even fallible patterns)
- Loops as an expression
- Avoids the need for `mut`
- Avoids the need of tail call optimization at the compiler level
- Increased readability

This pattern is used in Clojure with the loop/recur pattern where a loop might return a value
or call itself with new arguments.

This is a proposal for a new control flow mechanism in Rust as a language construct `loop let`.

```rs no_run
// Proposed syntax (no need for `fails` keyword)
loop let (pattern) = (initial) {
    // ...
    continue (new_input); // New syntax for continue
    // ...
    break (result);
}
```

## Example
```rs
fn fibo() {
    // Calculates the 6th fibonacci number in a tail call recursive way
    let fib = loop_let!((n, curr, next) = (6, 0, 1); {
        if n == 0 {
            Break(curr)
        } else {
            Continue((n - 1, next, curr + next))
        }
    });

    assert_eq!(fib, 8);
}
```

## License

[MIT](./LICENSE) 

## Contributing

Any suggestions are welcome, just open a new issue describing your suggestion. After some discussion if you 
were encouraged to continue you might just fork this repository, create a new branch do your modifications
and then open a Pull Request