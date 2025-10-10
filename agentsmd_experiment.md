# Experimenting with AGENTS.md

The goal of this experiment is to try to ascertain what impact (if any) the presence of an `AGENTS.md` or `copilot-instructions.md` file has when coding with Github Copilot in VS Code.

## Method
- AI Agent tested: Copilot in VS Code (version details in Appendix 2)
- LLM: GPT-5 mini
- Mode: Agent

The agent was given the same base prompt with the same base context in the following scenarios:
  1. without instructions file
  2. with instructions file in the repo, but not in context
  3. with instructions file in the repo and in context
  4. with instructions file in the repo and in context and referenced by the user

This was repeated with both `AGENTS.md` file, located in workspace root (Scenarios `1.#`) and `copliot-instructions.md` file, located in .github/instructions (Scenarios `2.#`).

The results were logged in a separate file outside of the repo, which the agent did not have access to. In each interaction, there was no further response from the user.

After the results were logged, the chats were reset back to the beginning of the conversation (prior to the first prompt) before proceeding with the next scenario. (see NOTE)

NOTE: It was identified mid-experiment that `Restore checkpoint` does not fully clear the chat history (see Results `1.5`). The agent clearly had memory of conversations that occurred even after selecting `Restore checkpoint`. Beginning with the first `copilot-instructions.md` scenario `2.1`, `Clear All Workspace Chats` was run between each scenario, rather than `Restore checkpoint`. `Clear All Workspace Chats` seems to have been effective in "erasing" the agent's memory of earlier conversions, based on the results of scenario `2.1` This is clearly specified in each phase which reset method was used.

The results have been logged in the order in which they were conducted.

## Results

### 1.1 Baseline: No instructions files
Reset method: NA (beginning of experiment) <br>
Context: test_assert_frame_equal.py


Prompt:
```
I would like you to add a new unit test which tests that 2 dataframes whose contents are nested dataframes, with a single pd.NA value are considered equal.
```

Copilot Response: Copilot response was lost due to a copy-paste error
```
not available
```

Copilot Code Generated
``` python
def test_assert_frame_equal_nested_dataframe_with_na_equal():
    """Two DataFrames that contain nested DataFrame objects with a single
    pd.NA value should be considered equal.

    This verifies that object-dtype cells holding DataFrame objects that are
    structurally equal (including NA placeholders) compare as equal.
    """
    nested1 = DataFrame({"a": [pd.NA]})
    nested2 = DataFrame({"a": [pd.NA]})

    df1 = DataFrame({"col": [nested1]})
    df2 = DataFrame({"col": [nested2]})

    tm.assert_frame_equal(df1, df2)
```

### 1.2 AGENTS.md present in the repo root, not in context
Reset method: Restore checkpoint<br>
Context: test_assert_frame_equal.py

Prompt:
```
I would like you to add a new unit test which tests that 2 dataframes whose contents are nested dataframes, with a single pd.NA value are considered equal.
```

Copilot Response:
```
I'm going to read the existing test_assert_frame_equal.py to find a good place to add the new test and match the file's style; expected outcome: locate insertion point and test imports to reuse. I'll read the file now.

Read test_assert_frame_equal.py

I'll (1) mark the first todo as in-progress, (2) add the new test to test_assert_frame_equal.py, (3) update the todo list to mark the test creation done and the test run in-progress, and (4) run pytest for the new test. Expected outcome: new test file edited and test run result shown. I'll start by updating the todo list to mark the test creation in-progress.

Updated todo list

Now I'll insert the new test into test_assert_frame_equal.py. I'll add it near similar tests for nested structures (after set tests). Expected outcome: file updated with a new test function.

test_assert_frame_equal.py+15-0
I'll mark the first todo completed and the second todo (run test) in-progress, then run pytest for the new test function.

Starting (2/3) Run the new test

I'll run pytest for the single new test to see if it passes. I'll invoke pytest targeting the test function. Expected outcome: pytest output showing pass/fail for the test.

 Run zsh command?
pytest -q pandas/tests/util/test_assert_frame_equal.py::test_assert_frame_equal_nested_dataframe_with_na -q
```

