# 2019ML-FinalProject
2019 1st semester Machine Learning Final Project at SNU
### Project Objective
![intro](https://github.com/blackco66/2019ML-FinalProject/blob/master/images/intro.JPG) 

### Project Specification

1. 모델 설명<br/>
Spatiotemporal prediction 을 위해서 가장 흔히 쓰이는 것이 Recurrent Neural Net 구조이다. 가장 기본적인 RNN 부터 시작해서 LSTM, GRU 등 여러 가지 모델들이 사용되어 왔다. Prediction을 구현하기 위한 대부분의 모델들은 공통적으로 long-term dependency 성능 문제와 vanishing gradient 문제를 겪기 때문에 이러한 문제들을 얼마나 효과적으로 해결하느냐가 모델의 성능에 굉장히 중요하다. 이번 프로젝트에서 우리의 모델은 크게 Autoencoder 모델과 prediction 모델로 나눌 수 있다. 오토인코더의 경우 Dense layer 와 Convolution layer을 사용하였고 prediction의 경우 LSTM을 사용하였다.
2. 모델 구조 <br/>
(1) Autoencoder <br/>
Encoder 부분에는 Dense layer 1층, decoder 부분에는 convolutional layer을 6층으로 구성하였다. 각각의 layer들의 dimension은 몇 번의 시행착오를 걸쳐 heuristic하게 결정하였다. <br/>
(2) LSTM  <br/>
Keras 의 LSTM을 RNN 구조의 기본 cell로 사용하였으며 좋은 성능을 위하여 각 time step마다 2개 깊이로 쌓았다. 더 많이 쌓을 수도 있지만 그럴 경우 vanishing gradient 문제가 발생하여 학습이 더뎌지는 현상이 발생하였다. 때문에 성능과 학습 속도 간의 tradeoff 관계를 고려했을 때 2개의 LSTM cell이 적당하다고 판단되었다. 마지막에는 Time distributed Dense layer를 추가하였다. 
3. 코드 설명<br/>
코드는 크게 오토인코더 정의 및 훈련, LSTM 레이어 정의 및 훈련, 결과물 저장으로 세 부분으로 나뉜다. 모델들을 훈련하기 위해선 model.fit이 써 있는 셀을 포함하여 모든 셀을 실행하면 된다. 훈련과정을 제외하고 inference만 하기 위해선 model.fit이 있는 셀을 제외하고 셀을 실행하면 된다. 결과물 저장은 마지막 3개의 셀에서 이뤄진다.





4. 실행결과 
- Text sequence35(randomly chosen) from 1st to 10th ↓
![result01](https://github.com/blackco66/2019ML-FinalProject/blob/master/images/result01.JPG) 
11th to 20th predicted results↑
- Text sequence50(randomly chosen) from 1st to 10th ↓
![result02](https://github.com/blackco66/2019ML-FinalProject/blob/master/images/result02.JPG)           
11th to 20th predicted results↑

5. 토의<br/>
모델의 더 좋은 성능과 효과적인 학습을 위해 여러 가지를 시도하였는데 그 중 몇 가지를 분석하고 앞으로 개선할 수 있는 방향에 대해서 생각해보았다. <br/>
(1) Encoder는 CNN layer로 구성하는 것 보단 fully connected의 방식이 mae, mse, ssim 값의 측면에서 본연의 모습을 더 잘 유지해줬다.<br/>
(2) Decoder를 [8, 8, 16] 에서 [64, 64, 3]으로 upsampling할 때 단순 interpolation에 의한 upsampling과 conv layer가 아닌 transpose convolution을 이용하여 upsampling과정에서 학습할 수 있는 여지를 주었다. <br/>
(3) Encoder의 output과 LSTM의 output의 distribution을 통일시키는 것이 학습에 도움이 되었다. 두 output의 distribution이 다른 경우보다 output을 tanh로 통일시켜주었을 때 학습이 더 빠르게 되는 경향이 있었다. <br/>
(4) .Encoder의 output을 decoder의 input으로 넣어줄 때 volume으로 바꿔주었는데 이 때 width, height, depth의 크기가 비슷하게끔 설정해주는 것이 학습이 더 잘되는 경향을 보였다. 예를 들어, 우리 모델에서는 encoder의 output이 크기1024짜리 1차원 벡터였는데 이를 (16, 16, 4) 나 (4, 4, 64) 보다는 (8, 8, 16)으로 바꿔주는 것이 학습이 더 잘 되었다. <br/>
(5) LSTM 구조에서 LSTM cell을 두개 쌓고 마지막에 Dense layer을 통과시켜주었더니 그렇지 않았을 때 보다 학습이 더 잘 되었다. LSTM의 raw output을 그대로 사용하기 보다는 조금 더 nonlinearity power을 줄 수 있는 Dense layer을 사용하였기 때문으로 생각된다. <br/>
(6) 실행 결과를 보면 뒤쪽 frame들의 prediction 결과들이 더 안 좋음을 알 수 있다. Long-term dependency 성능이 떨어지기 때문으로 해석되는 데 이를 개선하기 위해 LSTM을 더 깊이 쌓고 그와 동시에 gradient highway같은 구조를 추가적으로 구성할 수 있으면 훨씬 좋은 성능을 구현할 수 있을 것 같다. 
