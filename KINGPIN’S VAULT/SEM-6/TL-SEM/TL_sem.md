# questions:

ln-1:
	tl- diagram
	strategies
	methodologies 
	state-of-art models 
	bench mark datasets
	vgg16 architecture [diagram, layers]

ln-2:
	==all architectures with layer names and layer representations==
	own cnn 
	vgg16 
	Inception V3
	resnet
	datasets: cifar10 , dog breed , mnist , own [plant diseases]

ln3:
	cbow vs skipgram explanation
	sentiment using vectorisation
	sentiment analysis using cnn
	multiclass text classification - [concentrate in the model layers using gpt] 
	text summarisation 

ln4:
	EDA
	audio event classification using cnn[vgg16,resnet50,mobilenetv2]

ln5:
	image colourisation code 
	color net theory


## CNN - MNIST [IMAGE CLASSIFICATION]:

### 1] Image classification - using own CNN

```
import numpy as np
import matplotlib.pyplot as plt
import tensorflow as tf


from tensorflow.keras import layers, models
from tensorflow.keras.datasets import mnist
from sklearn.metrics import classification_report, confusion_matrix
import seaborn as sns


# Load dataset
(X_train, y_train), (X_test, y_test) = mnist.load_data()

# Normalize
X_train = X_train / 255.0
X_test = X_test / 255.0

# Reshape (CNN needs 4D input)
X_train = X_train.reshape(-1, 28, 28, 1)
X_test = X_test.reshape(-1, 28, 28, 1)

# Build CNN model
model = models.Sequential([
    layers.Conv2D(32, (3,3), activation='relu', input_shape=(28,28,1)),
    layers.MaxPooling2D(2,2),

    layers.Conv2D(64, (3,3), activation='relu'),
    layers.MaxPooling2D(2,2),
    
    ---------------------- for cifar 10 -----------------------
    layers.Conv2D(128, (3,3), activation='relu'),
    layers.MaxPooling2D(2,2), 
	-----------------------------------------------------------
	
    layers.Flatten(),
    layers.Dense(128, activation='relu'),
    layers.Dropout(0.5), // [for cifar10]
    layers.Dense(10, activation='softmax')
])

# Compile model
model.compile(optimizer='adam',
              loss='sparse_categorical_crossentropy',
              metrics=['accuracy'])

# Train model
model.fit(X_train, y_train, epochs=5, batch_size=64, validation_split=0.1)

# Evaluate
test_loss, test_acc = model.evaluate(X_test, y_test)
print("Test Accuracy:", test_acc)

# Predictions
y_pred_probs = model.predict(X_test)
y_pred = np.argmax(y_pred_probs, axis=1)

# Classification metrics
print("\nClassification Report:\n")
print(classification_report(y_test, y_pred))

# Confusion Matrix
cm = confusion_matrix(y_test, y_pred)

plt.figure(figsize=(8,6))
sns.heatmap(cm, annot=True, fmt='d')
plt.xlabel("Predicted")
plt.ylabel("Actual")
plt.title("Confusion Matrix")
plt.show()
```

### 2] Image classification - using pretrained CNN
- vgg16
- resnet50
- inception v3

```
import numpy as np
import tensorflow as tf
import matplotlib.pyplot as plt


from tensorflow.keras import layers, models
from tensorflow.keras.datasets import mnist
from sklearn.metrics import classification_report, confusion_matrix
import seaborn as sns

from tensorflow.keras.applications import VGG16
from tensorflow.keras.utils import to_categorical

# ------------------ Load Data ------------------
(X_train, y_train), (X_test, y_test) = mnist.load_data()

# Normalize
X_train = X_train / 255.0
X_test = X_test / 255.0

# Convert to RGB (3 channels) // [pretends to be rgb but it is a pure gray scale]
X_train = np.stack([X_train]*3, axis=-1)
X_test = np.stack([X_test]*3, axis=-1)

# Resize to 224x224
X_train = tf.image.resize(X_train, (224,224))
X_test = tf.image.resize(X_test, (224,224))

# One-hot encoding
y_train_cat = to_categorical(y_train, 10)
y_test_cat = to_categorical(y_test, 10)

# ------------------ VGG16 Model ------------------
base_model = VGG16(weights='imagenet', include_top=False, input_shape=(224,224,3))

# Freeze all layers initially
for layer in base_model.layers:
    layer.trainable = False

# Add custom classifier
x = base_model.output
x = layers.Flatten()(x)
x = layers.Dense(128, activation='relu')(x)
x = layers.Dropout(0.5)(x)
output = layers.Dense(10, activation='softmax')(x)

model = models.Model(inputs=base_model.input, outputs=output)

# Compile
model.compile(optimizer='adam',
              loss='categorical_crossentropy',
              metrics=['accuracy'])

# Train (Feature Extraction)
model.fit(X_train, y_train_cat, epochs=3, batch_size=64, validation_split=0.1)

# ------------------ Fine Tuning ------------------

# Unfreeze layers starting with 'block5'
for layer in base_model.layers:
    if layer.name.startswith('block5'):
        layer.trainable = True

# Recompile with low learning rate
model.compile(optimizer=tf.keras.optimizers.Adam(1e-5),
              loss='categorical_crossentropy',
              metrics=['accuracy'])

# Train again
model.fit(X_train, y_train_cat, epochs=2, batch_size=64)

# ------------------ Evaluation ------------------

# Accuracy
loss, acc = model.evaluate(X_test, y_test_cat)
print("Test Accuracy:", acc)

# Predictions
y_pred_probs = model.predict(X_test)
y_pred = np.argmax(y_pred_probs, axis=1)

# Precision, Recall, F1-score
print("\nClassification Report:\n")
print(classification_report(y_test, y_pred))

# Confusion Matrix
cm = confusion_matrix(y_test, y_pred)

plt.figure(figsize=(8,6))
sns.heatmap(cm, annot=True, fmt='d')
plt.xlabel("Predicted")
plt.ylabel("Actual")
plt.title("Confusion Matrix")
plt.show()

```

