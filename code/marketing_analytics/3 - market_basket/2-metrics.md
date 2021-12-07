# Metrics

Support as a standalone metric doesn't tell us much. It can be misleading as it can show a high level of support for items that are not necessarily related to eachother but just are popular items.

Summary of metrics to consider:

* X = Product 1
* Y = Product 2
* N = Total transactions

**1. Support**
<!-- $Support(X) = \frac{Frequency(X)}{N}$ --> 
<img style="transform: translateY(0.1em); background: white;" src="https://render.githubusercontent.com/render/math?math=Support(X)%20%3D%20%5Cfrac%7BFrequency(X)%7D%7BN%7D">

<br>
<!-- $Support(X \rightarrow Y) = \frac{Frequency(X\&Y)}{N}$ --> <img style="transform: translateY(0.1em); background: white;" src="https://render.githubusercontent.com/render/math?math=Support(X%20%5Crightarrow%20Y)%20%3D%20%5Cfrac%7BFrequency(X%5C%26Y)%7D%7BN%7D">

**2. Confidence**

<!-- $Confidence(X \rightarrow Y) = \frac{Support(X \rightarrow Y)}{Support(X)}$ --> <img style="transform: translateY(0.1em); background: white;" src="https://render.githubusercontent.com/render/math?math=Confidence(X%20%5Crightarrow%20Y)%20%3D%20%5Cfrac%7BSupport(X%20%5Crightarrow%20Y)%7D%7BSupport(X)%7D">


**3. Lift**
<!-- $Lift(X \rightarrow Y) = \frac{Support(X \rightarrow Y)}{Support(X) \times Support(Y)}$ --> <img style="transform: translateY(0.1em); background: white;" src="https://render.githubusercontent.com/render/math?math=Lift(X%20%5Crightarrow%20Y)%20%3D%20%5Cfrac%7BSupport(X%20%5Crightarrow%20Y)%7D%7BSupport(X)%20%5Ctimes%20Support(Y)%7D">

**4. Leverage**
<!-- $Leverage(X \rightarrow Y) = Support(X\&Y) - (Support(X) \times Support(Y))$ --> <img style="transform: translateY(0.1em); background: white;" src="https://render.githubusercontent.com/render/math?math=Leverage(X%20%5Crightarrow%20Y)%20%3D%20Support(X%5C%26Y)%20-%20(Support(X)%20%5Ctimes%20Support(Y))">

**5. Conviction**

<!-- $Conviction(X \rightarrow Y) = \frac{Support(X) \times Support(\bar{Y})}{Support(X\&\bar{Y})}$ --> <img style="transform: translateY(0.1em); background: white;" src="https://render.githubusercontent.com/render/math?math=Conviction(X%20%5Crightarrow%20Y)%20%3D%20%5Cfrac%7BSupport(X)%20%5Ctimes%20Support(%5Cbar%7BY%7D)%7D%7BSupport(X%5C%26%5Cbar%7BY%7D)%7D">

---

## Confidence

<!-- $confidence = \dfrac{Support (X\&Y)}{Support (X)}$ --> 
<div align="center"><img style="transform: translateY(0.1em); background: white;" src="https://render.githubusercontent.com/render/math?math=confidence%20%3D%20%5Cdfrac%7BSupport%20(X%5C%26Y)%7D%7BSupport%20(X)%7D"></div>

In other words, if the Support(Milk & Coffee) = 0.2, and the Support(Milk) = 1.0, confidence = 0.2/1. Purchasing milk tells us nothing about purchasing coffee because milk is in every transaction.

Instead, if Support(Milk) = 0.3 because it's only in 3 out of the ten transactions, then confidence = 0.2/0.3 = 66%. This means that when buying milk, there's a 66% chance of also buying coffee. 

## Lift

* Numerator: Proportion of transactions that contain X and Y
* Denominator: Proportion if X and Y assigned randomly and independently
* Lift value > 1 indicates that we see them purchased together more often than we'd expect by their individual support values. It means this relationship is unlikely to be due to random chance.