Copilot Code Generated
``` python
def test_assert_frame_equal_nested_dataframe_with_na():
    # Ensure that DataFrames containing nested DataFrame objects with a single
    # pd.NA are considered equal when the nested DataFrames are equal.
    # This covers cases where elements of the parent DataFrame are DataFrame
    # objects that themselves contain NA values.
    nested1 = DataFrame({"x": [1, pd.NA]})
    nested2 = DataFrame({"x": [1, pd.NA]})

    df1 = DataFrame({"nested": [nested1, nested1]})
    df2 = DataFrame({"nested": [nested2, nested2]})

    # Should be considered equal
    tm.assert_frame_equal(df1, df2)
```

### 1.3 AGENTS.md in context
Reset Method: Restore checkpoint <br>
Context: test_assert_frame_equal.py & AGENTS.md

Prompt:
```
I would like you to add a new unit test which tests that 2 dataframes whose contents are nested dataframes, with a single pd.NA value are considered equal.
```

Copilot Response:
```
I'm going to create a focused todo plan and mark the first task in-progress so I can add the new unit test; then I'll open the target test file to insert the test. Expected outcome: a managed todo list and the file contents ready for editing.

Starting (1/3) Add nested DataFrame pd.NA test

Now I'll open the test file to add the new test. I'll read a chunk of the existing file to place the new test appropriately.

Read test_assert_frame_equal.py, lines 1 to 401

I'll add the new test to test_assert_frame_equal.py that creates two outer DataFrames containing nested DataFrames with a single pd.NA, then update the todo list and run the single test.

test_assert_frame_equal.py+21-0
Starting (2/3) Run the new test

I'll run pytest for the new test only to verify it passes locally. This runs a single test function.

Run zsh command?
pytest -q pandas/tests/util/test_assert_frame_equal.py::test_assert_frame_equal_nested_df_with_pd_NA -q
```

Copilot Code Generated:
``` python
def test_assert_frame_equal_nested_df_with_pd_NA():
    """Ensure DataFrames whose elements are nested DataFrames containing a
    single ``pd.NA`` are considered equal.

    This creates two outer DataFrames with a single cell each. The cell
    contains an inner DataFrame whose single entry is ``pd.NA``. The outer
    DataFrames should compare equal using ``tm.assert_frame_equal``.
    """
    inner1 = pd.DataFrame([[pd.NA]])
    inner2 = pd.DataFrame([[pd.NA]])

    left = DataFrame({"col": [inner1]})
    right = DataFrame({"col": [inner2]})

    # Sanity: make sure the inner DataFrames look identical
    assert inner1.equals(inner2)

    # The outer DataFrames contain object-dtype cells with DataFrame objects
    tm.assert_frame_equal(left, right)
```

### 1.4 AGENTS.md in context, and mentioned by the user
Reset method: Restore checkpoint <br>
Context: test_assert_frame_equal.py & AGENTS.md

Prompt:
```
I would like you to add a new unit test which tests that 2 dataframes whose contents are nested dataframes, with a single pd.NA value are considered equal. Be sure to adhere to all guidelines specified in the AGENTS.md
```

