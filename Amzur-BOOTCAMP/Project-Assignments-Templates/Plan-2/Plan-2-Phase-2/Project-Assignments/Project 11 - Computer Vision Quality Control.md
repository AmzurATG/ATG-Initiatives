# **Project 11: Computer Vision Quality Control**
## Sprint-Based Skill Development - Week 6

**Duration:** 1 week (Sprint 5)  
**Goal:** Build an automated quality control system using computer vision and deep learning for manufacturing defect detection  
**Technology Stack:** PyTorch/TensorFlow, OpenCV, YOLO, FastAPI, PostgreSQL, Docker, AWS/GCP

---

## **Project Overview**

### **Objective**
Develop a comprehensive computer vision system for automated quality control in manufacturing, capable of detecting defects, classifying products, and providing real-time feedback with high accuracy and speed for production line integration.

### **Learning Outcomes**
- Master computer vision and object detection techniques
- Implement deep learning models for defect detection and classification
- Build real-time image processing pipelines
- Create automated quality control workflows
- Deploy CV systems for industrial applications

---

## **Sprint-Based Development Approach**

**Philosophy**: Fast-paced, outcome-driven development ending in a **working demo, comprehensive README, and peer review**. Focus on building a production-ready quality control system.

### **Sprint Goal**: Automated Visual Quality Control Platform
**Total Sprint Duration**: 5-7 days  
**End Goal**: Deployed system processing images with >99% accuracy and <200ms latency per inspection

---

## **Daily Sprint Breakdown**

### **Day 1-2: Data Pipeline and Image Processing Foundation**
**Estimated Time**: 16-20 hours

#### **Sprint Deliverables:**
- [ ] **S5-D1-DATA-01**: Synthetic defect dataset generation
  - **Description**: Create diverse dataset with defects, annotations, and quality labels
  - **Estimated**: 180 minutes
- [ ] **S5-D1-PREPROCESS-01**: Image preprocessing pipeline
  - **Description**: Implement robust preprocessing with augmentation and normalization
  - **Estimated**: 150 minutes
- [ ] **S5-D1-AUGMENT-01**: Advanced data augmentation
  - **Description**: Create realistic augmentations for defect variations and lighting
  - **Estimated**: 120 minutes
- [ ] **S5-D1-ANNOTATION-01**: Automated annotation tools
  - **Description**: Build tools for efficient defect annotation and labeling
  - **Estimated**: 180 minutes
- [ ] **S5-D1-QUALITY-01**: Data quality validation
  - **Description**: Implement validation for image quality and annotation accuracy
  - **Estimated**: 150 minutes

#### **Daily Review Requirements:**
- [ ] **Data Quality Review**: Dataset diversity and annotation accuracy
- [ ] **Preprocessing Review**: Image enhancement and normalization effectiveness
- [ ] **Pipeline Architecture Review**: Data flow design and scalability

### **Day 3-4: Deep Learning Models and Object Detection**
**Estimated Time**: 16-20 hours

#### **Sprint Deliverables:**
- [ ] **S5-D3-DETECTION-01**: YOLO object detection implementation
  - **Description**: Implement YOLOv8/YOLOv9 for defect detection and localization
  - **Estimated**: 240 minutes
- [ ] **S5-D3-CLASSIFICATION-01**: ResNet classification model
  - **Description**: Build ResNet/EfficientNet for defect classification and severity
  - **Estimated**: 180 minutes
- [ ] **S5-D3-SEGMENTATION-01**: Semantic segmentation for precise defect mapping
  - **Description**: Implement U-Net/DeepLab for pixel-level defect segmentation
  - **Estimated**: 200 minutes
- [ ] **S5-D3-ENSEMBLE-01**: Model ensemble and fusion
  - **Description**: Create ensemble approach combining detection, classification, and segmentation
  - **Estimated**: 120 minutes
- [ ] **S5-D3-EVAL-01**: Comprehensive model evaluation
  - **Description**: Implement mAP, precision-recall, and IoU metrics for assessment
  - **Estimated**: 120 minutes

