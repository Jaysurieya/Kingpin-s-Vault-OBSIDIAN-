IMDB - dataset with vector embeddings:

# Vectorisation in IMDB dataset 

==word_index.items()==
==TfidfVectorizer(max_features=5000)==
==model.vector_size==
==clf_tfidf.fit(X_train_tfidf, y_train) --> only .fit is used ==
==glove function==

```
# =================================================
# 0. INSTALL REQUIRED PACKAGES
# =================================================
!pip install gensim -q

# =================================================
# 1. IMPORT LIBRARIES
# =================================================
from tensorflow.keras.datasets import imdb
from tensorflow.keras.preprocessing.sequence import pad_sequences
from tensorflow.keras.preprocessing.text import Tokenizer
import numpy as np
import pandas as pd
import re
from sklearn.feature_extraction.text import TfidfVectorizer
from gensim.models import Word2Vec
from sklearn.linear_model import LogisticRegression
from sklearn.metrics import accuracy_score

# =================================================
# 2. LOAD IMDB DATASET
# =================================================
# limit vocab for simplicity
(X_train, y_train), (X_test, y_test) = imdb.load_data(num_words=10000)

print("Train size:", len(X_train))
print("Test size :", len(X_test))

# =================================================
# 3. CREATE INDEX → WORD MAPPING (FOR DECODING)
# =================================================
word_index = imdb.get_word_index()
index_word = {v + 3: k for k, v in word_index.items()}
index_word[0] = "<PAD>"
index_word[1] = "<START>"
index_word[2] = "<UNK>"

def decode_review(sequence):
    return " ".join(index_word.get(i, "<UNK>") for i in sequence)

# Convert encoded reviews → text
train_texts = [decode_review(x) for x in X_train]
test_texts  = [decode_review(x) for x in X_test]

# =================================================
# 4. PAD SEQUENCES (OPTIONAL DEMO)
# =================================================
X_train_pad = pad_sequences(X_train, maxlen=200)
X_test_pad  = pad_sequences(X_test, maxlen=200)

print("Padded shape:", X_train_pad.shape)

# =================================================
# 5. TEXT PREPROCESSING
# =================================================
def preprocess(sentences):
    processed = []
    for s in sentences:
        s = s.lower()
        s = re.sub(r'[^a-z\s]', '', s)
        processed.append(s.split())
    return processed

train_tokens = preprocess(train_texts)
test_tokens  = preprocess(test_texts)

print("Sample tokens:", train_tokens[0][:10])

# =================================================
# 6. VECTOR METHOD 1 — TF-IDF
# =================================================
tfidf = TfidfVectorizer(max_features=5000)

X_train_tfidf = tfidf.fit_transform(train_texts)
X_test_tfidf  = tfidf.transform(test_texts)

print("TF-IDF shape:", X_train_tfidf.shape)

# =================================================
# 7. VECTOR METHOD 2 — WORD2VEC
# =================================================
w2v_model = Word2Vec(
    sentences=train_tokens,
    vector_size=100,
    window=5,
    min_count=2,
    workers=4
)

def sentence_vector_w2v(sentence, model):
    vectors = [model.wv[w] for w in sentence if w in model.wv]
    if len(vectors) == 0:
        return np.zeros(model.vector_size)
    return np.mean(vectors, axis=0)

X_train_w2v = np.array([sentence_vector_w2v(s, w2v_model) for s in train_tokens])
X_test_w2v  = np.array([sentence_vector_w2v(s, w2v_model) for s in test_tokens])

print("Word2Vec shape:", X_train_w2v.shape)

# =================================================
# 8. DOWNLOAD GLOVE
# =================================================
!wget -q http://nlp.stanford.edu/data/glove.6B.zip
!unzip -q glove.6B.zip

# =================================================
# 9. VECTOR METHOD 3 — GLOVE
# =================================================
glove = {}
with open("glove.6B.100d.txt", "r", encoding="utf-8") as f:
    for line in f:
        values = line.split()
        glove[values[0]] = np.array(values[1:], dtype="float32")

def sentence_vector_glove(sentence, glove, dim=100):
    vectors = [glove[w] for w in sentence if w in glove]
    if len(vectors) == 0:
        return np.zeros(dim)
    return np.mean(vectors, axis=0)

X_train_glove = np.array([sentence_vector_glove(s, glove) for s in train_tokens])
X_test_glove  = np.array([sentence_vector_glove(s, glove) for s in test_tokens])

print("GloVe shape:", X_train_glove.shape)

# =================================================
# 10. TRAIN SAME CLASSIFIER
# =================================================
# TF-IDF
clf_tfidf = LogisticRegression(max_iter=1000)
clf_tfidf.fit(X_train_tfidf, y_train)

# Word2Vec
clf_w2v = LogisticRegression(max_iter=1000)
clf_w2v.fit(X_train_w2v, y_train)

# GloVe
clf_glove = LogisticRegression(max_iter=1000)
clf_glove.fit(X_train_glove, y_train)

# =================================================
# 11. ACCURACY COMPARISON
# =================================================
acc_tfidf = accuracy_score(y_test, clf_tfidf.predict(X_test_tfidf)) * 100
acc_w2v   = accuracy_score(y_test, clf_w2v.predict(X_test_w2v)) * 100
acc_glove = accuracy_score(y_test, clf_glove.predict(X_test_glove)) * 100

print("\nModel Accuracy Comparison")
print("-------------------------")
print(f"TF-IDF   Accuracy : {acc_tfidf:.2f}%")
print(f"Word2Vec Accuracy : {acc_w2v:.2f}%")
print(f"GloVe    Accuracy : {acc_glove:.2f}%")

# =================================================
# 12. TABLE VIEW
# =================================================
results = pd.DataFrame({
    "Model": ["TF-IDF", "Word2Vec", "GloVe"],
    "Accuracy (%)": [acc_tfidf, acc_w2v, acc_glove]
})

print("\nAccuracy Table:")
print(results)
```

