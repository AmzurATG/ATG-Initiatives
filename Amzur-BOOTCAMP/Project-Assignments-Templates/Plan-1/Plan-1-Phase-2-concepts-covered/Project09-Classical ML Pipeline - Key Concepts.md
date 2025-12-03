# Project 9: Classical ML Pipeline - Key Concepts

> **Note:** This document establishes the foundational ML concepts for bootcamp students that will be extended and integrated throughout Phase 2 projects.

## 1. Simple Data Pipeline Design & Basic ETL

### Concept Overview
Simple data pipeline design for ML involves creating straightforward workflows that load data from CSV files, clean it for machine learning, and prepare it for model training using popular, well-documented libraries like pandas and scikit-learn.

### Problem It Solves
Raw CSV data typically contains missing values, inconsistent formats, and mixed data types that prevent direct use in machine learning models. Manual data preparation is time-consuming and error-prone for bootcamp students learning ML fundamentals.

### Business Benefits & Value Proposition
**Quantifiable Benefits:**
- **Learning Speed**: 70% faster ML concept understanding through hands-on practice
- **Practical Skills**: Immediate applicability to real-world data problems
- **Portfolio Value**: Working ML pipeline suitable for job applications
- **Foundation Building**: Solid base for advanced ML concepts

**Educational Impact Examples:**
- Students can process real datasets (Titanic, Boston Housing) successfully
- Clear understanding of data → model → prediction workflow
- Practical experience with industry-standard libraries
- Confidence in building complete ML systems

**Learning Advantages:**
- Hands-on experience with popular ML libraries
- Understanding of complete ML workflow
- Practical problem-solving skills
- Portfolio-ready project for job applications

### Solution Approach
- **Simple Pipeline Architecture**: Clear data → processing → model → API flow
- **Basic Data Quality**: Essential validation and cleaning steps
- **Popular Libraries**: Using pandas, scikit-learn, FastAPI (well-documented)
- **Step-by-Step Learning**: Daily progression with clear objectives
- **Working Examples**: Complete, testable implementations

