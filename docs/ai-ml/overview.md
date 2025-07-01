# 人工智能与机器学习 - 总览

## 概述

人工智能和机器学习已成为当今最热门的技术领域，深刻改变着各行各业。本章节涵盖机器学习基础理论、深度学习框架、自然语言处理、计算机视觉以及MLOps工程实践，为AI相关岗位面试做好准备。

## 📚 章节内容

### 1. [机器学习基础概念](./ml-fundamentals.md)
- **监督学习**: 线性回归、逻辑回归、决策树、随机森林
- **无监督学习**: 聚类算法、降维技术、异常检测
- **强化学习**: Q-Learning、策略梯度、深度强化学习
- **模型评估**: 交叉验证、偏差-方差权衡、性能指标
- **特征工程**: 特征选择、特征缩放、特征构造

### 2. [深度学习框架](./deep-learning.md)
- **TensorFlow/Keras**: 模型构建、训练、部署
- **PyTorch**: 动态图机制、自动求导、分布式训练
- **神经网络基础**: 前向传播、反向传播、激活函数
- **CNN卷积神经网络**: 图像识别、目标检测
- **RNN循环神经网络**: 序列建模、LSTM、GRU

### 3. [自然语言处理](./nlp.md)
- **文本预处理**: 分词、词性标注、命名实体识别
- **词向量**: Word2Vec、GloVe、FastText
- **语言模型**: N-gram、神经语言模型
- **Transformer架构**: Attention机制、BERT、GPT
- **应用场景**: 机器翻译、文本分类、情感分析

### 4. [计算机视觉](./computer-vision.md)
- **图像处理**: 滤波、边缘检测、形态学操作
- **传统方法**: SIFT、SURF、HOG特征
- **深度学习**: CNN、ResNet、YOLO、R-CNN
- **应用领域**: 图像分类、目标检测、语义分割
- **实践项目**: 人脸识别、医学影像分析

### 5. [MLOps与模型部署](./mlops.md)
- **模型生命周期**: 数据管理、实验跟踪、版本控制
- **模型训练**: 分布式训练、超参数优化、AutoML
- **模型部署**: REST API、批处理、流式处理
- **监控与维护**: 模型漂移、A/B测试、持续学习
- **工具链**: MLflow、Kubeflow、TensorFlow Serving

## 🎯 技术栈对比

### 深度学习框架对比

| 框架 | 优势 | 劣势 | 适用场景 |
|------|------|------|---------|
| **TensorFlow** | 生产就绪、生态完善 | 学习曲线陡峭 | 工业部署、大规模系统 |
| **PyTorch** | 动态图、易调试 | 部署相对复杂 | 研究、原型开发 |
| **JAX** | 函数式、可并行 | 生态较新 | 高性能计算、研究 |
| **PaddlePaddle** | 中文支持好 | 国际生态有限 | 中文NLP、国内场景 |

### 云ML平台对比

| 平台 | 特色服务 | 优势 | 适用对象 |
|------|---------|------|---------|
| **AWS SageMaker** | 端到端ML工作流 | 功能全面、可扩展性强 | 企业级用户 |
| **Google Colab** | 免费GPU/TPU | 易用、免费额度 | 学生、研究者 |
| **Azure ML** | 企业集成 | 与Office生态集成 | 企业用户 |
| **阿里云PAI** | 本土化服务 | 中文支持、合规性 | 国内企业 |

## 🧠 核心算法实现

### 线性回归（从零实现）

```python
import numpy as np
import matplotlib.pyplot as plt

class LinearRegression:
    def __init__(self, learning_rate=0.01, n_iterations=1000):
        self.learning_rate = learning_rate
        self.n_iterations = n_iterations
        self.weights = None
        self.bias = None
        self.costs = []
    
    def fit(self, X, y):
        # 初始化参数
        n_samples, n_features = X.shape
        self.weights = np.zeros(n_features)
        self.bias = 0
        
        # 梯度下降
        for i in range(self.n_iterations):
            # 前向传播
            y_predicted = np.dot(X, self.weights) + self.bias
            
            # 计算代价函数
            cost = (1 / (2 * n_samples)) * np.sum((y_predicted - y) ** 2)
            self.costs.append(cost)
            
            # 计算梯度
            dw = (1 / n_samples) * np.dot(X.T, (y_predicted - y))
            db = (1 / n_samples) * np.sum(y_predicted - y)
            
            # 更新参数
            self.weights -= self.learning_rate * dw
            self.bias -= self.learning_rate * db
    
    def predict(self, X):
        return np.dot(X, self.weights) + self.bias
```

### 神经网络前向传播

