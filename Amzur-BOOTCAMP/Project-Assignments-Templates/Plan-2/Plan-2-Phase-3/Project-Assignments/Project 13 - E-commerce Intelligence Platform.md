# **Project 13: E-commerce Intelligence Platform - Client Simulation**
## Team-Based Client Project - Weeks 9-10

**Duration:** 2 weeks (Team Sprint)  
**Team Size:** 4-5 members  
**Client:** RetailMax Digital (Simulated)  
**Goal:** Build comprehensive AI-driven e-commerce platform with personalization, demand forecasting, and customer analytics  
**Technology Stack:** TensorFlow/PyTorch, React/Next.js, Python, Redis, Elasticsearch, AWS, MLOps

---

## **Client Brief & Business Context**

### **Client Background**
**RetailMax Digital** is a rapidly growing e-commerce company with 2M+ customers and $500M annual revenue. They operate across fashion, electronics, and home goods with both B2C and B2B channels.

### **Business Challenge**
- **Personalization Gap**: Generic product recommendations leading to low conversion (2.3%)
- **Inventory Issues**: $50M in overstock and frequent stockouts hurting revenue
- **Customer Churn**: 35% annual churn rate with poor customer lifetime value
- **Pricing Inefficiency**: Static pricing missing revenue optimization opportunities
- **Operational Costs**: Manual processes consuming 40% of operational budget

### **Strategic Goals**
- **Revenue Growth**: 25% increase through personalization and dynamic pricing
- **Inventory Optimization**: Reduce overstock by 60% and stockouts by 80%
- **Customer Retention**: Improve customer lifetime value by 40%
- **Operational Efficiency**: Automate 70% of manual processes
- **Market Expansion**: AI-driven insights for new market and product opportunities

---

## **Project Overview**

### **Team Objectives**
- Experience end-to-end e-commerce platform development with real business constraints
- Master recommendation systems, demand forecasting, and customer analytics
- Build production-scale systems handling millions of transactions
- Develop business acumen through revenue optimization and customer experience
- Create enterprise-grade solutions with monitoring and analytics

### **Platform Components**
1. **Intelligent Recommendation Engine** - Personalized product recommendations
2. **Demand Forecasting System** - Inventory planning and purchasing optimization
3. **Dynamic Pricing Engine** - Real-time price optimization
4. **Customer Analytics Platform** - Segmentation, churn prediction, and LTV modeling
5. **Business Intelligence Dashboard** - Executive insights and operational metrics

---

## **Team Structure & Roles**

### **Product Manager/Technical Lead** (1 person)
**Responsibilities:**
- Product strategy and feature prioritization
- Client stakeholder management and requirements gathering
- Technical architecture and team coordination
- Business metrics and KPI definition

**Key Skills:** Product management, e-commerce domain, business analysis

### **ML Engineers** (2 people)
**Responsibilities:**
- Recommendation system development and optimization
- Demand forecasting and inventory models
- Customer analytics and churn prediction
- A/B testing and model performance optimization

**Key Skills:** Recommendation systems, time series forecasting, customer analytics

### **Full-Stack Developer** (1 person)
**Responsibilities:**
- E-commerce platform frontend and user experience
- Admin dashboard and business intelligence interfaces
- API development and third-party integrations
- Performance optimization and user analytics

**Key Skills:** React/Next.js, e-commerce platforms, UI/UX design

### **Data Engineer/DevOps** (1 person)
**Responsibilities:**
- Data pipeline and real-time processing systems
- Search and analytics infrastructure (Elasticsearch)
- Cloud deployment and scaling architecture
- Monitoring, logging, and performance optimization

**Key Skills:** Big data processing, search systems, cloud architecture

---

## **Two-Week Sprint Plan**

### **Week 9: Platform Foundation and Core Intelligence**

#### **Day 1-2: Discovery and Architecture Design**
**Team Activities:**
- [ ] **Client Stakeholder Interviews**: Business requirements and success metrics
- [ ] **E-commerce Data Analysis**: Customer behavior, product catalog, and transaction analysis
- [ ] **Competitive Research**: Industry benchmarks and best practices
- [ ] **Technical Architecture**: System design and scalability planning
- [ ] **Data Strategy**: Data modeling and pipeline architecture

