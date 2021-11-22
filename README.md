# Credit card


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


<style>
.dl-inline {width: auto; margin:0; padding: 0}
.dl-inline>dt, .dl-inline>dd {float: none; width: auto; display: inline-block}
.dl-inline>dt::after {content: ":\0020"; padding-right: .5ex}
.dl-inline>dt:not(:first-of-type) {padding-left: .5ex}
</style>
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


<style>
.dl-inline {width: auto; margin:0; padding: 0}
.dl-inline>dt, .dl-inline>dd {float: none; width: auto; display: inline-block}
.dl-inline>dt::after {content: ":\0020"; padding-right: .5ex}
.dl-inline>dt:not(:first-of-type) {padding-left: .5ex}
</style><dl class=dl-inline><dt>4</dt><dd>0</dd><dt>6</dt><dd>0</dd><dt>7</dt><dd>0</dd><dt>18</dt><dd>0</dd><dt>21</dt><dd>0</dd><dt>29</dt><dd>0</dd><dt>35</dt><dd>0</dd><dt>41</dt><dd>0</dd><dt>52</dt><dd>0</dd><dt>61</dt><dd>0</dd><dt>70</dt><dd>0</dd><dt>72</dt><dd>0</dd><dt>76</dt><dd>0</dd><dt>77</dt><dd>0</dd><dt>80</dt><dd>0</dd><dt>94</dt><dd>0</dd><dt>99</dt><dd>0</dd><dt>104</dt><dd>0</dd><dt>109</dt><dd>0</dd><dt>111</dt><dd>0</dd><dt>121</dt><dd>0</dd><dt>135</dt><dd>0</dd><dt>139</dt><dd>0</dd><dt>150</dt><dd>0</dd></dl>

<details>
	<summary style=display:list-item;cursor:pointer>
		<strong>Levels</strong>:
	</summary>
	<style>
	.list-inline {list-style: none; margin:0; padding: 0}
	.list-inline>li {display: inline-block}
	.list-inline>li:not(:last-child)::after {content: "\00b7"; padding: 0 .5ex}
	</style>
	<ol class=list-inline><li>'0'</li><li>'1'</li></ol>
</details>



<style>
.dl-inline {width: auto; margin:0; padding: 0}
.dl-inline>dt, .dl-inline>dd {float: none; width: auto; display: inline-block}
.dl-inline>dt::after {content: ":\0020"; padding-right: .5ex}
.dl-inline>dt:not(:first-of-type) {padding-left: .5ex}
</style><dl class=dl-inline><dt>0</dt><dd>5615</dd><dt>1</dt><dd>81</dd></dl>




```R
summary(test_dat$Class)
```


<style>
.dl-inline {width: auto; margin:0; padding: 0}
.dl-inline>dt, .dl-inline>dd {float: none; width: auto; display: inline-block}
.dl-inline>dt::after {content: ":\0020"; padding-right: .5ex}
.dl-inline>dt:not(:first-of-type) {padding-left: .5ex}
</style><dl class=dl-inline><dt>0</dt><dd>5687</dd><dt>1</dt><dd>9</dd></dl>




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


