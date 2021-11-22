<p align="center">    
<img src="./.github/logo.gif?sanitize=true">
<img src="https://img.shields.io/badge/Group9-Tran%20Quang%20%7C%20Le%20Minh%20Thinh%20%7C%20Tran%20Hoang%20Vinh%20%7C%20Nguyen%20Hoang%20Phuc%20%7C%20Nguyen%20Dinh%20Hoang%20Quy-blue?style=plastic"> <img src="https://img.shields.io/badge/Lecturer-Tran%20Minh%20Quang-orange?style=plastic">
<p>


```R
#Import library
require(dplyr)
require(ggplot2)
require(caTools)
require(ROSE)
require(smotefamily)
require(rpart)
require(rpart.plot)
require(caret)
```

## Tổng quan về dữ liệu

<p align="justify">Tập dữ liệu <b>Fraud Credit Card</b> biểu diễn <i>284 807</i> giao dịch trong 2 ngày ở Châu Âu vào tháng 9 năm 2013. Trong đó, theo điều tra gồm có <i>492</i> gian lận chiếm <i>0.172%</i> tổng các giao dịch. Gồm có 31 biến trong tập dữ liệu:</p>

- _**Time**_ : Thời gian giao dịch diễn ra tính từ giao dịch đầu tiên (seconds)
- <div>\(V_i\) (Với i = 1,2..., 28): Là các thông số sau khi phân tích PCA và ẩn đi thông tin</div>
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




```R
credit_dat$Class = as.factor(credit_dat$Class)
credit_card = credit_dat
```


```R
require(ggplot2)
p = ggplot(credit_dat,aes(Class))
summary(credit_dat$Class)
options(repr.plot.width = 6, repr.plot.height = 4, repr.plot.res = 200)
p + geom_bar(aes(y = (..count..)/sum(..count..),fill = Class)) +
scale_y_continuous(labels=scales::percent) +
ylab("Transtation") 
# scale_color_manual(values=c('dodgerblue2','red'))
```

<dl class=dl-inline><dt>0</dt><dd>284315</dd><dt>1</dt><dd>492</dd></dl>




<p align="center">    
<img src="./.github/output_5_1.png?sanitize=true">
<p>



```R
# No model
predictions = rep.int(0,nrow(credit_dat))
predictions = factor(predictions,levels=c(0,1))
```


```R
require(dplyr)
set.seed(1)
credit_dat = credit_card %>% sample_frac(0.1)
ggplot(credit_dat, aes(x=V1,y=V2,col=Class)) +
    geom_point() +
    theme_bw() +
    scale_color_manual(values=c('dodgerblue2','red'))
```


<p align="center">    
<img src="./.github/output_7_0.png?sanitize=true">
<p>


### Tách biệt dữ liệu ra thành 2 tập Train và Test


```R
require(caTools)
set.seed(1)
dat_smpl = sample.split(credit_dat$Class,SplitRat=0.8)
train_dat = subset(credit_dat,dat_smpl == T)
test_dat = subset(credit_dat,dat_smpl == F)
train_dim = dim(train_dat)
test_dim = dim(test_dat)
print(paste(c("Train Dataset: Records = ","Variables = "),train_dim))
print(paste(c("Test Dataset : Records =  ","Variables = "),test_dim))
```

    [1] "Train Dataset: Records =  22785" "Variables =  31"                
    [1] "Test Dataset : Records =   5696" "Variables =  31"                
    


```R
tmp = table(train_dat$Class)
n_total = tmp[1]
n_total
summary(dat_smpl)
```


<strong>0:</strong> 22750



       Mode   FALSE    TRUE 
    logical    5696   22785 


### Lấy mẫu ROS - Random Over Sampling


```R
require(ROSE)
n_legal = 22750
frac_legal = 0.5
n_total = n_legal/frac_legal
ovrsmpl_res = ovun.sample(
                        Class ~ .
                        , data = train_dat
                        , method = "over"
                        , N = n_total
                        , seed = 100
)
```


```R
ovrsmpl = ovrsmpl_res$data
table(ovrsmpl$Class)
ggplot(ovrsmpl, aes(x=V1,y=V2,col=Class)) +
    geom_point() +
    theme_bw() +
    scale_color_manual(values=c('dodgerblue2','red'))
```


    
        0     1 
    22750 22750 



<p align="center">    
<img src="./.github/output_13_1.png?sanitize=true">
<p>    
    


### Lấy mẫu RUS - Random Under Sampling


```R
n_fraud = 35
frac_fraud = 0.5
n_total = n_fraud/frac_fraud
undrsmpl_res = ovun.sample(
                        Class ~ .
                        , data = train_dat
                        , method = "under"
                        , N = n_total
                        , seed = 100
)

undrsmpl = undrsmpl_res$data
table(undrsmpl$Class)
ggplot(undrsmpl, aes(x=V1,y=V2,col=Class)) +
    geom_point() +
    theme_bw() +
    scale_color_manual(values=c('dodgerblue2','red'))
```


    
     0  1 
    35 35 



<p align="center">    
<img src="./.github/output_15_1.png?sanitize=true">
<p>
    


### Lấy mẫu kết hợp


```R
n_smpl = nrow(train_dat)
frac_fraud = 0.5
sampling_res = ovun.sample(
                        Class ~ .
                        , data = train_dat
                        , method = "both"
                        , N = n_smpl
                        , seed = 100
)
sampls = sampling_res$data
table(sampls$Class)
ggplot(sampls, aes(x=V1,y=V2,col=Class)) +
    geom_point() +
    theme_bw() +
    scale_color_manual(values=c('dodgerblue2','red'))
```


    
        0     1 
    11489 11296 



