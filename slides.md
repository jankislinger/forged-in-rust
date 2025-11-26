---
theme: seriph
background: https://cdn.jsdelivr.net/gh/slidevjs/slidev-covers@main/static/6terqWC_KCk.webp
title: Forged in Rust, Spoken in Python
class: text-center
drawings:
  persist: false
transition: slide-left
mdc: true
duration: 35min
---

# Forged in Rust,<br>Spoken in Python
## Jan Kislinger
### PyData Prague, November 2025


---
layout: image-right
image: https://cdn.jsdelivr.net/gh/slidevjs/slidev-covers@main/static/6terqWC_KCk.webp
class: text-3xl
---

# 2018: R ⇨ Python

- dplyr <span v-click>⇨ Polars</span>
- ggplot2 <span v-click>😔</span>
- Rcpp <span v-click>⇨ PyO3 + maturin</span>



---
class: text-3xl
---


# Why Rust

<v-clicks depth="1">

- Performance
- Most admired language
- Effortless integration

</v-clicks>


<img src="/assets/logos/ferris.png"
     draggable="true"
     style="
       position: absolute;
       left: 45%;
       top: 45%;
       width: 40%;
     "
/>

<!--
[click]
- Python: 55x slower than C
- Rust: 17% slower than C
- ( programming-language-benchmarks.vercel.app )
[click]
- Stack overflow survey
- Python: most desired (39.2%)
- Rust: most admired (72.4%)
[click]
- easy to use with Python
-->


---
layout: image-right
image: https://cdn.jsdelivr.net/gh/slidevjs/slidev-covers@main/static/4uH95YbrT0c.webp
class: text-3xl
---

# You'll Learn Today

- Basic Rust concepts
- Call Rust from Python
- Popular projects
- Write Polars extension
- (+1 bonus tip)

<!--
goals:
- it's simple
-->

---
layout: two-cols-header
class: text-xl
---

# Concepts of Rust

::left::

<v-clicks>

- Compiled
- Strongly typed
- Struct (no inheritance)
- Trait, Generics
- Enum
- Option, Result
- Error as value
- Borrow checker
- Macros

</v-clicks>

::right::

<div v-click="['1', '+1']" position="absolute">

```rust {1,11-14}
   Compiling guessing_game v0.1.0 (...)
warning: unused `Result` that must be used
  --> src/main.rs:10:5
   |
10 |     io::stdin().read_line(&mut guess);
   |     ^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
   |
   = note: this `Result` may be an `Err` variant,
        which should be handled
   = note: `#[warn(unused_must_use)]` on by default
help: use `let _ = ...` to ignore the resulting value
   |
10 |     let _ = io::stdin().read_line(&mut guess);
   |     +++++++
```
</div>

<div v-click="['2', '+1']" position="absolute">

```rust
fn add(a: i32, b: i32) -> i32 {
    a + b
}

fn main() {
    let x = 3;
    let s = add(x, 2);
}
```
</div>

<div v-click="['3', '+1']" position="absolute">

```rust
struct Rectangle {
    a: f32,
    b: f32,
}

impl Rectangle {
    fn new(a: f32, b: f32) -> Self {
        Self { a, b }
    }
    
    fn area(&self) -> f32 {
        self.a * self.b
    }
}
```
</div>

<div v-click="['4', '+1']" position="absolute">

```rust
trait Shape {
    fn area(&self) -> f32;
}

struct Square { a: f32 };
impl Shape for Square {
    fn area(&self) -> f32 { self.a * self.a }
}

struct Circle { r: f32 };
impl Shape for Circle {
    fn area(&self) -> f32 { PI * self.r * self.r }
}

fn volume<T: Shape>(base: &T, height: f32) -> f32 {
    base.area() * height
}
```
</div>

<div v-click="['5', '+1']" position="absolute">

```rust
enum Shape {
    Square(f32),
    Rectangle(f32, f32),
    Circle(f32),
}

fn area(x: &Shape) -> f32 {
    match x {
        Shape::Square(a) => a * a,
        Shape::Rectangle(a, b) => a * b,
        Shape::Circle(r) => PI * r * r,
    }
}
```
</div>

<div v-click="['6', '+1']" position="absolute">

```rust
enum Option<T> {
    None,
    Some(T),
}

enum Result<T, E> {
    Ok(T),
    Err(E),
}
```
</div>

<div v-click="['7', '+1']" position="absolute">

```rust
fn parse_int_fallback(s: &str) -> i32 {
    match s.parse() {
        Ok(n) => n,
        Err(_) => i32::MIN,
    }
}