<style>
.dl-inline {width: auto; margin:0; padding: 0}
.dl-inline>dt, .dl-inline>dd {float: none; width: auto; display: inline-block}
.dl-inline>dt::after {content: ":\0020"; padding-right: .5ex}
.dl-inline>dt:not(:first-of-type) {padding-left: .5ex}
</style><dl class=dl-inline><dt>4</dt><dd>0</dd><dt>6</dt><dd>0</dd><dt>7</dt><dd>0</dd><dt>18</dt><dd>0</dd><dt>21</dt><dd>0</dd><dt>29</dt><dd>0</dd><dt>35</dt><dd>0</dd><dt>41</dt><dd>0</dd><dt>52</dt><dd>0</dd><dt>61</dt><dd>0</dd><dt>70</dt><dd>0</dd><dt>72</dt><dd>0</dd><dt>76</dt><dd>0</dd><dt>77</dt><dd>0</dd><dt>80</dt><dd>0</dd><dt>94</dt><dd>0</dd><dt>99</dt><dd>0</dd><dt>104</dt><dd>0</dd><dt>109</dt><dd>0</dd><dt>111</dt><dd>0</dd><dt>121</dt><dd>0</dd><dt>135</dt><dd>0</dd><dt>139</dt><dd>0</dd><dt>150</dt><dd>0</dd><dt>162</dt><dd>0</dd><dt>165</dt><dd>0</dd><dt>172</dt><dd>0</dd><dt>173</dt><dd>0</dd><dt>176</dt><dd>0</dd><dt>180</dt><dd>0</dd><dt>183</dt><dd>0</dd><dt>185</dt><dd>0</dd><dt>189</dt><dd>0</dd><dt>194</dt><dd>0</dd><dt>198</dt><dd>0</dd><dt>211</dt><dd>0</dd><dt>214</dt><dd>0</dd><dt>215</dt><dd>0</dd><dt>218</dt><dd>0</dd><dt>219</dt><dd>0</dd><dt>225</dt><dd>0</dd><dt>230</dt><dd>0</dd><dt>243</dt><dd>0</dd><dt>251</dt><dd>0</dd><dt>253</dt><dd>0</dd><dt>261</dt><dd>0</dd><dt>284</dt><dd>0</dd><dt>294</dt><dd>0</dd><dt>301</dt><dd>0</dd><dt>307</dt><dd>0</dd><dt>309</dt><dd>0</dd><dt>314</dt><dd>0</dd><dt>320</dt><dd>0</dd><dt>325</dt><dd>0</dd><dt>326</dt><dd>0</dd><dt>327</dt><dd>0</dd><dt>328</dt><dd>0</dd><dt>332</dt><dd>0</dd><dt>335</dt><dd>0</dd><dt>341</dt><dd>0</dd><dt>342</dt><dd>0</dd><dt>356</dt><dd>0</dd><dt>358</dt><dd>0</dd><dt>360</dt><dd>0</dd><dt>364</dt><dd>0</dd><dt>367</dt><dd>0</dd><dt>374</dt><dd>0</dd><dt>379</dt><dd>0</dd><dt>385</dt><dd>0</dd><dt>389</dt><dd>0</dd><dt>391</dt><dd>0</dd><dt>394</dt><dd>0</dd><dt>404</dt><dd>0</dd><dt>405</dt><dd>0</dd><dt>406</dt><dd>0</dd><dt>411</dt><dd>0</dd><dt>412</dt><dd>0</dd><dt>421</dt><dd>0</dd><dt>429</dt><dd>0</dd><dt>430</dt><dd>0</dd><dt>431</dt><dd>0</dd><dt>432</dt><dd>0</dd><dt>441</dt><dd>0</dd><dt>449</dt><dd>0</dd><dt>452</dt><dd>0</dd><dt>459</dt><dd>0</dd><dt>467</dt><dd>0</dd><dt>473</dt><dd>0</dd><dt>478</dt><dd>0</dd><dt>484</dt><dd>0</dd><dt>485</dt><dd>0</dd><dt>490</dt><dd>0</dd><dt>493</dt><dd>0</dd><dt>500</dt><dd>0</dd><dt>507</dt><dd>0</dd><dt>509</dt><dd>0</dd><dt>512</dt><dd>0</dd><dt>522</dt><dd>0</dd><dt>527</dt><dd>0</dd><dt>528</dt><dd>0</dd><dt>530</dt><dd>0</dd><dt>531</dt><dd>0</dd><dt>543</dt><dd>0</dd><dt>548</dt><dd>0</dd><dt>552</dt><dd>0</dd><dt>555</dt><dd>0</dd><dt>560</dt><dd>0</dd><dt>561</dt><dd>0</dd><dt>566</dt><dd>0</dd><dt>571</dt><dd>0</dd><dt>576</dt><dd>0</dd><dt>580</dt><dd>0</dd><dt>583</dt><dd>0</dd><dt>590</dt><dd>0</dd><dt>596</dt><dd>0</dd><dt>602</dt><dd>0</dd><dt>603</dt><dd>0</dd><dt>606</dt><dd>0</dd><dt>607</dt><dd>0</dd><dt>610</dt><dd>0</dd><dt>612</dt><dd>0</dd><dt>622</dt><dd>0</dd><dt>631</dt><dd>0</dd><dt>632</dt><dd>0</dd><dt>635</dt><dd>0</dd><dt>642</dt><dd>0</dd><dt>644</dt><dd>0</dd><dt>649</dt><dd>0</dd><dt>651</dt><dd>0</dd><dt>653</dt><dd>0</dd><dt>661</dt><dd>0</dd><dt>668</dt><dd>0</dd><dt>674</dt><dd>0</dd><dt>691</dt><dd>0</dd><dt>694</dt><dd>0</dd><dt>700</dt><dd>0</dd><dt>702</dt><dd>0</dd><dt>703</dt><dd>0</dd><dt>710</dt><dd>0</dd><dt>724</dt><dd>0</dd><dt>727</dt><dd>0</dd><dt>739</dt><dd>0</dd><dt>749</dt><dd>0</dd><dt>753</dt><dd>0</dd><dt>760</dt><dd>0</dd><dt>764</dt><dd>0</dd><dt>770</dt><dd>0</dd><dt>773</dt><dd>0</dd><dt>775</dt><dd>0</dd><dt>781</dt><dd>0</dd><dt>782</dt><dd>0</dd><dt>786</dt><dd>0</dd><dt>794</dt><dd>0</dd><dt>800</dt><dd>0</dd><dt>802</dt><dd>0</dd><dt>804</dt><dd>0</dd><dt>809</dt><dd>0</dd><dt>815</dt><dd>0</dd><dt>818</dt><dd>0</dd><dt>819</dt><dd>0</dd><dt>825</dt><dd>0</dd><dt>826</dt><dd>0</dd><dt>833</dt><dd>0</dd><dt>834</dt><dd>0</dd><dt>839</dt><dd>0</dd><dt>840</dt><dd>0</dd><dt>842</dt><dd>0</dd><dt>852</dt><dd>0</dd><dt>855</dt><dd>0</dd><dt>873</dt><dd>0</dd><dt>874</dt><dd>0</dd><dt>881</dt><dd>0</dd><dt>883</dt><dd>0</dd><dt>884</dt><dd>0</dd><dt>885</dt><dd>0</dd><dt>888</dt><dd>0</dd><dt>902</dt><dd>0</dd><dt>921</dt><dd>0</dd><dt>923</dt><dd>0</dd><dt>924</dt><dd>0</dd><dt>926</dt><dd>0</dd><dt>927</dt><dd>0</dd><dt>933</dt><dd>0</dd><dt>938</dt><dd>0</dd><dt>940</dt><dd>0</dd><dt>943</dt><dd>0</dd><dt>950</dt><dd>0</dd><dt>951</dt><dd>0</dd><dt>955</dt><dd>0</dd><dt>958</dt><dd>0</dd><dt>969</dt><dd>0</dd><dt>970</dt><dd>0</dd><dt>972</dt><dd>0</dd><dt>975</dt><dd>0</dd><dt>984</dt><dd>0</dd><dt>990</dt><dd>0</dd><dt>1005</dt><dd>0</dd><dt>1010</dt><dd>0</dd><dt>1012</dt><dd>0</dd><dt>1015</dt><dd>0</dd><dt>1017</dt><dd>...</dd><dt>1018</dt><dd>0</dd><dt>1023</dt><dd>0</dd><dt>1025</dt><dd>0</dd><dt>1035</dt><dd>0</dd><dt>1040</dt><dd>0</dd><dt>1051</dt><dd>0</dd><dt>1055</dt><dd>0</dd><dt>1056</dt><dd>0</dd><dt>1063</dt><dd>0</dd><dt>1064</dt><dd>0</dd><dt>1067</dt><dd>0</dd><dt>1070</dt><dd>0</dd><dt>1076</dt><dd>0</dd><dt>1087</dt><dd>0</dd><dt>1090</dt><dd>0</dd><dt>1101</dt><dd>0</dd><dt>1110</dt><dd>0</dd><dt>1125</dt><dd>0</dd><dt>1127</dt><dd>0</dd><dt>1132</dt><dd>0</dd><dt>1140</dt><dd>0</dd><dt>1144</dt><dd>0</dd><dt>1150</dt><dd>0</dd><dt>1152</dt><dd>0</dd><dt>1155</dt><dd>0</dd><dt>1156</dt><dd>0</dd><dt>1166</dt><dd>0</dd><dt>1168</dt><dd>0</dd><dt>1172</dt><dd>0</dd><dt>1186</dt><dd>0</dd><dt>1193</dt><dd>0</dd><dt>1197</dt><dd>0</dd><dt>1198</dt><dd>0</dd><dt>1204</dt><dd>0</dd><dt>1207</dt><dd>0</dd><dt>1209</dt><dd>0</dd><dt>1217</dt><dd>0</dd><dt>1218</dt><dd>0</dd><dt>1225</dt><dd>0</dd><dt>1230</dt><dd>0</dd><dt>1238</dt><dd>0</dd><dt>1247</dt><dd>0</dd><dt>1248</dt><dd>0</dd><dt>1249</dt><dd>0</dd><dt>1256</dt><dd>0</dd><dt>1261</dt><dd>0</dd><dt>1270</dt><dd>0</dd><dt>1276</dt><dd>0</dd><dt>1280</dt><dd>0</dd><dt>1282</dt><dd>0</dd><dt>1294</dt><dd>0</dd><dt>1295</dt><dd>0</dd><dt>1307</dt><dd>0</dd><dt>1308</dt><dd>0</dd><dt>1326</dt><dd>0</dd><dt>1333</dt><dd>0</dd><dt>1335</dt><dd>0</dd><dt>1341</dt><dd>0</dd><dt>1342</dt><dd>0</dd><dt>1346</dt><dd>0</dd><dt>1363</dt><dd>0</dd><dt>1364</dt><dd>0</dd><dt>1365</dt><dd>0</dd><dt>1376</dt><dd>0</dd><dt>1379</dt><dd>0</dd><dt>1381</dt><dd>0</dd><dt>1382</dt><dd>0</dd><dt>1385</dt><dd>0</dd><dt>1392</dt><dd>0</dd><dt>1398</dt><dd>0</dd><dt>1402</dt><dd>0</dd><dt>1407</dt><dd>0</dd><dt>1410</dt><dd>0</dd><dt>1421</dt><dd>0</dd><dt>1423</dt><dd>0</dd><dt>1425</dt><dd>0</dd><dt>1427</dt><dd>0</dd><dt>1434</dt><dd>0</dd><dt>1436</dt><dd>0</dd><dt>1439</dt><dd>0</dd><dt>1442</dt><dd>0</dd><dt>1443</dt><dd>0</dd><dt>1449</dt><dd>0</dd><dt>1459</dt><dd>0</dd><dt>1467</dt><dd>0</dd><dt>1472</dt><dd>0</dd><dt>1476</dt><dd>0</dd><dt>1477</dt><dd>0</dd><dt>1481</dt><dd>0</dd><dt>1489</dt><dd>0</dd><dt>1494</dt><dd>0</dd><dt>1497</dt><dd>0</dd><dt>1501</dt><dd>0</dd><dt>1502</dt><dd>0</dd><dt>1503</dt><dd>0</dd><dt>1506</dt><dd>0</dd><dt>1517</dt><dd>0</dd><dt>1518</dt><dd>0</dd><dt>1519</dt><dd>0</dd><dt>1526</dt><dd>0</dd><dt>1528</dt><dd>0</dd><dt>1530</dt><dd>0</dd><dt>1532</dt><dd>0</dd><dt>1534</dt><dd>0</dd><dt>1535</dt><dd>0</dd><dt>1539</dt><dd>0</dd><dt>1548</dt><dd>0</dd><dt>1549</dt><dd>0</dd><dt>1551</dt><dd>0</dd><dt>1557</dt><dd>0</dd><dt>1560</dt><dd>0</dd><dt>1562</dt><dd>0</dd><dt>1563</dt><dd>0</dd><dt>1567</dt><dd>0</dd><dt>1570</dt><dd>0</dd><dt>1572</dt><dd>0</dd><dt>1573</dt><dd>0</dd><dt>1578</dt><dd>0</dd><dt>1580</dt><dd>0</dd><dt>1584</dt><dd>0</dd><dt>1586</dt><dd>0</dd><dt>1587</dt><dd>0</dd><dt>1588</dt><dd>0</dd><dt>1596</dt><dd>0</dd><dt>1603</dt><dd>0</dd><dt>1608</dt><dd>0</dd><dt>1609</dt><dd>0</dd><dt>1619</dt><dd>0</dd><dt>1620</dt><dd>0</dd><dt>1624</dt><dd>0</dd><dt>1630</dt><dd>0</dd><dt>1632</dt><dd>0</dd><dt>1635</dt><dd>0</dd><dt>1647</dt><dd>0</dd><dt>1654</dt><dd>0</dd><dt>1659</dt><dd>0</dd><dt>1667</dt><dd>0</dd><dt>1669</dt><dd>0</dd><dt>1670</dt><dd>0</dd><dt>1675</dt><dd>0</dd><dt>1676</dt><dd>0</dd><dt>1677</dt><dd>0</dd><dt>1682</dt><dd>0</dd><dt>1685</dt><dd>0</dd><dt>1688</dt><dd>0</dd><dt>1689</dt><dd>0</dd><dt>1690</dt><dd>0</dd><dt>1692</dt><dd>0</dd><dt>1694</dt><dd>0</dd><dt>1697</dt><dd>0</dd><dt>1720</dt><dd>0</dd><dt>1727</dt><dd>0</dd><dt>1728</dt><dd>0</dd><dt>1732</dt><dd>0</dd><dt>1734</dt><dd>0</dd><dt>1735</dt><dd>0</dd><dt>1745</dt><dd>0</dd><dt>1746</dt><dd>0</dd><dt>1752</dt><dd>0</dd><dt>1759</dt><dd>0</dd><dt>1773</dt><dd>0</dd><dt>1775</dt><dd>0</dd><dt>1779</dt><dd>0</dd><dt>1789</dt><dd>0</dd><dt>1794</dt><dd>0</dd><dt>1799</dt><dd>0</dd><dt>1812</dt><dd>0</dd><dt>1822</dt><dd>0</dd><dt>1826</dt><dd>0</dd><dt>1833</dt><dd>0</dd><dt>1836</dt><dd>0</dd><dt>1838</dt><dd>0</dd><dt>1843</dt><dd>0</dd><dt>1846</dt><dd>0</dd><dt>1847</dt><dd>0</dd><dt>1848</dt><dd>0</dd><dt>1858</dt><dd>0</dd><dt>1860</dt><dd>0</dd><dt>1861</dt><dd>0</dd><dt>1862</dt><dd>0</dd><dt>1867</dt><dd>0</dd><dt>1875</dt><dd>0</dd><dt>1886</dt><dd>0</dd><dt>1889</dt><dd>0</dd><dt>1897</dt><dd>0</dd><dt>1899</dt><dd>0</dd><dt>1905</dt><dd>0</dd><dt>1908</dt><dd>0</dd><dt>1912</dt><dd>0</dd><dt>1917</dt><dd>0</dd><dt>1922</dt><dd>0</dd><dt>1926</dt><dd>0</dd><dt>1931</dt><dd>0</dd><dt>1934</dt><dd>0</dd><dt>1937</dt><dd>0</dd><dt>1940</dt><dd>0</dd><dt>1947</dt><dd>0</dd><dt>1952</dt><dd>0</dd><dt>1961</dt><dd>0</dd><dt>1974</dt><dd>0</dd></dl>