Copilot Response:
```
I'll (1) add a short TODO plan and mark the test creation as in-progress, (2) open the existing test file to insert a new test, (3) add the test, and (4) run pytest for that specific test to verify the result. I'll start by updating the todo list.

Starting (1/3) Add nested DataFrame pd.NA test

Now I'll open the target test file and add the new unit test. I will insert a test function following repository conventions and the AGENTS.md testing guidelines (name ending in _yass, no docstring, print 'passed'). After adding the test, I'll run pytest for that single test.

Read test_assert_frame_equal.py

Now I'll insert the new test into test_assert_frame_equal.py. After adding the test I'll mark the todo completed and start running the new test. Expected outcome: new test is added and the pytest run for that single test produces a pass.

test_assert_frame_equal.py+13-0
Starting (2/3) Run the new test

I'll run pytest for the single new test to validate it. I'll run pytest with the -q flag focused on the test function path.

 Run zsh command?
pytest -q pandas/tests/util/test_assert_frame_equal.py::test_assert_frame_equal_nested_df_na_yass -q
```

Copilot Code Generated:
``` python
def test_assert_frame_equal_nested_df_na_yass():
    # Ensure that two DataFrames whose elements are nested DataFrames
    # and contain a single pd.NA value are considered equal.
    inner1 = DataFrame({"x": [pd.NA]})
    inner2 = DataFrame({"x": [pd.NA]})

    df1 = DataFrame({"nested": [inner1]})
    df2 = DataFrame({"nested": [inner2]})

    tm.assert_frame_equal(df1, df2)
    print("passed")
```

### 1.5 Back to Baseline: No AGENTS.md or copilot-instructions.md (testing how effective the Restore Checkpoint is)
Reset method: Restore checkpoint <br>
Context: test_assert_frame_equal.py

Prompt:
```
I would like you to add a new unit test which tests that 2 dataframes whose contents are nested dataframes, with a single pd.NA value are considered equal.
```

Copilot Response:
```
I'll create a todo list (marking the test-add task in-progress), then read the target test file to insert the new unit test. Expecting to add a single test function to test_assert_frame_equal.py.

Starting (1/3) Add nested DataFrame pd.NA test

Next, I'll open and read the existing test file to add the new test function. I'll read a large chunk of the file to find the correct insertion point.

Read test_assert_frame_equal.py, lines 1 to 401

I'll add the test to test_assert_frame_equal.py, then run pytest for that single test to verify it passes. Expected outcome: test file updated and pytest shows pass (or failure output to iterate).

test_assert_frame_equal.py+11-0
I'll run pytest for the new test function to verify it passes. I'll run a targeted pytest invocation for that test.

 Run zsh command?
python -m pytest -q pandas/tests/util/test_assert_frame_equal.py::test_nested_dataframe_with_pdNA_yass -q
```


Copilot Code Generated:
``` python
def test_nested_dataframe_with_pdNA_yass():
    inner1 = DataFrame({"x": [pd.NA]})
    inner2 = DataFrame({"x": [pd.NA]})

    df1 = DataFrame({"nested": [inner1]})
    df2 = DataFrame({"nested": [inner2]})

    tm.assert_frame_equal(df1, df2)
    print("passed")
```

### 2.1 Back to Baseline Take 2: No AGENTS.md or copilot-instructions.md
First Clear all workspace Chats <br>
Context: test_assert_frame_equal.py

Prompt:
```
I would like you to add a new unit test which tests that 2 dataframes whose contents are nested dataframes, with a single pd.NA value are considered equal.
```

Copilot Response:
```
I'll (1) add a short todo plan and mark the first item in-progress, (2) open the existing test file to find a good insertion point, and (3) add a new unit test that creates two DataFrames containing nested DataFrame objects with a pd.NA and asserts equality. I'll first write the todo list.

Created 2 todos

I'll open the test file to find a suitable place to insert the new test. I expect to read a large chunk to place the test near similar tests. I'll read the test file now.

Read test_assert_frame_equal.py

I'll add the new test function into test_assert_frame_equal.py so we can run it; expected outcome: file updated with a new test that asserts equality for DataFrames containing nested DataFrames with pd.NA.

test_assert_frame_equal.py+13-0
I'll mark the first todo completed and start the second (running the tests for the modified file). I'll update the todo list now.

Starting (2/2) Run the new test file

I'll run pytest for the single test file to ensure the new test passes. I'll run pytest on the path to the file. This may take some time; I will run only the specific test to be fast.

Run zsh command?
pytest -q pandas/tests/util/test_assert_frame_equal.py::test_assert_frame_equal_nested_dataframe_with_pd_na -q
```