**Deliverables:**
- [ ] Product requirements document with business metrics
- [ ] Technical architecture and system design
- [ ] Data analysis report with customer insights
- [ ] Sprint backlog with team assignments

#### **Day 3-5: Core ML Systems Development**
**ML Team:**
- [ ] Recommendation engine with collaborative and content-based filtering
- [ ] Customer segmentation and behavior analysis models
- [ ] Demand forecasting models for inventory optimization
- [ ] Initial A/B testing framework for recommendation evaluation

**Full-Stack Team:**
- [ ] E-commerce platform foundation with product catalog
- [ ] User authentication and profile management
- [ ] Product recommendation UI components
- [ ] Basic admin dashboard for business metrics

**Data Engineering Team:**
- [ ] Real-time data processing pipeline
- [ ] Elasticsearch integration for product search
- [ ] Redis caching layer for performance
- [ ] Database design for customer and product data

#### **Day 6-7: Integration and Initial Testing**
**Team Activities:**
- [ ] System integration and API testing
- [ ] End-to-end user journey testing
- [ ] Performance benchmarking and optimization
- [ ] **Client Demo #1**: Core platform functionality and initial results

### **Week 10: Advanced Features and Production Deployment**

#### **Day 8-10: Advanced Intelligence and Optimization**
**ML Team:**
- [ ] Dynamic pricing optimization with market analysis
- [ ] Customer lifetime value prediction and churn modeling
- [ ] Advanced recommendation features (seasonal, cross-sell, upsell)
- [ ] Real-time personalization and content optimization

**Full-Stack Team:**
- [ ] Advanced e-commerce features (cart, checkout, order management)
- [ ] Comprehensive business intelligence dashboard
- [ ] Mobile-responsive design and PWA features
- [ ] Customer support and notification systems

**Data Engineering Team:**
- [ ] Production data pipeline with monitoring
- [ ] Advanced analytics and real-time dashboards
- [ ] Performance optimization and auto-scaling
- [ ] Security implementation and compliance

#### **Day 11-12: Business Intelligence and Analytics**
**Team Activities:**
- [ ] Advanced analytics and reporting system
- [ ] Revenue attribution and ROI measurement
- [ ] Customer journey analysis and optimization
- [ ] Inventory planning and procurement recommendations
- [ ] **Client Demo #2**: Advanced features and business impact analysis

#### **Day 13-14: Production Launch and Business Results**
**Team Activities:**
- [ ] Production deployment with monitoring
- [ ] Load testing and performance validation
- [ ] Business impact measurement and ROI calculation
- [ ] User training and documentation delivery
- [ ] **Final Client Presentation**: Complete platform demonstration and business case
- [ ] **Team Retrospective**: Project learnings and improvement recommendations

---

## **Technical Requirements**

### **Core Technology Stack**
```python
# Machine Learning & AI
tensorflow>=2.13.0
torch>=2.0.0
scikit-learn>=1.3.0
lightgbm>=4.0.0
surprise>=1.1.3  # Recommendation systems

# Data Processing
pandas>=1.5.0
numpy>=1.24.0
dask>=2023.6.0
apache-airflow>=2.6.0

# Web Framework
fastapi>=0.100.0
nextjs>=13.0.0
react>=18.0.0
typescript>=5.0.0

# Database & Search
postgresql>=15.0
redis>=7.0.0
elasticsearch>=8.8.0
clickhouse>=23.0.0  # Analytics DB

# Cloud & Infrastructure
docker>=24.0.0
kubernetes>=1.27.0
aws-cli>=2.13.0
terraform>=1.5.0

# Monitoring & Analytics
prometheus>=2.45.0
grafana>=10.0.0
datadog>=0.46.0
```

### **E-commerce Platform Features**
- **Product Catalog**: Multi-category inventory with rich metadata
- **User Management**: Authentication, profiles, and preference management
- **Shopping Experience**: Cart, checkout, payment integration
- **Order Management**: Fulfillment, shipping, and customer service
- **Analytics**: Real-time tracking and business intelligence

### **Performance Requirements**
- **Recommendation Latency**: <50ms for real-time personalization
- **Search Performance**: <100ms for product search queries
- **Platform Uptime**: 99.9% availability with fault tolerance
- **Concurrent Users**: Support 10,000+ simultaneous shoppers
- **Data Processing**: Real-time processing of customer interactions

---

