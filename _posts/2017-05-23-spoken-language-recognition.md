---
inFeed: true
description: >-
  I trained a convolutional neural network to classify audio files of voice
  recordings into the languages that were spoken. The dataset I used contained
  66.000 files across 176 languages. I found it on TopCoder. I liked the idea
  behind this problem, because it's very hard for humans to do. It's interesting
  to see that CNNs perform well on problems where intuition doesn't get you
  anywhere.
dateModified: '2017-08-22T13:08:44.497Z'
datePublished: '2017-08-22T13:08:44.789Z'
title: Spoken Language Recognition
author: []
publisher: {}
via: {}
sourcePath: _posts/2017-05-23-spoken-language-recognition.md
hasPage: true
starred: false
datePublishedOriginal: '2017-05-23T21:32:41.511Z'
url: spoken-language-recognition/index.html
_type: Article

---
# Spoken Language Recognition
![](https://the-grid-user-content.s3-us-west-2.amazonaws.com/d404f00f-cca8-429a-98ea-e7a949ed2001.jpg)

I trained a convolutional neural network to classify audio files of voice recordings into the languages that were spoken. The dataset I used contained 66.000 files across 176 languages. I found it on [TopCoder][0]. I liked the idea behind this problem, because it's very hard for humans to do. It's interesting to see that CNNs perform well on problems where intuition doesn't get you anywhere.

---

I included my pre-trained model in the code on [GitHub][1], which evaluates to an accuracy of **98,79%**. Further notes on development can be seen in the Jupyter Notebook. The raw data consists of 66176 44,1kHz stereo mp3 file with a length of 10 seconds each. The dataset is perfectly balanced with 376 files per language.

We can visualise this file by converting it to a log-mel-spectrogram.
![](https://the-grid-user-content.s3-us-west-2.amazonaws.com/d6bb1c15-c388-4c2b-93a5-9581a5fb321c.png)

* the y-axis shows the frequency
* the x-axis shows the time
* the color shows the intensity of a frequency at a given time

The spectrograms gave me headaches at first. Although it's easy to read a spectrogram, it's hard to "intuitively" judge the content of an audio file. My first try of converting them looked totally fine, but I wasn't able to train my network at all. I used a regular spectrogram which I then converted to log scale frequencies. The trouble was that by squeezing the higher frequencies I was pulling apart the lower frequencies. That's the general idea of a log scale and all, but I didn't take into account that the resolution in the lower frequencies would suffer badly. To my luck I wasn't the first person with this problem, so in 1980 a couple of guys came up with the mel-spectrogram which gives more resolution to lower frequencies. After that I also log scaled the intensities of my data points which seemed to help as well.

I started out with a resolution of 224x448 pixels but this took forever on my computer. I applied some asymetrical resizing and noticed that my assumption of reserving more space for the time axis was wrong. Square images seemed to perform best. So, I went ahead and converted everything to 192x192, which didn't hurt the performance all that much.

The "sanity check" of the data turned out to be difficult with this dataset. Apparently you can have 176 different languages without including english, german or french. But all the dutch samples sounded like what dutch people sound like, so I figured it couldn't be that wrong.

I tried roughly 30 different models with focus on newer architectures like residual networks, networks in networks, squeezing and expanding convolutions, but in the end a 5x-Conv-MaxPool worked best. I really wanted to replace the last Dense layers with AveragePooling. They give a little more insight to what's happening in comparison to the "black box"-model that results from dense layers. However it didn't work out as well. I'm guessing this is because spectrograms show a different abstraction of information in comparison to a regular photo showing one object.

In my tests the use of Elu replaced the need for Batch Normalization, which usually improves any model. I didn't include them for performance reasons.

[0]: https://goo.gl/G5XBJl "TopCoder Competition"
[1]: https://github.com/pietz/language-recognition "Code on GitHub"