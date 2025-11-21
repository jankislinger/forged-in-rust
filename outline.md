# Transition from R (4 min)

I've switched from R to python in 2018.
There were three things that I missed after the transition.
Dplyr syntax for data transformations - chaining of operations.
Ggplot2, matplotlib was the standard at that time, plotly was fairly new and not feature-rich.
Rcpp just took C++ code and generated boilerplate code for linking and data type conversion.

# Rust features (6 min)

Give overview of main differences between Python and Rust.
Rust is strongly typed, though variable shadowing exists.
Rust is compiled - a binary is needed if we want to use in Python.
Errors are explicitly propagated instead of thrown while hoping somebody will catch them. 
There is clear ownership of the data via borrow checker.
Rich type system with enums, options, and results - correct control flow is enforced.
"Zero-cost abstraction" allows to write performant code that appears to be high-level.

# Tooling (3 min)

In Python, we have `pip`, `virtualenv`, `pipx`, `poetry`, `uv` for environment management.
There are `black`, `isort`, `flake8`, `ruff` for formatting and linting.
There are `mypy`, `ty` for static code analysis.
Other tools for documentation, unit tests, benchmarking.
Rust has cargo that does all that in a unified way.

# Python features (4 min)

Just not to say that Rust is better in every aspect.
Integers in Python are unbounded.
Strings have cached hashes which speeds up comparison and lookup in dictionaries.
Dictionaries are ordered - you have to keep that in mind when "translating" your code from Python to any other.
Interactivity, and fast-to-prototype are nice features as well.
Don't forget about notebooks.
A lot of libraries for machine learning, data transformations, visualization, etc.

# Example projects (3 min)

List successful projects that combine Rust and Python.
You may use them even if you decide not to do anything in Rust.
Tooling utilities `uv`, `ruff`, `ty` by Astral all written in Rust making live easier for Python devs.
`Polars` is a popular data frame library written in Rust.
Library `orjson` is faster alternative to built-in `json`.
There is (yet another) Python web framework written in Rust - `Robyn`.
The common feature of these projects is speed.
That doesn't mean Rust is in general faster than C, Fortran.

# Interoperability (7 min)

Tools `pyo3` and `maturing` make it easy to bind binaries and convert data types.
Demo - create project, write some Rust code, call from Python.
Talk about class grouping: python class, rust objects in python, pyo3 wrapper struct, rust struct.
Same grouping is for the libraries itself.
Write Python function and call it from Rust in the end.

# How (not) to call Python from Rust (3 min)

Calling Python from Rust slows down your pipeline.
There is a method `pl.Expr.map_elements()` which can call Python code from polars.
That is discouraged and users should use expression methods - very rich.
If you miss something, just write it in Rust and link using `pyo3-polars`.
Show example with `farmfingerprint` hashing.

# Rust pipeline in Rust (1 min)

Imagine a step in your Airflow DAG is just an ETL.
Write the Polars code in Rust, the docker image will be much smaller.
