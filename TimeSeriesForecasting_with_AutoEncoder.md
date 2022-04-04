**Paper review on "Visual Time Series Forecasting: An Image-driven Approach" (Srijan Sood, Zhen Zeng, Naftali Cohen, Tucker Balch, and Manuela Veloso (J.P. Morgan AI Research)**

*by Edwin Sutrisno, March 2022.*

---

**Short Summary**

This paper challenges the traditional models in time-series forecasting such as the auto-regressive family models and stochastic models by reposing the problem as a purely image prediction problem. Univariate time-series data are converted into a 2-dimensional images which are then passed into an auto-encoder neural network. Each training sample has the last 25% of the data cropped by which the auto-encoder is trained to generate using the first 75% of data as input. The results show important benefits over traditional forecasting models in addition to a better accuracy.



**Motivation**

The output of time-series (TS) forecasts are typically consumed visually by end users as a reasoning to decide the next action rather than as the final answer. For example, a trader wants to see the forecast price of a stock before deciding to buy or sell. An equipment operator wants to see the trajectory of degradation to decide on a maintenance event. Traditional TS forecasting models produce pointwise output while human intuition seeks for visual information. Traditional TS models are parametric statistical models which require defining certain parameters beforehand specific to the data being analyzed. On the other hand, advances in the deep-learning networks have shown effective use cases in modeling images, in this case time-series patterns, without much knowledge of the underlying data. 



**Methodology**

1. Sample preparation
Time-series (TS) data is plotted in a 2D image where the x-axis is the time intervals and y-axis the values. The images are standardized into 80 x 80 pixels and the data points are represented by bright pixels (values close to 1) in a dark background (values close to 0). The image is normalized such that the pixel values in each column (time segment) sum to 1. 

2. Auto-encoder model
The architecture of the AE is 2D convolutional layers with a kernel size of 5x5, stride 2, and padding 2. All layers are followed by ReLU activation and batch normalization. The encoder part has 3 convolutional layers which transform an input image of 80 x 80 x 1 into 10 x 10 x 512. The decoder is just a mirror of the encoder. 

The model is trained to predict the future data by forcing the model to reconstruct to next 25% length of input data. 

3. Loss Function
The loss function is critical in telling the auto-encoder network of what the desired output should look like. By hiding the future 25% length of data from the input image, the AE is trained to extract important features in the previous data to predict the future. One challenge in evaluating the error of prediction out of a time-series image is that the information is concentrated on a thin curve, leaving the rest of the image dark. The learning will be very slow if all pixels are equally considered in the error propagation, so a method to measure dissimilarity of curves is used instead. Error is computed column-wise by comparing the distribution of the bright pixels in that column. The metric to measure dissimilarity is the Jensen-Shannon Divergence. More details is available in the paper. 


**Results**

A metric to evaluate accuracy that is common in object detection is the Intersection-over-Union (IoU) which means how much two images are matching if they are stacked. The paper made a case that IoU is the more relevant metric to evaluate accuracy compared to traditional metrics like Mean Absolute Error because they can mislead the user to accepting a low error number while the output shows no similarity to the ground truth.  

The visual AE is compared with other methods of Random Walk, a numerical 1D Auto Encoder, and ARIMA. Four types of datasets were used in the evaluation: harmonic data, Ornstein-Uhlenbeck (OU) data, ECG data, and financial stock data. The accuracy results are as follow:

![Figure7](/reference_figures/TSAE_Figure7.JPG)

To make the case that IoU is a better metric in assessing prediction accuracy over typical forecast metrics SMAPE (symmetric mean absolute percentage error) and MASE (mean absolute scaled error), consider the following prediction output and their corresponding metrics. Keep in mind that for IoU, the higher is the better and is scaled from 0-1.

![Figure8](/reference_figures/TSAE_Figure8.JPG)

For instance, in Figure 8i showcasing the harmonic data prediction, the traditional SMAPE and MASE metrics suggest that the numerical AE performed better than the visual AE, but a human visual inspection would suggest otherwise. Another case is in Figure 8ii, the SMAPE and MAPE metrics suggest that the NumAE and VisualAE had very similar performances followed closely behind by ARIMA, but a human visual inspection would clearly see that the VisualAE was the only model that captured the long term trend of the data. The IoU metric consistently agreed with these quality assessments.



**Discussion**

The proposed methodology is quite straight forward and surprisingly worked well despite not embedding LSTM into the network. This novel time-series forecasting approach can be used in forecasting the production of unconventional wells for Artificial Lift business, material cost forecasting for Planning & Supply Chain, and equipment efficiency degradation.


Link to download the paper: 

https://arxiv.org/abs/2011.09052