```
import numpy as np
import tensorflow as tf
import matplotlib.pyplot as plt

from tensorflow.keras import layers, models
from tensorflow.keras.datasets import cifar10
from sklearn.metrics import classification_report, confusion_matrix
import seaborn as sns

from tensorflow.keras.applications import InceptionV3
from tensorflow.keras.utils import to_categorical

# ------------------ Load Data ------------------
(X_train, y_train), (X_test, y_test) = cifar10.load_data()

# Normalize
X_train = X_train / 255.0
X_test = X_test / 255.0

# Resize to 299x299
X_train = tf.image.resize(X_train, (299,299))
X_test = tf.image.resize(X_test, (299,299))

y_train = y_train.flatten() // changes for cifar10
y_test = y_test.flatten() //

# One-hot encoding
y_train_cat = to_categorical(y_train, 10)
y_test_cat = to_categorical(y_test, 10)

# ------------------ Load InceptionV3 ------------------
base_model = InceptionV3(weights='imagenet',
                         include_top=False,
                         input_shape=(299,299,3))

# Freeze all layers
for layer in base_model.layers:
    layer.trainable = False

# ------------------ Custom Model ------------------
x = base_model.output
x = layers.GlobalAveragePooling2D()(x)
x = layers.BatchNormalization()(x)
x = layers.Dense(256, activation='relu')(x)
x = layers.Dropout(0.5)(x)
output = layers.Dense(10, activation='softmax')(x)

model = models.Model(inputs=base_model.input, outputs=output)

# Compile
model.compile(optimizer='adam',
              loss='categorical_crossentropy',
              metrics=['accuracy'])

# Train (Feature Extraction)
model.fit(X_train, y_train_cat, epochs=5, batch_size=64, validation_split=0.1)

# ------------------ Fine Tuning using startswith ------------------

for layer in base_model.layers:
    if (layer.name.startswith('mixed7') or
        layer.name.startswith('mixed8') or
        layer.name.startswith('mixed9') or
        layer.name.startsw22qith('mixed10')):
        layer.trainable = True

# Recompile with low LR
model.compile(optimizer=tf.keras.optimizers.Adam(1e-5),
              loss='categorical_crossentropy',
              metrics=['accuracy'])

# Train again
model.fit(X_train, y_train_cat, epochs=3, batch_size=64)

# ------------------ Evaluation ------------------

# Accuracy
loss, acc = model.evaluate(X_test, y_test_cat)
print("Test Accuracy:", acc)

# Predictions
y_pred_probs = model.predict(X_test)
y_pred = np.argmax(y_pred_probs, axis=1)

# Flatten labels
y_test_flat = y_test.flatten()

# Precision, Recall, F1-score
print("\nClassification Report:\n")
print(classification_report(y_test_flat, y_pred))

# Confusion Matrix
cm = confusion_matrix(y_test_flat, y_pred)

plt.figure(figsize=(8,6))
sns.heatmap(cm, annot=True, fmt='d')
plt.xlabel("Predicted")
plt.ylabel("Actual")
plt.title("Confusion Matrix")
plt.show()
```

```
import numpy as np
import tensorflow as tf
import matplotlib.pyplot as plt

from tensorflow.keras import layers, models
from sklearn.metrics import classification_report, confusion_matrix
import seaborn as sns

from tensorflow.keras.applications import ResNet50
from tensorflow.keras.preprocessing.image import ImageDataGenerator

# ------------------ Data Augmentation ------------------

train_datagen = ImageDataGenerator(
    rescale=1./255,
    rotation_range=20,
    zoom_range=0.2,
    width_shift_range=0.1,
    height_shift_range=0.1,
    shear_range=0.1,
    horizontal_flip=True,
    validation_split=0.1
)

val_datagen = ImageDataGenerator(rescale=1./255, validation_split=0.1)

# Load training data
train_data = train_datagen.flow_from_directory(
    'dataset/train',
    target_size=(224,224),
    batch_size=32,
    class_mode='categorical',
    subset='training'
)

# Load validation data
val_data = val_datagen.flow_from_directory(
    'dataset/train',
    target_size=(224,224),
    batch_size=32,
    class_mode='categorical',
    subset='validation'
)

# ------------------ Load ResNet50 ------------------

base_model = ResNet50(weights='imagenet',
                      include_top=False,
                      input_shape=(224,224,3))

# Freeze all layers
for layer in base_model.layers:
    layer.trainable = False

# ------------------ Custom Model ------------------

x = base_model.output
x = layers.GlobalAveragePooling2D()(x)
x = layers.BatchNormalization()(x)
x = layers.Dense(256, activation='relu')(x)
x = layers.Dropout(0.5)(x)
output = layers.Dense(train_data.num_classes, activation='softmax')(x)

model = models.Model(inputs=base_model.input, outputs=output)

# Compile
model.compile(optimizer='adam',
              loss='categorical_crossentropy',
              metrics=['accuracy'])

# Train (Feature Extraction)
model.fit(train_data, epochs=5, validation_data=val_data)

# ------------------ Fine Tuning ------------------

for layer in base_model.layers:
    if (layer.name.startswith('conv4') or
        layer.name.startswith('conv5')):
        layer.trainable = True

# Recompile
model.compile(optimizer=tf.keras.optimizers.Adam(1e-5),
              loss='categorical_crossentropy',
              metrics=['accuracy'])

# Train again
model.fit(train_data, epochs=3, validation_data=val_data)

# ------------------ Evaluation ------------------

val_data.reset()
y_pred_probs = model.predict(val_data)
y_pred = np.argmax(y_pred_probs, axis=1)
y_true = val_data.classes

# Accuracy
loss, acc = model.evaluate(val_data)
print("Validation Accuracy:", acc)

# Precision, Recall, F1-score
print("\nClassification Report:\n")
print(classification_report(y_true, y_pred))

# Confusion Matrix
cm = confusion_matrix(y_true, y_pred)

plt.figure(figsize=(8,6))
sns.heatmap(cm, annot=True, fmt='d')
plt.xlabel("Predicted")
plt.ylabel("Actual")
plt.title("Confusion Matrix")
plt.show()
```


### 3. Sentiment analysis