<!-- $lift = \dfrac{Support (X\&Y)}{Support(X) \times Support(Y)}$ --> 
<div align="center"><img style="transform: translateY(0.1em); background: white;" src="https://render.githubusercontent.com/render/math?math=lift%20%3D%20%5Cdfrac%7BSupport%20(X%5C%26Y)%7D%7BSupport(X)%20%5Ctimes%20Support(Y)%7D"></div>



```python
from mlxtend.preprocessing import TransactionEncoder
import pandas as pd

# libraries = list of lists of books

books = TransactionEncoder().fit(libraries).transform(libraries)

books = pd.DataFrame(books, columns = encoder.columns_)

supportHG = np.logical_and(books['Hunger'],books['Gatsby']).mean()
supportH = books['Hunger'].mean()
supportG = books['Gatsby'].mean()

confidence = supportHG / supportH
lift = supportHG / (supportH * supportG)
```

## Leverage

<img style="transform: translateY(0.1em); background: white;" src="https://render.githubusercontent.com/render/math?math=Leverage(X%20%5Crightarrow%20Y)%20%3D%20Support(X%5C%26Y)%20-%20(Support(X)%20%5Ctimes%20Support(Y))">

```python
supportHG = np.logical_and(books['Hunger'],books['Gatsby']).mean()
supportH = books['Hunger'].mean()
supportG = books['Gatsby'].mean()

leverage = supportHG - supportH * supportG
```

If greater than 0, then this is the same as lift > 1

## Conviction

* $Support(\bar{Y})$ represents all transactions that did not include Y

<img style="transform: translateY(0.1em); background: white;" src="https://render.githubusercontent.com/render/math?math=Conviction(X%20%5Crightarrow%20Y)%20%3D%20%5Cfrac%7BSupport(X)%20%5Ctimes%20Support(%5Cbar%7BY%7D)%7D%7BSupport(X%5C%26%5Cbar%7BY%7D)%7D">



```python
supportTP = np.logical_and(books['Twilight'], books['Potter']).mean()
supportT = books['Twilight'].mean()

# Compute support for NOT Harry Potter
supportnP = 1 - books['Potter'].mean()

# Compute support for Twilight and Not Harry Potter
supportTnP = supportT - supportPT

conviction = supportT*supportnP / supportTnP
```


## Dissociation

Zhang's metric:

* Value of +1 indicates perfect association
* Value of -1 indicates perfect dissociation

<!-- $Zhang(A \rightarrow B) = \dfrac{Confidence(A \rightarrow B) - Confidence(\bar{A} \rightarrow B)}{Max[Confidence(A \rightarrow B), Condience(\bar{A} \rightarrow B)]}$ --> <img style="transform: translateY(0.1em); background: white;" src="https://render.githubusercontent.com/render/math?math=Zhang(A%20%5Crightarrow%20B)%20%3D%20%5Cdfrac%7BConfidence(A%20%5Crightarrow%20B)%20-%20Confidence(%5Cbar%7BA%7D%20%5Crightarrow%20B)%7D%7BMax%5BConfidence(A%20%5Crightarrow%20B)%2C%20Condience(%5Cbar%7BA%7D%20%5Crightarrow%20B)%5D%7D">

We can simplify the formula as:

<!-- $Zhang(A \rightarrow B) = \dfrac{Support(AB) - Support(A)Support(B)}{Max[Support(AB)(1 - Support(A)), Support(A)(Support(B) - Support(AB))]}$ --> <img style="transform: translateY(0.1em); background: white;" src="https://render.githubusercontent.com/render/math?math=Zhang(A%20%5Crightarrow%20B)%20%3D%20%5Cdfrac%7BSupport(AB)%20-%20Support(A)Support(B)%7D%7BMax%5BSupport(AB)(1%20-%20Support(A))%2C%20Support(A)(Support(B)%20-%20Support(AB))%5D%7D">

```python
supportH = books['hobbit'].mean()
supportP = books['pride'].mean()
supportHP = np.logical_and(books['hobbit'], books['pride']).mean()

num = supportHP - supportH*supportP

denom = max(supportHP*(1-supportH), supportH*(supportP-supportHP))

zhang = num / denom

print(zhang)
```