#### **Daily Review Requirements:**
- [ ] **Model Performance Review**: Accuracy metrics and defect detection capability
- [ ] **Architecture Review**: Model design and computational efficiency
- [ ] **Evaluation Methodology Review**: Metrics selection and validation approach

### **Day 5-6: Real-Time Processing and API Development**
**Estimated Time**: 16-20 hours

#### **Sprint Deliverables:**
- [ ] **S5-D5-API-01**: Real-time inspection API
  - **Description**: Build FastAPI service for real-time image processing and analysis
  - **Estimated**: 180 minutes
- [ ] **S5-D5-STREAM-01**: Video stream processing
  - **Description**: Implement real-time video processing for production line monitoring
  - **Estimated**: 200 minutes
- [ ] **S5-D5-BATCH-01**: Batch processing capabilities
  - **Description**: Build batch processing for historical analysis and quality reports
  - **Estimated**: 150 minutes
- [ ] **S5-D5-OPTIMIZE-01**: Model optimization and acceleration
  - **Description**: Implement TensorRT/ONNX optimization for faster inference
  - **Estimated**: 180 minutes
- [ ] **S5-D5-INTEGRATION-01**: Manufacturing system integration
  - **Description**: Build interfaces for PLC, SCADA, and manufacturing systems
  - **Estimated**: 150 minutes

#### **Daily Review Requirements:**
- [ ] **Performance Review**: Processing speed and real-time capability
- [ ] **Integration Review**: Manufacturing system compatibility
- [ ] **Optimization Review**: Model efficiency and resource utilization

### **Day 7: Dashboard, Reporting, and Production Deployment**
**Estimated Time**: 8-12 hours

#### **Sprint Deliverables:**
- [ ] **S5-D7-DASHBOARD-01**: Quality control monitoring dashboard
  - **Description**: Create real-time dashboard for quality metrics and defect tracking
  - **Estimated**: 180 minutes
- [ ] **S5-D7-ALERTS-01**: Automated alert and notification system
  - **Description**: Implement alerts for quality issues and system anomalies
  - **Estimated**: 120 minutes
- [ ] **S5-D7-REPORTS-01**: Quality analytics and reporting
  - **Description**: Generate automated quality reports and trend analysis
  - **Estimated**: 120 minutes
- [ ] **S5-D7-DEPLOY-01**: Production deployment with edge computing
  - **Description**: Deploy system to edge devices with cloud backup and monitoring
  - **Estimated**: 150 minutes
- [ ] **S5-D7-DOCS-01**: Comprehensive documentation and demo prep
  - **Description**: Technical docs, operation guides, and demonstration materials
  - **Estimated**: 90 minutes

#### **Final Review Requirements:**
- [ ] **Production Readiness Review**: System reliability and manufacturing integration
- [ ] **Dashboard Review**: User interface and quality monitoring capabilities
- [ ] **Documentation Review**: Technical and operational documentation completeness

---

## **Technical Requirements**

### **Core Technology Stack**
```python
# Computer Vision & Deep Learning
torch>=2.0.0
torchvision>=0.15.0
ultralytics>=8.0.0  # YOLOv8
opencv-python>=4.8.0
albumentations>=1.3.0
timm>=0.9.0  # Vision models

# Image Processing
pillow>=10.0.0
scikit-image>=0.21.0
imageio>=2.31.0
matplotlib>=3.7.0

# API and Services
fastapi>=0.100.0
uvicorn>=0.22.0
websockets>=11.0.0
aiofiles>=23.0.0

# Database and Storage
sqlalchemy>=2.0.0
psycopg2-binary>=2.9.0
minio>=7.1.0  # Object storage

# Optimization and Deployment
onnx>=1.14.0
onnxruntime>=1.15.0
tensorrt>=8.6.0  # NVIDIA optimization
docker>=6.1.0
```

### **Computer Vision Pipeline Features**
- **Defect Detection**: Multiple defect types (scratches, dents, discoloration, cracks)
- **Classification**: Severity levels (minor, major, critical) and defect categories
- **Localization**: Precise defect location with bounding boxes and segmentation
- **Quality Scoring**: Comprehensive quality scores and pass/fail decisions
- **Trend Analysis**: Quality trends and statistical process control