fn parse_int_option(s: &str) -> Option<i32> {
    s.parse().ok()
}

fn parse_int_panic(s: &str) -> i32 {
    s.parse().expect("Failed to parse")
}
```
</div>

<div v-click="['8', '+1']" position="absolute">

```rust
fn print_first(vec: Vec<u32>) {
    println!("First element: {}", vec[0]);
}

fn main() {
    vec = vec![1, 2, 3];
    print_first(vec);
    print_first(vec);  // Error: Value used after being moved
}
```
</div>

<div v-click="9" position="absolute">

```rust
#[derive(Debug)]
struct Point {
    x: i32,
    y: i32,
}

fn main() {
    let point = Point { x: 1, y: 2 };
    println!("{:?}", point);
    // Point { x: 1, y: 2 }
}
```
</div>


<!--
[click]
- compiler is your friend
- error -> suggestion for a fix
[click]
- not pedantic
- some types are inferred
[click]
- encapsulates data and functions
- similar to dataclasses, pydantic
- not OO lang
- no inheritance
- there is polymorphism, though
[click]
- makes polymorphism possible
- similar to protocol class
- interface in go, java
- generic - accept any struct that implements
- compiler creates version per struct
[click]
- another way to handle polymorphism
- py: just categories (int, str)
- rust: can hold data
- all types are known
- nobody cam implement triangle
[click]
- special type of enum
- implemented in standard lib
- option - either nothing or something
- result - ok with data or an error
- used instead of exceptions
[click]
- if function can fail, returns result
- each result has to be handled
- depends on application
- default value, propagate, halt = panic
[click]
- most complicated concept
- exactly one owner of each object
- pass by value = give ownership
- pass reference = borrow
[click]
- like decorator
- generates boilerplate code
- compile-time
-->



---

# Development Tools

### Python

<v-clicks>

- Environment: `pip`, `virtualenv`, `pipx`, `poetry`, <span v-mark.orange.circle="{ at: 7 }">`uv`</span>
- Formatting: `black`, `isort`, `flake8`, <span v-mark.orange.circle="{ at: 7, delay: 250 }">`ruff`</span>
- Static Analysis: `mypy`, <span v-mark.orange.circle="{ at: 7, delay: 600 }">`ty`</span>
- Documentation: `pydoc`, `sphinx`

</v-clicks>

<br>

<div v-click>

### Rust
</div>

<div v-click>

- `cargo`
</div>

---
layout: image-left
image: https://cdn.jsdelivr.net/gh/slidevjs/slidev-covers@main/static/OWXQvvovpeE.webp
class: text-3xl
---

# Just in Python

### Hidden features

<br>

<v-clicks>

- `int` (unbounded)
- `str` (cached)
- `dict` (sorted)

</v-clicks>

<!--
- not to say Rust is better in any way
- lots of areas python still wins
- interactivity
- rapid prototyping
- lots of libraries (ML, visualization, data proc)
- notebooks
- 3 hidden features - miss when switch lang
[click]
- 32, 64
- signed, unsigned
[click]
- faster comparison, dict lookup
- hash of string cached
- calculated only once
- I've written algo based on string lookup...
[click]
- same order as created
- don't realize we rely on
- header - previous talk
-->

---
layout: section
---

# Popular Projects

Written in Rust; Used by Python developers


---
layout: two-cols-header
---

# Python Tooling

[astral.sh](https://astral.sh/)

::left::

![img.png](/assets/screenshot_uv.png)

::right::

![img.png](/assets/screenshot_ruff.png)

<style>
.two-cols-header {
  column-gap: 20px;
}
</style>

<!--
- CLI tools
- common denominator - speed
- branch pruning -> feasible versions
- install = symlink to cache
- not just speed
- uv - makes development in monorepo easier
- ruff - single installations, all rules, no plugins
- unsafe fixes ... brave enough
-->

---
layout: two-cols-header
class: text-2xl
---

::left::

<v-clicks start="0">

- Data frame library
- Backend written in Rust
- Multithreaded by default
- 10x - 30x faster than Pandas

</v-clicks>



::right::

<v-clicks start="+1" depth="2">

- Lazy expressions 🤗
- Consistent syntax
- No (multi-) indices

</v-clicks>

::bottom::

<img src="/assets/logos/polars.png"
     draggable="true"
     style="
       position: absolute;
       left: 45%;
       top: 60%;
       width: 40%;
     "
/>

<!--
[click]
[click]
[click]
[click]
- 3 years ago
- Showmax
- 12x - 15x
- blog post
- Leading companies using Polars
-->

---
layout: two-cols-header
---

<br>

```python {all|1,4-5|1,6-7|1,3,8|all}
# Polars
result = (
    pl.scan_csv("data.csv")
    .filter(pl.col("value") > 10)
    .with_columns(double=pl.col("value") * 2)
    .group_by("category")
    .agg(double_mean=pl.col("double").mean())
    .collect()
)
```

<br>

::left::

```python {all|1,4-5|1,6-8|1,3|all}{at:'0'}
# Pandas
result = (
    pd.read_csv("data.csv")
    .iloc[lambda d: d["value"] > 10]
    .assign(double=lambda d: d["value"] * 2)
    .groupby("category")
    .agg(double_mean=("double", "mean"))
    .reset_index()
)
```


::right::


```r {all|1,3-4|1,5-6|1,2,7|all}{at:'0'}
# R::dplyr
result <- read_csv("data.csv") |>
  filter(value > 10) |>
  mutate(double = value * 2) |>
  group_by(category) |>
  summarise(double_mean = mean(double, na.rm = TRUE))
