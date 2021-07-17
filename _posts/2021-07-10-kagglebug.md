---
title: Cannot download all the data through kaggle API
published: true
tag:
 - Kaggle
 - API
---

# Backgrounds

Some kaggle competitions have a large dataset (>=50GB). For those competitions we often need the cloud environment for modelling and storing. I often used the combination of Google Colab and Google Drive. Kaggle recommends using their API to process the competitions and get the datasets. 

However, through my usage of their API service. I found their APIs are not consistent and reliable. For example, to download the dataset for ***siim-covid19-detection***, which is ~86GB. The kaggle API service can only download about 80MB data and then stopped. This often happens for competitions with large datasets. And very little solutions are shared online.

# Solution

So we could use ```wget``` instead with Google Colab. However, kaggle requires user authentication to download the datasets. We could use cookies from kaggle to authenticate our request. And here are the steps on how to do it.

First, we need a chrome extention called [get cookies.txt](https://chrome.google.com/webstore/detail/get-cookiestxt/bgaddhkoddajcdgocldbbfleckgcbcid/related). Then you need to go to a logged in kaggle webpage to download the kaggle cookies and upload it to Google Drive. After that:

1. Go to the Google Colab, mount the drive.
1. Go to the path you stored kaggle.com_cookies.txt. 
1. Run the following command.

```
!wget -x --load-cookies kaggle.com_cookies.txt "https://www.kaggle.com/c/[Competition Name]/download-all" -O data.zip
```

Done. [Source](https://www.kaggle.com/questions-and-answers/135301) credit @ Anusha Nidamanuri