# Multiclass Text Classification using CNN

```
# multi_class_text_cnn.py
# Multi-class Text Classification using CNN (Keras)

import numpy as np
from tensorflow.keras.models import Model
from tensorflow.keras.layers import (
    Input, Embedding, Conv1D, GlobalMaxPooling1D,
    Concatenate, Dense, Dropout
)
from tensorflow.keras.preprocessing.text import Tokenizer
from tensorflow.keras.preprocessing.sequence import pad_sequences
from tensorflow.keras.utils import to_categorical
from sklearn.model_selection import train_test_split

# -----------------------------
# 1. Sample Dataset
# -----------------------------
texts = [
    "The government passed a new law today",
    "The election results were announced",
    "The team won the football match",
    "The player scored a century",
    "New AI technology is emerging",
    "The software update improves performance",
    "Parliament debated the budget",
    "The coach announced the squad",
    "A new smartphone was launched",
    "Cyber security is very important"
]

# Labels: 0-Politics, 1-Sports, 2-Technology
labels = [0, 0, 1, 1, 2, 2, 0, 1, 2, 2]

NUM_CLASSES = 3

# -----------------------------
# 2. Text Preprocessing
# -----------------------------
MAX_LEN = 20
VOCAB_SIZE = 5000
EMBED_DIM = 100

tokenizer = Tokenizer(num_words=VOCAB_SIZE, oov_token="<OOV>")
tokenizer.fit_on_texts(texts)

sequences = tokenizer.texts_to_sequences(texts)
X = pad_sequences(sequences, maxlen=MAX_LEN, padding='post')

y = to_categorical(labels, num_classes=NUM_CLASSES)

X_train, X_test, y_train, y_test = train_test_split(
    X, y, test_size=0.2, random_state=42
)

# -----------------------------
# 3. CNN Model
# -----------------------------
inputs = Input(shape=(MAX_LEN,))

embedding = Embedding(
    input_dim=VOCAB_SIZE,
    output_dim=EMBED_DIM,
    input_length=MAX_LEN
)(inputs)

conv3 = Conv1D(filters=128, kernel_size=3, activation='relu')(embedding)
conv4 = Conv1D(filters=128, kernel_size=4, activation='relu')(embedding)
conv5 = Conv1D(filters=128, kernel_size=5, activation='relu')(embedding)

pool3 = GlobalMaxPooling1D()(conv3)clf_tfidf.fit(X_train_tfidf, y_train)
pool4 = GlobalMaxPooling1D()(conv4)
pool5 = GlobalMaxPooling1D()(conv5)

merged = Concatenate()([pool3, pool4, pool5])

dense = Dense(128, activation='relu')(merged)
dropout = Dropout(0.5)(dense)

outputs = Dense(NUM_CLASSES, activation='softmax')(dropout)

model = Model(inputs, outputs)

# -----------------------------
# 4. Compile Model
# -----------------------------
model.compile(
    optimizer='adam',
    loss='categorical_crossentropy',
    metrics=['accuracy']
)

model.summary()

# -----------------------------
# 5. Train Model
# -----------------------------
model.fit(
    X_train, y_train,
    epochs=10,
    batch_size=2,
    validation_data=(X_test, y_test)
)

# -----------------------------
# 6. Evaluate Model
# -----------------------------
loss, accuracy = model.evaluate(X_test, y_test)
print(f"Test Accuracy: {accuracy * 100:.2f}%")
```