## **Assessment Criteria**

### **Technical Excellence (30 points)**
- **ML Model Performance**: Recommendation accuracy and business impact (15 pts)
- **System Scalability**: Performance under load and scalability design (10 pts)
- **Code Quality**: Architecture, testing, and maintainability (5 pts)

### **Business Impact (30 points)**
- **Revenue Optimization**: Measurable improvement in conversion and sales (15 pts)
- **Customer Experience**: User engagement and satisfaction metrics (10 pts)
- **Operational Efficiency**: Process automation and cost reduction (5 pts)

### **Client Delivery (25 points)**
- **Requirements Fulfillment**: Meeting all specified business objectives (12 pts)
- **Communication**: Professional client interaction and presentation (8 pts)
- **Project Management**: Timeline adherence and quality delivery (5 pts)

### **Innovation and Excellence (15 points)**
- **Technical Innovation**: Novel solutions and advanced features (8 pts)
- **Business Insights**: Data-driven recommendations and strategic value (7 pts)

**Total**: 100 points  
**Team Success Threshold**: 85 points  
**Individual Excellence**: 80+ points for standout performance

---

## **Client Deliverables**

### **Technical Platform**
- [ ] **Production E-commerce Platform**: Fully functional online store
- [ ] **Recommendation Engine**: Personalized product recommendations
- [ ] **Analytics Dashboard**: Real-time business intelligence
- [ ] **Admin Portal**: Inventory, pricing, and customer management
- [ ] **Mobile Application**: Responsive mobile shopping experience

### **Business Intelligence**
- [ ] **Performance Report**: Conversion rates, revenue impact, and KPI improvements
- [ ] **Customer Insights**: Segmentation analysis and behavioral patterns
- [ ] **Inventory Optimization**: Demand forecasting and purchasing recommendations
- [ ] **ROI Analysis**: Cost-benefit analysis and revenue attribution
- [ ] **Growth Strategy**: Data-driven recommendations for business expansion

### **Operational Deliverables**
- [ ] **Implementation Guide**: Deployment and configuration procedures
- [ ] **User Training**: Staff training materials and onboarding process
- [ ] **Maintenance Plan**: Ongoing support and model update procedures
- [ ] **Integration Documentation**: Third-party system integration guides
- [ ] **Security Audit**: Compliance and security assessment report

---

## **Sample Implementation Framework**