```python
import numpy as np

def sigmoid(x):
    return 1 / (1 + np.exp(-np.clip(x, -500, 500)))

def relu(x):
    return np.maximum(0, x)

def softmax(x):
    exp_x = np.exp(x - np.max(x, axis=1, keepdims=True))
    return exp_x / np.sum(exp_x, axis=1, keepdims=True)

class NeuralNetwork:
    def __init__(self, layer_sizes):
        self.layer_sizes = layer_sizes
        self.weights = []
        self.biases = []
        
        # 初始化权重和偏置
        for i in range(len(layer_sizes) - 1):
            w = np.random.randn(layer_sizes[i], layer_sizes[i+1]) * 0.1
            b = np.zeros((1, layer_sizes[i+1]))
            self.weights.append(w)
            self.biases.append(b)
    
    def forward(self, X):
        activations = [X]
        
        for i in range(len(self.weights)):
            z = np.dot(activations[-1], self.weights[i]) + self.biases[i]
            
            if i == len(self.weights) - 1:  # 输出层
                a = softmax(z)
            else:  # 隐藏层
                a = relu(z)
            
            activations.append(a)
        
        return activations
```

## 🔥 热门应用场景

### 1. 推荐系统

#### 协同过滤
```python
import numpy as np
from sklearn.metrics.pairwise import cosine_similarity

class CollaborativeFiltering:
    def __init__(self):
        self.user_similarity = None
        self.item_similarity = None
        self.user_mean = None
    
    def fit(self, ratings_matrix):
        # ratings_matrix: users x items
        self.user_mean = np.mean(ratings_matrix, axis=1, keepdims=True)
        
        # 计算用户相似度
        centered_ratings = ratings_matrix - self.user_mean
        self.user_similarity = cosine_similarity(centered_ratings)
        
        # 计算物品相似度
        self.item_similarity = cosine_similarity(ratings_matrix.T)
    
    def predict_user_based(self, user_id, item_id, ratings_matrix, k=10):
        # 找到最相似的k个用户
        similarities = self.user_similarity[user_id]
        similar_users = np.argsort(similarities)[::-1][1:k+1]
        
        # 计算预测评分
        numerator = 0
        denominator = 0
        
        for similar_user in similar_users:
            if ratings_matrix[similar_user, item_id] > 0:
                similarity = similarities[similar_user]
                rating_diff = (ratings_matrix[similar_user, item_id] - 
                             self.user_mean[similar_user])
                numerator += similarity * rating_diff
                denominator += abs(similarity)
        
        if denominator == 0:
            return self.user_mean[user_id, 0]
        
        prediction = self.user_mean[user_id, 0] + numerator / denominator
        return np.clip(prediction, 1, 5)
```

### 2. 时间序列预测

#### LSTM模型
```python
import tensorflow as tf
from tensorflow.keras.models import Sequential
from tensorflow.keras.layers import LSTM, Dense, Dropout

def create_lstm_model(sequence_length, n_features, n_outputs):
    model = Sequential([
        LSTM(50, return_sequences=True, 
             input_shape=(sequence_length, n_features)),
        Dropout(0.2),
        
        LSTM(50, return_sequences=True),
        Dropout(0.2),
        
        LSTM(50),
        Dropout(0.2),
        
        Dense(n_outputs)
    ])
    
    model.compile(
        optimizer='adam',
        loss='mse',
        metrics=['mae']
    )
    
    return model

def prepare_time_series_data(data, sequence_length):
    X, y = [], []
    
    for i in range(len(data) - sequence_length):
        X.append(data[i:(i + sequence_length)])
        y.append(data[i + sequence_length])
    
    return np.array(X), np.array(y)
```

## 📊 模型评估与优化

### 分类模型评估
```python
import numpy as np
from sklearn.metrics import confusion_matrix, classification_report
import matplotlib.pyplot as plt
import seaborn as sns

def evaluate_classification_model(y_true, y_pred, class_names=None):
    # 混淆矩阵
    cm = confusion_matrix(y_true, y_pred)
    
    # 绘制混淆矩阵
    plt.figure(figsize=(8, 6))
    sns.heatmap(cm, annot=True, fmt='d', cmap='Blues',
                xticklabels=class_names, yticklabels=class_names)
    plt.title('Confusion Matrix')
    plt.ylabel('True Label')
    plt.xlabel('Predicted Label')
    plt.show()
    
    # 分类报告
    print(classification_report(y_true, y_pred, target_names=class_names))
    
    # 计算各种指标
    tp = np.diag(cm)
    fp = cm.sum(axis=0) - tp
    fn = cm.sum(axis=1) - tp
    tn = cm.sum() - (fp + fn + tp)
    
    precision = tp / (tp + fp)
    recall = tp / (tp + fn)
    f1 = 2 * (precision * recall) / (precision + recall)
    
    return {
        'precision': precision,
        'recall': recall,
        'f1_score': f1,
        'confusion_matrix': cm
    }
```