```
 ============================================================
# TEXT REPRESENTATION METHODS FOR SENTIMENT CLASSIFICATION
# BoW, CountVectorizer, TF-IDF, Word2Vec, Skip-Gram, GloVe
# Dataset: IMDB (Kaggle)
# ============================================================

# Install libraries
!pip install kaggle gensim scikit-learn nltk -q
import kagglehub
import numpy as np
import pandas as pd

from sklearn.feature_extraction.text import CountVectorizer
from sklearn.feature_extraction.text import TfidfVectorizer
from sklearn.linear_model import LogisticRegression
from sklearn.model_selection import train_test_split
from sklearn.metrics import accuracy_score

from gensim.models import Word2Vec

import nltk
nltk.download('punkt')
nltk.download('punkt_tab') # Download missing resource

from nltk.tokenize import word_tokenize

# ============================================================
# DOWNLOAD DATASET FROM KAGGLE
# ============================================================

data_path = kagglehub.dataset_download("lakshmi25npathi/imdb-dataset-of-50k-movie-reviews")

print("Dataset Path:",data_path)

# Load the dataset into a pandas DataFrame
data = pd.read_csv(f"{data_path}/IMDB Dataset.csv")

# Convert sentiment labels
data['sentiment'] = data['sentiment'].map({
    'positive':1,
    'negative':0
})

# Reduce size for faster training
data = data.sample(30000, random_state=42)

X = data['review']
y = data['sentiment']

X_train, X_test, y_train, y_test = train_test_split(
    X, y, test_size=0.2, random_state=42
)

# ============================================================
# 1 BAG OF WORDS / COUNT VECTORIZER
# ============================================================

print("\n===== CountVectorizer ====")

cv = CountVectorizer(max_features=5000)

X_train_cv = cv.fit_transform(X_train)
X_test_cv = cv.transform(X_test)

model_cv = LogisticRegression(max_iter=1000)

model_cv.fit(X_train_cv, y_train)

pred_cv = model_cv.predict(X_test_cv)

print("Accuracy:", accuracy_score(y_test, pred_cv))


# ============================================================
# 2 TF-IDF
# ============================================================

print("\n===== TF-IDF ====")

tfidf = TfidfVectorizer(max_features=5000)

X_train_tfidf = tfidf.fit_transform(X_train)
X_test_tfidf = tfidf.transform(X_test)

model_tfidf = LogisticRegression(max_iter=1000)

model_tfidf.fit(X_train_tfidf, y_train)

pred_tfidf = model_tfidf.predict(X_test_tfidf)

print("Accuracy:", accuracy_score(y_test, pred_tfidf))


# ============================================================
# TOKENIZE TEXT
# ============================================================

tokenized = [word_tokenize(text.lower()) for text in X_train]


# ============================================================
# 3 WORD2VEC (CBOW)
# ============================================================

print("\n===== Word2Vec (CBOW) ====")

w2v_model = Word2Vec(
    sentences=tokenized,
    vector_size=100,
    window=5,
    min_count=2,
    sg=0
)

def get_vector(text):

    words = word_tokenize(text.lower())
    vectors = []

    for word in words:
        if word in w2v_model.wv:
            vectors.append(w2v_model.wv[word])

    if len(vectors) == 0:
        return np.zeros(100)

    return np.mean(vectors, axis=0)

X_train_w2v = np.array([get_vector(t) for t in X_train])
X_test_w2v = np.array([get_vector(t) for t in X_test])

model_w2v = LogisticRegression(max_iter=1000)

model_w2v.fit(X_train_w2v, y_train)

pred_w2v = model_w2v.predict(X_test_w2v)

print("Accuracy:", accuracy_score(y_test, pred_w2v))


# ============================================================
# 4 SKIP-GRAM WORD2VEC
# ============================================================

print("\n===== Word2Vec Skip-Gram ====")

skip_model = Word2Vec(
    sentences=tokenized,
    vector_size=100,
    window=5,
    min_count=2,
    sg=1
)

def get_skip_vector(text):

    words = word_tokenize(text.lower())
    vectors = []

    for word in words:
        if word in skip_model.wv:
            vectors.append(skip_model.wv[word])

    if len(vectors) == 0:
        return np.zeros(100)

    return np.mean(vectors, axis=0)

X_train_skip = np.array([get_skip_vector(t) for t in X_train])
X_test_skip = np.array([get_skip_vector(t) for t in X_test])

model_skip = LogisticRegression(max_iter=1000)

model_skip.fit(X_train_skip, y_train)

pred_skip = model_skip.predict(X_test_skip)

print("Accuracy:", accuracy_score(y_test, pred_skip))


# ============================================================
# 5 GLOVE EMBEDDINGS
# ============================================================

print("\n===== GloVe ====")

!wget http://nlp.stanford.edu/data/glove.6B.zip
!unzip glove.6B.zip

embedding_index = {}

with open("glove.6B.100d.txt", encoding="utf8") as f:

    for line in f:

        values = line.split()
        word = values[0]
        vector = np.asarray(values[1:], dtype='float32')
        embedding_index[word] = vector


def glove_vector(text):

    words = word_tokenize(text.lower())
    vectors = []

    for word in words:
        if word in embedding_index:
            vectors.append(embedding_index[word])

    if len(vectors) == 0:
        return np.zeros(100)

    return np.mean(vectors, axis=0)

X_train_glove = np.array([glove_vector(t) for t in X_train])
X_test_glove = np.array([glove_vector(t) for t in X_test])

model_glove = LogisticRegression(max_iter=1000)

model_glove.fit(X_train_glove, y_train)

pred_glove = model_glove.predict(X_test_glove)

print("Accuracy:", accuracy_score(y_test, pred_glove))
```

### 4. Sentiment analysis using cnn

