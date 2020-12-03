# UTS
---
title: "One R"
author: "HAYUN"
date: "27/10/2020"
output:
  word_document: default
  html_document: default
---

### Load Library
Library yang dibutuhkan adalah **oneR**. Silahkan install terlebih dahulu jika belum terisntall, dengan perintah `install.packages("oneR")`
```{r}
library(OneR)
```

### Baca Data
Data tersimpan di folder `dataset`
```{r}
data <- read.csv("C:/Users/Asus/Downloads/sonar1.csv", header=FALSE)
head(data)
```

### Explorasi Data
#### Melihat struktur data
```{r}
str(data)
```

#### Melihat apa terdapat data yang miss
```{r}
colSums(is.na(data))
```
#### Mengubah tipe variabel menjadi factor
```{r}
data$V61 <- as.factor(data$V61)
```

### Split Data
Memecah data menjadi data training(80% dari data awal) dan data test (20% dari data awal)
```{r}
set.seed(1234)
sampel<-sample(2,nrow(data),replace = T, prob = c(0.8,0.2))
trainingdat<-data[sampel==1, ]
testingdat<-data[sampel==2, ]
print(paste("Jumlah Train Data: ", nrow(trainingdat), "| Jumlah Test Data: ", nrow(testingdat)))
```

### Membuat Model
```{r}
model.OneR <- OneR(V61~., data = trainingdat, verbose = TRUE)
summary(model.OneR)
```
Dari Model diatas Mendapatkan 5 Rules, dimana accuracy nya (75.74%) . 128  data dari 169  terklasifikasi dengan benar. yaitu
Rules:
If V11 = (0.0282,0.17] then V61 = R
If V11 = (0.17,0.311]  then V61 = M
If V11 = (0.311,0.452] then V61 = M
If V11 = (0.452,0.593] then V61 = M
If V11 = (0.593,0.735] then V61 = M

### OneR Model
```{r}
plot(model.OneR)
```
### Model Evaluation
```{r}
pc <- predict(model.OneR, testingdat, type = "class")
eval_model(pc,testingdat)
```

1. Accuracy
Dari Confussion Matrix di atas, untuk True Positif yaitu 9 dan True Negatif 12,accuracy merupakan rasio prediksi benar (positif dan negatif) dengan keseluruhan data. Jadi untuk Nilai accuracy nya adalah 0.7436 (29/39)

2. Recall atau Sensitivity (True Positive Rate)
recall merupakan rasio prediksi benar positif dibandingkan dengan keseluruhan data yang benar positif. 
Jadi dari confussion matriks diatas diperoleh Sensitivity M adalah 0.916 (22/24)

3. Precision (Positive Predictive Value)
Precision menggambarkan tingkat keakuratan antara data yang diminta dengan hasil prediksi yang diberikan oleh model.
dari confussion matriks diatas diperoleh precision adalah 0.73 (22/30)

4. Specificity
Merupakan kebenaran memprediksi negatif dibandingkan dengan keseluruhan data negatif.
dari confussion matrix diatas diperoleh Specificity adalah 0.46 (7/15)

5. F1 Score
F1 Score = 2 * (Recall*Precission) / (Recall + Precission)
F1 Score = 0.8125




---
title: "Zero R"
author: "HAYUN"
date: "27/10/2020"
output:
  word_document: default
  html_document: default
---

### Load Library
Library yang dibutuhkan adalah **tidyverse**. Silahkan install terlebih dahulu jika belum terisntall, dengan perintah `install.packages("tidyverse")`. Library ini aka digunakan untuk pengolahan variabel dan data.

```{r message=FALSE, warning=FALSE}
library(tidyverse)
```

### Baca Data
Data tersimpan di folder `dataset`
```{r}
data <- read.csv("C:/Users/Asus/Downloads/sonar1.csv", header=FALSE)
head(data)
```

### Ekplorasi Data
#### Melihat struktur data
```{r}
str(data)
```

#### Melihat apa terdapat data yang miss 
```{r}
colSums(is.na(data))
```

#### Mengubah tipe variabel menjadi factor
```{r}
data$V61 <- as.factor(data$V61)
```

### Split Data
Memecah data menjadi data training(80% dari data awal) dan data test (20% dari data awal)
```{r}
set.seed(1234)
sampel<-sample(2, nrow(data), replace = T, prob = c(0.8,0.2))
trainingdat<-data[sampel==1, ]
testingdat<-data[sampel==2, ]
print(paste("Jumlah Train Data: ", nrow(trainingdat), "| Jumlah Test Data: ", nrow(testingdat)))
```

### Hanya mengambil target class saja
```{r}
trainingdat<- trainingdat[, 61]
testingdat<- testingdat[, 61]
```

### Ambil banyak yes dan no pada target class
```{r}
banyakR <- sum(trainingdat == "R")
banyakR
```

```{r}
banyakM <- sum(trainingdat == "M")
banyakM
```

### Hitung Peluang
```{r}
probR <- banyakR/length(trainingdat)
probM <- banyakM/length(trainingdat)
print(paste("Peluang R: ", probR, " | Peluang M: ", probM))
```
Karena peluang M **lebih besar** dari M maka ` sonar mendeteksi tambang' adalah model ZeroR dengan `akurasi = 0.514792899408284`

