## 📈 Human Evolution & Junior Image Simulation <br/>

  
### 1. &nbsp; Research Objective <br/><br/> 

- _The wine dataset contains the results of a chemical analysis of wines grown in a specific area of Italy. Three types of wine are represented in the 178 samples, with the results of 13 chemical analyses recorded for each sample. The Type variable has been transformed into a categoric variable. The data contains no missing values and consits of only numeric data, with a three class target variable (Type) for classification._ <br/>

- _The goal here is to find a model that can predict the class of wine given the 13 measured parameters and find out the major differences among the three different classes. This is a classification problem and here I will describe three models and asses the accuracy of each model._ <br/><br/><br/>

### 2. &nbsp; Data Preprocessing and Analysis <br/><br/>

- _**Package Settings**_ <br/> 
  
  ```
  # sklearn 패키지에서 와인 데이터 세트를 사용하기 위해 load_wine 설정
  # 표준화를 위해 StandaradScaler 설정
  # 학습용과 테스트 데이터 분리를 위해 train_test_split 설정
  from sklearn.datasets import load_wine
  from sklearn.preprocessing import StandardScaler
  from sklearn.model_selection import train_test_split
  import pandas as pd
  import matplotlib.pyplot as plt
  ```
- _**Data Preparation**_ <br/> 

  ```
  # 데이터 불러오기
  data = load_wine(as_frame = True)
  
  # 데이터 프레임 출력 
  # 데이터를 출력함으로써 개략적인 수치를 확인 
  print(data.frame)
  
  # 입력 부분과 목표 값을 출력 
  # 데이터의 입력 부분과 목표 변수 부분으 나누어 출력함으로써 개략적인 수치를 확인 
  print(data.data)
  print(data.target)
  ```
  
- _**Exploratory Data Analysis (EDA)**_ <br/> 

  ```
  # 데이터 세트 개요
  # 각 열별 최대 및 최소 분포를 파악
  print(data.DESCR)
  
  # 데이터 프레임의 특성(feature) 이름과 목표 변수(target) 이름
  # 목표 변수에서 순서대로 'class_0'는 0, 'class_1'은 1, 'class_2'은 2을 의미
  print("[ 데이터 프레임의 특성(feature) ]")
  for feature in data.feature_names:
      print(f' - {feature}')
  print("\n [ 데이터 프레임의 목표 변수(target) ]")
  for target in data.target_names:
      print(f' - {target}')
  ```

- _**Splitting Data**_ <br/> 

  ```
  # random_state = 자신의 생일로 설정 => 07월 23일 => 0723 => 723
  # data 부분을 8:2 비율로 x_train과 x_test로 나눔
  # target 부분을 8:2 비율로 y_train과 y_test로 나눔
  # 학습용과 테스트 데이터 분리
  x_train, x_test, y_train, y_test = train_test_split(data.data, data.target, test_size=0.2, random_state=723)
  ```  
  
- _**Feature Scaling**_ <br/> 
  ```
  # 피처 스케일링 : 학습 데이터 
  # 학습 데이터의 입력 데이터를 각 열별로 표준화
  scaler_x = StandardScaler()
  scaler_x.fit(x_train)
  x_train_std = scaler_x.transform(x_train)

  # 피처 스케일링 : 테스트 데이터 
  # 학습 데이터의 표준화 스케일을 사용해 테스트 데이터를 표준화
  x_test_std = scaler_x.transform(x_test)
  ```

### 3. &nbsp; Training and Testing Machine Learning Models <br/><br/>

