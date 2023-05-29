# Fast Exam

Create randomized exams based on a data base of exercises.
The program supports closed-answer quiz, open questions and random exercises.
It creates and compiles a Latex document using the `exam` class to produce the exam text and solution PDF.

Try the following command and observe the created documents:
```
./fast-exam --randomorder --quizbegin --quizrandom --solutionsummary
```

## Dependencies

You need Python3 and [Latex](https://www.tug.org/texlive/) installed, on a Linux or MacOS machine. Probably the software runs in Windows as well.
You need the `pyyaml jinja2` packages, that you can install with `pip`.

## Database

The program runs on a database of quiz, open questions and exercises.
The database is contained in a folder and consists of one or more `.yml` files.

Each file contains one or more questions, and all questions in a file build a **group** of questions and are identified by the name of the file. For example, the `sample-db` folder contains a file called `bash.yml` that defines the `bash` group of questions consisting of all entries in that file.

Fast Exam selects a certain number of questions from each section as configured by the user.
This is done with `--structure STRUCTURE` command-line switch.
The `STRUCTURE` must be a comma separated list of entries, each in the form `group:N`, where `group` indicates from which group to sample questions and `N` indicates how many. Fro example: if `STRUCTURE` is `bash:2,general:2`, Fast Exam creates an exam with 2 questions chosen randomly from the `bash` group (in the `bash.yml` file) and 2 questions from the `general` group (in the `general.yml` file)

Then, Fast Exam creates a configurable number of different versions of the exam by shuffling the questions and optionally the quiz answers.

The questions can be of three types:

### Quiz
Questions of type `quiz` are classical closed-answer quiz. Fast Exam can shuffle the choice order and highlight the correct answer in the solution PDF. They are created with the following form:
```yaml
- question: The question of the quiz
  type: quiz
  options:
  - Option 1
  - Option 2
  solution: 1 # Index of correct answer. Index start from 0
```

### Open Questions
Questions of type `open` are classical open-answer questions, where a blank space is left for the answer. Fast Exam can optionally print a solution text in the solution PDF.

```yaml
- question: Text of the exercise
  type: open
  height: 5cm # How tall is the box for the student to write. Default 5cm
  solution: Solution text, optional
```

### Dynamic exercises

Finally, questions can be `exercise`, which is the most flexible way.
In this way, it possible to create dynamic exercises, where input data (and the solution) are randomized.

The `exercise` is thus based on a Python code that is executed at each run, included in the `handler` field of the `question`. The Python code must define a `handler()` function, which returns the (randomized) exercise at each invocation. The `handler()` function must return a dictionary, with keys `question` and optionally `solution`. Both fields must the Latex code that is used to build the document.

The `handler()` function is invoked by Fast Exam for each **version**, so that exercise parameters are randomized for each version. 

A complete example is provided in the `exercise.yml` file. The fundamental code is below: 

```yaml
- type: exercise
  height: 5cm
  handler: |
    import random
    import math
    def handler():
        ...
        return {"question": QUESTION, "solution": ANSWER}
```

## Usage

Run:
```
fast-exam [-h] [--date DATE] [--course COURSE] [--professor PROFESSOR] [--institution INSTITUTION] [--versions VERSIONS]
                 [--disclaimer DISCLAIMER] [--db DB] [--structure STRUCTURE] [--outdir OUTDIR] [--lang LANG] [--seed SEED]
                 [--template TEMPLATE] [--keeptex] [--randomorder] [--solutionsummary] [--quizbegin] [--quizrandom]s
```

The command-line options are:
- Exam Heading:
  - `--date DATE` The date of the exam
  - `--course COURSE` The name of the course
  - `--professor PROF` The name of the professor
  - `--institution INSTITUTION` The name of the institution
  - `--versions VERSIONS` How many versions of the exam to create
  - `--disclaimer DISCLAIMER` Instructions for the exam to print in the PDF
- Exam Building:
  - `--db DB` The folder of the quiz DB
  - `--structure STRUCTURE` How to sample the DB. Must be a list of group:n entries, separated by comma. E.g., `general:1,bash:1,open:1`
  - `--outdir OUTDIR` Where to save PDFs
  - `--lang LANG` Language. Supported IT and EN
  - `--seed SEED` Seed of the `random`
  - `--template TEMPLATE` Template to use. Defaults to 'template.tex'
  - `--keeptex` Keep Latex source for further editing
- Question options:
  - `--randomorder` If set, randomize the order of questions
  - `--quizbegin` If set, put all quiz before open questions
  - `--quizrandom` If set, randomize quiz answer order
  - `--solutionsummary` Print summary of the correct answers to quiz
  - `--quizbox` Print a box where students must write answers to quiz, to ease the correction