### **Intelligent Recommendation Engine**
```python
import numpy as np
import pandas as pd
from sklearn.feature_extraction.text import TfidfVectorizer
from sklearn.metrics.pairwise import cosine_similarity
from surprise import Dataset, Reader, SVD, accuracy
from surprise.model_selection import train_test_split
import redis
import json
from typing import List, Dict, Tuple

class HybridRecommendationEngine:
    def __init__(self, redis_host='localhost', redis_port=6379):
        self.redis_client = redis.Redis(host=redis_host, port=redis_port, decode_responses=True)
        self.collaborative_model = SVD(n_factors=100, n_epochs=20, lr_all=0.005, reg_all=0.02)
        self.content_vectorizer = TfidfVectorizer(max_features=5000, stop_words='english')
        self.item_similarity_matrix = None
        self.user_profiles = {}
        
    def train_collaborative_filtering(self, ratings_df: pd.DataFrame):
        """Train collaborative filtering model using SVD"""
        reader = Reader(rating_scale=(1, 5))
        data = Dataset.load_from_df(ratings_df[['user_id', 'product_id', 'rating']], reader)
        
        trainset, testset = train_test_split(data, test_size=0.2, random_state=42)
        self.collaborative_model.fit(trainset)
        
        # Evaluate model
        predictions = self.collaborative_model.test(testset)
        rmse = accuracy.rmse(predictions, verbose=False)
        print(f"Collaborative Filtering RMSE: {rmse}")
        
        return rmse
    
    def build_content_similarity(self, products_df: pd.DataFrame):
        """Build content-based similarity matrix"""
        # Combine product features into text
        products_df['content'] = (
            products_df['category'].fillna('') + ' ' +
            products_df['brand'].fillna('') + ' ' +
            products_df['description'].fillna('') + ' ' +
            products_df['tags'].fillna('')
        )
        
        # Create TF-IDF matrix
        tfidf_matrix = self.content_vectorizer.fit_transform(products_df['content'])
        
        # Calculate similarity matrix
        self.item_similarity_matrix = cosine_similarity(tfidf_matrix)
        
        # Cache product metadata
        for idx, row in products_df.iterrows():
            product_data = {
                'product_id': row['product_id'],
                'category': row['category'],
                'brand': row['brand'],
                'price': row['price'],
                'rating': row.get('avg_rating', 0),
                'popularity': row.get('popularity_score', 0)
            }
            self.redis_client.setex(
                f"product:{row['product_id']}", 
                3600, 
                json.dumps(product_data)
            )
    
    def get_collaborative_recommendations(self, user_id: str, n_recommendations: int = 10) -> List[Tuple[str, float]]:
        """Get recommendations using collaborative filtering"""
        # Get all products user hasn't rated
        user_rated_products = self._get_user_rated_products(user_id)
        all_products = self._get_all_products()
        unrated_products = [p for p in all_products if p not in user_rated_products]
        
        # Predict ratings for unrated products
        predictions = []
        for product_id in unrated_products:
            predicted_rating = self.collaborative_model.predict(user_id, product_id).est
            predictions.append((product_id, predicted_rating))
        
        # Sort by predicted rating and return top N
        predictions.sort(key=lambda x: x[1], reverse=True)
        return predictions[:n_recommendations]
    
    def get_content_recommendations(self, user_id: str, n_recommendations: int = 10) -> List[Tuple[str, float]]:
        """Get recommendations using content-based filtering"""
        # Get user's purchase/rating history
        user_products = self._get_user_rated_products(user_id)
        
        if not user_products:
            return self._get_popular_products(n_recommendations)
        
        # Calculate average similarity to user's preferred products
        product_scores = {}
        product_indices = {pid: idx for idx, pid in enumerate(self._get_all_products())}
        
        for product_id in self._get_all_products():
            if product_id not in user_products:
                similarity_scores = []
                product_idx = product_indices.get(product_id)
                
                if product_idx is not None:
                    for user_product in user_products:
                        user_product_idx = product_indices.get(user_product)
                        if user_product_idx is not None:
                            similarity = self.item_similarity_matrix[product_idx][user_product_idx]
                            similarity_scores.append(similarity)
                
                if similarity_scores:
                    avg_similarity = np.mean(similarity_scores)
                    product_scores[product_id] = avg_similarity
        
        # Sort by similarity score
        recommendations = sorted(product_scores.items(), key=lambda x: x[1], reverse=True)
        return recommendations[:n_recommendations]
    
    def get_hybrid_recommendations(self, user_id: str, n_recommendations: int = 10, 
                                 collaborative_weight: float = 0.6) -> List[Dict]:
        """Get hybrid recommendations combining collaborative and content-based"""
        # Get recommendations from both approaches
        collaborative_recs = self.get_collaborative_recommendations(user_id, n_recommendations * 2)
        content_recs = self.get_content_recommendations(user_id, n_recommendations * 2)
        
        # Normalize scores
        collab_scores = {pid: score for pid, score in collaborative_recs}
        content_scores = {pid: score for pid, score in content_recs}
        
        # Combine scores
        all_products = set(collab_scores.keys()) | set(content_scores.keys())
        hybrid_scores = {}
        
        for product_id in all_products:
            collab_score = collab_scores.get(product_id, 0)
            content_score = content_scores.get(product_id, 0)
            
            # Weighted combination
            hybrid_score = (collaborative_weight * collab_score + 
                          (1 - collaborative_weight) * content_score)
            hybrid_scores[product_id] = hybrid_score
        
        # Sort and get top recommendations
        sorted_recommendations = sorted(hybrid_scores.items(), key=lambda x: x[1], reverse=True)
        
        # Enrich with product details and explanation
        recommendations = []
        for product_id, score in sorted_recommendations[:n_recommendations]:
            product_data = self._get_product_details(product_id)
            recommendation = {
                'product_id': product_id,
                'score': score,
                'product_name': product_data.get('name', ''),
                'category': product_data.get('category', ''),
                'brand': product_data.get('brand', ''),
                'price': product_data.get('price', 0),
                'image_url': product_data.get('image_url', ''),
                'explanation': self._generate_explanation(user_id, product_id, collab_score, content_score)
            }
            recommendations.append(recommendation)
        
        return recommendations
    
    def get_real_time_recommendations(self, user_id: str, current_product_id: str = None, 
                                    context: Dict = None) -> List[Dict]:
        """Get real-time recommendations based on current context"""
        # Start with hybrid recommendations
        base_recommendations = self.get_hybrid_recommendations(user_id, 20)
        
        # Apply contextual filtering
        if current_product_id:
            # Add similar products to current viewing
            similar_products = self._get_similar_products(current_product_id, limit=5)
            base_recommendations.extend(similar_products)
        
        if context:
            # Apply contextual filters
            if context.get('category'):
                base_recommendations = [
                    rec for rec in base_recommendations 
                    if rec['category'] == context['category']
                ]
            
            if context.get('price_range'):
                min_price, max_price = context['price_range']
                base_recommendations = [
                    rec for rec in base_recommendations 
                    if min_price <= rec['price'] <= max_price
                ]
            
            if context.get('brand_preference'):
                preferred_brands = context['brand_preference']
                base_recommendations = [
                    rec for rec in base_recommendations 
                    if rec['brand'] in preferred_brands
                ]
        
        # Re-rank based on business rules
        final_recommendations = self._apply_business_rules(base_recommendations, user_id)
        
        return final_recommendations[:10]
    
    def _apply_business_rules(self, recommendations: List[Dict], user_id: str) -> List[Dict]:
        """Apply business rules for recommendation ranking"""
        user_segment = self._get_user_segment(user_id)
        
        for rec in recommendations:
            # Boost high-margin products
            if rec.get('margin_percent', 0) > 40:
                rec['score'] *= 1.2
            
            # Boost products with high inventory
            if rec.get('inventory_level', 0) > 100:
                rec['score'] *= 1.1
            
            # Boost trending products
            if rec.get('trending_score', 0) > 0.8:
                rec['score'] *= 1.15
            
            # Segment-specific boosting
            if user_segment == 'premium' and rec['price'] > 100:
                rec['score'] *= 1.1
            elif user_segment == 'budget' and rec['price'] < 50:
                rec['score'] *= 1.1
        
        # Sort by adjusted scores
        recommendations.sort(key=lambda x: x['score'], reverse=True)
        return recommendations
    
    def track_user_interaction(self, user_id: str, product_id: str, 
                             interaction_type: str, context: Dict = None):
        """Track user interactions for model improvement"""
        interaction_data = {
            'user_id': user_id,
            'product_id': product_id,
            'interaction_type': interaction_type,  # view, click, cart, purchase
            'timestamp': pd.Timestamp.now().isoformat(),
            'context': context or {}
        }
        
        # Store in Redis for real-time updates
        self.redis_client.lpush(
            f"user_interactions:{user_id}", 
            json.dumps(interaction_data)
        )
        
        # Keep only last 100 interactions per user
        self.redis_client.ltrim(f"user_interactions:{user_id}", 0, 99)
        
        # Update user profile
        self._update_user_profile(user_id, product_id, interaction_type)
    
    def _update_user_profile(self, user_id: str, product_id: str, interaction_type: str):
        """Update user profile based on interactions"""
        profile_key = f"user_profile:{user_id}"
        profile = self.redis_client.get(profile_key)
        
        if profile:
            profile_data = json.loads(profile)
        else:
            profile_data = {
                'categories': {},
                'brands': {},
                'price_range': {'min': float('inf'), 'max': 0},
                'interaction_count': 0
            }
        
        # Get product details
        product_data = self._get_product_details(product_id)
        
        # Update category preferences
        category = product_data.get('category', 'unknown')
        profile_data['categories'][category] = profile_data['categories'].get(category, 0) + 1
        
        # Update brand preferences
        brand = product_data.get('brand', 'unknown')
        profile_data['brands'][brand] = profile_data['brands'].get(brand, 0) + 1
        
        # Update price range
        price = product_data.get('price', 0)
        if price > 0:
            profile_data['price_range']['min'] = min(profile_data['price_range']['min'], price)
            profile_data['price_range']['max'] = max(profile_data['price_range']['max'], price)
        
        # Increment interaction count
        profile_data['interaction_count'] += 1
        
        # Store updated profile
        self.redis_client.setex(profile_key, 7200, json.dumps(profile_data))  # 2 hours TTL
```

