# sushi-sandwich-classifier

I've added detailed comments in the jupyter notebook also to showcase the reasoning.

Data:
402 Images of sandwich and 402 images of sushi. All images are of different sizes ranging from 199by199 to 1024by1024. 

Approach:
1.	Take random crop out of larger images to up-sample the data.
2.	Split the dataset in train-validation-test. I used 80%-12%-8%.
3.	Train your model. Validate. Then test once and report the results.
4.	Metrices involved are – Confusion matrix, Accuracy score, F1 score and ROC curve.


Procedure:
1.	I used transfer learning for this task. Initially I chose InceptionResnet to get the best results and achieved 92.93% accuracy 
with 0.899 F1 score. Although the scores are really good and it performs classification quite well, this model takes quite a lot of 
space and not very suitable for mobile applications. So I chose MobileNet next which is much lighter and suitable for this purpose.
2.	I first used the bottleneck weights (saved to reduce computational time) from the pre-trained model and added a fully connected 
layer on top of it to make prediction.  So the model was like pre-trained features – hidden layer-output layer. I later realized 
that adding the hidden layer only slows down the training and doesn’t add much to the performance of the model. So I discarded and 
hidden layer and directly made predictions from pre-trained features.
3.	After that I tried fine-tuning 2 of the frozen layers of the pre-trained network but that didn’t improve the model performance 
much so I discarded that idea. 
4.	Now to make predictions on new images I combined the pre-trained mobile-net weights and my top model.

Results:
An accuracy of 88% with F1-score of 0.84 and ROC-AUC of 0.93 indicates the success of the model. This also includes some of the images 
that doesnt contain sushi or sandwich but just the background i.e fillings,plate, spoon etc.Recalculating the accuracy metric by removing
such images lead to an accuracy score of ~95% and a better f1-score. Looking at the confusion matrix we can see that the system is not
biased towards any particular class as the misclassification rate is the same for both classes.

Conclusion and Improvements: 
1. I think using the random cropping in addition to other data augmentation techniques for training definitely made the model more 
robust. This is because if a model can predict a sandwich even in a badly taken (only corner of bread let’s say) pic, then it’s 
definitely worth it.
2. Get more data.
3. Apply fuzzy logic so that when presented with an image where the model is confused (not very high probability for sandwich or 
sushi e.g 0.45 or 0.63 etc.), it returns, “I don’t know” rather than sandwich or sushi. For example I included some images of 
background plates, spoon etc.  in my training set and the model returned “sandwich”. This is not good. Therefore fuzzy logic 
should help. As a customer I’d prefer a “I don’t know” rather than a wrong answer.
4. Given the resources, maybe train the full mobile-net model from scratch for Food related images; this will serve two purposes 
– more categories of food, better classification. This is because the pre-trained network is on IMAGENET where the images were of 
dogs, cats, etc.

Docker:

docker build -t sushi-sandwich-clf .


docker run -it -p 8888:8888 -v /Users/sharadjoshi/Desktop/Job\ applications/Cookpad/Assignment/Final/:/Cookpad/Assignment sushi-sandwich-clf jupyter notebook --ip=0.0.0.0 --allow-root



The jupyter notebook is in the Cookpad/Assignment folder.
