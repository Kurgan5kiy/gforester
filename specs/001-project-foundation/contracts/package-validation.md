# Contract: Package Validation

## Build

1. Synchronize the locked development environment.
2. Run `uv build --no-sources`.
3. Require both:
   - a source distribution;
   - a `py3-none-any` wheel.
4. Treat warnings about missing license files or invalid metadata as failures.

## Clean Installation

1. Create a new Python 3.14 virtual environment outside the development
   environment.
2. Use that environment's interpreter to run
   `python -m pip install <exact-wheel-path>`.
3. Do not use an editable install.
4. Do not install the source tree directly.

## Smoke Verification

Using only the clean environment:

1. `python -c "import gforester"` exits successfully.
2. The installed `gforester --help` command starts and exits successfully.
3. Package metadata reports:
   - project name `gforester`;
   - the expected version;
   - Python requirement 3.14 or later;
   - GPL-3.0-only license metadata.

## Failure Conditions

- The build requires undeclared local sources.
- The wheel is missing or is not platform-independent.
- Installation requires the development environment.
- Import or command startup fails.
- Required license metadata or `LICENSE.md` is absent.
