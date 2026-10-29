# reprotrace

> **A small command-line tool for recording command runs and comparing
> their results.**

**Team:** Samiksha Pawar (Roll No. 19) and Aditee Sable (Roll No. 28)\
**Repository:** https://github.com/Samiksha17Pawar/REPROTRACE-cmd\
**Project stage:** Early prototype

## 1. The idea in simple words

Imagine that a command works today but fails tomorrow. To understand
why, a developer may need to search terminal history, run the command
again, and compare output and error messages manually.

`reprotrace` is our proposed way to keep that information together. It
runs a command, records useful details about that execution, and saves
them so they can be inspected later. The planned next step is to compare
two saved runs and show what changed.

**In one sentence:** `reprotrace` aims to make command-run evidence
easier to save, find, and compare.

## 2. The problem we want to explore

A command execution can produce several kinds of evidence:

-   **Command:** what was run
-   **stdout:** normal output from the command
-   **stderr:** error or diagnostic output
-   **Exit code:** the status returned when the command finishes
-   **Duration:** how long the command took
-   **Working directory:** the directory in which it ran

When this information is not saved together, comparing separate runs may
take manual effort.

### Example situation

1.  A test command passes.
2.  Later, the command fails or prints different output.
3.  The developer wants to compare both executions to understand what
    changed.

`reprotrace` aims to save a record for each execution. It does **not**
automatically identify the root cause of every failure.

## 3. Evidence from our current prototype

We have tested a basic command run on our development Mac. The command
was:

``` bash
python3 -m reprotrace.cli run -- python3 -c "print('Hello from reprotrace!')"
```

The Terminal output observed during our test was:

``` text
Hello from reprotrace!

Run ID: da287ad9
Exit code: 0
Duration: 0.0736 seconds
Saved to: /Users/samiksha.pawar/.reprotrace/runs/da287ad9.json
```

The recorded duration and run ID are from that particular execution;
they will differ on future runs.

We also checked that the JSON file existed with:

``` bash
ls ~/.reprotrace/runs
```

Observed output:

``` text
da287ad9.json
```

This is evidence that the basic prototype can run a command, show its
output and exit code, measure its duration, and save a run record. It is
**not** evidence that all planned features are finished.

## 4. What we plan to build

  ----------------------------------------------------------------------------------------
  Command                                  Purpose                 Status
  ---------------------------------------- ----------------------- -----------------------
  `reprotrace run -- COMMAND [ARGS...]`    Execute a command and   Basic prototype
                                           save a record.          demonstrated

  `reprotrace list`                        List saved run records. Planned; not yet
                                                                   verified

  `reprotrace show RUN_ID`                 Display one saved       Planned; not yet
                                           record.                 verified

  `reprotrace compare RUN_ID_1 RUN_ID_2`   Compare two saved       Planned; not yet
                                           records.                verified
  ----------------------------------------------------------------------------------------

Example of the intended interface:

``` bash
# Record a command
reprotrace run -- python3 -c "print('hello')"

# List saved runs
reprotrace list

# Inspect one run
reprotrace show RUN_ID

# Compare two runs
reprotrace compare RUN_ID_1 RUN_ID_2
```

The last three examples describe the planned interface; they should not
be treated as working commands until implemented and tested.

## 5. How it is designed to work

``` mermaid
flowchart TD
    A[User enters a command] --> B[CLI parses the request]
    B --> C[Runner executes the command]
    C --> D[Capture output, errors, exit code and duration]
    D --> E[Prepare a run record]
    E --> F[Save the record as local JSON]
    F --> G[Later: list, inspect or compare saved runs]
    G --> H[Display results]
```

### Planned file structure

``` text
reprotrace/
├── reprotrace/
│   ├── __init__.py
│   ├── cli.py        # command-line interface
│   ├── runner.py     # runs commands and captures results
│   ├── storage.py    # saves and loads JSON records
│   └── compare.py    # compares saved records
├── tests/
│   └── test_reprotrace.py
├── README.md
├── pyproject.toml
└── .gitignore
```

### Why these technologies?

-   **Python:** readable and suitable for a small CLI prototype.
-   **`subprocess`:** runs child commands and captures their output and
    return code.
-   **`argparse`:** reads command-line arguments.
-   **JSON:** stores records in a human-readable format.
-   **`time.perf_counter()`:** measures elapsed time.
-   **Automated tests:** help check that successful runs, failures,
    storage, and comparisons behave as expected.

## 6. What is in scope?

### Planned for the prototype

-   Save command output and execution status.
-   Store records locally as JSON.
-   Retrieve saved records.
-   Compare two runs and report relevant differences.
-   Handle missing records and storage errors clearly.

### Not promised

-   Automatically fixing bugs.
-   Guaranteeing that an old command can be reproduced exactly.
-   Recreating an entire operating-system environment.
-   Finding the root cause of every difference.
-   Uploading run data to a remote service.

A difference between two runs is not automatically a bug. Output can
change because of timestamps, random values, dependencies, environment
settings, or external services.

## 7. Existing tools and research

We know that similar tools already exist, so we are not claiming that
the general idea is new.

-   **`diff`** compares text files. It can compare saved output files,
    but it does not itself create structured command-run records.
-   **`script`** records terminal sessions. Its exact capabilities
    depend on the operating system and version.
-   **`hyperfine`** measures command performance through benchmarking.
-   **RepoTrace on PyPI** describes a closely related workflow involving
    command capture, saved reports, replay, and comparison:
    https://pypi.org/project/repotrace/

**Research issue:** RepoTrace is a close overlap with our current
proposal. We need to investigate its actual features and limitations,
then discuss with our instructor whether we should narrow or change the
project scope. We should not claim a unique contribution until we have
evidence for one.

## 8. How we will test it

These are planned checks; results will be added after they are actually
run.

1.  A successful command records output and exit code `0`.
2.  A failing command records its error output and nonzero exit code.
3.  A saved JSON record can be loaded again.
4.  An unknown run ID produces a clear error.
5.  Comparing identical records reports no meaningful differences.
6.  Comparing records with known differences reports those differences.
7.  Secret-redaction tests check that selected test secrets are not
    exposed in protected fields.

We will only make claims about reliability or time saved if testing
provides evidence.

## 9. Current status and next steps

**Demonstrated:** basic command execution, output display, exit-code
capture, duration measurement, run ID generation, and JSON record
saving.

**Still to implement and test:** the complete `list`, `show`, and
`compare` commands; robust error handling; automated tests;
secret-redaction behaviour; and Linux installation/testing.

**Next steps:** 1. Keep the code separated into clear modules. 2.
Implement and test the remaining commands. 3. Investigate RepoTrace and
decide on a defensible project scope. 4. Test success and failure cases
and document real outputs. 5. Test installation on Linux.

## 10. References

-   Python `subprocess`:
    https://docs.python.org/3/library/subprocess.html
-   Python `argparse`: https://docs.python.org/3/library/argparse.html
-   GNU Diffutils manual: https://www.gnu.org/software/diffutils/manual/
-   `script` manual: https://man7.org/linux/man-pages/man1/script.1.html
-   Hyperfine: https://github.com/sharkdp/hyperfine
-   RepoTrace on PyPI: https://pypi.org/project/repotrace/

------------------------------------------------------------------------

*This README explains the project idea and records the current prototype
status. Update it as features are implemented and verified.*