### **Demand Forecasting System**
```python
import pandas as pd
import numpy as np
from sklearn.preprocessing import StandardScaler
from sklearn.ensemble import RandomForestRegressor
from sklearn.metrics import mean_absolute_error, mean_squared_error
import lightgbm as lgb
from prophet import Prophet
import warnings
warnings.filterwarnings('ignore')

class DemandForecastingSystem:
    def __init__(self):
        self.models = {
            'prophet': Prophet(),
            'lightgbm': lgb.LGBMRegressor(random_state=42),
            'random_forest': RandomForestRegressor(n_estimators=100, random_state=42)
        }
        self.scalers = {}
        self.feature_importance = {}
        self.forecast_horizon = 30  # days
    
    def prepare_features(self, sales_data: pd.DataFrame, external_data: pd.DataFrame = None) -> pd.DataFrame:
        """Prepare features for demand forecasting"""
        df = sales_data.copy()
        
        # Ensure datetime index
        df['date'] = pd.to_datetime(df['date'])
        df = df.sort_values('date')
        
        # Time-based features
        df['day_of_week'] = df['date'].dt.dayofweek
        df['month'] = df['date'].dt.month
        df['quarter'] = df['date'].dt.quarter
        df['year'] = df['date'].dt.year
        df['is_weekend'] = (df['day_of_week'] >= 5).astype(int)
        df['is_month_start'] = df['date'].dt.is_month_start.astype(int)
        df['is_month_end'] = df['date'].dt.is_month_end.astype(int)
        
        # Lag features
        for lag in [1, 7, 14, 30]:
            df[f'sales_lag_{lag}'] = df.groupby('product_id')['sales'].shift(lag)
        
        # Rolling statistics
        for window in [7, 14, 30]:
            df[f'sales_rolling_mean_{window}'] = df.groupby('product_id')['sales'].rolling(window).mean().reset_index(0, drop=True)
            df[f'sales_rolling_std_{window}'] = df.groupby('product_id')['sales'].rolling(window).std().reset_index(0, drop=True)
        
        # Product-specific features
        product_stats = df.groupby('product_id')['sales'].agg(['mean', 'std', 'min', 'max']).reset_index()
        product_stats.columns = ['product_id', 'product_mean_sales', 'product_std_sales', 'product_min_sales', 'product_max_sales']
        df = df.merge(product_stats, on='product_id', how='left')
        
        # Seasonal features
        df['sales_season_factor'] = df.groupby(['product_id', 'month'])['sales'].transform('mean') / df['product_mean_sales']
        
        # Price elasticity features
        if 'price' in df.columns:
            df['price_change'] = df.groupby('product_id')['price'].pct_change()
            df['sales_price_ratio'] = df['sales'] / df['price']
        
        # External data integration
        if external_data is not None:
            df = df.merge(external_data, on='date', how='left')
        
        # Promotion and marketing features
        if 'promotion' in df.columns:
            df['promotion_lag_1'] = df.groupby('product_id')['promotion'].shift(1)
            df['promotion_lead_1'] = df.groupby('product_id')['promotion'].shift(-1)
        
        return df
    
    def train_prophet_model(self, df: pd.DataFrame, product_id: str) -> dict:
        """Train Prophet model for specific product"""
        product_data = df[df['product_id'] == product_id].copy()
        prophet_df = product_data[['date', 'sales']].rename(columns={'date': 'ds', 'sales': 'y'})
        
        # Initialize Prophet with business-specific parameters
        model = Prophet(
            daily_seasonality=False,
            weekly_seasonality=True,
            yearly_seasonality=True,
            changepoint_prior_scale=0.05,
            seasonality_prior_scale=10.0
        )
        
        # Add external regressors if available
        if 'price' in product_data.columns:
            model.add_regressor('price')
            prophet_df['price'] = product_data['price'].values
        
        if 'promotion' in product_data.columns:
            model.add_regressor('promotion')
            prophet_df['promotion'] = product_data['promotion'].values
        
        # Fit model
        model.fit(prophet_df)
        
        # Generate forecast
        future = model.make_future_dataframe(periods=self.forecast_horizon)
        if 'price' in prophet_df.columns:
            # Use last known price for future periods
            future['price'] = prophet_df['price'].iloc[-1]
        if 'promotion' in prophet_df.columns:
            future['promotion'] = 0  # Assume no promotions unless specified
        
        forecast = model.predict(future)
        
        return {
            'model': model,
            'forecast': forecast,
            'product_id': product_id
        }
    
    def train_ml_models(self, df: pd.DataFrame) -> dict:
        """Train machine learning models for demand forecasting"""
        # Prepare features
        feature_columns = [col for col in df.columns if col not in ['date', 'product_id', 'sales']]
        feature_columns = [col for col in feature_columns if not df[col].isna().all()]
        
        X = df[feature_columns].fillna(0)
        y = df['sales']
        
        # Split data (time-based split)
        split_date = df['date'].quantile(0.8)
        train_mask = df['date'] <= split_date
        
        X_train, X_test = X[train_mask], X[~train_mask]
        y_train, y_test = y[train_mask], y[~train_mask]
        
        # Scale features
        scaler = StandardScaler()
        X_train_scaled = scaler.fit_transform(X_train)
        X_test_scaled = scaler.transform(X_test)
        
        self.scalers['demand_forecast'] = scaler
        
        # Train models
        results = {}
        
        # LightGBM
        lgb_model = lgb.LGBMRegressor(
            n_estimators=500,
            learning_rate=0.05,
            max_depth=8,
            num_leaves=31,
            feature_fraction=0.8,
            bagging_fraction=0.8,
            random_state=42
        )
        lgb_model.fit(X_train, y_train)
        lgb_pred = lgb_model.predict(X_test)
        
        # Random Forest
        rf_model = RandomForestRegressor(
            n_estimators=200,
            max_depth=15,
            min_samples_split=5,
            min_samples_leaf=2,
            random_state=42
        )
        rf_model.fit(X_train, y_train)
        rf_pred = rf_model.predict(X_test)
        
        # Evaluate models
        lgb_mae = mean_absolute_error(y_test, lgb_pred)
        rf_mae = mean_absolute_error(y_test, rf_pred)
        
        results = {
            'lightgbm': {
                'model': lgb_model,
                'mae': lgb_mae,
                'predictions': lgb_pred
            },
            'random_forest': {
                'model': rf_model,
                'mae': rf_mae,
                'predictions': rf_pred
            },
            'feature_importance': {
                'lightgbm': dict(zip(feature_columns, lgb_model.feature_importances_)),
                'random_forest': dict(zip(feature_columns, rf_model.feature_importances_))
            }
        }
        
        self.models.update({
            'lightgbm': lgb_model,
            'random_forest': rf_model
        })
        
        return results
    
    def forecast_demand(self, product_id: str, horizon_days: int = 30, 
                       external_factors: dict = None) -> dict:
        """Generate demand forecast for specific product"""
        # Get historical data for the product
        historical_data = self._get_product_historical_data(product_id)
        
        if len(historical_data) < 30:
            return {'error': 'Insufficient historical data'}
        
        forecasts = {}
        
        # Prophet forecast
        prophet_result = self.train_prophet_model(historical_data, product_id)
        prophet_forecast = prophet_result['forecast'].tail(horizon_days)
        
        forecasts['prophet'] = {
            'forecast': prophet_forecast['yhat'].values,
            'lower_bound': prophet_forecast['yhat_lower'].values,
            'upper_bound': prophet_forecast['yhat_upper'].values,
            'dates': prophet_forecast['ds'].dt.date.values
        }
        
        # ML model forecast
        if 'lightgbm' in self.models:
            ml_forecast = self._generate_ml_forecast(product_id, horizon_days, external_factors)
            forecasts['lightgbm'] = ml_forecast
        
        # Ensemble forecast
        ensemble_forecast = self._create_ensemble_forecast(forecasts)
        
        # Add business insights
        insights = self._generate_business_insights(product_id, ensemble_forecast)
        
        return {
            'product_id': product_id,
            'horizon_days': horizon_days,
            'forecasts': forecasts,
            'ensemble_forecast': ensemble_forecast,
            'insights': insights,
            'confidence_score': self._calculate_forecast_confidence(forecasts)
        }
    
    def optimize_inventory(self, products: list, lead_time_days: int = 14, 
                          service_level: float = 0.95) -> dict:
        """Optimize inventory levels based on demand forecasts"""
        inventory_recommendations = {}
        
        for product_id in products:
            # Get demand forecast
            forecast_result = self.forecast_demand(product_id, horizon_days=lead_time_days + 30)
            
            if 'error' in forecast_result:
                continue
            
            forecast = forecast_result['ensemble_forecast']['forecast']
            forecast_std = np.std(forecast_result['ensemble_forecast']['uncertainty'])
            
            # Calculate safety stock
            from scipy.stats import norm
            z_score = norm.ppf(service_level)
            safety_stock = z_score * forecast_std * np.sqrt(lead_time_days)
            
            # Calculate reorder point and optimal order quantity
            average_demand = np.mean(forecast[:lead_time_days])
            reorder_point = (average_demand * lead_time_days) + safety_stock
            
            # Economic Order Quantity (EOQ) calculation
            current_inventory = self._get_current_inventory(product_id)
            holding_cost = self._get_holding_cost(product_id)
            ordering_cost = self._get_ordering_cost(product_id)
            
            if holding_cost > 0:
                eoq = np.sqrt((2 * average_demand * 365 * ordering_cost) / holding_cost)
            else:
                eoq = average_demand * 30  # 30-day supply as fallback
            
            # Recommendations
            inventory_recommendations[product_id] = {
                'current_inventory': current_inventory,
                'forecasted_demand': average_demand,
                'reorder_point': max(0, reorder_point),
                'optimal_order_quantity': max(1, eoq),
                'safety_stock': max(0, safety_stock),
                'days_of_inventory': current_inventory / max(average_demand, 1),
                'action': self._determine_inventory_action(current_inventory, reorder_point, eoq),
                'risk_level': self._assess_stockout_risk(current_inventory, forecast, lead_time_days)
            }
        
        return inventory_recommendations
    
    def _create_ensemble_forecast(self, forecasts: dict) -> dict:
        """Create ensemble forecast from multiple models"""
        if len(forecasts) == 1:
            return list(forecasts.values())[0]
        
        # Weight models based on historical accuracy
        weights = {
            'prophet': 0.4,
            'lightgbm': 0.6
        }
        
        ensemble_forecast = None
        total_weight = 0
        
        for model_name, forecast_data in forecasts.items():
            weight = weights.get(model_name, 0.5)
            forecast_values = forecast_data['forecast']
            
            if ensemble_forecast is None:
                ensemble_forecast = forecast_values * weight
            else:
                ensemble_forecast += forecast_values * weight
            
            total_weight += weight
        
        if total_weight > 0:
            ensemble_forecast /= total_weight
        
        # Calculate uncertainty
        forecast_std = np.std([f['forecast'] for f in forecasts.values()], axis=0)
        
        return {
            'forecast': ensemble_forecast,
            'lower_bound': ensemble_forecast - 1.96 * forecast_std,
            'upper_bound': ensemble_forecast + 1.96 * forecast_std,
            'uncertainty': forecast_std
        }
```

