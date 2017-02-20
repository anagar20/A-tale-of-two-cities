

```python
%load_ext cython
```


```python
%%cython
import numpy as np
import string
import requests
import timeit
from bs4 import BeautifulSoup
import re
from string import punctuation
from nltk.corpus import stopwords

def function():
    html  = requests.get("https://archive.org/stream/ataleoftwocities00098gut/98.txt").text
    soup  = BeautifulSoup(html, 'html5lib')
    paragraph = soup.pre.string

    pattern = re.compile(r'[^\w\s]', re.U)
    paragraph = str(re.sub(r'_', '', re.sub(pattern, '', paragraph)))
    replace_punctuation = string.maketrans(string.punctuation, ' '*len(string.punctuation))
    s = paragraph.translate(replace_punctuation)
    s = ''.join([i for i in s if not i.isdigit()])
    s = s.lower()
   
    def strip_punctuation(s):
        s=''.join(_ for _ in s if _ not in punctuation)
        return s
    
    s = strip_punctuation(s)
    ss = s.split()
    del ss[ :382]
    
    stpwrds = set(stopwords.words('english'))
    filtered_w = [_ for _ in ss if _ not in stpwrds]
    
    from collections import Counter
    frequencies = Counter(filtered_w)
    
    def sorting(freqdict):
        aux = [(freqdict[key], key) for key in freqdict]
        aux.sort()
        aux.reverse()
        return aux
    return (filtered_w,sorting(frequencies))
```


```python
start = timeit.default_timer()
filtered_words_list,sorted_words_with_decreasing_frequency = (function())
print timeit.default_timer()-start
```

    2.12113213539



```python
% timeit function()
```

    1 loop, best of 3: 1.53 s per loop



```python
filtered_words_list[1:10]
```




    ['best',
     'times',
     'worst',
     'times',
     'age',
     'wisdom',
     'age',
     'foolishness',
     'epoch']




```python
sortedvar = sorted_words_with_decreasing_frequency
sortedvar[1:10]
```




    [(621, 'mr'),
     (437, 'one'),
     (341, 'would'),
     (336, 'lorry'),
     (291, 'upon'),
     (282, 'could'),
     (280, 'defarge'),
     (279, 'man'),
     (265, 'little')]




```python
sorted_np = np.array(sortedvar)
sorted_np
```




    array([['660', 'said'],
           ['621', 'mr'],
           ['437', 'one'],
           ..., 
           ['1', 'aamatter'],
           ['1', 'aaabusiness'],
           ['1', 'aa']], 
          dtype='|S23')




```python
import plotly.plotly as py
import plotly.graph_objs as go


y = sorted_np[:,0]
x = sorted_np[:,1]
ww = len(x)
x1=x[ :ww]
y1=y[ :ww]

trace1 = go.Scatter(
    x = x1,
    y = y1,
    mode='markers',
    marker=dict(
        size='11',
        color = y1, #set color equal to a variable
        colorscale='Viridis',
        showscale=True
    )
)
data = [trace1]

py.iplot(data, filename='scatter-plot-with-colorscale')

```




<iframe id="igraph" scrolling="no" style="border:none;" seamless="seamless" src="https://plot.ly/~anagar20/77.embed" height="525px" width="100%"></iframe>




```python

```


```python

```