```

<style>
.two-cols-header {
  column-gap: 4%;
}
.col-header .slidev-code-wrapper {
    width: 74%;
    padding-left: 26%;
}
</style>

<!--
- the same transformation in all
- read csv, filter, create a new column, group, and aggregate
[click]
- R: feels like you’re talking about the column, not variable
- Polars: pl.col plays the same role
[click]
- full expressions, any transformations
- pandas - constrained
[click]
- creates a plan, no read
- optimizes pipeline
- push filter down
- skip unused columns
- process larger files
[click]
- subjective
- moves syntax closer to R
-->


---

# Other Examples

## orjson
Fast, correct JSON library for Python.

<v-clicks start="-1" every="2">

## Pydantic
Data validation using Python type hints.

## Robyn
High-Performance \[...\] Web Framework with a Rust runtime.

</v-clicks>

<!--
[click]
[click]
- impit - previous talk
- seems like all new libraries in Rust
- why?
- because of ...
-->

---
layout: section
---

# PyO3 + maturin


---
layout: two-cols-header
class: text-xl
---

# Call Rust from Python

::left::

- Create new project

<v-clicks>

- Write Rust code
<v-click-gap size="0" />
- Decorate using <code>pyo3</code> macros
<v-click-gap size="0" />
- Export as Python module
<v-click-gap size="0" />
- Compile &amp; install
<v-click-gap size="0" />
- Run Python

</v-clicks>

::right::

````md magic-move {lines: true, at:0}
```shell {1,6-9}{at:0}
> maturin init my-lib
✔ 🤷 Which kind of bindings to use? · pyo3
  ✨ Done! Initialized project my-lib
> tree my-lib
my-lib
├── Cargo.toml
├── pyproject.toml
└── src
    └── lib.rs

1 directory, 3 files
```

```rust {all}{at:0} twoslash
// src/lib.rs
fn fibo(n: u32) -> u32 {
    match n {
        ..2 => 1,  // range(2)
        _ => fibo(n-1) + fibo(n-2)
    }
}
```

```rust {2-10}{at:2} twoslash
// src/lib.rs
use pyo3::prelude::*;

#[pyfunction]
fn fibo(n: u32) -> u32 {
    match n {
        ..2 => 1,
        _ => fibo(n-1) + fibo(n-2)
    }
}
```

```rust {2,12-16}{at:2} twoslash
// src/lib.rs
use pyo3::prelude::*;

#[pyfunction]
fn fibo(n: u32) -> u32 {
    match n {
        ..2 => 1,
        _ => fibo(n-1) + fibo(n-2)
    }
}

#[pymodule]
mod my_lib {
    #[pymodule_export]
    use super::fibo;
}
```

```bash {1}
> maturin develop
🍹 Building a mixed python/rust project
🔗 Found pyo3 bindings
🐍 Found CPython 3.13 at .venv/bin/python
📦 Built wheel for CPython 3.13 to <...>.whl
✏️ Setting installed package as editable
🛠 Installed my-lib-0.1.0
```

```python {all}
# main.py
from my_lib import fibo

print(fibo(12))
```
````

---
layout: two-cols-header
---

# Binding Structs to Classes

::left::

````md magic-move {lines: true, at:0}
```rust {1-15}
#[pyclass]
struct Accumulator {
    value: i64,
}

#[pymethods]
impl Accumulator {
    fn add(&mut self, x: i64) {
        self.value += x;
    }

    fn get(&self) -> i64 {
        self.value
    }
}

#[pymodule]
mod my_lib {
    #[pymodule_export]
    use super::Accumulator;
}
```

```rust {8-11|0|0}
#[pyclass]
struct Accumulator {
    value: i64,
}