### Implementation Insight
```python
import pandas as pd
import numpy as np
from sklearn.model_selection import train_test_split
from sklearn.preprocessing import StandardScaler, LabelEncoder
from sklearn.ensemble import RandomForestClassifier
from sklearn.metrics import accuracy_score, confusion_matrix
import joblib

class SimpleMLPipeline:
    """
    Simple ML pipeline for bootcamp learning - focuses on understanding fundamentals
    """
    
    def __init__(self):
        self.scaler = StandardScaler()
        self.encoder = LabelEncoder()
        self.model = None
        self.is_trained = False
        
    def load_data(self, filepath: str) -> pd.DataFrame:
        """Load CSV data with basic error handling"""
        try:
            data = pd.read_csv(filepath)
            print(f"Data loaded successfully: {data.shape}")
            return data
        except Exception as e:
            print(f"Error loading data: {e}")
            return None
    
    def explore_data(self, data: pd.DataFrame) -> dict:
        """Basic data exploration for understanding"""
        exploration = {
            'shape': data.shape,
            'columns': list(data.columns),
            'missing_values': data.isnull().sum().to_dict(),
            'data_types': data.dtypes.to_dict()
        }
        
        print("=== Data Exploration ===")
        print(f"Shape: {exploration['shape']}")
        print(f"Columns: {exploration['columns']}")
        print("Missing values:")
        for col, missing in exploration['missing_values'].items():
            if missing > 0:
                print(f"  {col}: {missing}")
        
        return exploration
    
    def clean_data(self, data: pd.DataFrame, target_column: str) -> tuple:
        """Simple data cleaning for ML"""
        print("=== Cleaning Data ===")
        
        # Separate features and target
        if target_column not in data.columns:
            raise ValueError(f"Target column '{target_column}' not found")
        
        y = data[target_column].copy()
        X = data.drop(columns=[target_column])
        
        # Handle missing values simply
        for col in X.columns:
            if X[col].isnull().sum() > 0:
                if X[col].dtype in ['object']:
                    # Fill categorical with mode
                    mode_val = X[col].mode().iloc[0] if len(X[col].mode()) > 0 else 'Unknown'
                    X[col].fillna(mode_val, inplace=True)
                    print(f"Filled {col} missing values with: {mode_val}")
                else:
                    # Fill numerical with median
                    median_val = X[col].median()
                    X[col].fillna(median_val, inplace=True)
                    print(f"Filled {col} missing values with median: {median_val}")
        
        # Simple encoding for categorical variables
        categorical_cols = X.select_dtypes(include=['object']).columns
        for col in categorical_cols:
            if X[col].nunique() <= 10:  # One-hot encode if few categories
                dummies = pd.get_dummies(X[col], prefix=col, drop_first=True)
                X = pd.concat([X, dummies], axis=1)
                X.drop(columns=[col], inplace=True)
                print(f"One-hot encoded: {col}")
            else:  # Label encode if many categories
                X[col] = self.encoder.fit_transform(X[col].astype(str))
                print(f"Label encoded: {col}")
        
        print(f"Final dataset shape: {X.shape}")
        return X, y
    
    def prepare_data(self, X: pd.DataFrame, y: pd.Series) -> tuple:
        """Prepare data for training (split and scale)"""
        print("=== Preparing Data for Training ===")
        
        # Split data
        X_train, X_test, y_train, y_test = train_test_split(
            X, y, test_size=0.2, random_state=42
        )
        
        # Scale numerical features
        numerical_cols = X_train.select_dtypes(include=[np.number]).columns
        if len(numerical_cols) > 0:
            X_train_scaled = X_train.copy()
            X_test_scaled = X_test.copy()
            
            X_train_scaled[numerical_cols] = self.scaler.fit_transform(X_train[numerical_cols])
            X_test_scaled[numerical_cols] = self.scaler.transform(X_test[numerical_cols])
            
            print(f"Scaled {len(numerical_cols)} numerical features")
            return X_train_scaled, X_test_scaled, y_train, y_test
        
        return X_train, X_test, y_train, y_test
    
    def train_model(self, X_train: pd.DataFrame, y_train: pd.Series) -> dict:
        """Train simple Random Forest model"""
        print("=== Training Model ===")
        
        # Use Random Forest (good default choice)
        self.model = RandomForestClassifier(
            n_estimators=100,
            random_state=42,
            max_depth=10  # Prevent overfitting
        )
        
        # Train model
        self.model.fit(X_train, y_train)
        self.is_trained = True
        
        # Get feature importance
        feature_importance = dict(zip(
            X_train.columns,
            self.model.feature_importances_
        ))
        
        print("Model trained successfully!")
        print("Top 5 important features:")
        sorted_features = sorted(feature_importance.items(), key=lambda x: x[1], reverse=True)
        for feature, importance in sorted_features[:5]:
            print(f"  {feature}: {importance:.3f}")
        
        return feature_importance
    
    def evaluate_model(self, X_test: pd.DataFrame, y_test: pd.Series) -> dict:
        """Evaluate model performance"""
        if not self.is_trained:
            raise ValueError("Model must be trained first")
        
        print("=== Evaluating Model ===")
        
        # Make predictions
        y_pred = self.model.predict(X_test)
        
        # Calculate metrics
        accuracy = accuracy_score(y_test, y_pred)
        conf_matrix = confusion_matrix(y_test, y_pred)
        
        results = {
            'accuracy': accuracy,
            'confusion_matrix': conf_matrix,
            'test_samples': len(y_test)
        }
        
        print(f"Accuracy: {accuracy:.3f} ({accuracy*100:.1f}%)")
        print(f"Test samples: {len(y_test)}")
        
        return results
    
    def save_model(self, filepath: str = 'simple_ml_model.pkl'):
        """Save trained model"""
        if not self.is_trained:
            raise ValueError("Model must be trained first")
        
        model_package = {
            'model': self.model,
            'scaler': self.scaler,
            'encoder': self.encoder,
            'is_trained': self.is_trained
        }
        
        joblib.dump(model_package, filepath)
        print(f"Model saved to: {filepath}")
    
    def load_model(self, filepath: str):
        """Load saved model"""
        try:
            model_package = joblib.load(filepath)
            self.model = model_package['model']
            self.scaler = model_package['scaler']
            self.encoder = model_package['encoder']
            self.is_trained = model_package['is_trained']
            print(f"Model loaded from: {filepath}")
        except Exception as e:
            print(f"Error loading model: {e}")
    
    def predict_single(self, sample_data: dict) -> dict:
        """Make prediction on single sample"""
        if not self.is_trained:
            raise ValueError("Model must be trained first")
        
        # Convert to DataFrame
        sample_df = pd.DataFrame([sample_data])
        
        # Make prediction
        prediction = self.model.predict(sample_df)[0]
        confidence = self.model.predict_proba(sample_df)[0].max()
        
        return {
            'prediction': prediction,
            'confidence': confidence
        }

# Usage example for bootcamp learning
def demonstrate_ml_pipeline():
    """Complete example showing the ML pipeline"""
    
    print("=== Bootcamp ML Pipeline Demo ===")
    
    # Initialize pipeline
    pipeline = SimpleMLPipeline()
    
    # Load data (using a sample dataset)
    data = pipeline.load_data('sample_data.csv')
    
    if data is not None:
        # Explore data
        exploration = pipeline.explore_data(data)
        
        # Clean data
        X, y = pipeline.clean_data(data, target_column='target')
        
        # Prepare data
        X_train, X_test, y_train, y_test = pipeline.prepare_data(X, y)
        
        # Train model
        feature_importance = pipeline.train_model(X_train, y_train)
        
        # Evaluate model
        results = pipeline.evaluate_model(X_test, y_test)
        
        # Save model
        pipeline.save_model()
        
        print("=== Pipeline Complete! ===")
        print(f"Final accuracy: {results['accuracy']:.3f}")
        
        return pipeline, results
    
    return None, None
```

