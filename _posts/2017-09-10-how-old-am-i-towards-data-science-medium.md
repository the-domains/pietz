---
author:
  - name: Paul-Louis Pröve
    url: 'https://medium.com/@pietz'
    avatar: {}
related: []
publisher: {}
keywords: []
description: >-
  Can you guess how old the person is behind this knee MRI? I give you a hint.
  It’s a recording of a German male between the age of 14 and 21 years. No idea?
  I like to imagine that this is how a neural network sees the world before it
  has been trained. It has absolutely no clue. So, let me train you.
app_links:
  - type: android
    namespace: ai
    app_name: Medium
    app_store_id: '828256236'
    package: com.medium.reader
  - url: 'medium://p/f62538487d36'
    type: ios
    namespace: ai
    app_name: Medium
  - url: 'medium://p/f62538487d36'
    type: android
    namespace: ai
  - url: 'https://medium.com/towards-data-science/how-old-am-i-f62538487d36'
    type: web
    namespace: ai
  - url: 'medium://p/f62538487d36'
    namespace: twitter
    type: iphone
    name: Medium
    id: '828256236'
  - path: https/medium.com/p/f62538487d36
    package: com.medium.reader
    namespace: google
    type: android
title: How old am I?
datePublished: '2017-10-21T08:08:11.235Z'
dateModified: '2017-10-21T08:08:10.247Z'
via: {}
inFeed: true
sourcePath: _posts/2017-09-10-how-old-am-i-towards-data-science-medium.md
hasPage: true
starred: false
datePublishedOriginal: '2017-09-10T19:27:35.404Z'
url: how-old-am-i-towards-data-science-medium/index.html
_type: Article
_context: 'http://schema.org'

---
# How old am I?

