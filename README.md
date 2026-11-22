# metafam: knowledge graph analysis and link prediction

my solution to the precog (iiit hyderabad) recruitment task on **metafam**, a made up
family tree stored as a knowledge graph: triples like `olivia0 sisterOf selina10`.
13,821 training triples and 590 test triples, 28 relation types, about 50 separate families.
done in february 2026.

the full write-up with plots is `Knowledge Graph Analysis and Link Prediction Report.pdf`.

## what's in the notebooks

`notebook/exploration.ipynb`

- **graph structure**: degree, betweenness and closeness centrality, connected components,
  generation depth from parent-child edges. the graph is about 50 separate families of
  26 to 27 people each, shallow in width but several generations deep.
- **family clusters**: louvain and girvan-newman community detection on the largest family,
  and how the communities line up with real family units.
- **rule mining**: horn clause rules (e.g. a sister of my mother is my aunt), inverse rules
  (fatherOf / sonOf) and multi hop rules, with support and confidence.
- an interactive view of the largest family (`largest_family_only.html`, made with pyvis).

`notebook/linkprediction.ipynb`

- **transe** written from scratch in pytorch, first plain, then with changes for this graph.
- **r-gcn** (relational graph convolutional network) written from scratch.
- filtered evaluation: when ranking the right answer for a test triple, other true triples
  are not counted against it.

## results (test set, filtered)

| model | mrr | hits@1 | hits@3 | hits@10 |
|---|---|---|---|---|
| transe, plain | 0.188 | 0.000 | | 0.664 |
| transe, with changes | 0.414 | 0.190 | 0.539 | 0.903 |
| r-gcn | **0.651** | **0.449** | | **0.992** |

transe struggles with exact answers (hits@1) because many family relations are one to many
or symmetric, which a single translation vector can't model well. r-gcn uses the
neighbourhood of each person and does much better.

## run it

```
python3 -m venv .venv && .venv/bin/pip install -r requirements.txt
.venv/bin/jupyter notebook notebook/
```

the data is in `data/` (train.txt, test.txt, one triple per line).