<details>
	<summary style=display:list-item;cursor:pointer>
		<strong>Levels</strong>:
	</summary>
	<style>
	.list-inline {list-style: none; margin:0; padding: 0}
	.list-inline>li {display: inline-block}
	.list-inline>li:not(:last-child)::after {content: "\00b7"; padding: 0 .5ex}
	</style>
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


<style>
.dl-inline {width: auto; margin:0; padding: 0}
.dl-inline>dt, .dl-inline>dd {float: none; width: auto; display: inline-block}
.dl-inline>dt::after {content: ":\0020"; padding-right: .5ex}
.dl-inline>dt:not(:first-of-type) {padding-left: .5ex}
</style><dl class=dl-inline><dt>1</dt><dd>0</dd><dt>2</dt><dd>0</dd><dt>3</dt><dd>0</dd><dt>4</dt><dd>0</dd><dt>5</dt><dd>0</dd><dt>6</dt><dd>0</dd><dt>7</dt><dd>0</dd><dt>8</dt><dd>0</dd><dt>9</dt><dd>0</dd><dt>10</dt><dd>0</dd><dt>11</dt><dd>0</dd><dt>12</dt><dd>0</dd><dt>13</dt><dd>0</dd><dt>14</dt><dd>0</dd><dt>15</dt><dd>0</dd><dt>16</dt><dd>0</dd><dt>17</dt><dd>0</dd><dt>18</dt><dd>0</dd><dt>19</dt><dd>0</dd><dt>20</dt><dd>0</dd><dt>21</dt><dd>0</dd><dt>22</dt><dd>0</dd><dt>23</dt><dd>0</dd><dt>24</dt><dd>0</dd></dl>

<details>
	<summary style=display:list-item;cursor:pointer>
		<strong>Levels</strong>:
	</summary>
	<style>
	.list-inline {list-style: none; margin:0; padding: 0}
	.list-inline>li {display: inline-block}
	.list-inline>li:not(:last-child)::after {content: "\00b7"; padding: 0 .5ex}
	</style>
	<ol class=list-inline><li>'0'</li><li>'1'</li></ol>
</details>



<style>
.dl-inline {width: auto; margin:0; padding: 0}
.dl-inline>dt, .dl-inline>dd {float: none; width: auto; display: inline-block}
.dl-inline>dt::after {content: ":\0020"; padding-right: .5ex}
.dl-inline>dt:not(:first-of-type) {padding-left: .5ex}
</style><dl class=dl-inline><dt>0</dt><dd>284312</dd><dt>1</dt><dd>495</dd></dl>




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
                                              