```
pip install kagglehub tensorflow nltk -q

import numpy as np
import pandas as pd
import kagglehub

from sklearn.model_selection import train_test_split

from tensorflow.keras.preprocessing.text import Tokenizer
from tensorflow.keras.preprocessing.sequence import pad_sequences
from tensorflow.keras.models import Sequential
from tensorflow.keras.layers import Embedding, Conv1D
from tensorflow.keras.layers import GlobalMaxPooling1D, Dense

import nltk
nltk.download('punkt')
from nltk.tokenize import word_tokenize

# ============================================================
# DOWNLOAD IMDB DATASET
# ============================================================

path = kagglehub.dataset_download(
    "lakshmi25npathi/imdb-dataset-of-50k-movie-reviews"
)

data = pd.read_csv(path + "/IMDB Dataset.csv")

# Convert labels
data['sentiment'] = data['sentiment'].map({
    'positive':1,
    'negative':0
})

# Reduce dataset size (faster training)
data = data.sample(20000)

X = data['review']
y = data['sentiment']

# ============================================================
# TEXT TOKENIZATION
# ============================================================

max_words = 10000
max_len = 200

tokenizer = Tokenizer(num_words=max_words)

tokenizer.fit_on_texts(X)

sequences = tokenizer.texts_to_sequences(X)

X_pad = pad_sequences(sequences, maxlen=max_len)

X_train, X_test, y_train, y_test = train_test_split(
    X_pad, y, test_size=0.2, random_state=42
)

# ============================================================
# DOWNLOAD GLOVE EMBEDDINGS
# ============================================================

!wget http://nlp.stanford.edu/data/glove.6B.zip
!unzip glove.6B.zip

embedding_index = {}

with open("glove.6B.100d.txt", encoding="utf8") as f:

    for line in f:

        values = line.split()
        word = values[0]
        vector = np.asarray(values[1:], dtype='float32')

        embedding_index[word] = vector

print("Loaded word vectors:", len(embedding_index))

# ============================================================
# CREATE EMBEDDING MATRIX
# ============================================================

embedding_dim = 100
word_index = tokenizer.word_index

embedding_matrix = np.zeros((max_words, embedding_dim))

for word, i in word_index.items():

    if i < max_words:

        vector = embedding_index.get(word)

        if vector is not None:

            embedding_matrix[i] = vector

# ============================================================
# CNN MODEL WITH TRANSFER LEARNING
# ============================================================

model = Sequential()

model.add(Embedding(
    input_dim=max_words,
    output_dim=embedding_dim,
    weights=[embedding_matrix],
    input_length=max_len,
    trainable=False
))

model.add(Conv1D(128, 5, activation='relu'))

model.add(GlobalMaxPooling1D())

model.add(Dense(64, activation='relu'))

model.add(Dense(1, activation='sigmoid'))

model.compile(
    optimizer='adam',
    loss='binary_crossentropy',
    metrics=['accuracy']
)

model.summary()

# ============================================================
# TRAIN MODEL
# ============================================================

model.fit(
    X_train,
    y_train,
    epochs=5,
    batch_size=32,
    validation_split=0.2
)

# ============================================================
# EVALUATE MODEL
# ============================================================

loss, accuracy = model.evaluate(X_test, y_test)

print("Test Accuracy:", accuracy)
```

### 5. Text Summarisation using cnn

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
Machine Learning is changing many industries.
It allows computers to learn from data.
ML models help businesses make better decisions.
Many companies use machine learning for predictions.
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

### 6. multiclass text classification

```
import numpy as np
from sklearn.datasets import fetch_20newsgroups
from sklearn.model_selection import train_test_split
from tensorflow.keras.preprocessing.text import Tokenizer
from tensorflow.keras.preprocessing.sequence import pad_sequences
from tensorflow.keras.models import Sequential
from tensorflow.keras.layers import Embedding, Conv1D, GlobalMaxPooling1D, Dense, Dropout
from tensorflow.keras.utils import to_categorical

# 1. Load Dataset
data = fetch_20newsgroups(subset='all')
X = data.data
y = data.target

num_classes = len(set(y))

# 2. Split Data
X_train, X_test, y_train, y_test = train_test_split(X, y, test_size=0.2)

# 3. Tokenization
vocab_size = 10000
max_len = 200

tokenizer = Tokenizer(num_words=vocab_size)
tokenizer.fit_on_texts(X_train)

X_train_seq = tokenizer.texts_to_sequences(X_train)
X_test_seq = tokenizer.texts_to_sequences(X_test)

# 4. Padding
X_train_pad = pad_sequences(X_train_seq, maxlen=max_len)
X_test_pad = pad_sequences(X_test_seq, maxlen=max_len)

# 5. One-hot Encoding
y_train_cat = to_categorical(y_train, num_classes)
y_test_cat = to_categorical(y_test, num_classes)

# 6. Build CNN Model
model = Sequential()
model.add(Embedding(vocab_size, 128, input_length=max_len))
model.add(Conv1D(128, 5, activation='relu'))
model.add(GlobalMaxPooling1D())
model.add(Dense(64, activation='relu'))
model.add(Dropout(0.5))
model.add(Dense(num_classes, activation='softmax'))

# 7. Compile
model.compile(loss='categorical_crossentropy',
              optimizer='adam',
              metrics=['accuracy'])

# 8. Train
model.fit(X_train_pad, y_train_cat,
          epochs=5,
          batch_size=64,
          validation_split=0.1)

# 9. Evaluate
loss, acc = model.evaluate(X_test_pad, y_test_cat)
print("Test Accuracy:", acc)

# 🔟 Prediction Function
def predict_category(text):
    seq = tokenizer.texts_to_sequences([text])
    pad = pad_sequences(seq, maxlen=max_len)
   
    pred = model.predict(pad)
    class_index = np.argmax(pred)
   
    print("\nInput Sentence:", text)
    print("Predicted Category:", data.target_names[class_index])

# 🔥 Test with your own sentences
predict_category("The government is planning new policies for the economy")
predict_category("The car engine performance is very fast and efficient")
predict_category("The team played a great match and won the tournament")
```

### 7. Sound Classification

