![[Transfer Learning Syllabus.pdf]]

![[HANDS ON TRANSFER LEARNING WITH OYTHON-TEXT BOOK.pdf]]

![[Unit-I Chapter 5 (1).pptx.pdf]]

![[Unit-II Chapter 6.pptx.pdf]]

![[Unit-I chapter 4 (1).pptx.pdf]]


VGG16 code:

![[Pasted image 20260106145411.png]]
![[Pasted image 20260106145628.png]]

## difference between Traditional ML AND TL: [2M]

Traditional ML: 
- Traditional ML trains every model in isolation based on the specific domain, data and task
- even if two models perform similar tasks then ML will train them seperately/ in isolation

![[Pasted image 20260106153743.png]]

TL: It is the process of reusing / transfering the knowledge of one model to another model performing similar tasks

![[Pasted image 20260106153804.png]]

## Advantages of TL:
![[Pasted image 20260106154306.png]]

- Improved Baseline performance
- Less model development time
- Improved final performance

## TL strategies: [10m]

Domain - tuple with feature space and marginal probability  [with formulas]
Task - tuple with label space and objective function [with formulas ]

with this TL is defined as the process of improving the ==target== obj function,fr [target task (Tt) ] in the target domain (Dt) , by reusing / transferring the knowledge form the Source Task (Ts) in the Source Domain (Ds)

possibilities:
- feature space : [Xs!=Xt]
- marginal probability : [P(Xs)!=P(Xt)] (also known as domain adaptation)
- label space : [Ys!=Yt]
- conditional probability : [P(Ys|Xs)!=P(Yt|Xt)]

examples:
**1. Feature Space (Xs ≠ Xt)**  
_Source uses different input features than target._  
**Example:** Source dataset contains **text in English**, target dataset contains **text in images (OCR features)**.  
So model sees word embeddings in source, but pixel/OCR numeric features in target.

**2. Marginal Probability / Domain Adaptation (P(Xs) ≠ P(Xt))**  
_Input distribution is different._  
**Example:** Source docs are **formal news articles**, target docs are **casual WhatsApp chats from farmers**.  
Both are English, but style and word frequency differ → distribution shift.

**3. Label Space (Ys ≠ Yt)**  
_Source and target tasks/classes are different._  
**Example:**

- Source labels: **{Sports, Politics, Tech}**
    
- Target labels: **{Vegetables, Fruits, Grains}**  
    Totally different categories → different label space.
    

**4. Conditional Probability (P(Ys|Xs) ≠ P(Yt|Xt))**  
_Same features & labels, but relationship between them changes._  
**Example:** Word **“Apple”**

- In source (news): label = **Tech**
    
- In target (market docs): label = **Fruits**  
    Same word appears, but meaning → label mapping differs based on domain.

### Strategies:
what to transfer : 
- part of the knowledge that will be useful and improve the target tasks performance 
- knowledge is classified as ==source specific== and common b/w source and target

when to transfer:
- TL should not be used just becoz we can ---> becoz it may lead to  negative transfer
- it should be used only when it is useful and improve the target tasks performance
- we should know when to use and not

how to transfer:
- several ways - suitable should be chosen

### Categories: 
Inductive Transfer:
- Here the source and target domains are same but the source and target tasks are different
- The knowledge learned from the source domain is transfered to the target domain/task in order to increase performance
- based on weather the source domain contains labels or not: 
	- multitask learning - learning multiple similar tasks 
	- self-taught learning - learning patterns from unlabelled data 

Unsupervised transfer:
- similar to the inductive learning
- Here also the source and target domains are same but the source and target tasks are different
- but it uses unlabelled data [both source domain and target dont have labels]
- learning patterns from unlabelled data and used for a specific unsupervised task after fine tunning

Transductive Learning:
- Here the source and target domains are different and the source and target tasks are same 
- domain - labelled data
- target - unlabelled data

examples:

1. **Inductive Transfer (Same domain, different tasks)**  
- Domain: Chest X-ray images (same type of medical images)
- Source Task: Detect pneumonia from chest X-rays
- Target Task: Detect lung cancer from chest X-rays (same domain - chest X-rays, but different task)

multitask learning :
- Domain: Patient medical records
- Tasks learned simultaneously:
    - Task 1: Predict heart disease risk
    - Task 2: Predict diabetes risk
    - Task 3: Predict hypertension risk

self-taught learning:
- Domain: General images
- Step 1 (Unlabeled): Learn general features from millions of unlabeled images - edges, textures, shapes, colors
- Step 2 (Labeled): Use these learned features to specifically recognize cats with only 100 labeled cat images

```

Self-Taught Learning (Inductive Transfer)

- Source Domain: Has unlabeled data
- Target Domain: Has labeled data (for the specific task)
- Process: Learn general patterns from unlabeled source data → Apply to labeled target task
- Goal: Improve performance on a supervised task using unlabeled data

 Unsupervised Transfer Learning

- Source Domain: Has unlabeled data
- Target Domain: Has unlabeled data (for unsupervised tasks)
- Process: Learn patterns from unlabeled source data → Apply to unlabeled target tasks
- Goal: Improve performance on unsupervised tasks like clustering or dimensionality reduction

 Concrete Examples to Show the Difference:

 Example 1: Image Processing

Self-Taught Learning:

- Unlabeled: 1M random images (learn general features)
- Labeled: 100 cat images with labels ("cat"/"not cat")
- Task: Supervised cat classification

Unsupervised Transfer Learning:

- Unlabeled: 1M random images (learn general features)
- Unlabeled: 10K medical images (no labels)
- Task: Unsupervised clustering of medical images by disease type

```



3. **Transductive Transfer (Similar tasks, different domains, only source labeled)**  
**Example:**
model used for email spam and ham is used for sms spam and ham

### Approaches ... ---> ollama

1] instance transfer
2] feature-representation transfer
3] parameter transfer
4] relational-knowledge transfer


## TL Methodologies:

What is TL - diff b/w ml and dl and the usage of tl

### TL - Feature Extraction:

![[Pasted image 20260106193222.png|575]]

- DL models have **many layers**.
- Each layer learns **different features** (edges → shapes → objects, etc.)
- And finally all the layer are connected to the output layer which gives the output
- TL - Feature Extraction ---> the final/top layer ---> output layer  can be removed and the remaining all layers can be reused in other domains performing similar tasks[only output layer not FC layer]
- ex: AlexNet with final layers removed ,can be used to convert images into 4096 number vectors

### TL - Fine Tunning:
- Here we not only change the last layer — we can also **retrain some earlier layers** if needed. 
- **First/initial layers learn general features/stuff** (like edges, shapes, patterns).
- **Later layers learn task-specific features/stuff** (like “this is a disease leaf” or “this is a buyer message”).
- So we **freeze (lock) the general layers** and **fine-tune (retrain) the important later ones**.
- This gives **better accuracy + less training time + less data needed**.

### TL - Pre-trained Models:

- The important/fundamental req of the TL is a strong source model / a model that perform well on source tasks 

	Pretrained models:
	 - high performance DL models - that is shared publicly for the people to reuse it
	 - they have millions of parameters/weights 
	 - You can download them using libraries like **Keras, TensorFlow, or Model Zoos**.
	 - Popular pretrained models: **Xception, VGG16, Inception etc.**