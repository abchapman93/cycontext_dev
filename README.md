# cycontext
A Python implementation of the ConText algorithm for clinical text concept assertion using the spaCy framework

# Overview
This package implements the ConText algorithm within the [spaCy](https://spacy.io) framework. 
[ConText](https://www.sciencedirect.com/science/article/pii/S1532046409000744) detects semantic modifiers such as **negation**, 
**family history**, and **certainty** in clinical text by
relating target concepts (such as "pneumonia") with semantic modifiers (such as "no evidence of"). 

This builds on [pyConText](https://github.com/chapmanbe/pyConTextNLP), which extracts both targets and modifiers using
regular expressions and relates them using a NetworkX graph.

# Key Features
- cycontext is used as a modular [spaCy component](https://spacy.io/usage/processing-pipelines)
- Modifiers are defined in a knowledge base which are used to modify target spans
- Results are stored in a custom attribute `Doc._.context_graph`
- Modifier -> target relationships are stored in `Doc._.context_graph.edges`

# Basic Usage

## Example
```python
import spacy

from cycontext import ConTextComponent, ItemData

nlp = spacy.load("en_core_web_sm")
text = "There is no evidence of pneumonia."

###########################################
# Add code for extracting target concepts #
###########################################
doc = nlp(text)
print(doc.ents)
>>> (pneumonia,)

item_data = [ItemData("no evidence of", "DEFINITE_NEGATED_EXISTENCE", rule="forward")]
context = ConTextComponent(nlp)
context.add(item_data)
nlp.add_pipe(context, last=True)

doc = nlp(text)
print(doc._.context_graph.edges)
>>> [(pneumonia, <TagObject> [No evidence of, definite_negated_existence])]

for target in doc.ents:
    print(target._.modifiers)

>>> (<TagObject> [No evidence of, definite_negated_existence],)
```

## Visualization
We can use [spaCy's visualizers](https://spacy.io/usage/visualizers) to display the results of cycontext.
This NER-style visualization highlights the targets and modifiers in text:

```python
from cycontext import viz
viz.visualize_ent(doc, colors ={"EVIDENCE_OF_PNEUMONIA": "orange",
                                   "DEFINITE_NEGATED_EXISTENCE": "#34b1eb",
                               })
``` 

<p align="center"><img width="75%" src="docs/ent_viz.png" /></p>

While this dependency-style visualization shows the relationships between targets and modifiers:
```python
viz.visualize_dep(doc)
``` 
<p align="center"><img width="75%" src="docs/dep_viz.png" /></p>


# Contact Information
Alec Chapman: alec.chapman@hsc.utah.edu
