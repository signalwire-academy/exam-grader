# Exam Grader

GitHub Action for grading written exams submitted via GitHub Issues.

## How It Works

1. Students open an issue using the exam submission template
2. They enter their answers in the issue body (e.g., `1. A`, `2. B`, etc.)
3. The workflow triggers on issue creation/edit
4. Answers are graded against the answer key stored in secrets
5. Results are posted as a comment
6. Issue is closed if passed (optional)

## Usage

### 1. Store Answer Key as Secret

Create a repository or organization secret containing your answer key as JSON:

**Secret name:** `EXAM_ANSWERS` (or level-specific like `EXAM_L1_ANSWERS`)

**Secret value:**
```json
{"name":"Level 1 Written Exam","answers":{"1":"B","2":"A","3":"C","4":"B","5":"D"}}
```

### 2. Workflow Setup

```yaml
name: Grade Exam

on:
  issues:
    types: [opened, edited]

jobs:
  grade:
    if: contains(github.event.issue.labels.*.name, 'exam-submission')
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      
      - name: Grade exam
        uses: signalwire-academy/exam-grader@v1
        with:
          answer-key-json: ${{ secrets.EXAM_ANSWERS }}
          passing-score: 70
```

### 3. Issue Template

Create `.github/ISSUE_TEMPLATE/exam-submission.md`:

```markdown
---
name: Submit Exam
about: Submit your exam answers
labels: exam-submission
---

## My Answers

1. 
2. 
3. 
...
```

## Inputs

| Input | Description | Default |
|-------|-------------|---------|
| `answer-key-json` | Answer key JSON string (from secret) | *required* |
| `passing-score` | Minimum % to pass | `70` |
| `show-answers-on-pass` | Reveal answers when passed | `true` |
| `close-on-pass` | Close issue when passed | `true` |

## Outputs

| Output | Description |
|--------|-------------|
| `score` | Correct answers |
| `total` | Total questions |
| `percentage` | Score percentage |
| `passed` | Boolean pass status |

## Answer Key Format

Store as a GitHub Secret (JSON string, single line):

```json
{"name":"Exam Name","answers":{"1":"A","2":"B","3":"C","4":"D","5":"A"}}
```

## Answer Format

Students can use any of these formats:
- `1. A`
- `1: A`
- `1) A`
- `Q1: A`
- `1 A`

Case insensitive (a = A).

## Security

Answer keys are stored as GitHub Secrets - they are never exposed in logs or to students.

## License

MIT