Unit - 2


```
import tensorflow as tf
from tensorflow.keras import layers, models, optimizers
from tensorflow.keras.applications import VGG16
from tensorflow.keras.applications.vgg16 import preprocess_input

# -----------------------------
# PARAMETERS
# -----------------------------
IMG_SIZE = 224
BATCH_SIZE = 32
NUM_CLASSES = 120   # Dog breeds
EPOCHS = 10

# -----------------------------
# DATASET PATHS
# -----------------------------
train_dir = "path_to_train_folder"
test_dir  = "path_to_test_folder"

# -----------------------------
# DATA AUGMENTATION
# -----------------------------
train_datagen = tf.keras.preprocessing.image.ImageDataGenerator(
    preprocessing_function=preprocess_input,
    rotation_range=30,
    zoom_range=0.2,
    width_shift_range=0.2,
    height_shift_range=0.2,
    horizontal_flip=True,
    shear_range=0.2,
    fill_mode="nearest"
)

test_datagen = tf.keras.preprocessing.image.ImageDataGenerator(
    preprocessing_function=preprocess_input
)

train_generator = train_datagen.flow_from_directory(
    train_dir,
    target_size=(IMG_SIZE, IMG_SIZE),
    batch_size=BATCH_SIZE,
    class_mode="categorical"
)

test_generator = test_datagen.flow_from_directory(
    test_dir,
    target_size=(IMG_SIZE, IMG_SIZE),
    batch_size=BATCH_SIZE,
    class_mode="categorical",
    shuffle=False
)

# -----------------------------
# VGG16 BASE MODEL
# -----------------------------
base_model = VGG16(
    weights="imagenet",
    include_top=False,
    input_shape=(IMG_SIZE, IMG_SIZE, 3)
)

base_model.trainable = False   # Feature extraction

# -----------------------------
# CUSTOM CLASSIFIER
# -----------------------------
model = models.Sequential([
    base_model,
    layers.GlobalAveragePooling2D(),
    layers.Dense(512, activation="relu"),
    layers.Dropout(0.5),
    layers.Dense(NUM_CLASSES, activation="softmax")
])

# -----------------------------
# COMPILE
# -----------------------------
model.compile(
    optimizer=optimizers.Adam(learning_rate=1e-3),
    loss="categorical_crossentropy",
    metrics=["accuracy"]
)

# -----------------------------
# TRAIN (Feature Extraction)
# -----------------------------
model.fit(
    train_generator,
    epochs=EPOCHS,
    validation_data=test_generator
)

# -----------------------------
# FINE-TUNING (Unfreeze block5)
# -----------------------------
base_model.trainable = True

for layer in base_model.layers:
    if layer.name.startswith("block5"):
        layer.trainable = True
    else:
        layer.trainable = False

model.compile(
    optimizer=optimizers.Adam(learning_rate=1e-4),
    loss="categorical_crossentropy",
    metrics=["accuracy"]
)

model.fit(
    train_generator,
    epochs=10,
    validation_data=test_generator
)

# -----------------------------
# EVALUATION
# -----------------------------
test_loss, test_acc = model.evaluate(test_generator)
print("Test Accuracy:", test_acc)
```