### **Performance Requirements**
- **Accuracy**: >99% defect detection with <0.1% false positive rate
- **Speed**: <200ms per image inspection including preprocessing
- **Throughput**: >100 images per second for production line integration
- **Uptime**: 99.9% system availability with fault tolerance

---

## **Assessment Criteria**

### **Computer Vision Excellence (35 points)**
- **Model Accuracy**: Detection precision, recall, and F1-score (20 pts)
- **Model Architecture**: Design sophistication and innovation (10 pts)
- **Processing Speed**: Real-time performance optimization (5 pts)

### **System Integration (30 points)**
- **Real-time Processing**: Video stream analysis and edge deployment (15 pts)
- **Manufacturing Integration**: PLC/SCADA connectivity and protocols (15 pts)

### **Quality Analytics (20 points)**
- **Dashboard Design**: User interface and monitoring capabilities (12 pts)
- **Reporting System**: Quality analytics and trend analysis (8 pts)

### **Production Features (15 points)**
- **Edge Deployment**: Local processing and cloud synchronization (8 pts)
- **Documentation**: Technical and operational guides (7 pts)

**Total**: 100 points  
**Sprint Success Threshold**: 80 points

---

## **Sprint Deliverables**

### **Working Demo Requirements**
- [ ] Real-time defect detection with live camera feed
- [ ] Interactive dashboard showing quality metrics and alerts
- [ ] Batch processing demonstration with quality reports
- [ ] Manufacturing system integration simulation
- [ ] Model performance benchmarks and accuracy validation

### **Comprehensive README**
- [ ] Computer vision methodology and model architecture
- [ ] Defect detection taxonomy and classification approach
- [ ] Real-time processing pipeline and optimization techniques
- [ ] Manufacturing integration guide and protocol documentation
- [ ] Quality metrics definition and business impact analysis

### **Peer Review Materials**
- [ ] Complete source code with modular CV pipeline
- [ ] Model weights and training notebooks
- [ ] Test suite including accuracy validation and performance tests
- [ ] Integration documentation and API specifications

---

## **Sample Implementation Framework**