#[pymethods]
impl Accumulator {
    #[new]
    fn new() -> Self {
        Self { value: 0 }
    }
    
    fn add(&mut self, x: i64) {
        self.value += x;
    }

    fn get(&self) -> i64 {
        self.value
    }
}
```

```rust {2,17-19|0}
#[pyclass]
#[derive(Debug)]
struct Accumulator {
    value: i64,
}

#[pymethods]
impl Accumulator {
    #[new]
    fn new() -> Self {
        Self { value: 0 }
    }
    
    fn add(&mut self, x: i64) { ... }
    fn get(&self) -> i64 { ... }
    
    fn __str__(&self) -> String {
        format!("{:?}", &self)
    }
}
```

````


::right::


````md magic-move {lines: true}
```python {0|0|1-8|10-11|0}{at:2} twoslash
from my_lib import Accumulator

x = Accumulator()
x.add(1)
x.add(2)

print(x.get())
# 3

print(x)
# <builtins.Accumulator object at 0x7dfc89505c50>

```

```python {10-11}{at:2} twoslash
from my_lib import Accumulator

x = Accumulator()
x.add(1)
x.add(2)

print(x.get())
# 3

print(x)
# Accumulator { value: 3 }
```

````

<style>
.two-cols-header {
  column-gap: 2%;
}
</style>

---
layout: two-cols-header
---

# Python Exceptions

::left::

````md magic-move {lines: true, at:0}
```rust {3-12}
use pyo3::prelude::*;

#[pyfunction]
fn parse_int(text: String) -> PyResult<i64> {
    match text.trim().parse::<i64>() {
        Ok(v) => Ok(v),
        Err(e) => {
            let msg = format!("cannot parse integer: {e}");
            Err(PyValueError::new_err(msg))
        }
    }
}

#[pymodule]
mod my_lib {
    #[pymodule_export]
    use super::parse_int;
}
```

```rust {3-7}
use pyo3::prelude::*;

#[pyfunction]
fn parse_int(text: String) -> PyResult<i64> {
    text.trim().parse()
        .map_err(PyValueError::new_err)
}

#[pymodule]
mod my_lib {
    #[pymodule_export]
    use super::parse_int;
}
```
````


::right::


````md magic-move {lines: true}
```python {5-9|5-9} twoslash
from my_lib import parse_int

assert parse_int("  123 ") == 123

try:
    parse_int("foo")
except ValueError as e:
    print(e)
    # invalid digit found in string
```
````

<style>
.two-cols-header {
  column-gap: 2%;
}
</style>


---
layout: two-cols
class: text-xl
---

# Good Case for Rust

<v-clicks>

- Performance-critical code
- Tight loops over large arrays
- Parsing / validation
- Specific algorithm
- Wrapping an existing Rust crate

</v-clicks>

::right::

# Stay in Python

<v-clicks>

- I/O-bound tasks
- Leverage existing library
- Rapid prototyping
- Interactive data exploration

</v-clicks>


---

# (Not) Calling Python from Rust

```python {1-6|5,10-13}{lines: true, at:2} twoslash
import polars as pl

df = (
    pl.DataFrame({"x": [1, 2, 3]})
    .with_columns(y=pl.col("x").map_elements(lambda x: x+1))
)

# Expr.map_elements is significantly slower than the native expressions API.
# Only use if you absolutely CANNOT implement your logic otherwise.
# Replace this expression...
#   - pl.col("x").map_elements(lambda x: ...)
# with this one instead:
#   + pl.col("x") + 1
# 
#   .with_columns(y=pl.col("x").map_elements(lambda x: x+1))
```




---
layout: two-cols-header
---

# Polars Extension

::left::

````md magic-move {lines: true, at:0}
```python {all|2,7-9|0|0|0|0|0}{lines: true} twoslash
import polars as pl
from farmhash import hash64

(
    pl.DataFrame({"text": ["hello", "world"]})
    .with_columns(
        hash=pl.col("text").map_elements(
            hash64, return_dtype=pl.UInt64
        )
    )
)

# ┌───────┬──────────────────────┐
# │ text  ┆ hash                 │
# │ ---   ┆ ---                  │
# │ str   ┆ u64                  │
# ╞═══════╪══════════════════════╡
# │ hello ┆ 13009744463427800296 │
# │ world ┆ 16436542438370751598 │
# └───────┴──────────────────────┘
```

```python {4|6-12|14-17}{lines: true} twoslash
import polars as pl
from polars.plugins import register_plugin_function