### Key Performance Parameters (Bootcamp Level)
**Metrics to Track:**
- **Model Accuracy**: Target > 70% for learning datasets (Iris, Titanic, etc.)
- **Pipeline Completion**: Successfully complete all steps without errors
- **Understanding Check**: Can explain each step of the pipeline
- **Code Quality**: Clean, readable code with comments
- **Working Demo**: API endpoint responds with predictions

**Learning Benchmarks:**
- Day 1: Successfully load and explore data
- Day 2: Train multiple models and compare results
- Day 3: Deploy working API for predictions
- Portfolio readiness: Professional documentation and demo

**Success Indicators:**
- **Functional Pipeline**: Data → Model → Predictions working end-to-end
- **Model Comparison**: Understand why one model works better than another
- **API Integration**: Working FastAPI endpoint for predictions
- **Documentation**: Clear explanation of approach and results

### Common Pitfalls & Anti-Patterns (Bootcamp Context)
- **Overcomplicating**: Trying to implement enterprise features instead of learning fundamentals
- **Skipping Exploration**: Not understanding the data before modeling
- **Poor Documentation**: Not explaining what the code does and why
- **No Testing**: Not validating that the pipeline actually works
- **Perfectionism**: Spending too much time optimizing instead of completing working system
- **Library Confusion**: Using too many different libraries instead of mastering the basics

### Cross-References & Dependencies (Simplified)
- **Extends to Project 10**: ML model results can feed into AI agent decision-making
- **Supports Project 14**: Data preprocessing patterns apply to image classification
- **Integrates with Project 12**: N8N can trigger model training workflows
- **Feeds into Project 16**: Capstone integrates ML pipeline with other components
- **Foundation Knowledge**: Pandas for data manipulation, scikit-learn for ML basics