### **Defect Detection Model**
```python
import torch
import torch.nn as nn
from ultralytics import YOLO
import cv2
import numpy as np
from torchvision import transforms
from typing import List, Dict, Tuple
import albumentations as A

class DefectDetectionPipeline:
    def __init__(self, config: dict):
        self.config = config
        self.device = torch.device('cuda' if torch.cuda.is_available() else 'cpu')
        
        # Load models
        self.yolo_model = YOLO('yolov8n.pt')  # Will be fine-tuned
        self.classification_model = None
        self.segmentation_model = None
        
        # Preprocessing transforms
        self.preprocess_transform = A.Compose([
            A.Resize(640, 640),
            A.Normalize(mean=[0.485, 0.456, 0.406], std=[0.229, 0.224, 0.225]),
            A.pytorch.ToTensorV2()
        ])
        
        # Quality thresholds
        self.quality_thresholds = {
            'minor_defect': 0.3,
            'major_defect': 0.6,
            'critical_defect': 0.8
        }
    
    def preprocess_image(self, image: np.ndarray) -> torch.Tensor:
        """Preprocess image for model inference"""
        # Image enhancement
        enhanced = self.enhance_image(image)
        
        # Apply transforms
        transformed = self.preprocess_transform(image=enhanced)['image']
        
        return transformed.unsqueeze(0).to(self.device)
    
    def enhance_image(self, image: np.ndarray) -> np.ndarray:
        """Enhance image quality for better defect detection"""
        # Histogram equalization
        if len(image.shape) == 3:
            # Convert to LAB color space
            lab = cv2.cvtColor(image, cv2.COLOR_BGR2LAB)
            l, a, b = cv2.split(lab)
            
            # Apply CLAHE to L channel
            clahe = cv2.createCLAHE(clipLimit=2.0, tileGridSize=(8, 8))
            l = clahe.apply(l)
            
            # Merge channels and convert back
            enhanced = cv2.merge([l, a, b])
            enhanced = cv2.cvtColor(enhanced, cv2.COLOR_LAB2BGR)
        else:
            # Grayscale image
            clahe = cv2.createCLAHE(clipLimit=2.0, tileGridSize=(8, 8))
            enhanced = clahe.apply(image)
        
        # Gaussian blur for noise reduction
        enhanced = cv2.GaussianBlur(enhanced, (3, 3), 0)
        
        return enhanced
    
    def detect_defects(self, image: np.ndarray) -> Dict:
        """Comprehensive defect detection using multiple models"""
        preprocessed = self.preprocess_image(image)
        
        # YOLO detection
        yolo_results = self.yolo_model(image, conf=0.25, iou=0.45)
        detections = self.parse_yolo_results(yolo_results[0])
        
        # Classification for each detection
        classified_defects = []
        for detection in detections:
            roi = self.extract_roi(image, detection['bbox'])
            defect_class, confidence = self.classify_defect(roi)
            
            detection.update({
                'defect_type': defect_class,
                'severity': self.determine_severity(confidence, detection['area']),
                'classification_confidence': confidence
            })
            classified_defects.append(detection)
        
        # Segmentation for precise defect mapping
        segmentation_mask = self.segment_defects(image, classified_defects)
        
        # Quality assessment
        quality_score = self.calculate_quality_score(classified_defects)
        pass_fail = quality_score > self.config['quality_threshold']
        
        return {
            'detections': classified_defects,
            'segmentation_mask': segmentation_mask,
            'quality_score': quality_score,
            'pass_fail': pass_fail,
            'total_defects': len(classified_defects),
            'defect_summary': self.summarize_defects(classified_defects)
        }
    
    def parse_yolo_results(self, results) -> List[Dict]:
        """Parse YOLO detection results"""
        detections = []
        
        if results.boxes is not None:
            boxes = results.boxes.xyxy.cpu().numpy()
            confidences = results.boxes.conf.cpu().numpy()
            classes = results.boxes.cls.cpu().numpy()
            
            for box, conf, cls in zip(boxes, confidences, classes):
                x1, y1, x2, y2 = box
                area = (x2 - x1) * (y2 - y1)
                
                detections.append({
                    'bbox': [int(x1), int(y1), int(x2), int(y2)],
                    'confidence': float(conf),
                    'class_id': int(cls),
                    'area': float(area)
                })
        
        return detections
    
    def classify_defect(self, roi: np.ndarray) -> Tuple[str, float]:
        """Classify defect type and severity"""
        if self.classification_model is None:
            # Simple rule-based classification for demo
            gray = cv2.cvtColor(roi, cv2.COLOR_BGR2GRAY)
            
            # Calculate texture features
            mean_intensity = np.mean(gray)
            std_intensity = np.std(gray)
            
            # Simple classification rules
            if std_intensity > 50:
                return "scratch", 0.8
            elif mean_intensity < 100:
                return "dark_spot", 0.7
            elif mean_intensity > 200:
                return "bright_spot", 0.6
            else:
                return "surface_defect", 0.5
        
        # TODO: Implement deep learning classification
        # roi_tensor = self.preprocess_image(roi)
        # with torch.no_grad():
        #     output = self.classification_model(roi_tensor)
        #     class_probs = torch.softmax(output, dim=1)
        #     class_id = torch.argmax(class_probs, dim=1).item()
        #     confidence = class_probs[0, class_id].item()
        
        return "unknown", 0.5
    
    def segment_defects(self, image: np.ndarray, detections: List[Dict]) -> np.ndarray:
        """Generate precise segmentation mask for defects"""
        mask = np.zeros(image.shape[:2], dtype=np.uint8)
        
        for detection in detections:
            x1, y1, x2, y2 = detection['bbox']
            roi = image[y1:y2, x1:x2]
            
            # Simple threshold-based segmentation
            gray_roi = cv2.cvtColor(roi, cv2.COLOR_BGR2GRAY)
            _, thresh = cv2.threshold(gray_roi, 0, 255, cv2.THRESH_BINARY + cv2.THRESH_OTSU)
            
            # Morphological operations
            kernel = np.ones((3, 3), np.uint8)
            cleaned = cv2.morphologyEx(thresh, cv2.MORPH_CLOSE, kernel)
            
            # Place back in full mask
            mask[y1:y2, x1:x2] = cleaned
        
        return mask
    
    def calculate_quality_score(self, detections: List[Dict]) -> float:
        """Calculate overall quality score (0-1)"""
        if not detections:
            return 1.0
        
        severity_weights = {
            'minor': 0.1,
            'major': 0.5,
            'critical': 1.0
        }
        
        total_penalty = 0
        for detection in detections:
            severity = detection.get('severity', 'minor')
            area_factor = min(detection['area'] / 10000, 1.0)  # Normalize area
            penalty = severity_weights.get(severity, 0.5) * area_factor
            total_penalty += penalty
        
        # Convert penalty to quality score
        quality_score = max(0, 1.0 - total_penalty)
        return quality_score
    
    def determine_severity(self, confidence: float, area: float) -> str:
        """Determine defect severity based on confidence and area"""
        area_normalized = min(area / 10000, 1.0)
        severity_score = confidence * 0.7 + area_normalized * 0.3
        
        if severity_score > 0.8:
            return "critical"
        elif severity_score > 0.5:
            return "major"
        else:
            return "minor"
    
    def summarize_defects(self, detections: List[Dict]) -> Dict:
        """Summarize detected defects by type and severity"""
        summary = {
            'by_type': {},
            'by_severity': {'minor': 0, 'major': 0, 'critical': 0},
            'total_area': 0
        }
        
        for detection in detections:
            defect_type = detection.get('defect_type', 'unknown')
            severity = detection.get('severity', 'minor')
            area = detection.get('area', 0)
            
            # Count by type
            summary['by_type'][defect_type] = summary['by_type'].get(defect_type, 0) + 1
            
            # Count by severity
            summary['by_severity'][severity] += 1
            
            # Total defect area
            summary['total_area'] += area
        
        return summary
```