LIB = Path(__file__).parent / "my_lib.abi3.so"

def farm64(col_name: str) -> pl.Expr:
    return register_plugin_function(
        plugin_path=LIB,
        args=[pl.col(col_name)],
        function_name="farm64",
        is_elementwise=True,
    )

(
    pl.DataFrame({"text": ["hello", "world"]})
    .with_columns(hash=farm64("text"))
)
```

````

::right::

```rust {0|all|6|7-9|10|13-14|13-14|0}{lines: true, at:2} twoslash
use polars::prelude::*;
use pyo3_polars::derive::polars_expr;

#[polars_expr(output_type=UInt64)]
fn farm64(inputs: &[Series]) -> PolarsResult<Series> {
    let strings: &StringChunked = inputs[0].str()?;
    let hashes: UInt64Chunked = strings.iter()
        .map(|x| x.map(farm::fingerprint64))
        .collect();
    Ok(hashes.into_series())
}

#[pymodule]
mod my_lib {}
```

<style>
.two-cols-header {
  column-gap: 2%;
}
</style>

---
layout: two-cols-header
---

# Docker Image Optimization

::left::

````md magic-move {lines: true, at:0}
```python
# pipeline.py
(
    pl.scan_csv("data.csv")
    .filter(pl.col("value") > 10)
    .with_columns(double=pl.col("value") * 2)
    .group_by("category")
    .agg(double_mean=pl.col("double").mean())
    .sink_csv("result.csv")
)
```

```python
# pipeline.py
(
    pl.scan_csv("data.csv")
    .filter(pl.col("value").gt(10))
    .with_columns(
        pl.col("value").mul(2).alias("double"),
    )
    .group_by(pl.col("category"))
    .agg(
        pl.col("double").mean().alias("double_mean"),
    )
    .sink_csv("result.csv")
)
```

```rust{*|0}
// pipeline.rs
let lf = LazyCsvReader::new("data.csv")
    .has_header(true)
    .finish()?
    .filter(col("value").gt(lit(10)))
    .with_columns([
        col("value").mul(lit(2)).alias("double"),
    ])
    .groupby([col("category")])
    .agg([
        col("double").mean().alias("double_mean"),
    ]);

lf.sink(
    SinkDestination::File("result.csv".into()),
    FileType::Csv.into(),
    Default::default(),
)?;
```
````


::right::

````md magic-move {lines: true, at:0}
```dockerfile{*|0|0}
FROM python

COPY . /app
RUN --mount=uv uv sync
```

```dockerfile
FROM rust as builder

COPY . /build
RUN cargo install

FROM debian
COPY --from builder cargo/bin/myapp /bin/myapp
```
````

<style>
.two-cols-header {
  column-gap: 2%;
}
</style>

<!--
- typical ETL pipeline step
- read, transform, save
- build docker image
- base 1.2 GB
- very simplified production env
[click]
- methods instead of operators
- alias instead of kwargs
[click]
- consistent syntax across languages
- read / write complicated
- processing part is the same
- transform -> function -> export
[click]
- two-stage build

-->

---
class: text-2xl
---

# Conclusion

<v-clicks>

- Rust performance with the interactivity of Python
- The ecosystem is ready: PyO3, maturin
- Several projects have already shown success
- Start tiny: one function, one Polars plugin

</v-clicks>

<br>

<div v-click class="opacity-60 text-7xl mt-8 mx-8">
  Q & A
</div>

<!--
[click]
- Key message: this is not "abandon Python"
- Keep Python for interactivity, notebooks, ecosystem
- Use Rust where performance matters

[click]
- Tooling is boring in a good way now
- PyO3 + maturin for bindings
- Combine with cargo / uv

[click]
- Polars is becoming mainstream
- Rust-based tools like uv / ruff already in your daily workflow
- Speed is their common trait

[click]
- You don't need to rewrite everything
- Wrap a single hot loop, or one hashing / parsing function

[click]
- Invite questions
- Encourage them to actually try maturin or a tiny Rust experiment
-->



---
layout: two-cols-header
class: text-2xl
---

# Jan Kislinger

<br>
<br>

::left::

- ML Engineer
- Personalization

<br>
<br>

<div style="height:120px; display:flex; align-items:flex-start;">
  <img src="/assets/logos/sky.png" alt="Sky"
       style="height:100%; object-fit:contain;" />
</div>

::right::



- PhD student
- Full-page recommendations

<br>
<br>

<div style="height:120px; display:flex; align-items:flex-start;">
  <img src="/assets/logos/ctu.jpg" alt="CTU"
       style="height:100%; object-fit:contain;" />
</div>
