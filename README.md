# Analyzing Language Model Self-Play

Code for the paper ["Efficacy of Language Model Self-Play in Non-Zero-Sum Games."](https://arxiv.org/abs/2406.18872) We run experiments on a modified version of the Deal or No Deal game, originally introduced in [Lewis, et al. (2017)](https://arxiv.org/abs/1706.05125). This repository includes code for (1) evaluating language models on Deal or No Deal, (2) finetuning language models via self-play, and (3) a web interface for human-LM experiments.

<img width="1321" alt="Screenshot 2024-06-27 at 5 31 47 PM" src="https://github.com/nickatomlin/lm-selfplay/assets/13228316/51fac111-a590-4ee7-844d-1109e17376fe">

## Setup

To install necessary packages, run the following command:

```sh
pip install -r requirements.txt
```
We use the OpenAI API for model inference and finetuning. In order to run the code in this repository, you will need to set the `OPENAI_API_KEY` and `OPENAI_ORG_ID` environment variables. The current code will default to using the `gpt-3.5-turbo` model unless otherwise specified.

## Language Model Self-Play
Game logic is available in `games/negotiation.py` and LM prompts are available in the `prompts/` folder. The `data/` folder contains detokenized data from [Lewis, et al. (2017)](https://arxiv.org/abs/1706.05125).
### play.py

Script for running self-play (or human play) for a specific model. To run the script through the command line, specify the following parameters:

- `--objective` ["self", "coop", "comp"]: the objective that the games should be played under

- `--model` (e.g., `gpt-3.5-turbo`): the language model queried by the OpenAI chat endpoint for self-play

- `--temp` (defaults to `1`): temperature parameter for output generation

- `--num_runs` (int): the number of games of self-play to run

- `--output_dir`: path of the directory to be used for game logging


Example usage:
```sh
python3 play.py --objective "self" --model "gpt-4" --temp 1 --num_runs 10 --output_dir "data/gpt-4"
```

### finetuning.py

Script for creating a finetuning job for the data of a specific model. To run the script through the command line, specify the following parameters:

- `--dir`: the data directory (specified by the --output_dir flag used by play.py) used to create the training file

- `--objective` ["self", "coop", "comp"]: the objective that the training data was generated under (required to determine filtering criteria). Should be one of 

- `--filter` ["above_avg", "nonzero", "all"]: the criteria on which game samples are filtered. Should be one of "above_avg" (include only game samples that score above the average for this round of self-play), "nonzero" (include any game samples that scored above 0), or "all" (include all game samples)

- `--model_id`: the full ID of the OpenAI model to train. For training a base gpt-3.5-turbo model, this should be set to "gpt-3.5-turbo"

- `--suffix`: Specifies the desired suffix for the finetuned model. Useful for creating descriptive model IDs.

```sh
python3 finetuning.py --dir "data/gpt-4/" --objective "self" --filter "above_avg" --model_id "gpt-3.5" --suffix "semi-iter01"
```

### analysis.py

Specifies functions for analysis. Running this script will call the `analyze()` function, which prints a set of statistics for a particular round of self-play. To run the script through the command line, specify the following parameters:

- `--path`: the data directory (specified by the --output_dir flag used by play.py)
- `--objective`: the objective that the round of self-play was played under

```sh
python3 analysis.py --action "analyze" --path "data/gpt4_ftbm_coop/gpt-4/" --objective "coop"
```

Important: to run this script, you will need to modify the `create_csv()` function with the paths generated by the finetuning code.

### utils.py

Helper functions for extracting info from game data, along with creating training JSONs and starting fine-tuning jobs.

## Human Experiments
We also include a Flask website for running human-LM experiments on trained models. To launch the website, run the following  code:
```sh
cd web_interface
flask run
```

If you run into any issues with our code, please feel free to open a GitHub Issue or email the authors. Thanks! 

## Citation

```
@misc{liao2024efficacylanguagemodelselfplay,
      title={Efficacy of Language Model Self-Play in Non-Zero-Sum Games}, 
      author={Austen Liao and Nicholas Tomlin and Dan Klein},
      year={2024},
      eprint={2406.18872},
      archivePrefix={arXiv},
      primaryClass={cs.CL},
      url={https://arxiv.org/abs/2406.18872}, 
}
```