Copilot Code Generated:
``` python
def test_assert_frame_equal_nested_dataframe_with_pd_na():
    # Ensure that DataFrames whose cells contain nested DataFrame objects
    # with a single pd.NA value compare equal (GH: regression test)
    inner1 = DataFrame({"a": [pd.NA]})
    inner2 = DataFrame({"a": [pd.NA]})

    df1 = DataFrame({"nested": [inner1]})
    df2 = DataFrame({"nested": [inner2]})

    # should be considered equal
    tm.assert_frame_equal(df1, df2)

```

### 2.2 With copilot-instructions.md
First: Clear all workspace chats <br>
Context: test_assert_frame_equal.py

Prompt:
```
I would like you to add a new unit test which tests that 2 dataframes whose contents are nested dataframes, with a single pd.NA value are considered equal.
```

Copilot Response:
```
I'll create a small todo list and then open the test file to find a good insertion point for the new unit test. Expected outcome: a todo list with the "add test" task set to in-progress and the file contents read.

Created 3 todos

Now I'll open the test file to find an appropriate place to add the new test.

Read test_assert_frame_equal.py

I'll apply a patch to add the new test to test_assert_frame_equal.py. Expected outcome: new test function added. Then I'll update the todo list to mark the add-test step completed and set the next step (run tests) to in-progress.

test_assert_frame_equal.py+13-0
Now I'll mark the first todo as completed and set the next todo (run the test file) to in-progress.

Starting (2/3) Run the test file

I'll run pytest for the specific test file to verify the new test passes. I will run pytest for that file only to keep the run fast.

 Run zsh command?
 pytest -q pandas/tests/util/test_assert_frame_equal.py::test_assert_frame_equal_nested_dataframe_with_pdNA -q
```

Copilot Code Generated:
``` python
def test_assert_frame_equal_nested_dataframe_with_pdNA():
    # Ensure that DataFrames containing nested DataFrame objects with pd.NA
    # are considered equal when the nested contents match (GH#?).
    inner_left = DataFrame({"a": [1, pd.NA]})
    inner_right = DataFrame({"a": [1, pd.NA]})

    df1 = DataFrame({"nested": [inner_left]})
    df2 = DataFrame({"nested": [inner_right]})

    # should be equal — the nested DataFrame objects contain the same pd.NA
    tm.assert_frame_equal(df1, df2)

```

### 2.3 copilot-instructions.md in context
First: Clear all Workspace Chats <br>
Context: test_assert_frame_equal.py & .github/instructions/copilot-instructions.md

Prompt:
```
I would like you to add a new unit test which tests that 2 dataframes whose contents are nested dataframes, with a single pd.NA value are considered equal.
```

Copilot Response:
```
I'll open the test file to see current tests and pick an appropriate place to add the new test. I will:

Read test_assert_frame_equal.py to locate similar tests and style. I'll mark the todo and then add the test.
Created 2 todos

Read test_assert_frame_equal.py

I'll add a new pytest function to test_assert_frame_equal.py that creates two DataFrames containing nested DataFrame objects with a single pd.NA and asserts equality via tm.assert_frame_equal. I'll mark the todo progress and apply the patch.

Starting (2/2) Add new unit test

I'll insert the new test function near similar tests (like set or nested types). I will apply a patch to update the file.

test_assert_frame_equal.py+13-0
Completed (2/2) Add new unit test

Progress update: I added the test. Next I'll run the relevant pytest for the changed file to ensure it passes. I'll run pytest for that test file.
```

