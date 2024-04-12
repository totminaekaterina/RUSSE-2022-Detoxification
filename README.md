# RUSSE-2022-Detoxification
The task of RUSSE Detoxification 2022 was completed at the level of toxic comments. In this formulation, the goal is to get a neutral sentence out of a toxic one. 
The criteria for the complexity of the sentence include the presence of rude and toxic words that complicate the understanding of the meaning of the message, stylized graphic images (emoji), the presence of rare, ambiguous and colloquial words, the presence of anglicisms, etc.

## SOLUTION
Solution consisted in:
1. Filtering the provided training data set. 
2. Fine-tuning the pretrained RuGPT3 model.
3. Selecting examples of detoxified (neutral) sentences generated with its help based on their cosine similarity and ROUGE-L to the input toxic sentence for their subsequent processing using the ruPrompts library for ruGPT3. 
4. The final stage of processing the generated neutral comments was carried out using the Delete method.

## QUALITY CRITERIA
To automatically evaluate the models, the organizers gave the following indicators:
1. Style transfer accuracy (**STA**).
2. Meaning preservation (**SIM**).
3. Fluency score (**FL**).
4. Joint score (**J**).
5. Metric for evaluating texts based on the similarity of characters between generated and reference texts (**ChrF1**).

## STAGE I. DATA PREPROCESSING
Firstly, we use the toxic and corresponding neutral comments and filter them by cosine similarity and ROUGE-L metrics between toxic and neutral sentences.
To select good examples, we used the following indicators to select the data of toxic and neutral proposals.

The filtered data set was obtained by selecting pairs of sentences that make up 3869 comments (Fig. 1) and have: 
1. Cosine similarity between 0.6 and 0.99. 
2. ROUGE-L between 0.1 and 0.8. 
3. The length of the token of a neutral sentence, which is less than or equal to the length of the token of a toxic sentence.

![data_metrics](https://raw.githubusercontent.com/totminaekaterina/RUSSE-2022-Detoxification/main/data_metrics.png)

## STAGE II. A PRETRAINED AUTOREGRESSIVE MODEL ruGPT3
### Step 1

After we used a pretrained autoregressive GPT2-like model with 350M parameters from SberDevices called RuGPT3Medium. Finetuning was done on the prepared examples from the filtered train dataset using transformers library. 

### Step 2

These examples were fed into the model with the addition of special tokens (<|startoftext|> - in the beginning, <|sep|> - between toxic and detoxified sentences, <|pad|> - padding token): 
**<|startoftext|>toxic sentence<|sep|>detoxified sentence**

After finetuning it is possible to feed into the model a prepared example as follows: 
**<|startoftext|>new toxic sentence<|sep|>**
and have the model generate a detoxified sentence.

### Step 3

Feeding the toxic sentence as a prompt into the model can generate several neutral sentences. Parameters were chosen empirically and presented in Table 2.




 
