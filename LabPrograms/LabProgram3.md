### For a given set of training data examples stored in a .CSV file, implement and demonstrate the Candidate-Elimination algorithm to output a description of the set of all hypotheses consistent with the training examples.
**Candidate Elimination Algorithm**

The Candidate Elimination algorithm is a concept learning algorithm used for inductive learning. Its purpose is to generate a hypothesis of a concept based on a set of training examples. The algorithm maintains two hypotheses: the most specific hypothesis (S) and the most general hypothesis (G). Initially, S is set to the most specific hypothesis, and G is set to the most general hypothesis.

**Algorithm Overview**

1. **Initialization:**
   - S is initialized as the most specific hypothesis with all attributes set to "null" or "?".
   - G is initialized as the most general hypothesis with all attributes set to a wildcard "*" (indicating a match with any value).

2. **Iterative Process:**
   - The algorithm processes each training example one by one.
   - For each positive example, S is updated to make it more general to match the positive example.
   - For each negative example, G is updated to make it more specific to ensure it doesn't match the negative example.

3. **Final Hypotheses:**
   - After processing all training examples, S represents the most specific concept covering all positive examples, and G represents the most general concept not matching any negative examples.

4. **Output:**
   - The algorithm outputs the final S and G, which can be used for predictions or classification of new examples.

**Explanation of the Code**

The provided code is an implementation of the Candidate Elimination algorithm for concept learning. Let's break down the code step by step:

**Data Import and Preprocessing**

The code uses Pandas to read training data from a CSV file and select specific columns for training.

**Initialization**

- `specific_h` is initialized as the most specific hypothesis with attribute values from the first instance.
- `general_h` is initialized as the most general hypothesis with wildcard values for attributes.

**Learning Loop**

The code enters a loop to process each instance in the training data.

**Positive and Negative Instance Handling**

- For each instance, the code checks the target value to determine if it's positive or negative.
- If positive, `specific_h` is updated to be more general, and `general_h` becomes more specific to match the positive instance.
- If negative, `general_h` is updated to avoid matching the negative instance.

**Print Hypotheses**

The code prints the specific and general hypotheses after processing each instance to visualize hypothesis generation.

**Final Hypotheses**

After processing all instances, the code refines the general hypothesis by removing unnecessary '?' values.

**Output**

The final specific and general hypotheses are printed, representing the learned concept boundaries from the training examples.

This code provides a simple implementation of the Candidate Elimination algorithm, showing how hypotheses are updated based on training examples. It's a fundamental concept in machine learning and concept learning.

```python
import numpy as np
import pandas as pd

data = pd.read_csv('enjoysport.csv', delimiter=',', usecols=[0, 1, 2, 3, 4, 5, 6])

print(data)
concepts = np.array(data.iloc[:, 0:-1])
print("\nInstances are:\n", concepts)
target = np.array(data.iloc[:, -1])
print("\nTarget Values are: ", target)

def learn(concepts, target):
    specific_h = concepts[0].copy()
    print("\nInitialization of specific_h and general_h")
    print("\nSpecific Boundary: ", specific_h)
    general_h = [["?" for i in range(len(specific_h))] for i in range(len(specific_h))]
    print("\nGeneric Boundary: ", general_h)

    for i, h in enumerate(concepts):
        print("\nStep", i + 1, ": ", h)
        if target[i] == "Yes":
            print("Instance is Positive ")
            for x in range(len(specific_h)):
                if h[x] != specific_h[x]:
                    specific_h[x] = '?'
                    general_h[x][x] = '?'

        if target[i] == "No":
            print("Instance is Negative ")
            for x in range(len(specific_h)):
                if h[x] != specific_h[x]:
                    general_h[x][x] = specific_h[x]
                else:
                    general_h[x][x] = '?'

        print("Specific Boundary ", specific_h)
        print("Generic Boundary", general_h)
        print("\n")
    indices = [i for i, val in enumerate(general_h) if val == ['?', '?', '?', '?', '?', '?']]
    for i in indices:
        general_h.remove(['?', '?', '?', '?', '?', '?'])
    return specific_h, general_h

s_final, g_final = learn(concepts, target)
print("Final Specific_h: ", s_final, sep="\n")
print("Final General_h: ", g_final, sep="\n")
```