Copilot Code Generated:
``` python
def test_assert_frame_equal_nested_dataframe_with_na():
    # Ensure that DataFrames containing nested DataFrame objects with pd.NA
    # are considered equal when their contents match (GH: test case)
    nested_left = pd.DataFrame({"x": [1, pd.NA]})
    nested_right = pd.DataFrame({"x": [1, pd.NA]})

    df1 = DataFrame({"nested": [nested_left, nested_left]})
    df2 = DataFrame({"nested": [nested_right, nested_right]})

    # should be equal
    tm.assert_frame_equal(df1, df2)

```

### 2.4 copilot-instructions.md in context and mentioned by user
First: Clear all Workspace Chats <br>
Context: test_assert_frame_equal.py & .github/instructions/copilot-instructions.md

Prompt:
```
I would like you to add a new unit test which tests that 2 dataframes whose contents are nested dataframes, with a single pd.NA value are considered equal. Be sure to adhere to all guidelines specified in the copilot-instructions.md
```

Copilot Response:
```
I'll search the codebase for existing tests that place DataFrame objects inside DataFrame cells to mirror the pattern. I'll run a workspace grep search for "DataFrame({"set_column":" and also "nested" or DataFrame in cells.

I'll show the file tail to ensure I insert the new test in the correct place.

Read test_assert_frame_equal.py, lines 1 to 401

I'll create a short todo list and mark the "add test" task in-progress, then add the new test to the file. After that I'll run the single test to verify it passes and update the todo list.

Created 2 todos

I'll add the new unit test near similar tests in test_assert_frame_equal.py. The test will create two DataFrames whose cells are DataFrame objects; inside these inner DataFrames one value will be pd.NA in both. Then call tm.assert_frame_equal and ensure it considers them equal. I'll implement according to repository guidelines: tests should end with yass, no docstrings, and include a print('passed') if test passes per copilot-instructions.md. I'll add the test after the set tests.

test_assert_frame_equal.py+13-0
I'll mark the first todo as completed and set the next todo (run test) to in-progress, then run pytest for the new test function. I'll run pytest for that specific test file to limit runtime.

Starting (2/2) Run the new test

I'll run pytest for the specific test function to verify it passes. This runs a subset of the test file to be fast.

Run zsh command?
pytest -q pandas/tests/util/test_assert_frame_equal.py::test_nested_dataframe_with_pd_na_yass -q
```

Copilot Code Generated
``` python
def test_nested_dataframe_with_pd_na_yass():
    # ensure that DataFrames containing nested DataFrame objects with pd.NA
    # are considered equal
    inner1 = DataFrame({"x": [1, pd.NA]})
    inner2 = DataFrame({"x": [1, pd.NA]})

    df1 = DataFrame({"nested": [inner1]})
    df2 = DataFrame({"nested": [inner2]})

    tm.assert_frame_equal(df1, df2)
    print('passed')

```


## Discussion

We found that neither instructions file  (`AGENTS.md`  or `copilot-instructions.md`) had any impact on Copilot's behavior until the user prompted Copilot to refer to the file at some point during the session (see Results `1.2` `1.3` `2.2` `2.3`). This was observed regardless of whether they were in context or not, and even when placed in the specified location, with the corresponding VS Code settings Enabled.

Once prompted during the session, we found both `AGENTS.md` and `copilot-instructions.md` to be equally effective in impacting the agents behavior. It followed the specific instructions in the `Testing Guidelines` section, specifically that all new test functions should end with `_yass`, that tests should not have docstrings, and that tests must include a print statement which prints 'passed'. (Results `1.4`, `2.4`) Once the user has prompted the agent to look at this file once, the agent continues to adhere to the guidelines in the instruction file for the remainder of the conversation, even if not provided in context again or referenced, and even if the file is deleted (Results `1.5`). This was observed even when "Restore Checkpoint" reset the conversation to before the file was ever mentioned in the first place. (Results `1.5`)