### Architecture Diagram (Simplified)
```
┌─────────────────┐    ┌──────────────────┐    ┌─────────────────┐
│                 │    │                  │    │                 │
│  CSV Data       │───▶│  Simple Clean    │───▶│  Basic Model    │
│                 │    │                  │    │                 │
│ • Load pandas   │    │ • Handle missing │    │ • Random Forest │
│ • Basic explore │    │ • Simple encode  │    │ • Train & test  │
│ • Check quality │    │ • Basic scaling  │    │ • Evaluate      │
└─────────────────┘    └──────────────────┘    └─────────────────┘
                                │                        │
                                │                        │
┌─────────────────┐    ┌──────────────────┐    ┌─────────▼───────┐
│                 │    │                  │    │                 │
│  FastAPI Web    │◀───│  Model Save      │◀───│  Model Results  │
│                 │    │                  │    │                 │
│ • Predict endpoint │  │ • joblib save    │    │ • Accuracy      │
│ • Simple interface│  │ • Easy load      │    │ • Confusion     │
│ • Test easily   │    │ • Basic metadata │    │ • Compare models│
└─────────────────┘    └──────────────────┘    └─────────────────┘
```

### Scalability & Production Considerations (Bootcamp Perspective)
**Learning Progression:**
- **Start Simple**: Focus on understanding core concepts first
- **Add Complexity Gradually**: Introduce new concepts step by step
- **Practice with Real Data**: Use datasets students can relate to
- **Build Portfolio Pieces**: Create work suitable for job applications

**Resource Requirements (Local Development):**
- **Laptop Friendly**: Works on standard bootcamp hardware
- **Popular Libraries**: Well-documented tools with good community support
- **Small Datasets**: Focus on learning, not performance optimization
- **Local Development**: No cloud setup required for core learning

**Skills Development:**
- **ML Fundamentals**: Understanding the complete workflow
- **Python Proficiency**: Practical experience with data science libraries
- **Problem Solving**: Learning to debug and fix common issues
- **Communication**: Explaining approach and results clearly

### Industry Use Cases & Examples (Bootcamp Context)
**Learning-Friendly Applications:**
- **Customer Classification**: Predicting customer behavior (accessible business context)
- **Product Recommendation**: Basic recommendation system concepts
- **Quality Prediction**: Manufacturing quality control (clear success metrics)
- **Risk Assessment**: Simple risk scoring (practical business application)

**Portfolio Examples:**
- **Titanic Survival**: Classic ML problem with clear story
- **House Price Prediction**: Relatable problem with interpretable results
- **Customer Churn**: Business-relevant with clear value proposition
- **Product Classification**: E-commerce application with visual appeal

**Career Relevance:**
- **Data Science Roles**: Foundation for ML engineer positions
- **Business Analysis**: Understanding of data-driven decision making
- **Product Management**: Technical literacy for AI product development
- **Consulting**: Ability to implement and explain ML solutions

### Further Resources (Bootcamp Friendly)
- [scikit-learn User Guide](https://scikit-learn.org/stable/user_guide.html) - Comprehensive ML library documentation
- [Pandas Getting Started](https://pandas.pydata.org/docs/getting_started/index.html) - Data manipulation fundamentals
- [FastAPI Tutorial](https://fastapi.tiangolo.com/tutorial/) - Building APIs for ML models
- [Kaggle Learn](https://www.kaggle.com/learn) - Free micro-courses on ML fundamentals
- [Real Python ML Tutorials](https://realpython.com/tutorials/machine-learning/) - Practical Python ML guides

### Assessment & Progress Tracking
**Daily Checkpoints:**
- **Day 1**: Can load data, handle missing values, split data properly
- **Day 2**: Can train multiple models, compare results, save best model
- **Day 3**: Can deploy API, make predictions, document approach

**Mastery Indicators:**
- **Explains Process**: Can walk through each step and explain why it's needed
- **Debugs Issues**: Can identify and fix common data/model problems
- **Applies Learning**: Can adapt pipeline to new datasets
- **Communicates Results**: Can present findings to non-technical audience

**Portfolio Readiness:**
- **Working Demo**: Complete system that can be shown to employers
- **Clear Documentation**: Professional README with setup instructions
- **Business Context**: Explains problem solved and value created
- **Technical Depth**: Shows understanding of ML concepts and implementation