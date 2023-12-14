---
layout: page
title: Soil Type Prediction
description: binary logistic regression <i>from scratch</i>
img: assets/img/project/thumbnail/02.png
importance: 2
category: fun
related_publications: 
---

To demonstrate the capabilities and advantages of machine learning to my peers, I worked on an underutilized algorithm, binary classification, on a soil dataset. 
Without using the scikit-learn machine learning library, I developed a logistic regression code with the following objective in mind: 

>To prove how blowcounts can be used to predict soil type, with enough data. 

The training dataset was prepared using several data reduction and transformation techniques, such as feature selection, polynomial feature mapping, and normalization. 
By using a higher-order, non-linear model, the algorithm achieves training accuracy of about 90% with up to five training features: 
Depth, N-value, N<sub>6"</sub>, N<sub>12"</sub>, and N<sub>18"</sub>.

> **89.68%** accuracy with two features <br> 
>
> **91.27%** accuracy with three features <br>
>
> **90.48%** accuracy with five features <br>

A decision boundary for Depth and N-value are presented below to illustrate the class separation. This exercise is inspired by Andrew Ng's ML Speciliazation on Coursera. 
If you are interested in more details, please continue to read below.

<div class="row justify-content-sm-center">
    <div class="col-sm-8 mt-3 mt-md-0">
        {% include figure.html path="assets/img/project/plot_bc.jpg" class="img-fluid rounded z-depth-1" %}
    </div>
</div>
<div class="caption">
    Figure 1. Second order decision boundary (green) line separating Sand (y=1) and Clay (y=0).
</div>
<br>


***

### Motivations

Interpreting soil type and properties from in-situ testing and measurement data is one of the daily tasks in my field. The most uniquitous, or perhaps the first to be standardized (i.e., ASTM),
is the Standard Penetration Test (SPT). The SPT test method retrieves soil samples and blowcounts in three consecutive 6 inches, of which the blowcounts of the first 6 inches are neglected as disturbed.
The sum of the second and last blowcounts is termed the "standard penetration resistance," or N-value, which has been extensively studied and empirically correlated to the stiffness or density of specific soil types.
In this context, soil type refers to coarse- and fine-grained soils, i.e., sand and clay. Five (5) or "1-2-3" blowcount soil, for instance,
is regarded as loose sand or medium stiff clay. However, unless the recovered soil sample is visually categorized or verified through additional testing, it is unknown whether the soil type is sand or clay.
In practice, blowcounts are not used for determining soil type.

This project explored the potential of identifying soil type based on sampling depths and blowcounts, contrary to general practice. The logic behind this is that if drilling mud is used to lessen soil disturbance or stress relief,
the blowcounts, assuming that the SPT hammer system is properly calibrated and operated at the accepted 60% or higher energy ratio, would correlate strongly to soil type.
Depth is an important factor as it represents the state of stress of the soil element. In addition, the neglected blowcounts from the first 6 inches of spoon could potentially serve as a valuable
indicator for different soil types.


### Data Limitations

The dataset consists of nine sets of SPT data collected at a site within the San Francisco Bay Area. Groundwater was as shallow as 5 feet below ground surface (bgs) at this site.
The thickness of the artificial fill was in the range of 5 to 9 feet bgs, overlying marsh and alluvial deposits. These soil borings were drilled with the mud-rotary method to maximum depth of about 130 feet,
and the soil samples were logged by an experienced geologist or engineer. No bedrock was encountered in these borings. For the simplicity of this project, soils are categorized as either sand or clay.
Plotting the SPT N-value against the depth at which soil samples were collected, we see that clayey soils tend to have lower blowcounts compared to sandy soils with increasing depths.
This is reasonable given that natural clays submerged at shallow depth are very soft to soft, except one with an N-value of 14 at a depth of 5 feet bgs, which can be fill or clay crust.
At greater depths, blowcounts in clayey soil are still comparatively lower than sandy soil. 
Hint: the shallower and deeper geological clay deposits shown here are Young Bay Mud and Old Bay Clay, respectively.

<div class="row justify-content-sm-center">
    <div class="col-sm-8 mt-3 mt-md-0">
        {% include figure.html path="assets/img/project/plot_data.jpg" class="img-fluid rounded z-depth-1" %}
    </div>
</div>
<div class="caption">
    Figure 2. Depth plotted against SPT N-value: "-1" is used to represents the weight of hammer (WoH).
</div>


### Data Transformation

The logistic regression algorithm makes use of regularization and gradient descent to minimize the cost. Therefore, an inherent issue is numerical stability or convergence.
In order to better quantify the data, several extra steps are needed to prepare the training set prior to model training.

In very soft soil, a commonly encountered sampling condition is the weight of hammer (WoH). WoH represents the lowering of the sampling device to the sampling depth at which the combined weight
of the drilling rod(s) and soil sampler penetrate the whole 18 inches without driving the hammer. Therefore, no blowcount is noted. To distinguish the difference between WoH and zero (0-0-0)
blowcounts, WoH is set as a "-1" N-value in the dataset.

To further prepare training data for logistic regression, two other techniques were implemented: (i) normalization and (ii) feature mapping. Data normalization using z-score has been applied to
transform features to a similar scale to improve the performance and training stability of the model. In this context, "features" refer to variables of interest such as "depth" and "N-value".
Feature mapping is slightly more complicated and harder to explain. In brief, feature mapping refers to the transformation of training data from a lower-dimensional to a higher-dimensional space, where
it can be more effectively analyzed or classified. In other words, it nonlinearizes or converts data to higher order like X<sup>2</sup>, X<sup>3</sup> etc. One of the more tedious tasks is cracking
the feature mapping algorithm for multiple features, such as the [PolynomialFeatures( ) in sklearn](https://scikit-learn.org/stable/modules/generated/sklearn.preprocessing.PolynomialFeatures.html). 
Turns out that the PolynomialFeatures( ) simply loops through the specified "degree" with combinations function 
such as [itertools.combinations_with_replacement( )](https://docs.python.org/3/library/itertools.html#itertools.combinations_with_replacement): 


	import math
	import itertools

	X = [2,3,4]
	degree = 3
	res = []

	for i in range(1, degree+1):
		C = list(itertools.combinations_with_replacement(X, i)) 
		
		for j in range(len(C)):       
			res.append(math.prod(C[j]))

	print(res)
	print(len(res))

	# [2, 3, 4, 4, 6, 8, 9, 12, 16, 8, 12, 16, 18, 24, 32, 27, 36, 48, 64]
	# 19

	# degree = 1, 3 elems: [2, 3, 4]
	# degree = 2, 6 elems: [4, 6, 8, 9, 12, 16]
	# degree = 3, 10 elems: [8, 12, 16, 18, 24, 32, 27, 36, 48, 64]


***
Thumbnail photo credit: Makc76 <br>
Last update: 10/2023

