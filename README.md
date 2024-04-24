# First Order Logic Forward Chaining

```plaintext
    By Pranshu Acharya (02118866) & Abhinandan Varma Nadimpalli (02072062)
```

This Notebook implements Forward Chaining using First Order Logic (FOL) by creating a simple chatbot that builds Knowledge Base through a text file and answers basic queries.

- [First Order Logic Forward Chaining](#first-order-logic-forward-chaining)
  - [Requirements](#requirements)
  - [Knowledge Base (KB)](#knowledge-base-kb)
  - [Queries](#queries)
  - [Output](#output)
  - [How To Run](#how-to-run)
  - [Functions Explanation](#functions-explanation)
    - [1. readKB()](#1-readkb)
    - [2.readQueries()](#2readqueries)
    - [3. initOutput()](#3-initoutput)
    - [4. output(Query, Result)](#4-outputquery-result)
    - [5. mapVars(fact,left,right,maps)](#5-mapvarsfactleftrightmaps)
    - [6. matchRules(rule,fact)](#6-matchrulesrulefact)
    - [7. forwardChaining(KB,Query)](#7-forwardchainingkbquery)
  - [Code Explanation](#code-explanation)


## Requirements

1. Python
2. Jupyter

```bash
pip install notebook #For jupyter notebook
```

## Knowledge Base (KB)

The KB is stored in the "*Files*" folder and is labelled as KB.txt.
KB other than those can be added in the text file but needs to follow the format like below:

KB.txt

```text
RegisteredFor(x,'Algebra101')=>Student(x)
FailedMidterm(x)=>NeedsHelp(x)
ScoreAbove90(x)=>EligibleForHonorRoll(x)
AsksForTutoring(x)=>ReceivesTutoring(x)
StrugglesWith(x, y)=>ReceivesHelpOn(y)
StrugglesWith('Amy','QuadraticEquations')
FailedMidterm('Bob')
```

So, for example the following new line can be added to the KB.

```text
ScoreAbove90('Charlie')
```

Notes:

- I have edited some words to remove the letter 'x'.

   ```text
   Needs*Extra*Help is NeedsHelp.
  ```

  - The code depends on the variables x and y and thus would break if any other words contain 'x' or 'y'
- Changed the arrows.

  ```text
  ==> is =>
  ```

- Used single quotes.

  ```text
  FailedMidterm("Bob") is FailedMidterm('Bob')
  ```

## Queries

The Queries are stored in the "*Files*" folder and is labelled as queries.txt.
Queries other than those can be added in the text file but needs to follow the format like below:

queries.txt

```text
ReceivesHelpOn('QuadraticEquations')
NeedsHelp('Bob')
EligibleForHonorRoll('Charlie')
```

So, for example the following new line can be added to the queries:

```text
NeedsHelp('Charlie')
```

Note:
For the last query:

```text
If Charlie scores a 92 on his midterm, is he eligible for the honor roll?
```

The KB.txt should be added with a new knowledge:

```text
ScoreAbove90('Charlie')
```

Then only the query will be true.

## Output

The output is stored inside the *files* folder with the name output.txt. The format of the output is:

```text
Query: <Query>; Answer: <Result>
```

For Example:

```text
Query: ReceivesHelpOn('QuadraticEquations'); Answer: True
```

## How To Run

1. Extract all files including the 'files' folder and open the terminal in the folder where the ipynb is.
2. Open the notebook as a jupyter notebook.

   ```bash
   jupyter notebook 
   ```

3. Run the main cell of the notebook.
4. An output file should be generated inside the files folder.

## Functions Explanation

### 1. readKB()

```plaintext
Reads the KB.txt file and returns an array of each line after the removal of '\n'.
```

### 2.readQueries()

```plaintext
Reads the queries.txt file and returns an array of each line after the removal of '\n'.
```

### 3. initOutput()

```plaintext
Opens the output.txt file in write mode so that the file is emptied.
```

### 4. output(Query, Result)

```plaintext
Reads the Query and the Result and write in the output.txt file in the apprpriate format.
```

### 5. mapVars(fact,left,right,maps)

```plaintext
Takes in a fact, left, right and map to return the updated fact. 
```

Example:

```text
FailedMidterm(x) will be changed to FailedMidterm('Bob')
```

### 6. matchRules(rule,fact)

```plaintext
Checks two FOL if they are equal. If they are, return the variable mappings.
```

Example:

```plaintext
if:
fact= StrugglesWith(x, y)
rule= StrugglesWith('Amy','QuadraticEquations')

return:
{x:'Amy',y:'QuadraticEquations'}
```

### 7. forwardChaining(KB,Query)

```plaintext
Takes in Knowledge Base and a Query and returns if the Query is true or false.
```

## Code Explanation

1. First we load the Knowledge Base by reading a text file.

    ```py
    f = open("KB.txt", "r")
    lines = f.readlines()
    KB = []

    for line in lines:
        if line.endswith('\n'):
            line = line[:-1]  #Removing the last character (newline character)
        KB.append(line)
      ```

2. Similarly we load the queries and store it in an array.

3. We then initialize the output file to remove any previous outputs.

    ```py
    f=open("output.txt","w")
    f.close()
    ```

4. After that, we iterate through the queries array and apply forward chaining to each query.

    ```py
    for query in queries:
    result = forwardChaining(KB,query)
    ```

5. In forward chaining we first initalize a set called facts. We used set here to avoid any duplicate facts.
6. Then we run an infinite loop. The loop only ends when no new facts are discovered.

   ```py
   while True:
      new_facts = []
   ```

   ```python
   if not new_facts:
          break
   ```

7. First we split every KB into left and right.

   ```py
   for k in KB:
          try:
              l, r = k.split("=>")
   ```

   Example:

   ```text
   P(x,y)=>Q

   Left = P(x,y)
   Right = Q
   ```

8. If the knowledge is a statement, eg: NeedsHelp('Bob'), then add it to the fact set.
   Value error only occurs when there is no '=>' in our knowledge.

   ```py
   except ValueError:
              if k not in facts:
                  new_facts.append(k.strip())
   ```

9. If a fact is present in the set, we try to match it with the left part of the knowledge base.

    ```py
    al, ar = rule.split("(")
    bl, br = fact.split("(")
    if al == bl:
    ```

10. If it matches, then we create a dictionary where we map the variables.

    ```py
    if len(a_vars) == len(b_vars):
            a_vars[0] = a_vars[0].replace("(", "").replace(")", "")
            b_vars[0] = b_vars[0].replace("(", "").replace(")", "")

            if(len(a_vars)>1):
              a_vars[1] = a_vars[1].replace("(", "").replace(")", "")
              b_vars[1] = b_vars[1].replace("(", "").replace(")", "")
            variable_mappings = {a_var.strip(): b_var.strip() for a_var, b_var in zip(a_vars, b_vars)}
    ```

11. We use the dictioary to get the right part and append it to the new_fact array if it is not present.

    ```py
    if((matchRules(l,fact))):
                    new_r=mapVars(fact,l,r,matchRules(l,fact))
                    if(new_r.strip() not in facts):
                      new_facts.append(new_r.strip())
    ```

12. The facts set is updated with new_facts

    ```py
    facts.update(new_facts)
    ```

13. Finally we check if the Query is present in the facts set and determine the result based on that.

    ```py
    if Query.strip() in facts:
      print("Query is true")
      return True
    else:
      print("Query is false")
      return False
    ```
