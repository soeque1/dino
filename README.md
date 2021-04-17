# Datasets from Instructions (D<small>INO</small> 🦕)

This repository contains the code for [Generating Datasets with Pretrained Language Models](https://arxiv.org/abs/2104.07540). The paper introduces a method called *<b>D</b>atasets from <b>In</b>structi<b>o</b>ns* (D<small>INO</small> 🦕) that enables pretrained language models to generate entire datasets from scratch.

## 🔧 Setup

All requirements for D<small>INO</small> can be found in ``requirements.txt``. You can install all required packages in a new environment with ``pip install -r requirements.txt``.

## 💬 CLI Usage

#### Single Texts
To generate datasets for (single) text classification, you can use D<small>INO</small> as follows:
````
python3 dino.py \
 --output_dir <OUTPUT_DIR> \
 --task_file <TASK_FILE> \
 --num_entries_per_label <N>
````
where ``<OUTPUT_DIR>`` is a directory to which the generated dataset is written, ``<TASK_FILE>`` is a JSON file containing a *task specification* (see [Task Specs](#-task-specs)), and ``<N>`` is the number of examples to generate per label. To get an overview of additional parameters, run ``python3 dino.py --help``.

#### Text Pairs
To generate datasets for text pair classification, you first need a dataset of raw input texts (which you can also generate using D<small>INO</small>). You can then run
````
python3 dino.py \
 --output_dir <OUTPUT_DIR> \
 --task_file <TASK_FILE> \
 --input_file <INPUT_FILE> \
 --input_file_type <INPUT_FILE_TYPE> \
 --num_entries_per_input_and_label <N>
````
with ``<OUTPUT_DIR>`` and ``<TASK_FILE>`` as before. ``<INPUT_FILE>`` refers to the file containing raw input texts, ``<INPUT_FILE_TYPE>`` specifies its type, which should be one of

   - ``plain``: for a plain text file with one input text per line
   - ``jsonl``: for a dataset file generated by D<small>INO</small> in a previous step

and ``<N>`` is the number of examples to generate per label and input text.

## 📋 Task Specs

🚨 *Before you write custom task specifications, please note that this is still a very early release and we have not tested D<small>INO</small> on other tasks than semantic textual similarity yet. Please let us know if you see something strange.* 🚨

To generate a dataset for a task, you need to provide a file containing a *task specification*, containing (among other things) the instructions given to the pretrained language model. A task specification is a single JSON object that looks like this:

```
{
  "task_name": "<TASK_NAME>",
  "labels": {
    "<LABEL_1>": {
      "instruction": "<INSTRUCTION_1>",
      "counter_labels": [<COUNTER_LABELS_1>]
    },

    ...,

    "<LABEL_n>": {
      "instruction": "<INSTRUCTION_n>",
      "counter_labels": [<COUNTER_LABELS_n>]
    }
  }
}
```
Here, ``<TASK_NAME>`` is the name for the task and ``<LABEL_1>``, ..., ``<LABEL_n>`` are the task's labels. For each label ``<LABEL_i>``, ``<INSTRUCTION_i>`` is the instruction provided to the language model for generating examples with label `<LABEL_i>` (see [Writing Instructions](#writing-instructions)). You can additionally specify a list of counter labels ``<COUNTER_LABELS_n>`` for each label. This tells the model to generate outputs that are not only likely given the current label, but also unlikely given all counter labels (see [the paper](https://arxiv.org/abs/2104.07540) for details).

#### Examples

You can find two examples of task specifications in ``/task_specs``:

- ``sts.json`` is a task specification for generating a semantic textual similarity dataset if *a set of raw input texts is already given*.
- ``sts-x1.json`` is a task specification for generating a set of raw input texts. This set can then be used in a subsequent step to generate a full STS dataset using ``sts.json``.

#### Writing Instructions

When writing instructions for a new task, you should consider the following things:

- Always end your instructions with an (opening) **quotation mark** (`"`). This is required because it allows us to interpret the next quotation mark generated by the language model as a signal that it is done generating an example.
- For good results, keep the instructions as **short and simple** as possible as this makes it easier for a pretrained language model to understand them.
- If you are writing instructions for a text **pair** classification task, make sure that each instruction contains the placeholder ``<X1>`` exactly once. At this position, the provided raw input sentences are inserted during generation.

An example for an instruction that prompts the model to generate a positive review for a restaurant would be:
````
Task: Write a review for a really great restaurant.
Review: "
````

An example for an instruction that prompts the model to generate a sentence that has the same meaning as another given sentence would be:
````
Task: Write two sentences that mean the same thing.
Sentence 1: "<X1>"
Sentence 2: "
````

## 🦕 Generated D<small>INO</small>s

In this section, we will soon make publicly available a list of datasets that we have generated using D<small>INO</small>.

## 📕 Citation

If you make use of the code in this repository or of any D<small>INO</small>-based dataset, please cite the following paper:
````
@article{schick2020generating,
  title={Generating Datasets with Pretrained Language Models},
  author={Timo Schick and Hinrich Schütze},
  journal={Computing Research Repository},
  volume={arXiv:2104.07540},
  url={https://arxiv.org/abs/2104.07540},
  year={2021}
}
````
