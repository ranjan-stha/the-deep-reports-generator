## reports_generator
This package is intended to generate abstractive reports based on sentences or paragraphs treating the same topic.

### Install

```posh
pip install git+https://github.com/the-deep-nlp/the-deep-reports-generator
```

### Package description
- This package is based on pretrained models from the transformers library. 
It is a based on a multilingual summarization model (languages can be found [here](https://huggingface.co/csebuetnlp/mT5_multilingual_XLSum)). 
- It was first intended to automatically generate reports for the humanitarian world and tested to generate real world test reports, one example being a
[Humanitarian Needs Overview for the Ukraine Project](https://drive.google.com/file/d/1TZzRyRdNYxF0etbfmqd1BtqL4V3uT6Ob/view?usp=sharing).
- To keep the most relevant information only, the library is based on doing summarization iterations on the original text. 
- We show pseudocodes of the methodlogy used for reports creation
```
`Method: Main function call`
    `Inputs`: List of sentences or paragraph
    `Output`: summary
    
    summarized text = OneSummarizationIteration(original text)
    While (summarized text too long and maximum number of iterations not reached):
        summarized text = OneSummarizationIteration(summarized text)
        
        
        
`Method: OneSummarizationIteration`
    `Inputs`: text
    `Outputs`: Summarized Text
    
    1 - Split text into sentences
    2 - Get sentence embeddings for each sentence
        i - preprocess sentence (delete stop words, remove punctuation, stem and lemmatize words)
        ii - get sentence embeddings using preprocessed sentences (using pretrained HuggingFace models).
    3 - Cluster sentences into different categories using the HDBscan clustering method
    4 - Generate summary for each cluster (using pretrained HuggingFace models).
    5 - link summaries of each together together and return them.
```

### Usage
#### Basic Usage:
Get a report from raw text.
```
from reports_generator import ReportsGenerator
summarizer = ReportsGenerator()    
generated_report = summarizer(original_text, deployed_endpoint_name=ENDPOINT_NAME)
```
#### Advanced Usage:
We give an example of another possible usage of the library, using a multilabeled data. 
```
import pandas as pd
from reports_generator import ReportsGenerator
summarizer = ReportsGenerator()

# Example of classified data to import, with two columns, `original_text` and `groundtruth_labels` 
df = pd.read_csv([csv_path]) 
labels_list = df.groundtruth_labels.unique()

# Get summary for each label
generated_report = {}
for one_label in labels_list:
    mask_one_label = df.groundtruth_labels==one_label
    text_one_label = df[mask_one_label].original_text.tolist()
    generated_report[one_label] = summarizer(text_one_label, deployed_endpoint_name=ENDPOINT_NAME)
```
#### English and French languages
For English and french languages, we advise the usage of language specific summarization models by initializing the library the following ways:
* _English reports_:
```
summarizer = ReportsGenerator(summarization_model_name="sshleifer/distilbart-cnn-12-6") 
```
* _French reports_:
```
summarizer = ReportsGenerator(summarization_model_name="plguillou/t5-base-fr-sum-cnndm") 
```