---

## **Extension Challenges (Optional)**

### **Advanced E-commerce Intelligence**
- Implement visual search and image-based recommendations
- Add voice commerce integration with NLP
- Create augmented reality product visualization
- Build social commerce and influencer integration

### **Advanced Analytics**
- Implement cohort analysis and customer lifetime value optimization
- Add multi-touch attribution modeling
- Create competitive intelligence and market analysis
- Build supply chain optimization with logistics intelligence

### **Enterprise Features**
- Implement multi-tenant architecture for B2B marketplaces
- Add international expansion features (currency, language, regulations)
- Create omnichannel integration (online, mobile, in-store)
- Build advanced fraud detection and risk management

---

## **Resources & References**

### **Documentation**
- [Surprise Recommendation Library](https://surprise.readthedocs.io/)
- [Facebook Prophet](https://facebook.github.io/prophet/)
- [LightGBM Documentation](https://lightgbm.readthedocs.io/)

### **E-commerce Datasets**
- [Amazon Product Data](http://jmcauley.ucsd.edu/data/amazon/)
- [Retail Data Analytics](https://www.kaggle.com/datasets/manjeetsingh/retaildataset)
- [Online Retail Dataset](https://www.kaggle.com/datasets/lakshmi25npathi/online-retail-dataset)

### **Research Papers**
- "Deep Neural Networks for YouTube Recommendations"
- "Wide & Deep Learning for Recommender Systems"
- "Real-time Personalization using Embeddings for Search Ranking"