```
===============================
# 1. Import Libraries
# ===============================
import numpy as np
import pandas as pd
import tensorflow as tf

from tensorflow.keras import layers, models
from sklearn.model_selection import train_test_split

import librosa
import os

# ===============================
# 2. Dataset Path (YOUR CORRECT PATH)
# ===============================
DATASET_PATH = r"C:\Users\kaviy\Downloads\kaviyadataset\urbansound8k"

csv_path = os.path.join(DATASET_PATH, "metadata", "UrbanSound8K.csv")
audio_path = os.path.join(DATASET_PATH, "audio")

# Check path
print("CSV Exists:", os.path.exists(csv_path))

# Load metadata
metadata = pd.read_csv(csv_path)

# Create full audio path
metadata['path'] = metadata.apply(
    lambda row: os.path.join(audio_path, f"fold{row['fold']}", row['slice_file_name']),
    axis=1
)

print("Metadata Loaded ✅")

# ===============================
# 3. Feature Extraction (Mel Spectrogram)
# ===============================
def extract_features(file_path, max_len=128):
    try:
        audio, sr = librosa.load(file_path, sr=22050)

        mel = librosa.feature.melspectrogram(y=audio, sr=sr, n_mels=128)
        mel_db = librosa.power_to_db(mel, ref=np.max)

        # Fix size
        if mel_db.shape[1] < max_len:
            pad = max_len - mel_db.shape[1]
            mel_db = np.pad(mel_db, ((0,0),(0,pad)), mode='constant')
        else:
            mel_db = mel_db[:, :max_len]

        return mel_db

    except:
        return None


# ===============================
# 4. Prepare Dataset
# ===============================
X, y = [], []

for i, row in metadata.iterrows():
    feature = extract_features(row['path'])

    if feature is not None:
        X.append(feature)
        y.append(row['classID'])

X = np.array(X)
y = np.array(y)

print("Dataset Shape:", X.shape)

# Add channel dimension
X = X[..., np.newaxis]

# Convert to 3 channels (for CNN)
X = np.repeat(X, 3, axis=-1)

# ===============================
# 5. Train-Test Split
# ===============================
X_train, X_test, y_train, y_test = train_test_split(
    X, y,
    test_size=0.2,
    stratify=y,
    random_state=42
)

# ===============================
# 6. Transfer Learning Model
# ===============================
base_model = tf.keras.applications.MobileNetV2(
    weights='imagenet',
    include_top=False,
    input_shape=(128,128,3)
)

base_model.trainable = False

model = models.Sequential([
    base_model,
    layers.GlobalAveragePooling2D(),
    layers.Dense(128, activation='relu'),
    layers.Dropout(0.3),
    layers.Dense(10, activation='softmax')
])

model.compile(
    optimizer='adam',
    loss='sparse_categorical_crossentropy',
    metrics=['accuracy']
)

model.summary()

# ===============================
# 7. Training
# ===============================
history = model.fit(
    X_train,
    y_train,
    validation_data=(X_test, y_test),
    epochs=5,
    batch_size=16
)

# ===============================
# 8. Evaluation
# ===============================
loss, acc = model.evaluate(X_test, y_test)
print("Test Accuracy:", acc)

# ===============================
# 9. Prediction
# ===============================
pred = model.predict(X_test[:1])
print("Predicted Class:", np.argmax(pred))

```

### 8. Image colourisation

```
# ========================================================= 
# Image Colorization using Transfer Learning (ResNet50) 
# ========================================================= 
import tensorflow as tf 
import numpy as np 
import matplotlib.pyplot as plt

from tensorflow.keras import layers, Model 
from sklearn.model_selection import train_test_split 
from tensorflow.keras.applications import ResNet50 

import cv2 
import os 
from skimage.color import rgb2lab, lab2rgb 

# ========================================================= 
# PARAMETERS 
# ========================================================= 
IMG_SIZE = 224 
BATCH_SIZE = 8 
EPOCHS = 20 
DATASET_PATH = "dataset/"   # folder containing RGB images 
# ========================================================= 
# PREPROCESSING FUNCTION 
# ========================================================= 
def preprocess_image(img): 
img = cv2.resize(img, (IMG_SIZE, IMG_SIZE)) 
img = img.astype("float32") / 255 
 
    lab = rgb2lab(img) 
 
    L = lab[:,:,0] / 100 
    ab = lab[:,:,1:] / 128 
 
    L = np.expand_dims(L, axis=-1) 
 
    return L, ab 
 
 
# ========================================================= 
# LOAD DATASET 
# ========================================================= 
 
X_L = [] 
Y_ab = [] 
 
for file in os.listdir(DATASET_PATH): 
 
    path = os.path.join(DATASET_PATH, file) 
 
    img = cv2.imread(path) 
 
    if img is None: 
        continue 
 
    img = cv2.cvtColor(img, cv2.COLOR_BGR2RGB) 
 
    L, ab = preprocess_image(img) 
 
    X_L.append(L) 
    Y_ab.append(ab) 
 
X_L = np.array(X_L) 
Y_ab = np.array(Y_ab) 
 
print("Dataset shape:", X_L.shape) 
 
# ========================================================= 
# TRAIN TEST SPLIT 
# ========================================================= 
 
X_train, X_test, y_train, y_test = train_test_split( 
    X_L, 
    Y_ab, 
    test_size=0.2, 
    random_state=42 
) 
 
# ========================================================= 
# BUILD ENCODER (TRANSFER LEARNING) 
# ========================================================= 
 
def build_encoder(): 
 
    base_model = ResNet50( 
        weights="imagenet", 
        include_top=False, 
        input_shape=(IMG_SIZE, IMG_SIZE, 3) 
    ) 
 
    for layer in base_model.layers: 
        layer.trainable = False 
 
    return base_model 
 
 
# ========================================================= 
# BUILD COLORIZATION MODEL 
# ========================================================= 
 
def build_model(): 
 
    encoder = build_encoder() 
 
    input_l = layers.Input(shape=(IMG_SIZE, IMG_SIZE, 1)) 
 
    # convert grayscale to 3 channels 
    x = layers.Concatenate()([input_l, input_l, input_l]) 
 
    features = encoder(x) 
 
    # Decoder 
    x = layers.Conv2D(256, 3, padding='same', activation='relu')(features) 
    x = layers.UpSampling2D((2,2))(x) 
 
    x = layers.Conv2D(128, 3, padding='same', activation='relu')(x) 
    x = layers.UpSampling2D((2,2))(x) 
 
    x = layers.Conv2D(64, 3, padding='same', activation='relu')(x) 
    x = layers.UpSampling2D((2,2))(x) 
 
    x = layers.Conv2D(32, 3, padding='same', activation='relu')(x) 
    x = layers.UpSampling2D((2,2))(x) 
 
    x = layers.Conv2D(16, 3, padding='same', activation='relu')(x) 
 
    output_ab = layers.Conv2D(2, 1, activation='tanh')(x) 
 
    model = Model(inputs=input_l, outputs=output_ab) 
 
    return model 
 
 
# ========================================================= 
# CREATE MODEL 
# ========================================================= 
 
model = build_model() 
 
model.compile( 
    optimizer='adam', 
    loss='mse' 
) 
 
model.summary() 
 
# ========================================================= 
# TRAIN MODEL 
# ========================================================= 
 
history = model.fit( 
    X_train, 
    y_train, 
    validation_data=(X_test, y_test), 
    epochs=EPOCHS, 
    batch_size=BATCH_SIZE 
) 
 
# ========================================================= 
# POST PROCESSING 
# ========================================================= 
 
def reconstruct_image(L, ab): 
 
    L = L * 100 
    ab = ab * 128 
 
    lab = np.concatenate([L, ab], axis=-1) 
 
    rgb = lab2rgb(lab) 
 
    return rgb 
 
 
# ========================================================= 
# TEST MODEL 
# ========================================================= 
 
pred_ab = model.predict(X_test) 
 
index = 0 
 
pred_img = reconstruct_image(X_test[index], pred_ab[index]) 
plt.figure(figsize=(8,4)) 
plt.subplot(1,2,1) 
plt.title("Grayscale") 
plt.imshow(X_test[index].squeeze(), cmap='gray') 
plt.axis("off") 
plt.subplot(1,2,2) 
plt.title("Colorized") 
plt.imshow(pred_img) 
plt.axis("off") 
plt.show() 
# ========================================================= 
# SAVE MODEL 
# ========================================================= 
model.save("colorization_resnet_model.h5") 
print("Model saved successfully!") 
```