### Age Assessment using 3D Knee MRIs and Convolutional Neural Networks
![](https://the-grid-user-content.s3-us-west-2.amazonaws.com/82062838-f452-4e9b-85e5-d8718f2a454a.gif)

Can you guess how old the person is behind this knee MRI? I give you a hint. It's a recording of a German male between the age of 14 and 21 years. No idea? I like to imagine that this is how a neural network sees the world before it has been trained. It has absolutely no clue. So, let me train you.

Determining the chronological age of a person is a complex procedure when dealing with people who lack legal documentation. This is often the case in asylum applications or criminal proceedings. Children and adults are processed differently under the law, but we simply don't have an accurate way of determining how old somebody is. The best approaches we use today are based on X-ray, which is an invasive imaging technique that exposes the patient to ionizing radiation. That's why many European countries only allow these recordings as part of a judicial order. Some other methods include personal interviews to determine psychological and physiological traits, but they are time-consuming and also subjective. That's why there is a high demand for an automated and noninvasive method for accurately determining the age of a person.

As part of a study for the German Research Foundation (DFG), I was introduced to a data set of 3D MRIs showing the right knee of German males. The Femur in the upper half of the images is the thighbone, and the Tibia right below is the shinbone. Towards the back of the recording, you can see another bone popping up in the lower left corner --- the Fibula. If you look even closer, you can spot dark horizontal lines in the bones that almost look like cracks. These are called growth plates because it's where your bones realize longitudinal growth. They consist of cartilage which is why they can be seen in MRI recordings. Once you stop growing, these regions will slowly start to close up until they are not visible anymore.
![](https://the-grid-user-content.s3-us-west-2.amazonaws.com/30725017-244d-4872-ba71-9482676a3a2f.jpg)
![](https://the-grid-user-content.s3-us-west-2.amazonaws.com/a5a335ef-7566-450f-b1a2-8394be17270c.jpg)
![](https://the-grid-user-content.s3-us-west-2.amazonaws.com/32c9b154-26fe-430b-86a3-e88bf986d916.jpg)

We used knee recordings for this study because the visible growth plates are known to close around the border to adulthood. Other studies use MRIs or X-rays of hands, where the growth plates are known to close earlier. In the images above you can see how the width of the dark horizontal gap correlates to the age of a person.

The individuals we recorded were between the age of 14 and 21 years.If we predicted the mean age of 17.5 for every person, we would never be off more than 3.5 years. In fact, we would be off by just 1.2 years on average because the data was normally distributed. This was the original baseline I tried to beat. I like to think of the baseline as the smartest stupid estimate you can make. A model that only predicts an age of 17.5 wouldshowa mean difference of 1.2 years. It would also be completely useless.
![](https://the-grid-user-content.s3-us-west-2.amazonaws.com/e26107e8-6b83-4ac9-98b9-38288fac91cd.png)

One of the first things you normally try in deep learning is a plug-and-play approach using a pre-trained architecture. This can often get you pretty far even if the original model was trained on a very different data set. I took my favorite transfer learning architecture VGG16 and weights it learned on ImageNet. However, I quickly understood that this wouldn't get me anywhere because the model didn't converge. I tried all kinds of other bells and whistles like far simpler architectures, special training procedures or turning the regression into a classification problem. Nothing worked.

So, I took a step back. Either the data shows no correlating features to the age or the problem is simply too complex for the size of my data set. Since the data consisted of only 145 images, the second possibility didn't seem so far off. In a situation like this you can do two things:

* get more training data
* reduce the complexity of the problem

In a perfect world the first option is definitely more desirable, but especially in the medical field it's sometimes not an option. We're under very strict privacy laws when it comes to medical data, which is a good thing unless you're working on a problem like this. So I decided to reduce the data complexity instead.

Lucky me, a diligent person working on the same project already labeled half of the data set with segmentation maps of the bones. It took him roughly 2 hours per sample using a semi automated approach based on region growing. Having these 76 masks, I set up a workflow that segmented the bones in 3D MRIs of human knees. I tried both 2D and 3D CNN architectures, but the 2D version showed more accurate results and was also much faster to train.
![](https://the-grid-user-content.s3-us-west-2.amazonaws.com/44307af0-48a4-4cf1-aa45-463339c4fdc1.png)

In the end, I got to **98.0% DSC **using a small variant of U-Net with a constant number of 32 output channels for every convolution. Things became a little problematic with the Fibula, the smallest bone, which is why I decided to train three separate models. I also tried a whole bunch of other things that you can read about in my thesis, but for now, I want to keep it at that.

By the way, aren't you surprised that this worked so well? I surely was at first. Think about it. I wasn't able to train a regressor on 145 samples, but a more complex segmentation on 76 samples worked flawlessly. Well, it turns out a segmentation can be seen as a classification for every pixel. That means for each sample I had _width\*height _information to back-propagate through the network. That's a lot more than one numeric value resulting from a simple regression.
![](https://the-grid-user-content.s3-us-west-2.amazonaws.com/c60a9acd-446e-40fb-9915-7e77458afc44.png)

With the masks applied to the input data, I got rid of all the non-bone tissue I didn't care about and was able to focus more clearly on the growth plates. I came back to the original problem but noticed quickly that it still wouldn't work. Only after taking the contracting side of my segmentation architecture and using it as a pre-trained model was I able to beat the baseline. This idea will not make the model converge every time, but when it does its predictions are stable.

Because this network used 2D slices out of each volumetric image I now had multiple predictions for each MRI. On average I was wrong by 0.64 (±0.48) years. Apparently, that's already a very good result beating previous carpal MRI studies and an algorithm called boneXpert that uses X-rays.

I analysed the different predictions for each 2D image and noticed that inner slices resulted in a higher accuracy. I ran experiments with weighted averages and discarding outer slices. Both concepts improved the results from before, but another approach yielded even higher accuracy. I set up aRandom Forest Regressorthat would take a vector of multiple age predictions and output a single estimate for each 3D MRI. This got me to a mean difference of**0.48 (±0.32) years**on the test set. In other words, this workflow let's me assess the age of caucasian male 14 to 21 year olds with an average error of half a year.

This project became my bachelor thesis. I'm planning on open sourcing the code and paper, so anyone can use it for future work. For now, I have to wait what the committee thinks about it before I release anything. I find it incredible that somebody like me with no medical background can contribute to current research using AI algorithms. It's what amazes me about this field of computer science and I hope to continue working on fascinating projects like this one in the future.