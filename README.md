<p align="center">    
<img src="./.github/logo.gif?sanitize=true">
<img src="https://img.shields.io/badge/Group9-Tran%20Quang%20%7C%20Le%20Minh%20Thinh%20%7C%20Tran%20Hoang%20Vinh%20%7C%20Nguyen%20Hoang%20Phuc%20%7C%20Nguyen%20Dinh%20Hoang%20Quy-blue?style=plastic"> <img src="https://img.shields.io/badge/Lecturer-Tran%20Minh%20Quang-orange?style=plastic">
<p>

# Implementation

<p align="center">
<img src="./.github/implementation.svg?sanitize=true">
</p>

## Tổng quan về dữ liệu

<p align="justify">Tập dữ liệu <b>Fraud Credit Card</b> biểu diễn <i>284 807</i> giao dịch trong 2 ngày ở Châu Âu vào tháng 9 năm 2013. Trong đó, theo điều tra gồm có <i>492</i> gian lận chiếm <i>0.172%</i> tổng các giao dịch. Gồm có 31 biến trong tập dữ liệu:</p>

- _**Time**_ : Thời gian giao dịch diễn ra tính từ giao dịch đầu tiên (seconds)
- **Vi** (Với i = 1,2..., 28): Là các thông số sau khi phân tích PCA và ẩn đi thông tin
- _**Amount**_ : Giá trị giao dịch
- _**Class**_ : Gồm có hai giá trị (*0 - Giao dịch bình thường / 1 - Giao dịch gian lận*)

Tập dữ liệu download từ <a href="https://www.kaggle.com/mlg-ulb/creditcardfraud/version/3"><b>Kaggle</b></a>.</p>


```R
credit_dat <- read.csv('creditcard.csv')
head(credit_dat)
```


<table class="dataframe">
<caption>A data.frame: 6 × 31</caption>
<thead>
	<tr><th></th><th scope=col>Time</th><th scope=col>V1</th><th scope=col>V2</th><th scope=col>V3</th><th scope=col>V4</th><th scope=col>V5</th><th scope=col>V6</th><th scope=col>V7</th><th scope=col>V8</th><th scope=col>V9</th><th scope=col>...</th><th scope=col>V21</th><th scope=col>V22</th><th scope=col>V23</th><th scope=col>V24</th><th scope=col>V25</th><th scope=col>V26</th><th scope=col>V27</th><th scope=col>V28</th><th scope=col>Amount</th><th scope=col>Class</th></tr>
	<tr><th></th><th scope=col>&lt;dbl&gt;</th><th scope=col>&lt;dbl&gt;</th><th scope=col>&lt;dbl&gt;</th><th scope=col>&lt;dbl&gt;</th><th scope=col>&lt;dbl&gt;</th><th scope=col>&lt;dbl&gt;</th><th scope=col>&lt;dbl&gt;</th><th scope=col>&lt;dbl&gt;</th><th scope=col>&lt;dbl&gt;</th><th scope=col>&lt;dbl&gt;</th><th scope=col>...</th><th scope=col>&lt;dbl&gt;</th><th scope=col>&lt;dbl&gt;</th><th scope=col>&lt;dbl&gt;</th><th scope=col>&lt;dbl&gt;</th><th scope=col>&lt;dbl&gt;</th><th scope=col>&lt;dbl&gt;</th><th scope=col>&lt;dbl&gt;</th><th scope=col>&lt;dbl&gt;</th><th scope=col>&lt;dbl&gt;</th><th scope=col>&lt;int&gt;</th></tr>