### **Real-Time Processing API**
```python
from fastapi import FastAPI, UploadFile, File, WebSocket, HTTPException
from fastapi.responses import StreamingResponse
import cv2
import numpy as np
import asyncio
import json
from typing import List, Dict
import base64
from datetime import datetime

app = FastAPI(title="Quality Control Vision API")

class QualityControlSystem:
    def __init__(self):
        self.defect_detector = DefectDetectionPipeline(config={
            'quality_threshold': 0.8,
            'min_defect_area': 100
        })
        self.active_inspections = {}
        self.quality_history = []
    
    async def process_image(self, image: np.ndarray, inspection_id: str) -> Dict:
        """Process single image for quality control"""
        start_time = datetime.now()
        
        # Detect defects
        results = self.defect_detector.detect_defects(image)
        
        # Add metadata
        processing_time = (datetime.now() - start_time).total_seconds() * 1000
        results.update({
            'inspection_id': inspection_id,
            'timestamp': start_time.isoformat(),
            'processing_time_ms': processing_time,
            'image_shape': image.shape
        })
        
        # Store in history
        self.quality_history.append(results)
        
        # Keep only last 1000 inspections
        if len(self.quality_history) > 1000:
            self.quality_history = self.quality_history[-1000:]
        
        return results
    
    async def process_video_stream(self, video_source: str):
        """Process video stream for real-time quality control"""
        cap = cv2.VideoCapture(video_source)
        
        try:
            while True:
                ret, frame = cap.read()
                if not ret:
                    break
                
                inspection_id = f"stream_{datetime.now().timestamp()}"
                results = await self.process_image(frame, inspection_id)
                
                # Draw annotations
                annotated_frame = self.draw_annotations(frame, results)
                
                # Encode frame for streaming
                _, buffer = cv2.imencode('.jpg', annotated_frame)
                frame_bytes = buffer.tobytes()
                
                yield (b'--frame\r\n'
                       b'Content-Type: image/jpeg\r\n\r\n' + frame_bytes + b'\r\n')
                
                await asyncio.sleep(0.033)  # ~30 FPS
        
        finally:
            cap.release()
    
    def draw_annotations(self, image: np.ndarray, results: Dict) -> np.ndarray:
        """Draw detection annotations on image"""
        annotated = image.copy()
        
        # Draw bounding boxes
        for detection in results['detections']:
            x1, y1, x2, y2 = detection['bbox']
            severity = detection.get('severity', 'minor')
            defect_type = detection.get('defect_type', 'unknown')
            
            # Color based on severity
            color_map = {
                'minor': (0, 255, 255),    # Yellow
                'major': (0, 165, 255),    # Orange
                'critical': (0, 0, 255)    # Red
            }
            color = color_map.get(severity, (128, 128, 128))
            
            # Draw bounding box
            cv2.rectangle(annotated, (x1, y1), (x2, y2), color, 2)
            
            # Draw label
            label = f"{defect_type} ({severity})"
            label_size = cv2.getTextSize(label, cv2.FONT_HERSHEY_SIMPLEX, 0.5, 2)[0]
            cv2.rectangle(annotated, (x1, y1 - label_size[1] - 10), 
                         (x1 + label_size[0], y1), color, -1)
            cv2.putText(annotated, label, (x1, y1 - 5), 
                       cv2.FONT_HERSHEY_SIMPLEX, 0.5, (255, 255, 255), 2)
        
        # Draw quality score
        quality_score = results['quality_score']
        pass_fail = "PASS" if results['pass_fail'] else "FAIL"
        score_color = (0, 255, 0) if results['pass_fail'] else (0, 0, 255)
        
        cv2.putText(annotated, f"Quality: {quality_score:.2f} ({pass_fail})", 
                   (10, 30), cv2.FONT_HERSHEY_SIMPLEX, 1, score_color, 2)
        
        return annotated

quality_system = QualityControlSystem()

@app.post("/inspect/image")
async def inspect_image(file: UploadFile = File(...)):
    """Inspect uploaded image for quality control"""
    try:
        # Read image
        contents = await file.read()
        nparr = np.frombuffer(contents, np.uint8)
        image = cv2.imdecode(nparr, cv2.IMREAD_COLOR)
        
        if image is None:
            raise HTTPException(status_code=400, detail="Invalid image format")
        
        # Process image
        inspection_id = f"upload_{datetime.now().timestamp()}"
        results = await quality_system.process_image(image, inspection_id)
        
        # Encode annotated image
        annotated = quality_system.draw_annotations(image, results)
        _, buffer = cv2.imencode('.jpg', annotated)
        annotated_b64 = base64.b64encode(buffer).decode()
        
        results['annotated_image'] = annotated_b64
        
        return results
        
    except Exception as e:
        raise HTTPException(status_code=500, detail=str(e))

@app.get("/inspect/stream")
async def stream_quality_control():
    """Stream real-time quality control from camera"""
    return StreamingResponse(
        quality_system.process_video_stream(0),  # Use default camera
        media_type="multipart/x-mixed-replace; boundary=frame"
    )

@app.websocket("/ws/quality-monitor")
async def quality_monitor_websocket(websocket: WebSocket):
    """WebSocket for real-time quality monitoring"""
    await websocket.accept()
    
    try:
        while True:
            # Send latest quality metrics
            recent_results = quality_system.quality_history[-10:] if quality_system.quality_history else []
            
            metrics = {
                'timestamp': datetime.now().isoformat(),
                'recent_inspections': len(recent_results),
                'pass_rate': sum(1 for r in recent_results if r['pass_fail']) / max(len(recent_results), 1),
                'average_quality': sum(r['quality_score'] for r in recent_results) / max(len(recent_results), 1),
                'defect_trends': calculate_defect_trends(recent_results)
            }
            
            await websocket.send_text(json.dumps(metrics))
            await asyncio.sleep(1)  # Update every second
            
    except Exception as e:
        print(f"WebSocket error: {e}")
    finally:
        await websocket.close()

@app.get("/analytics/quality-report")
async def get_quality_report(hours: int = 24):
    """Generate quality analytics report"""
    cutoff_time = datetime.now() - timedelta(hours=hours)
    
    # Filter recent data
    recent_data = [
        r for r in quality_system.quality_history 
        if datetime.fromisoformat(r['timestamp']) > cutoff_time
    ]
    
    if not recent_data:
        return {"message": "No data available for specified time range"}
    
    # Calculate metrics
    total_inspections = len(recent_data)
    passed_inspections = sum(1 for r in recent_data if r['pass_fail'])
    pass_rate = passed_inspections / total_inspections
    
    # Defect analysis
    all_defects = []
    for inspection in recent_data:
        all_defects.extend(inspection['detections'])
    
    defect_by_type = {}
    defect_by_severity = {'minor': 0, 'major': 0, 'critical': 0}
    
    for defect in all_defects:
        defect_type = defect.get('defect_type', 'unknown')
        severity = defect.get('severity', 'minor')
        
        defect_by_type[defect_type] = defect_by_type.get(defect_type, 0) + 1
        defect_by_severity[severity] += 1
    
    return {
        'report_period': f"Last {hours} hours",
        'total_inspections': total_inspections,
        'pass_rate': pass_rate,
        'total_defects': len(all_defects),
        'defects_by_type': defect_by_type,
        'defects_by_severity': defect_by_severity,
        'average_quality_score': sum(r['quality_score'] for r in recent_data) / total_inspections,
        'average_processing_time': sum(r['processing_time_ms'] for r in recent_data) / total_inspections
    }

def calculate_defect_trends(recent_results: List[Dict]) -> Dict:
    """Calculate defect trends over time"""
    if len(recent_results) < 2:
        return {"trend": "insufficient_data"}
    
    # Split into two halves
    mid_point = len(recent_results) // 2
    first_half = recent_results[:mid_point]
    second_half = recent_results[mid_point:]
    
    # Calculate defect rates
    first_defect_rate = sum(len(r['detections']) for r in first_half) / len(first_half)
    second_defect_rate = sum(len(r['detections']) for r in second_half) / len(second_half)
    
    # Determine trend
    if second_defect_rate > first_defect_rate * 1.1:
        trend = "increasing"
    elif second_defect_rate < first_defect_rate * 0.9:
        trend = "decreasing"
    else:
        trend = "stable"
    
    return {
        "trend": trend,
        "first_half_rate": first_defect_rate,
        "second_half_rate": second_defect_rate,
        "change_percentage": ((second_defect_rate - first_defect_rate) / max(first_defect_rate, 0.01)) * 100
    }
```

