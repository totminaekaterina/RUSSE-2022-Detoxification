# RUSSE-2022-Detoxification
The task of [RUSSE Detoxification 2022](https://www.dialog-21.ru/en/dialogue-evaluation/competitions/dialogue-evaluation-2022/russe-2022-detoxification/) was completed at the level of toxic comments. In this formulation, the goal is to get a neutral sentence out of a toxic one. 
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

![data_metrics](https://github.com/totminaekaterina/RUSSE-2022-Detoxification/blob/main/imgs/data_metrics.png)

*Figure 1: Fragment of tabular data*

## STAGE II. A PRETRAINED AUTOREGRESSIVE MODEL ruGPT3
### Step 1

After we used a pretrained autoregressive GPT2-like model with 350M parameters from SberDevices called RuGPT3Medium. Finetuning was done on the prepared examples from the filtered train dataset using transformers library. 

### Step 2

These examples were fed into the model with the addition of special tokens (<|startoftext|> - in the beginning, <|sep|> - between toxic and detoxified sentences, <|pad|> - padding token): 
*<|startoftext|>toxic sentence<|sep|>detoxified sentence*

After finetuning it is possible to feed into the model a prepared example as follows: 
*<|startoftext|>new toxic sentence<|sep|>*
and have the model generate a detoxified sentence.

### Step 3

Feeding the toxic sentence as a prompt into the model can generate several neutral sentences. Parameters were chosen empirically.

## STAGE III. USING THE RUPROMPTS LIBRARY

Subsequent processing takes place using the ruPrompts library for ruGPT-3 (Fig.2), in which the seed was searched by gradient descent, which was later trained. 

![ruPrompts](https://github.com/totminaekaterina/RUSSE-2022-Detoxification/blob/main/imgs/ruPrompts.png)

*Figure 2: ruPrompts library*

The training of the model was carried out using the completed ruGPT3 model obtained at the previous stage. The following arguments to train the model were selected.
 

## STAGE IV. USING THE DELETE METHOD

At the final stage of processing, the data obtained at the previous stage were finalized using the Delete method proposed by the organizers as the basic one.

## RESULTS
The results of the data obtained at the Evaluation stage on the test dataset provided as the Evaluation result are presented in Fig.3.

![Evaluation](https://github.com/totminaekaterina/RUSSE-2022-Detoxification/blob/main/imgs/Evaluation.png)

*Fig.3: Evaluation*

---

Were compared the results of neutralizing the toxic comments by two metrics: Cosine similarity and ROUGE-L to compare the initial results for the detoxified proposals of the organizers on training dataset and our final results (Fig. 4).

![Cosine similarity and ROUGE-L](https://github.com/totminaekaterina/RUSSE-2022-Detoxification/blob/main/imgs/Cosine%20similarity%20and%20ROUGE-L.png)

*Fig.4: Cosine similarity and ROUGE-L on train and test datasets*

---

Were selected some examples of the detoxified sentences predicted for test evaluation for the loss of the initial value of the input toxic sentence, as well as the complete or partial preservation of toxicity in them.

| Result | Signs of toxicity | 
|:------------|:-----------:|
| Complete absence of toxicity    | Absence of obscene vocabulary, words with negative connotation; complete preservation of meaning | 
| Partial loss of toxicity    | Preservation of negative connotation, but with the absence of obscene vocabulary. The meaning of the sentence differs from the original, the loss of some important details, but the result can be called nontoxic | 
| Preservation of toxicity    | Complete preservation of the toxicity of the original sentence or nonsense  | 


In Fig. 5, There are the proposals that *completely got rid of the negative connotation and moved into the category of neutral*.

![Category 1](https://github.com/totminaekaterina/RUSSE-2022-Detoxification/blob/main/imgs/completely%20got%20rid%20of%20the%20negative%20connotation.png)

Fig. 6 shows examples of those results of proposals that *were not fully detoxified or with errors*, but without any serious negative features. 

![Category 2](https://github.com/totminaekaterina/RUSSE-2022-Detoxification/blob/main/imgs/were%20not%20fully%20detoxified.png)

The examples of proposals presented in Fig. 7 can be characterized as those that have not been subjected to any restrictions at all, they have left a negative connotation and retained their original toxicity.

![Category 3](https://github.com/totminaekaterina/RUSSE-2022-Detoxification/blob/main/imgs/have%20not%20been%20subjected%20to%20any%20restrictions%20at%20all.png)

## FURTHER IMPROVEMENT OF THE MODEL
Were evaluated one of the approaches to detoxification of sentences by fine-tuning a pre-trained ruGPT3 model and selecting generated samples based on the similarities and differences between the input toxic and output neutral sentences, using the ruPrompts library and the Delete method. At the **Human Evaluation stage**, the system received a href metric value of **0.455**; at the **Automatic Evaluation stage** - **0.505**, and took eighth place at **Manual Evaluation**. The results published on [the conference page](https://www.dialog-21.ru/evaluation/2022/russe/).

On the ([Dialog conference](https://www.dialog-21.ru/media/5740/totminaev114.pdf)) website you can find the article about this work :)