### Example sentence - Understanding 3

"I love machine learning and I love AI"

---

## 1. Bag of Words (BoW)

👉 Count how many times each word appears

|Word|Count|
|---|---|
|I|2|
|love|2|
|machine|1|
|learning|1|
|and|1|
|AI|1|

➡️ Vector:

[2, 2, 1, 1, 1, 1]

---

## 2. CountVectorizer

👉 Same as BoW but automated

CountVectorizer().fit_transform(text)

➡️ Output:

[[2, 2, 1, 1, 1, 1]]

✔ Just a tool for BoW

---

## 3. TF-IDF

👉 Reduces importance of common words like "I"

|Word|TF-IDF (approx)|
|---|---|
|I|0.3|
|love|0.6|
|machine|0.7|
|learning|0.7|
|AI|0.8|

➡️ Important words get higher values

---

## 4. Word2Vec

👉 Converts each word into a vector

love → [0.2, 0.8, -0.1]  
AI   → [0.3, 0.7, -0.2]

✔ "AI" and "machine" will have similar vectors  
✔ Captures meaning

---

## 5. Skip-Gram

👉 Learns context relationships

Example:

Input word: "love"  
Output: ["I", "machine", "AI"]

✔ Learns:

- "love" appears near these words  
    ✔ Better for rare words

---

## 6. GloVe

👉 Pre-trained Word2Vec-like vectors

king → [0.5, 0.1, 0.9]  
queen → [0.52, 0.12, 0.88]

✔ Already trained on huge data  
✔ No need to train yourself

---

## Final intuition

|Method|What it captures|
|---|---|
|BoW|Count only|
|TF-IDF|Importance|
|Word2Vec|Meaning|
|Skip-Gram|Context|
|GloVe|Pre-trained meaning|

---

### One-line answer (exam-ready)

**All these methods convert text into numerical vectors, but differ in whether they capture frequency (BoW), importance (TF-IDF), or semantic meaning (Word2Vec, GloVe).**



 ### Image colorisation full code:

```
import tensorflow as tf
import numpy as np
import matplotlib.pyplot as plt

from tensorflow.keras import layers, Model
from sklearn.model_selection import train_test_split

import cv2
import os
from tensorflow.keras.applications import VGG16
from tensorflow.keras.applications.vgg16 import preprocess_input
from skimage.color import rgb2lab, lab2rgb

# =========================
# PARAMETERS
# =========================
IMG_SIZE = 224
DATASET_PATH = "dataset/"   # put few images here

# =========================
# PREPROCESSING
# =========================
def preprocess_image(img):

    img = cv2.resize(img, (IMG_SIZE, IMG_SIZE))
    img = img.astype("float32") / 255

    lab = rgb2lab(img)

    L = lab[:,:,0] / 100
    ab = lab[:,:,1:] / 128

    L = np.expand_dims(L, axis=-1)

    return L, ab

# =========================
# LOAD DATA
# =========================
X_L, Y_ab = [], []

for file in os.listdir(DATASET_PATH):
    path = os.path.join(DATASET_PATH, file)

    img = cv2.imread(path)
    if img is None:
        continue

    img = cv2.cvtColor(img, cv2.COLOR_BGR2RGB)

    L, ab = preprocess_image(img)

    X_L.append(L)
    Y_ab.append(ab)

X_L = np.array(X_L)
Y_ab = np.array(Y_ab)

# split
X_train, X_test, y_train, y_test = train_test_split(
    X_L, Y_ab, test_size=0.2, random_state=42
)

# =========================
# ENCODER
# =========================
def build_encoder(input_l):

    x = layers.Conv2D(64, 3, activation='relu', padding='same', strides=2)(input_l)
    x = layers.Conv2D(128, 3, activation='relu', padding='same')(x)
    x = layers.Conv2D(128, 3, activation='relu', padding='same', strides=2)(x)
    x = layers.Conv2D(256, 3, activation='relu', padding='same')(x)
    x = layers.Conv2D(256, 3, activation='relu', padding='same', strides=2)(x)
    x = layers.Conv2D(512, 3, activation='relu', padding='same')(x)
    x = layers.Conv2D(512, 3, activation='relu', padding='same')(x)
    x = layers.Conv2D(256, 3, activation='relu', padding='same')(x)

    return x

# =========================
# VGG FEATURE EXTRACTOR
# =========================
def build_vgg():

    vgg = VGG16(weights='imagenet', include_top=True)
    model = models.Model(inputs=vgg.input, outputs=vgg.output)

    model.trainable = False
    return model

# =========================
# COLORNET MODEL
# =========================
def build_colornet():

    input_l = layers.Input(shape=(IMG_SIZE, IMG_SIZE, 1))

    # -------- Encoder --------
    enc_output = build_encoder(input_l)

    # -------- VGG branch --------
    x = layers.Concatenate()([input_l, input_l, input_l])
    x = layers.Resizing(224, 224)(x)
    x = preprocess_input(x)

    vgg = build_vgg()
    embed = vgg(x)   # (batch, 1000)

    # -------- Fusion --------
    fusion = layers.RepeatVector(32*32)(embed)
    fusion = layers.Reshape((32, 32, 1000))(fusion)

    fusion = layers.Concatenate(axis=-1)([enc_output, fusion])

    fusion = layers.Conv2D(256, (1,1), activation='relu', padding='same')(fusion)

    # -------- Decoder --------
    x = layers.Conv2D(128, 3, activation='relu', padding='same')(fusion)
    x = layers.UpSampling2D((2,2))(x)

    x = layers.Conv2D(64, 3, activation='relu', padding='same')(x)
    x = layers.UpSampling2D((2,2))(x)

    x = layers.Conv2D(32, 3, activation='relu', padding='same')(x)
    x = layers.UpSampling2D((2,2))(x)

    x = layers.Conv2D(16, 3, activation='relu', padding='same')(x)

    output = layers.Conv2D(2, 3, activation='tanh', padding='same')(x)

    model = Model(inputs=input_l, outputs=output)

    return model

# =========================
# BUILD + COMPILE
# =========================
model = build_colornet()

model.compile(
    optimizer='adam',
    loss='mse'
)

model.summary()

# =========================
# TRAIN
# =========================
model.fit(
    X_train,
    y_train,
    validation_data=(X_test, y_test),
    epochs=5,
    batch_size=4
)

# =========================
# POSTPROCESS
# =========================
def reconstruct_image(L, ab):

    L = L * 100
    ab = ab * 128

    lab = np.concatenate([L, ab], axis=-1)

    rgb = lab2rgb(lab)

    return rgb

# =========================
# TEST
# =========================
pred_ab = model.predict(X_test)

idx = 0

pred_img = reconstruct_image(X_test[idx], pred_ab[idx])

plt.subplot(1,2,1)
plt.title("Grayscale")
plt.imshow(X_test[idx].squeeze(), cmap='gray')
plt.axis("off")

plt.subplot(1,2,2)
plt.title("Colorized")
plt.imshow(pred_img)
plt.axis("off")

plt.show()
```


### ==Cifar10 changes:==

- dataset loading
- remove np.stack ---> 3 channel input 
- use flatten for y_train and y_test
- custom classifier change

### audio exploratory:

```
import matplotlib.pyplot as plt
import numpy as np

import librosa
import librosa.display


// for drive uploads
from google.colab import drive
drive.mount('/content/drive')
file_path = "/content/drive/mydrive/..."
# -------------------------
# LOAD AUDIO
# -------------------------
audio, sr = librosa.load("sample.wav", sr=22050)

# -------------------------
# 1. WAVEFORM
# -------------------------
plt.figure(figsize=(10,4))
librosa.display.waveshow(audio, sr=sr)
plt.title("Waveform (Amplitude vs Time)")
plt.xlabel("Time")
plt.ylabel("Amplitude")
plt.show()

# -------------------------
# 2. SPECTROGRAM (STFT)
# -------------------------
stft = librosa.stft(audio)
spec_db = librosa.amplitude_to_db(abs(stft))

plt.figure(figsize=(10,4))
librosa.display.specshow(spec_db, sr=sr, x_axis='time', y_axis='hz')
plt.title("Spectrogram (Frequency vs Time)")
plt.colorbar()
plt.show()

# -------------------------
# 3. MEL SPECTROGRAM
# -------------------------
mel = librosa.feature.melspectrogram(y=audio, sr=sr, n_mels=128)
mel_db = librosa.power_to_db(mel)

plt.figure(figsize=(10,4))
librosa.display.specshow(mel_db, sr=sr, x_axis='time', y_axis='mel')
plt.title("Mel Spectrogram")
plt.colorbar()
plt.show()

# -------------------------
# 4. CHROMAGRAM (PITCH)
# -------------------------
chroma = librosa.feature.chroma_stft(y=audio, sr=sr)

plt.figure(figsize=(10,4))
librosa.display.specshow(chroma, x_axis='time', y_axis='chroma')
plt.title("Chromagram (Pitch Classes)")
plt.colorbar()
plt.show()

# -------------------------
# 5. MFCC (IMPORTANT FEATURE)
# -------------------------
mfcc = librosa.feature.mfcc(y=audio, sr=sr, n_mfcc=13)

plt.figure(figsize=(10,4))
librosa.display.specshow(mfcc, x_axis='time')
plt.title("MFCC (Mel Frequency Cepstral Coefficients)")
plt.colorbar()
plt.show()
```


## CBOW VS SKIP GRAM:

### 1. CBOW (`sg = 0`) → Predict word from context

👉 Take surrounding words → predict middle word

### Example:

Context: ["I", "love", "learning"]  
Target: "machine"

👉 Model learns:

("I", "love", "learning") → "machine"

✔ Uses context → predicts missing word

---

### 2. Skip-Gram (`sg = 1`) → Predict context from word

👉 Take one word → predict surrounding words

### Example:

Input: "machine"  
Output: ["I", "love", "learning"]

👉 Model learns:

"machine" → ("I", "love", "learning")

✔ Uses word → predicts its neighbors

---

### Key difference (easy way)

|Method|Input|Output|
|---|---|---|
|CBOW|Context words|Target word|
|Skip-Gram|Target word|Context words|