VGG-16:
```
# ============================================
# VGG16 — DOG BREED CLASSIFICATION
# ============================================

import tensorflow as tf
from tensorflow.keras import layers, models, optimizers
from tensorflow.keras.applications import VGG16
from tensorflow.keras.applications.vgg16 import preprocess_input
from tensorflow.keras.preprocessing.image import ImageDataGenerator

IMG_SIZE = 224
BATCH_SIZE = 32
NUM_CLASSES = 120
EPOCHS = 10

train_dir = "path_to_train_folder"
test_dir  = "path_to_test_folder"

# ---------------------------
# DATA GENERATORS
# ---------------------------
train_datagen = ImageDataGenerator(
    preprocessing_function=preprocess_input,
    rotation_range=30,
    zoom_range=0.2,
    width_shift_range=0.2,
    height_shift_range=0.2,
    horizontal_flip=True
)

test_datagen = ImageDataGenerator(
    preprocessing_function=preprocess_input
)

train_generator = train_datagen.flow_from_directory(
    train_dir,
    target_size=(IMG_SIZE, IMG_SIZE),
    batch_size=BATCH_SIZE,
    class_mode="categorical"
)

test_generator = test_datagen.flow_from_directory(
    test_dir,
    target_size=(IMG_SIZE, IMG_SIZE),
    batch_size=BATCH_SIZE,
    class_mode="categorical"
)

# ---------------------------
# BASE MODEL
# ---------------------------
base_model = VGG16(weights="imagenet", include_top=False,
                   input_shape=(IMG_SIZE, IMG_SIZE, 3))
base_model.trainable = False

# ---------------------------
# CUSTOM HEAD
# ---------------------------
model = models.Sequential([
    base_model,
    layers.GlobalAveragePooling2D(),
    layers.Dense(512, activation="relu"),
    layers.Dropout(0.5),
    layers.Dense(NUM_CLASSES, activation="softmax")
])

model.compile(optimizer=optimizers.Adam(1e-3),
              loss="categorical_crossentropy",
              metrics=["accuracy"])

model.fit(train_generator, epochs=EPOCHS,
          validation_data=test_generator)

# Fine-tune block5
base_model.trainable = True
for layer in base_model.layers:
    layer.trainable = layer.name.startswith("block5")

model.compile(optimizer=optimizers.Adam(1e-4),
              loss="categorical_crossentropy",
              metrics=["accuracy"])

model.fit(train_generator, epochs=10,
          validation_data=test_generator)

model.evaluate(test_generator)
```



## Document Summarisation:

