# Quiz Builder

Create randomized exams based on a bank of exercises.
Try it with:
```
./quiz-builder --quizbegin --quizrandom
```

## Dependencies

You need Python3 and Latex installed, on a Linux or MacOS machine.
You need the `pyyaml jinja2`

## Database

The program runs on a database of quiz and open questions.
The database is contained in a folder. Each file contains a *group* of questions.

### Quiz
Questions can be `quiz`, in the form:
```yaml
- question: The question of the quiz
  type: quiz
  options:
  - Option 1
  - Option 2
  solution: 1 # Index of correct answer
  type: quiz
```

### Open Questions
Questions can be `open` questions are in the form:
```yaml
- question: Text of the exercize
  type: open
  height: 5cm # How tall is the box for the student to write. Default 5cm
  solution: Solution text, optional
```

### Dynamic exercizes

Finally,  questions can be `exercize`, which is the most flexible way.

The question must include a `handler` field, which contains the python code that defines a `handler()` function.

The function must return a dictionary, with keys `question` and optionally `solution`.

The function is invoked in the program for each **version**, to obtain programmatically an exercize. This is useful to have exercizes with **random** parameters for different versions.

See `exercize-vm.yml` for a complete example. Fundamental code is below: 
```
- type: exercize
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
quiz-builder [-h] [--date DATE] [--course COURSE] [--db DB] [--versions VERSIONS] [--structure STRUCTURE]
                    [--outdir OUTDIR] [--quizbegin] [--quizrandom] [--lang LANG] [--seed SEED] [--template TEMPLATE]
                    [--disclaimer DISCLAIMER] [--keeptex]
```

The option are:
- `-h, --help` Show Help
- `--course COURSE` The name of the course
- `--db DB` The folder of the quiz DB
- `--versions VERSIONS` How many versions of the exam to create
- `--structure STRUCTURE` How to sample the DB. In the form of *group*:n,*group*:n. E.g., `general:1,bash:1,open:1`
- `--outdir OUTDIR` Where to save PDFs
- `--quizbegin` If set, put all quiz before open questions
- `--quizrandom` If set, randomize quiz aswer order
- `--lang LANG` Language. Supported IT and EN
- `--seed SEED` Seed of the `random`
- `--template TEMPLATE` Template to use. Defaults to 'template.tex'
- `--disclaimer DISCLAIMER` Instructions for the exam to print in the PDF
- `--keeptex` Keep Latex source for further editing

## Operation

The tool selects a certain number of questions from each section. Configure with `--structure STRUCTURE`. It creates different versions of the exam by shuffling the question order and optionally the question answer order.