<p align="center">    
<img src="./.github/output_17_1.png?sanitize=true">
<p>
    


### Lấy mẫu SMOTE


```R
n0 = 22750
n1 = 35
r0 = 0.6
ntimes = ((1-r0)/r0)*(n0/n1)-1
```


```R
smote_smpl = SMOTE( X = train_dat[,-c(1,31)]
                    , target = train_dat$Class
                    , dup_size = ntimes )
smote = smote_smpl$data
colnames(smote)[30] <- 'Class'
table(smote$Class)
ggplot(smote, aes(x=V1,y=V2,col=Class)) +
    geom_point() +
    theme_bw() +
    scale_color_manual(values=c('dodgerblue2','red'))
```


    
        0     1 
    22750 15155 



<p align="center">    
<img src="./.github/output_20_1.png?sanitize=true">
<p>
    


## CART MODEL - Classification And Regression Trees


```R
CART_model = rpart(Class ~ ., smote)
rpart.plot(CART_model, extra = 0, type=5, tweak=1.2)
```


<p align="center">    
<img src="./.github/output_22_0.png?sanitize=true">
<p>
    



```R
# Predict fraud classe
predicted_val = predict(CART_model, test_dat, type = 'class')
head(predicted_val,24)
summary(predicted_val)
```

<details>
	<summary style=display:list-item;cursor:pointer>
		<strong>Levels</strong>:
	</summary>
	<ol class=list-inline><li>'0'</li><li>'1'</li></ol>
</details>



<dl class=dl-inline><dt>0</dt><dd>5615</dd><dt>1</dt><dd>81</dd></dl>




```R
summary(test_dat$Class)
```


<dl class=dl-inline><dt>0</dt><dd>5687</dd><dt>1</dt><dd>9</dd></dl>




```R
confusionMatrix(predicted_val,test_dat$Class)
```


    Confusion Matrix and Statistics
    
              Reference
    Prediction    0    1
             0 5614    1
             1   73    8
                                              
                   Accuracy : 0.987           
                     95% CI : (0.9837, 0.9898)
        No Information Rate : 0.9984          
        P-Value [Acc > NIR] : 1               
                                              
                      Kappa : 0.1754          
                                              
     Mcnemar's Test P-Value : <2e-16          
                                              
                Sensitivity : 0.98716         
                Specificity : 0.88889         
             Pos Pred Value : 0.99982         
             Neg Pred Value : 0.09877         
                 Prevalence : 0.99842         
             Detection Rate : 0.98560         
       Detection Prevalence : 0.98578         
          Balanced Accuracy : 0.93803         
                                              
           'Positive' Class : 0               
                                              



```R
### No SMOTE
```


```R
CART_model = rpart(Class ~ . , train_dat[-1])
rpart.plot(CART_model, extra= 0, type = 5, tweak = 1.2)

# Predict fraud classes
predicted_val = predict(CART_model, test_dat[,-1], type= 'class')
```


<p align="center">    
<img src="./.github/output_27_0.png?sanitize=true">
<p>
    



```R
predicted_val
```


<details>
	<summary style=display:list-item;cursor:pointer>
		<strong>Levels</strong>:
	</summary>
	<ol class=list-inline><li>'0'</li><li>'1'</li></ol>
</details>



```R
confusionMatrix(predicted_val,test_dat$Class)
```


    Confusion Matrix and Statistics
    
              Reference
    Prediction    0    1
             0 5682    0
             1    5    9
                                             
                   Accuracy : 0.9991         
                     95% CI : (0.998, 0.9997)
        No Information Rate : 0.9984         
        P-Value [Acc > NIR] : 0.11550        
                                             
                      Kappa : 0.7822         
                                             
     Mcnemar's Test P-Value : 0.07364        
                                             
                Sensitivity : 0.9991         
                Specificity : 1.0000         
             Pos Pred Value : 1.0000         
             Neg Pred Value : 0.6429         
                 Prevalence : 0.9984         
             Detection Rate : 0.9975         
       Detection Prevalence : 0.9975         
          Balanced Accuracy : 0.9996         
                                             
           'Positive' Class : 0              
                                             


### Testing với tập dữ liệu ban đầu


```R
# Predict fraud classe
predicted_val = predict(CART_model, credit_card, type = 'class')
head(predicted_val,24)
summary(predicted_val)
```


<details>
	<summary style=display:list-item;cursor:pointer>
		<strong>Levels</strong>:
	</summary>
	<ol class=list-inline><li>'0'</li><li>'1'</li></ol>
</details>



<dl class=dl-inline><dt>0</dt><dd>284312</dd><dt>1</dt><dd>495</dd></dl>




```R
confusionMatrix(predicted_val,credit_card$Class)
```


    Confusion Matrix and Statistics
    
              Reference
    Prediction      0      1
             0 284205    107
             1    110    385
                                              
                   Accuracy : 0.9992          
                     95% CI : (0.9991, 0.9993)
        No Information Rate : 0.9983          
        P-Value [Acc > NIR] : <2e-16          
                                              
                      Kappa : 0.7798          
                                              
     Mcnemar's Test P-Value : 0.892           
                                              
                Sensitivity : 0.9996          
                Specificity : 0.7825          
             Pos Pred Value : 0.9996          
             Neg Pred Value : 0.7778          
                 Prevalence : 0.9983          
             Detection Rate : 0.9979          
       Detection Prevalence : 0.9983          
          Balanced Accuracy : 0.8911          
                                              
           'Positive' Class : 0               
                                              