### urban sound 8k structure :
```
UrbanSound8K/
│
├── audio/
│   ├── fold1/
│   │   ├── 100032-3-0-0.wav
│   │   ├── 100263-2-0-117.wav
│   │   └── ...
│   │
│   ├── fold2/
│   ├── fold3/
│   ├── ...
│   └── fold10/
│
├── metadata/
│   └── UrbanSound8K.csv
│
└── README.txt
```



## Multiclass Text Classification:

```
# ============================================================
# MULTI-CLASS TEXT CLASSIFICATION USING MULTI-KERNEL CNN
# (REAL DATASET + IMPROVED ARCHITECTURE)
# ============================================================

import numpy as np

from sklearn.datasets import fetch_20newsgroups
from sklearn.model_selection import train_test_split
from tensorflow.keras.models import Model
from tensorflow.keras.layers import (
    Input, Embedding, Conv1D, GlobalMaxPooling1D,
    Concatenate, Dense, Dropout
)
from tensorflow.keras.preprocessing.text import Tokenizer
from tensorflow.keras.preprocessing.sequence import pad_sequences
from tensorflow.keras.utils import to_categorical

import re

# ============================================================
# 1. LOAD DATASET
# ============================================================

# 🔥 REAL DATASET (recommended)
data = fetch_20newsgroups(subset='all')
X = data.data
y = data.target

# ------------------------------------------------------------
# 🔴 TOY DATASET (for testing only - NOT for training CNN)
# Uncomment below to use toy dataset
# ------------------------------------------------------------
"""
X = [
    "The government passed a new law",
    "The election results are out",
    "The team won the match",
    "The player scored a century",
    "New AI technology is emerging",
    "Software performance improved",
    "Parliament debated the budget",
    "The coach selected players",
    "New smartphone launched",
    "Cyber security matters"
]

y = [0, 0, 1, 1, 2, 2, 0, 1, 2, 2]
data = type('', (), {})()  # dummy object
data.target_names = ["Politics", "Sports", "Tech"]
"""
# ------------------------------------------------------------

num_classes = len(set(y))

# ============================================================
# 2. BASIC PREPROCESSING
# ============================================================

def clean_text(text):
    text = text.lower()
    text = re.sub(r'[^a-z\s]', '', text)
    return text

X = [clean_text(text) for text in X]

# ============================================================
# 3. TRAIN TEST SPLIT
# ============================================================

X_train, X_test, y_train, y_test = train_test_split(
    X, y, test_size=0.2, random_state=42
)

# ============================================================
# 4. TOKENIZATION
# ============================================================

VOCAB_SIZE = 10000
MAX_LEN = 200

tokenizer = Tokenizer(num_words=VOCAB_SIZE, oov_token="<OOV>")
tokenizer.fit_on_texts(X_train)

X_train_seq = tokenizer.texts_to_sequences(X_train)
X_test_seq  = tokenizer.texts_to_sequences(X_test)

X_train_pad = pad_sequences(X_train_seq, maxlen=MAX_LEN)
X_test_pad  = pad_sequences(X_test_seq, maxlen=MAX_LEN)

# ============================================================
# 5. LABEL ENCODING
# ============================================================

y_train_cat = to_categorical(y_train, num_classes)
y_test_cat  = to_categorical(y_test, num_classes)

# ============================================================
# 6. MULTI-KERNEL CNN MODEL (IMPROVED)
# ============================================================

EMBED_DIM = 128

inputs = Input(shape=(MAX_LEN,))

embedding = Embedding(
    input_dim=VOCAB_SIZE,
    output_dim=EMBED_DIM,
    input_length=MAX_LEN
)(inputs)

# 🔥 Parallel Conv Layers (better feature extraction)
conv3 = Conv1D(128, 3, activation='relu')(embedding)
conv4 = Conv1D(128, 4, activation='relu')(embedding)
conv5 = Conv1D(128, 5, activation='relu')(embedding)

pool3 = GlobalMaxPooling1D()(conv3)
pool4 = GlobalMaxPooling1D()(conv4)
pool5 = GlobalMaxPooling1D()(conv5)

# Merge features
merged = Concatenate()([pool3, pool4, pool5])

dense = Dense(128, activation='relu')(merged)
dropout = Dropout(0.5)(dense)

outputs = Dense(num_classes, activation='softmax')(dropout)

model = Model(inputs, outputs)

# ============================================================
# 7. COMPILE
# ============================================================

model.compile(
    optimizer='adam',
    loss='categorical_crossentropy',
    metrics=['accuracy']
)

model.summary()

# ============================================================
# 8. TRAIN
# ============================================================

model.fit(
    X_train_pad, y_train_cat,
    epochs=5,
    batch_size=64,
    validation_split=0.1
)

# ============================================================
# 9. EVALUATE
# ============================================================

loss, acc = model.evaluate(X_test_pad, y_test_cat)
print("Test Accuracy:", acc)

# ============================================================
# 🔟 PREDICTION FUNCTION
# ============================================================

def predict_category(text):
    text = clean_text(text)
    seq = tokenizer.texts_to_sequences([text])
    pad = pad_sequences(seq, maxlen=MAX_LEN)

    pred = model.predict(pad)
    class_index = np.argmax(pred)

    print("\nInput:", text)
    print("Predicted Category:", data.target_names[class_index])


# ============================================================
# TEST
# ============================================================

predict_category("The government is planning new policies")
predict_category("The match was exciting and thrilling")
predict_category("New AI model beats previous benchmarks")
```

## Architectures :

### vgg16 architecture:

![[Pasted image 20260409225519.png]]

```
Input: 224×224×3 image

Block 1:
Conv(64) → Conv(64) → MaxPool

Block 2:
Conv(128) → Conv(128) → MaxPool

Block 3:
Conv(256) → Conv(256) → Conv(256) → MaxPool

Block 4:
Conv(512) → Conv(512) → Conv(512) → MaxPool

Block 5:
Conv(512) → Conv(512) → Conv(512) → MaxPool

Fully Connected:
Dense(4096) → Dense(4096) → Dense(1000)

```

### InceptionV3:

![[Pasted image 20260410174430.png]]

![[Pasted image 20251027071552.png]]

### ResNet50: ??