```
# -----------------------------
# 1. Required Libraries
# -----------------------------

import nltk
import numpy as np
from nltk.tokenize import sent_tokenize

from tensorflow.keras.preprocessing.text import Tokenizer
from tensorflow.keras.preprocessing.sequence import pad_sequences
from tensorflow.keras.models import Model
from tensorflow.keras.layers import (
    Input, Embedding, Conv1D,
    GlobalMaxPooling1D, Dense, Concatenate
)

nltk.download('punkt_tab')

# -----------------------------
# 2. Input Document
# -----------------------------

document = """
Artificial Intelligence is transforming healthcare.
It helps doctors diagnose diseases more accurately.
AI systems improve efficiency and reduce costs.
Many hospitals are adopting AI technologies.
"""

# -----------------------------
# 3. Sentence Tokenization
# -----------------------------
# Split document into individual sentences
sentences = sent_tokenize(document)

# Each sentence will be treated as one training instance
print("Sentences:")
for i, s in enumerate(sentences, 1):
    print(f"{i}. {s}")

# -----------------------------
# 4. Text → Integer Encoding
# -----------------------------

# Tokenizer maps each word to a unique integer ID
tokenizer = Tokenizer()
tokenizer.fit_on_texts(sentences)

# Convert sentences into sequences of integers
sequences = tokenizer.texts_to_sequences(sentences)

# Pad all sentences to the same length
# This is required because CNN expects fixed-size input
MAX_LEN = 20
X = pad_sequences(sequences, maxlen=MAX_LEN, padding='post')

# Vocabulary size (+1 for padding index 0)
VOCAB_SIZE = len(tokenizer.word_index) + 1

# -----------------------------
# 5. CNN Model Architecture
# -----------------------------

# Input layer
# Shape = (MAX_LEN,)
# Each input is a sentence represented as word indices
inputs = Input(shape=(MAX_LEN,))

# -----------------------------
# Embedding Layer
# -----------------------------
# Converts word indices → dense vectors
# Output shape = (batch_size, MAX_LEN, EMBED_DIM)

EMBED_DIM = 100

embedding = Embedding(
    input_dim=VOCAB_SIZE,   # size of vocabulary
    output_dim=EMBED_DIM,   # vector size for each word
    input_length=MAX_LEN
)(inputs)

# -----------------------------
# CNN Layers (n-gram feature extraction)
# -----------------------------
# Conv1D slides over word embeddings
# Kernel size decides n-gram length


# 3-gram features
conv3 = Conv1D(
    filters=128,           # number of feature maps
    kernel_size=3,         # trigram
    activation='relu'
)(embedding)               # <-- applied ON embedding output
# Formula : output_length = input_length - kernel_size + 1
#: 20-3+1 = 18
# 4-gram features
conv4 = Conv1D(
    filters=128,
    kernel_size=4,         # four-word phrases
    activation='relu'
)(embedding)

# 5-gram features
conv5 = Conv1D(
    filters=128,
    kernel_size=5,         # five-word phrases
    activation='relu'
)(embedding)

# -----------------------------
# Pooling Layers
# -----------------------------
# GlobalMaxPooling picks the strongest feature
# This makes the model position-invariant

pool3 = GlobalMaxPooling1D()(conv3)
pool4 = GlobalMaxPooling1D()(conv4)
pool5 = GlobalMaxPooling1D()(conv5)

# -----------------------------
# Feature Concatenation
# -----------------------------
# Combine features from different n-gram filters
concat = Concatenate()([pool3, pool4, pool5])

# -----------------------------
# Output Layer
# -----------------------------
# Single neuron → sentence importance score
# Sigmoid gives value between 0 and 1

output = Dense(1, activation='sigmoid')(concat)

# -----------------------------
# Model Creation
# -----------------------------
model = Model(inputs, output)

model.compile(
    optimizer='adam',
    loss='binary_crossentropy',
    metrics=['accuracy']
)

model.summary()

# -----------------------------
# 6. Training (Demo labels)
# -----------------------------
# 1 → important sentence
# 0 → not important sentence

y = np.array([1, 1, 0, 0])

model.fit(X, y, epochs=10, verbose=1)

# -----------------------------
# 7. Sentence Scoring & Ranking
# -----------------------------

# Predict importance score for each sentence
scores = model.predict(X).flatten()

# Pair scores with sentences and sort
ranked_sentences = sorted(
    zip(scores, sentences),
    reverse=True
)

print("\nRanked Sentences:")
for rank, (score, sent) in enumerate(ranked_sentences, 1):
    print(f"{rank}. Score: {score:.4f} | {sent}")

# -----------------------------
# 8. Final Extractive Summary
# -----------------------------

TOP_K = 2
summary = [sent for _, sent in ranked_sentences[:TOP_K]]

print("\n=== CNN Extractive Summary ===")
for s in summary:
    print(s)
```

unit-2
- [ ] 1.TL exp - 3,4(CIFAR10 , Dog Breed)
- [x] 2.all 5 model architecture diagram ✅ 2026-02-23

unit-3
- [x] 1.explain CBow,skip-gram,glove model with code ✅ 2026-02-23
- [x] 2.coding for text document categorization, multi class classification,word embedding ✅ 2026-02-23
- [ ] note complete  
note : there is no full 10 mark question only sub-divisions asked so write the part of code related to the question asked for example a)write the code to freeze the top 8 layers of vgg16 and print the accuracy result. b)write the code to extract the unique words using Tf-Idf

---