```text
     sky airtemp humidity    wind water forecast enjoysport
0  Sunny    Warm   Normal  Strong  Warm     Same        Yes
1  Sunny    Warm     High  Strong  Warm     Same        Yes
2  Rainy    Cold     High  Strong  Warm   Change         No
3  Sunny    Warm     High  Strong  Cool   Change        Yes

Instances are:
 [['Sunny' 'Warm' 'Normal' 'Strong' 'Warm' 'Same']
 ['Sunny' 'Warm' 'High' 'Strong' 'Warm' 'Same']
 ['Rainy' 'Cold' 'High' 'Strong' 'Warm' 'Change']
 ['Sunny' 'Warm' 'High' 'Strong' 'Cool' 'Change']]

Target Values are:  ['Yes' 'Yes' 'No' 'Yes']

Initialization of specific_h and general_h

Specific Boundary:  ['Sunny' 'Warm' 'Normal' 'Strong' 'Warm' 'Same']

Generic Boundary:  [['?', '?', '?', '?', '?', '?'], ['?', '?', '?', '?', '?', '?'], ['?', '?', '?', '?', '?', '?'], ['?', '?', '?', '?', '?', '?'], ['?', '?', '?', '?', '?', '?'], ['?', '?', '?', '?', '?', '?']]

Step 1 :  ['Sunny' 'Warm' 'Normal' 'Strong' 'Warm' 'Same']
Instance is Positive 
Specific Boundary  ['Sunny' 'Warm' 'Normal' 'Strong' 'Warm' 'Same']
Generic Boundary [['?', '?', '?', '?', '?', '?'], ['?', '?', '?', '?', '?', '?'], ['?', '?', '?', '?', '?', '?'], ['?', '?', '?', '?', '?', '?'], ['?', '?', '?', '?', '?', '?'], ['?', '?', '?', '?', '?', '?']]



Step 2 :  ['Sunny' 'Warm' 'High' 'Strong' 'Warm' 'Same']
Instance is Positive 
Specific Boundary  ['Sunny' 'Warm' '?' 'Strong' 'Warm' 'Same']
Generic Boundary [['?', '?', '?', '?', '?', '?'], ['?', '?', '?', '?', '?', '?'], ['?', '?', '?', '?', '?', '?'], ['?', '?', '?', '?', '?', '?'], ['?', '?', '?', '?', '?', '?'], ['?', '?', '?', '?', '?', '?']]



Step 3 :  ['Rainy' 'Cold' 'High' 'Strong' 'Warm' 'Change']
Instance is Negative 
Specific Boundary  ['Sunny' 'Warm' '?' 'Strong' 'Warm' 'Same']
Generic Boundary [['Sunny', '?', '?', '?', '?', '?'], ['?', 'Warm', '?', '?', '?', '?'], ['?', '?', '?', '?', '?', '?'], ['?', '?', '?', '?', '?', '?'], ['?', '?', '?', '?', '?', '?'], ['?', '?', '?', '?', '?', 'Same']]



Step 4 :  ['Sunny' 'Warm' 'High' 'Strong' 'Cool' 'Change']
Instance is Positive 
Specific Boundary  ['Sunny' 'Warm' '?' 'Strong' '?' '?']
Generic Boundary [['Sunny', '?', '?', '?', '?', '?'], ['?', 'Warm', '?', '?', '?', '?'], ['?', '?', '?', '?', '?', '?'], ['?', '?', '?', '?', '?', '?'], ['?', '?', '?', '?', '?', '?'], ['?', '?', '?', '?', '?', '?']]


Final Specific_h: 
['Sunny' 'Warm' '?' 'Strong' '?' '?']
Final General_h: 
[['Sunny', '?', '?', '?', '?', '?'], ['?', 'Warm', '?', '?', '?', '?']]
```