- _**KNN Model**_ <br/> 
  - _KNN works by finding the distances between a query and all the examples in the data, selecting the specified number examples (K) closest to the query, then votes for the most frequent label (in the case of classification)._ 
  
  ```
  # KNN 분류를 위해 KNeighborsClassifier 설정
  from sklearn.neighbors import KNeighborsClassifier

  ## 최근접 이웃 수 결정
  # 학습용 데이터의 분류 정확도
  knn_train_accuracy = []
  # 테스트 데이터의 분류 정확도
  knn_test_accuracy = []
  # 최근접 이웃의 수(k) 
  # -> k가 너무 작으면 데이터의 노이즈 성분까지 고려하는 과대적합(overfitting) 문제가 발생, 
  # -> 반대로 k를 너무 크게 하면 결정함수가 너무 과하게 평탄화(oversmoothing)되는 문제가 발생
  # -> 최적의 K 값을 찾는것이 중요
  # num_neighbors : 최적의 최근접 이웃의 수(k)을 찾기 위한 후보 숫자
  # k를 1~101의 범위에서 찾아봄 
  num_neighbors = range(1, 101)
  for k in num_neighbors:
      # 모형화
      # 유사도 측정 지표(metric)은 유클리드 거리, 맨하탄 거리, 민코브스킨 거리 중 유클리드 거리로 선정
      knn = KNeighborsClassifier(n_neighbors=k, metric='euclidean')
      # 학습
      knn.fit(x_train_std, y_train)
      # 학습 데이터의 분류 정확도
      score = knn.score(x_train_std, y_train) # 학습 모형에 의한 학습 데이터의 분류 정확도 값
      knn_train_accuracy.append(score) # 후보 k 값에 따른 학습 데이터의 분류 정확도 값 추가
      # 테스트 데이터의 분류 정확도
      score = knn.score(x_test_std, y_test) # 학습 모형에 의한 테스트 데이터의 분류 정확도 값
      knn_test_accuracy.append(score) # 후보 k 값에 따른 테스트 데이터의 분류 정확도 값 추가

  # 후보 k의 값에 따른 정확도를 비교
  # k 값이 80보다 커질 수록 과대적합(overfitting)&평탄화(oversmoothing) 문제 등이 발생!
  # 최적의 k 값은 5로 선정!
  max_accuracy = 0
  for num_k, accuracy in enumerate(knn_test_accuracy):
      if max_accuracy < accuracy :
          max_accuracy = accuracy
          best_k = num_k+1
  print(f"최적의 k 값 : {best_k}")  
  print(f'최적의 k 값의 정확도 : {max_accuracy} \n') 

  plt.plot(num_neighbors, knn_train_accuracy, label="train")
  plt.plot(num_neighbors, knn_test_accuracy, label="test")
  plt.xlabel("K")
  plt.ylabel("Accuracy")
  plt.legend()
  plt.show()
  ```  
  <img src="https://github.com/qortmdgh4141/Classifying_Wines_by_Quality_Using_Machine_Learning/blob/main/image/knn_graph.png?raw=true"  width="400" > <br/>
  
### 4. &nbsp; Research Objective <br/>   
- SVM
  -- SVM works by mapping data to a high-dimensional feature space so that data points can be categorized, even when the data are not otherwise linearly separable. A separator between the categories is found, then the data are transformed in such a way that the separator could be drawn as a hyperplane.
  

- KNN
 -- KNN works by finding the distances between a query and all the examples in the data, selecting the specified number examples (K) closest to the query, then votes for the most frequent label (in the case of classification) 
    
- C5.0 works by splitting the sample based on the field that provides the maximum information gain . Each sub-sample defined by the first split is then split again, usually based on a different field, and the process repeats until the subsamples cannot be split any further.


&nbsp; <img src="https://github.com/qortmdgh4141/Classifying_Wines_by_Quality_Using_Machine_Learning/blob/main/image/bar_graph.png?raw=true"  width="400" > <br/>
--------------------------
### 💻 S/W Development Environment
<p>
  <img src="https://img.shields.io/badge/Windows 10-0078D6?style=flat-square&logo=Windows&logoColor=white"/>
  <img src="https://img.shields.io/badge/Google Colab-black?style=flat-square&logo=Google Colab&logoColor=yellow"/>
</p>
<p>
  <img src="https://img.shields.io/badge/Python-3776AB?style=flat-square&logo=Python&logoColor=white"/>
  <img src="https://img.shields.io/badge/PyTorch-FF9900?style=flat-square&logo=PyTorch&logoColor=EE4C2C"/>
  <img src="https://img.shields.io/badge/Numpy-013243?style=flat-square&logo=Numpy&logoColor=blue"/>
</p>

### 🚀 Machine Learning Model
<p>
  <img src="https://img.shields.io/badge/KNN-FF0000?style=flat-square?"/>
  <img src="https://img.shields.io/badge/SVM-FFFF00?style=flat-square?"/>
  <img src="https://img.shields.io/badge/C5.0-00CC00?style=flat-square?"/>
</p> 

### 💾 Dataset used in the project
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; Wine Quality Data Set
