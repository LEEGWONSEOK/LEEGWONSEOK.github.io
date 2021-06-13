## [데이터 전처리] missingno 패키지

> 이 블로그는 순수하게 저 혼자 공부를 하기 위해 사용하고 있습니다.

### missingno 패키지

#### missingno 패키지 ?

현실에서 데이터를 수집하다 보면 데이터의 일부를 얻지 못하거나 누락되는 결측(missing) 데이터가 생긴다.<br>
`missingno` 패키지는 pandas 데이터프레임에서 **결측 데이터를 찾는 기능을 제공**한다.<br>
이 패키지를 사용하려면 데이터프레임에 결측 데이터가 NaN(not a number) 값으로 저장되어 있어야 한다.

#### 주의할 점

여기서 NaN값은 부동소수점 실수 자료형에만 있는 값이므로 
정수 자료를 데이터프레임에 넣을 때는 `Int64Dtype` 자료형을 명시해주어야 하고 
시간 자료형을 넣을 때도 `parse_dates` 인수로 날짜시간형 파싱을 해주어야 `datetime64[ns]` 자료형이 되어 결측 데이터가 NaT(not a time) 값으로 표시된다.

<br>


```python
import pandas as pd
from io import StringIO

csv_data = StringIO("""
x1,x2,x3,x4,x5
1,0.1,"1",2019-01-01,A
2,,,2019-01-02,B
3,,"3",2019-01-03,C
,0.4,"4",2019-01-04,A
5,0.5,"5",,B
,,,2019-01-06,C
""")

df = pd.read_csv(csv_data,                      # 만든 csv_data
                 dtype={"x1": pd.Int64Dtype()}, # x1 칼럼 내용
                 parse_dates=[3])               # 4번째 칼럼 날짜시간형으로 파싱 : NaT
df
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>x1</th>
      <th>x2</th>
      <th>x3</th>
      <th>x4</th>
      <th>x5</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>1</td>
      <td>0.1</td>
      <td>1.0</td>
      <td>2019-01-01</td>
      <td>A</td>
    </tr>
    <tr>
      <th>1</th>
      <td>2</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>2019-01-02</td>
      <td>B</td>
    </tr>
    <tr>
      <th>2</th>
      <td>3</td>
      <td>NaN</td>
      <td>3.0</td>
      <td>2019-01-03</td>
      <td>C</td>
    </tr>
    <tr>
      <th>3</th>
      <td>&lt;NA&gt;</td>
      <td>0.4</td>
      <td>4.0</td>
      <td>2019-01-04</td>
      <td>A</td>
    </tr>
    <tr>
      <th>4</th>
      <td>5</td>
      <td>0.5</td>
      <td>5.0</td>
      <td>NaT</td>
      <td>B</td>
    </tr>
    <tr>
      <th>5</th>
      <td>&lt;NA&gt;</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>2019-01-06</td>
      <td>C</td>
    </tr>
  </tbody>
</table>
</div>



<br>
판다스 df 자체적으로 결측치 확인이 가능하다

- `isnull()` : 결측치(True값)이 어디어디에 있는지 확인 가능
- `isnull().sum()` : 어떤 칼럼에 결측치가 몇개 있는지 확인 가능(데이터가 많을때 쓰는 방법)

<br>


```python
df.isnull()
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>x1</th>
      <th>x2</th>
      <th>x3</th>
      <th>x4</th>
      <th>x5</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
    </tr>
    <tr>
      <th>1</th>
      <td>False</td>
      <td>True</td>
      <td>True</td>
      <td>False</td>
      <td>False</td>
    </tr>
    <tr>
      <th>2</th>
      <td>False</td>
      <td>True</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
    </tr>
    <tr>
      <th>3</th>
      <td>True</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
    </tr>
    <tr>
      <th>4</th>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>True</td>
      <td>False</td>
    </tr>
    <tr>
      <th>5</th>
      <td>True</td>
      <td>True</td>
      <td>True</td>
      <td>False</td>
      <td>False</td>
    </tr>
  </tbody>
</table>
</div>




```python
df.isnull().sum()
```




    x1    2
    x2    3
    x3    2
    x4    1
    x5    0
    dtype: int64



<br>

하지만 missingno 패키지를 사용하면 대규모의 데이터에서도 결측 데이터에 대한 시각화를 쉽게 할 수 있다.<br>
`matrix()` 함수는 **결측 데이터를 시각화하는 명령**이다.<br>
결측된 데이터는 흰색으로, 그렇지 않은 데이터는 검은색으로 나타난다.<br>
가장 오른쪽에 있는 것은 스파크라인(spark line)이라고 부르고, 각 행의 데이터 완성도를 표현한다.

<br>


```python
import missingno as msno
import matplotlib.pyplot as plt

msno.matrix(df)
plt.show
```


    ---------------------------------------------------------------------------

    ModuleNotFoundError                       Traceback (most recent call last)

    <ipython-input-11-9e4b18d56964> in <module>
    ----> 1 import missingno as msno
          2 import matplotlib.pyplot as plt
          3 
          4 msno.matrix(df)
          5 plt.show


    ModuleNotFoundError: No module named 'missingno'



```python

```