### 超参数优化
```python
from sklearn.model_selection import GridSearchCV, RandomizedSearchCV
from sklearn.ensemble import RandomForestClassifier

def hyperparameter_tuning_example():
    # 网格搜索
    param_grid = {
        'n_estimators': [100, 200, 300],
        'max_depth': [10, 20, 30, None],
        'min_samples_split': [2, 5, 10],
        'min_samples_leaf': [1, 2, 4]
    }
    
    rf = RandomForestClassifier(random_state=42)
    
    # 网格搜索
    grid_search = GridSearchCV(
        estimator=rf,
        param_grid=param_grid,
        cv=5,
        scoring='f1_macro',
        n_jobs=-1,
        verbose=1
    )
    
    # 随机搜索（更高效）
    random_search = RandomizedSearchCV(
        estimator=rf,
        param_distributions=param_grid,
        n_iter=50,
        cv=5,
        scoring='f1_macro',
        n_jobs=-1,
        random_state=42,
        verbose=1
    )
    
    return grid_search, random_search
```

## 🚀 MLOps最佳实践

### 模型版本控制
```python
import mlflow
import mlflow.sklearn
from mlflow.tracking import MlflowClient

def log_model_experiment(model, X_train, y_train, X_test, y_test, params):
    with mlflow.start_run():
        # 记录超参数
        mlflow.log_params(params)
        
        # 训练模型
        model.fit(X_train, y_train)
        
        # 预测和评估
        train_score = model.score(X_train, y_train)
        test_score = model.score(X_test, y_test)
        
        # 记录指标
        mlflow.log_metric("train_score", train_score)
        mlflow.log_metric("test_score", test_score)
        
        # 记录模型
        mlflow.sklearn.log_model(model, "model")
        
        # 记录模型签名
        signature = mlflow.models.infer_signature(X_train, model.predict(X_train))
        mlflow.sklearn.log_model(model, "model", signature=signature)
        
        return mlflow.active_run().info.run_id
```

### 模型监控
```python
import numpy as np
from scipy import stats

class ModelMonitor:
    def __init__(self, reference_data):
        self.reference_data = reference_data
        self.reference_stats = self._compute_stats(reference_data)
    
    def _compute_stats(self, data):
        return {
            'mean': np.mean(data, axis=0),
            'std': np.std(data, axis=0),
            'quantiles': np.quantile(data, [0.25, 0.5, 0.75], axis=0)
        }
    
    def detect_drift(self, new_data, threshold=0.05):
        """使用KS检验检测数据漂移"""
        drift_results = {}
        
        for i in range(new_data.shape[1]):
            ks_stat, p_value = stats.ks_2samp(
                self.reference_data[:, i], 
                new_data[:, i]
            )
            
            drift_results[f'feature_{i}'] = {
                'ks_statistic': ks_stat,
                'p_value': p_value,
                'drift_detected': p_value < threshold
            }
        
        return drift_results
    
    def generate_report(self, new_data):
        drift_results = self.detect_drift(new_data)
        new_stats = self._compute_stats(new_data)
        
        report = {
            'drift_detection': drift_results,
            'statistical_comparison': {
                'reference_stats': self.reference_stats,
                'new_stats': new_stats
            }
        }
        
        return report
```

## 🎯 面试重点

### 理论基础
1. **机器学习基础**: 偏差-方差权衡、过拟合、正则化
2. **深度学习**: 反向传播、梯度消失、批归一化
3. **优化算法**: SGD、Adam、学习率调度
4. **评估指标**: 准确率、精确率、召回率、F1-score、AUC

### 实践技能
1. **数据预处理**: 缺失值处理、异常值检测、特征缩放
2. **模型选择**: 交叉验证、超参数调优、模型集成
3. **特征工程**: 特征选择、特征构造、降维技术
4. **模型部署**: API设计、容器化、监控

### 项目经验
1. **端到端项目**: 从数据收集到模型部署的完整流程
2. **问题解决**: 如何定义问题、选择模型、评估效果
3. **工程实践**: 代码规范、版本控制、测试策略
4. **业务理解**: 如何将技术与业务目标结合

## 📖 学习资源

### 经典教材
1. **《机器学习》** - 周志华（西瓜书）
2. **《统计学习方法》** - 李航
3. **《深度学习》** - Ian Goodfellow
4. **《Python机器学习》** - Sebastian Raschka

### 在线课程
1. [Andrew Ng机器学习课程](https://www.coursera.org/learn/machine-learning) - 入门经典
2. [Deep Learning Specialization](https://www.coursera.org/specializations/deep-learning) - 深度学习
3. [Fast.ai](https://www.fast.ai/) - 实用深度学习
4. [CS229 Stanford](http://cs229.stanford.edu/) - 斯坦福机器学习

### 实践平台
1. [Kaggle](https://www.kaggle.com/) - 数据科学竞赛
2. [Google Colab](https://colab.research.google.com/) - 免费GPU环境
3. [Papers with Code](https://paperswithcode.com/) - 论文与代码
4. [Hugging Face](https://huggingface.co/) - NLP模型库

---

通过系统学习AI/ML技术，您将具备解决实际问题的能力，为AI相关岗位面试和职业发展做好准备。 