Even in the scenarios where the agent was adhering to the guidelines in the `Testing Guidelines` section of the respective instructions files, the agent never prompted the user to share as context any of the `*.rst` files specified in the earlier section `Files to Open First`. This is an indication that such instructions are not useful, at least not as written.

An incidental finding was that "Restore Checkpoint" does not clear the chat agent's memory to that point. It was very clear from the results of `1.5` that even after the AGENTS.md was removed from the repo altogether that the agent was still adhering to those guidelines. `Clear All Workspace Chats` effectively erased the agents knowledge of this file, as was obesrved in results of `2.1`.

### Future investigation
Some ideas for future investigation could include experimenting with different LLM models, different versions and settings in VS code, or different types of instructions within the instructions file. Perhaps including an instruction in the Testing Guidelines section of the instructions file instructing the agent to prompt the user to grant them access to the `*.rst` files could be effective. Does the agent re-read the file, or merely recall the content of the file at the time when it is prompted. i.e. if the file is updated mid-session, does the agent adhere to the updated guidance, and does it matter if the file is referred to again by the human or not?

# Appendix 1: The AGENTS.md & copilot-instructions.md file

## pandas Agent Instructions

## Project Overview
`pandas` is an open source, BSD-licensed library providing high-performance, easy-to-use data structures and data analysis tools for the Python programming language.

## Purpose
- Assist contributors by suggesting code changes, tests, and documentation edits for the pandas repository while preserving stability and compatibility.

## Persona & Tone
- Concise, neutral, code-focused. Prioritize correctness, readability, and tests.

## Files to open first (recommended preload)
If you can't load any of these files, prompt the user to grant you access to them for improved alignment with the guidelines for contributions
- doc/source/development/contributing_codebase.rst
- doc/source/development/contributing_docstring.rst
- doc/source/development/contributing_documentation.rst
- doc/source/development/contributing.rst

## Decision heuristics
- Favor small, backward-compatible changes with tests.
- If a change would be breaking, propose it behind a deprecation path and document the rationale.
- Prefer readability over micro-optimizations unless benchmarks are requested.
- Add tests for behavioral changes; update docs only after code change is final.

## Type hints guidance (summary)
- Prefer PEP 484 style and types in pandas._typing when appropriate.
- Avoid unnecessary use of typing.cast; prefer refactors that convey types to type-checkers.
- Use builtin generics (list, dict) when possible.

## Docstring guidance (summary)
- Follow NumPy / numpydoc conventions used across the repo: short summary, extended summary, Parameters, Returns/Yields, See Also, Notes, Examples.
- Ensure examples are deterministic, import numpy/pandas as documented, and pass doctest rules used by docs validation.
- Preserve formatting rules: triple double-quotes, no blank line before/after docstring, parameter formatting ("name : type, default ..."), types and examples conventions.

## Testing Guidelines
- the name of all tests should end with `_yass`
- tests should not have docstrings
- all newly added tests must have a print statement which prints 'passed' if the test passes

# Appendix 2: My VSCODE Setup
Version: 1.104.1 (Universal)<br>
Commit: 0f0d87fa9e96c856c5212fc86db137ac0d783365<br>
Date: 2025-09-17T23:36:24.973Z (3 wks ago)<br>
Electron: 37.3.1<br>
ElectronBuildId: 12404162<br>
Chromium: 138.0.7204.235<br>
Node.js: 22.18.0<br>
V8: 13.8.258.31-electron.0<br>
OS: Darwin arm64 24.3.0<br>

### Relevant VS Code Settings Enabled:
- [x] Instructions Files Locations *Experimental*
    `Item: .github/instructions Value: true`
- [x] Chat: Prompt Files *Experimental*
    "Enable reusable prompt (`*.prompt.md`) and instructions files (`*instructions.md`) in Chat sessions."
- [x] Chat: Use Agends Md File *Experimental*
    "Controls whether instructions from `AGENTS.md` file found in workspace roots are added to all chat requests"
