# deepPavlovEval
Sentence embeddings evaluation for russian tasks.

## Datasets and tasks
Currently we support the following tasks:

|  Task                         | Dataset           | Dataset URL                                                            |
|-------------------------------|-------------------|------------------------------------------------------------------------|
|  Paraphrase detection         | Paraphraser       | http://paraphraser.ru/                                                 |
|  Semantic textual similarity  | translated MSRVid | http://files.deeppavlov.ai/datasets/STS2012_MSRvid_translated.tar.gz   |
|  Natural language inference   | XNLI              | http://www.nyu.edu/projects/bowman/xnli/                               |
|  Sentiment analysis           | Rusentiment       | http://text-machine.cs.uml.edu/projects/rusentiment/                   |

MSRVid is part of SEMEVAL-2012 TASK 17 dataset. It was automatically translated and checked manually. Original dataset and licence can be found [here](https://www.cs.york.ac.uk/semeval-2012/task6/data/uploads/datasets/train-readme.txt)

All data can be downloaded via `data/download.sh` script.

# Usage

```python
from deepPavlovEval import Evaluator
from deeppavlov.models.embedders.fasttext_embedder import FasttextEmbedder

evaluator = Evaluator()
fasttext = FasttextEmbedder('/data/embeddings/wiki.ru.bin', mean=True)
results_fasttext = evaluator.evaluate(fasttext)
```

Results have the following format
```python
>>> results_fasttext
{'paraphraser': {'pearson correlation': 0.37964098780007866},
 'msrvid': {'pearson correlation': 0.7330145176159141},
 'xnli': {'knn_f1': 0.3528115762625721,
  'knn_accuracy': 0.3586826347305389,
  'svm_f1': 0.46821573045481973,
  'svm_accuracy': 0.4694610778443114},
 'rusentiment': {'knn_f1': 0.357914627528629,
  'knn_accuracy': 0.40310077519379844,
  'svm_f1': 0.4482865224076,
  'svm_accuracy': 0.574654533198517}}
```

In order to use deepPavlovEval, model should have `.__call__()` method which returns
sentence embeddings given list of *tokenized* sentences. For example: `[['first', 'sentence'], ['second', 'sentence']]`

```python
import numpy as np
class MyRandomEmbedder:
    def __call__(self, batch):
        return np.random.uniform(size=(len(batch), 300))

my_embedder = MyRandomEmbedder()
results_random = evaluator.evaluate(my_embedder, model_name='random_embedder')
```

Evaluator object accumulates different experiments. They can be accessed via `.all_results`
and saved as .jsonl via `.save_results(save_path)`.

```python
>>> all_results = evaluator.all_results
>>> all_results
[{'task': 'paraphraser',
  'model': deeppavlov.models.embedders.fasttext_embedder.FasttextEmbedder,
  'metrics': {'pearson correlation': 0.37964098780007866}},
 {'task': 'msrvid',
  'model': deeppavlov.models.embedders.fasttext_embedder.FasttextEmbedder,
  'metrics': {'pearson correlation': 0.7330145176159141}},
 {'task': 'xnli',
  'model': deeppavlov.models.embedders.fasttext_embedder.FasttextEmbedder,
  'metrics': {'knn_f1': 0.3528115762625721,
   'knn_accuracy': 0.3586826347305389,
   'svm_f1': 0.46821573045481973,
   'svm_accuracy': 0.4694610778443114}},
 {'task': 'rusentiment',
  'model': deeppavlov.models.embedders.fasttext_embedder.FasttextEmbedder,
  'metrics': {'knn_f1': 0.357914627528629,
   'knn_accuracy': 0.40310077519379844,
   'svm_f1': 0.4482865224076,
   'svm_accuracy': 0.574654533198517}},
 {'task': 'paraphraser',
  'model': 'random_embedder',
  'metrics': {'pearson correlation': -0.004955005150548055}},
 {'task': 'msrvid',
  'model': 'random_embedder',
  'metrics': {'pearson correlation': 0.025535004548834218}},
 {'task': 'xnli',
  'model': 'random_embedder',
  'metrics': {'knn_f1': 0.32845812688562903,
   'knn_accuracy': 0.3401197604790419,
   'svm_f1': 0.33784182130058665,
   'svm_accuracy': 0.33812375249501}},
 {'task': 'rusentiment',
  'model': 'random_embedder',
  'metrics': {'knn_f1': 0.17511439267964699,
   'knn_accuracy': 0.32861476238624876,
   'svm_f1': 0.15701808622986574,
   'svm_accuracy': 0.4236602628918099}}]
```

Read `deepPavlovEval/evaluator.py` for full api description.

More examples in `deeppavlov_models.ipynb`.