---

## **Extension Challenges (Optional)**

### **Advanced Computer Vision**
- Implement 3D defect detection using stereo vision
- Add thermal imaging integration for temperature-based defects
- Create multi-spectral analysis for material composition verification
- Build automated calibration and self-correction systems

### **Manufacturing Integration**
- Implement OPC-UA protocol for industrial communication
- Add predictive maintenance based on quality trends
- Create automatic rejection and sorting mechanisms
- Build integration with MES (Manufacturing Execution Systems)

### **AI/ML Enhancements**
- Implement few-shot learning for new defect types
- Add active learning for continuous model improvement
- Create explainable AI features for defect analysis
- Build anomaly detection for unknown defect patterns

---

## **Resources & References**

### **Documentation**
- [YOLOv8 Ultralytics](https://docs.ultralytics.com/)
- [OpenCV Computer Vision](https://docs.opencv.org/)
- [PyTorch Vision](https://pytorch.org/vision/stable/index.html)

### **Datasets**
- [MVTec Anomaly Detection](https://www.mvtec.com/company/research/datasets/mvtec-ad)
- [Steel Defect Detection](https://www.kaggle.com/c/severstal-steel-defect-detection)
- [PCB Defect Dataset](https://github.com/Ixiaohuihuihui/Tiny-Defect-Detection-for-PCB)

### **Research Papers**
- "Real-time Defect Detection in Manufacturing using Deep Learning"
- "Industrial Quality Control using Computer Vision"
- "Automated Visual Inspection Systems: A Survey"
