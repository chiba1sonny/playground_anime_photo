# playground_anime_photo

この記事でscratchからOpenCVを使って画像をアニメ化してみます。

基本的な基準は、画像の輪郭を抽出するー画像を減色処理、ノイズ減少処理するーこの２つを結合する。

1.OpenCVをインストールする

1)Anaconda Promptを開く

2)Anaconda Promptで以下のコマンドよりOpenCVをインストールする

```
pip install opencv-python
```

2.元画像の読み出し

1)必要なライブラリをインポートする

```py
import cv2
import numpy as np
import matplotlib.image as img
from matplotlib import pyplot as plt
```

2)元画像の読み出し
以下のコードで画像が出てきます。

```py
img=cv2.imread("icon.jpg")
cv2.imshow("img",img)
cv2.waitKey()
cv2.destroyAllWindows()
```

![1628496761(1).png](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/1668082/3058b115-4296-f261-d8f5-ce92197f6341.png)

3.輪郭を抽出する

まず、cv2.cvtColorより元画像をグレースケールに変換します。さらに、cv2.medianBlur関数より平滑化処理します。最後に、cv2.adaptiveThresholdえ()を使って輪郭を抽出します。

```
gray = cv2.cvtColor(img, cv2.COLOR_BGR2GRAY)
gray_blur = cv2.medianBlur(gray,7)
edges = cv2.adaptiveThreshold(gray_blur, 255, cv2.ADAPTIVE_THRESH_MEAN_C, cv2.THRESH_BINARY,7,7)
```

以下のコードで抽出した輪郭を見てみます。

```py
cv2.imshow("edges",edges)
cv2.waitKey()
cv2.destroyAllWindows()
```

![598dc10a7060938729383af31f6f6df.png](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/1668082/d2acb8d6-1c62-e3e9-572d-fe38ec88aefc.png)

グレースケール画像と平滑化した画像もチェックしてみます。

![1628497975(1).png](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/1668082/ef84ec88-750d-24b2-6e91-a08bba6af647.png)

![1628498012(1).png](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/1668082/3eed0768-4b2d-4664-7745-208864562e68.png)

4.減色処理する

アニメ化画像の色の数量は元画像より少ないべきです。こちらでK-means法を使って減色処理をします。8個の色にしたいなので、kの値を8にします。以下のコードで減色処理をしました。

```py
data = np.float32(img).reshape((-1, 3))
criteria = (cv2.TERM_CRITERIA_EPS + cv2.TERM_CRITERIA_MAX_ITER, 20, 0.001)
ret, label, center = cv2.kmeans(data, 8, None, criteria, 10, cv2.KMEANS_RANDOM_CENTERS)
center = np.uint8(center)
result = center[label.flatten()]
result = result.reshape(img.shape)
```

以下のコードで減色処理した結果を見てみます。

```py
cv2.imshow("result",result)
cv2.waitKey()
cv2.destroyAllWindows()
```

![f1146b7fcc71147433aa85d93b45e3b.png](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/1668082/a8824968-6add-b532-18f6-b47f79e2c759.png)

5.ノイズを消す
このステップで、cv2.bilateralFilterを使って画像のノイズを消します。生成された画像の解像度を下げます。

```py
blurred = cv2.bilateralFilter(result, d=10, sigmaColor=250,sigmaSpace=250)
```
以下のコードで生成された画像を見てみます。

```py
cv2.imshow("blurred",blurred)
cv2.waitKey()
cv2.destroyAllWindows()
```

![29e6310675f015ab95d0911adfcfa3d.png](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/1668082/deab4437-499f-f53c-bec1-4202b9f0b4af.png)

6.減色処理とノイズ減少処理した画像を輪郭と結合する
cv2.bitwise_and関数を使って減色処理とノイズ減少処理した画像を輪郭と結合します。

```py
anime = cv2.bitwise_and(blurred, blurred,mask=edges)
```
最後のアニメ化した画像をチェックしてみます。

```py
cv2.imshow("anime",anime)
cv2.waitKey()
cv2.destroyAllWindows()
```

![9b15d9b96938d4168f747734a4b831d.png](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/1668082/29586cb6-7344-c6c2-2c9f-111f896cdc3f.png)

アニメ化画像みたいと思います。

オリジナルではありません。

勉強したサイト：[OpenCV projects – How to cartoonize an image with OpenCV in Python?](http://datahacker.rs/002-opencv-projects-how-to-cartoonize-an-image-with-opencv-in-python/#Detecting-and-emphasizing-edges)

引用文

OpenCV projects – How to cartoonize an image with OpenCV in Python?

http://datahacker.rs/002-opencv-projects-how-to-cartoonize-an-image-with-opencv-in-python/#Detecting-and-emphasizing-edges