</thead>
<tbody>
	<tr><th scope=row>1</th><td>0</td><td>-1.3598071</td><td>-0.07278117</td><td>2.5363467</td><td> 1.3781552</td><td>-0.33832077</td><td> 0.46238778</td><td> 0.23959855</td><td> 0.09869790</td><td> 0.3637870</td><td>...</td><td>-0.018306778</td><td> 0.277837576</td><td>-0.11047391</td><td> 0.06692807</td><td> 0.1285394</td><td>-0.1891148</td><td> 0.133558377</td><td>-0.02105305</td><td>149.62</td><td>0</td></tr>
	<tr><th scope=row>2</th><td>0</td><td> 1.1918571</td><td> 0.26615071</td><td>0.1664801</td><td> 0.4481541</td><td> 0.06001765</td><td>-0.08236081</td><td>-0.07880298</td><td> 0.08510165</td><td>-0.2554251</td><td>...</td><td>-0.225775248</td><td>-0.638671953</td><td> 0.10128802</td><td>-0.33984648</td><td> 0.1671704</td><td> 0.1258945</td><td>-0.008983099</td><td> 0.01472417</td><td>  2.69</td><td>0</td></tr>
	<tr><th scope=row>3</th><td>1</td><td>-1.3583541</td><td>-1.34016307</td><td>1.7732093</td><td> 0.3797796</td><td>-0.50319813</td><td> 1.80049938</td><td> 0.79146096</td><td> 0.24767579</td><td>-1.5146543</td><td>...</td><td> 0.247998153</td><td> 0.771679402</td><td> 0.90941226</td><td>-0.68928096</td><td>-0.3276418</td><td>-0.1390966</td><td>-0.055352794</td><td>-0.05975184</td><td>378.66</td><td>0</td></tr>
	<tr><th scope=row>4</th><td>1</td><td>-0.9662717</td><td>-0.18522601</td><td>1.7929933</td><td>-0.8632913</td><td>-0.01030888</td><td> 1.24720317</td><td> 0.23760894</td><td> 0.37743587</td><td>-1.3870241</td><td>...</td><td>-0.108300452</td><td> 0.005273597</td><td>-0.19032052</td><td>-1.17557533</td><td> 0.6473760</td><td>-0.2219288</td><td> 0.062722849</td><td> 0.06145763</td><td>123.50</td><td>0</td></tr>
	<tr><th scope=row>5</th><td>2</td><td>-1.1582331</td><td> 0.87773675</td><td>1.5487178</td><td> 0.4030339</td><td>-0.40719338</td><td> 0.09592146</td><td> 0.59294075</td><td>-0.27053268</td><td> 0.8177393</td><td>...</td><td>-0.009430697</td><td> 0.798278495</td><td>-0.13745808</td><td> 0.14126698</td><td>-0.2060096</td><td> 0.5022922</td><td> 0.219422230</td><td> 0.21515315</td><td> 69.99</td><td>0</td></tr>
	<tr><th scope=row>6</th><td>2</td><td>-0.4259659</td><td> 0.96052304</td><td>1.1411093</td><td>-0.1682521</td><td> 0.42098688</td><td>-0.02972755</td><td> 0.47620095</td><td> 0.26031433</td><td>-0.5686714</td><td>...</td><td>-0.208253515</td><td>-0.559824796</td><td>-0.02639767</td><td>-0.37142658</td><td>-0.2327938</td><td> 0.1059148</td><td> 0.253844225</td><td> 0.08108026</td><td>  3.67</td><td>0</td></tr>
</tbody>
</table>

## Phân bố tập dữ liệu

**Legit**: *284315*  
**Fraud**: *492*



<p align="center">    
<img src="./.github/output_5_1.png?sanitize=true">
<p>

### Biểu diễn phân bố điểm của dữ liệu theo chiều không gian (V1 và V2)

<p align="center">    
<img src="./.github/output_7_0.png?sanitize=true">
<p>

## Giải quyết vấn đề phân không cân bằng 
	
### Lấy mẫu ROS - Random Over Sampling

Phân bố dữ liệu:  
	
**Legit** : 22840  
**Fraud** : 22840  
	
<p align="center">    
<img src="./.github/output_13_1.png?sanitize=true">
<p>    
    

### Lấy mẫu RUS - Random Under Sampling

Phân bố dữ liệu :  
	
**Legit** : 35  
**Fraud** : 35

<p align="center">    
<img src="./.github/output_15_1.png?sanitize=true">
<p>

### Lấy mẫu kết hợp

Lấy mẫu kết hợp cả hai phương pháp RUS và ROS, giảm số lượng giao dịch thông thường trong dataset và tăng các giao dịch gian lận trong tập dữ liệu bằng cách sao chép dữ liệu.  
Kết quả thu được:  
**Legit** : 11489  
**Fraud** : 11296  

	
<p align="center">    
<img src="./.github/output_17_1.png?sanitize=true">
<p>
    

### Lấy mẫu SMOTE

Kết quả thu được:  

**Legit** : 22750  
**Fraud** : 15155  

<p align="center">    
<img src="./.github/output_20_1.png?sanitize=true">
<p>
   
## Xây dựng Mechine Learning Model

### CART MODEL - Classification And Regression Trees

<p align="center">    
<img src="./.github/output_22_0.png?sanitize=true">
<p>
    
**Legit** : 5615
**Fraud** : 81

Trong khi đó tập dữ liệu test:

**Legit** : 5687  
**Fraud** : 9  

## Đánh giá model - Quality Classification 

### Model Lấy Mẫu Kết Hợp

    Confusion Matrix and Statistics
    
              Reference
    Prediction    0    1
             0 5614    1
             1   73    8
                                              
                   Accuracy : 0.987           
                     95% CI : (0.9837, 0.9898)
        No Information Rate : 0.9984          
        P-Value [Acc > NIR] : 1               
                                              
     
                                              
Hệ số chính xác của model là **0.987**, dự đoán đúng được 8/9 trường hợp gian lận. Tuy nhiên, báo cáo sai 0.013% (73/5687) các trường hợp thông thường.


<p align="center">    
<img src="./.github/output_27_0.png?sanitize=true">
<p>
    

### Model Lấy mẫu SMOTE
	

    Confusion Matrix and Statistics
    
              Reference
    Prediction    0    1
             0 5682    0
             1    5    9
                                             
                   Accuracy : 0.9991         
                     95% CI : (0.998, 0.9997)   
                                             
 
Hệ số chính xác cao hơn hẳn model trước đó với **0.9991**, dự đoán đúng được tất cã trường hợp gian lận trong tập test. Tuy nhiên, vẫn xảy ra những báo động giả 5/5687 các trường hợp thông thường.

