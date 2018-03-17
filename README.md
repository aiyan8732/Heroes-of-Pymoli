

```python
import pandas as pd
```


```python
jsonFile = "purchase_data.json"
purchase_data_df = pd.read_json(jsonFile)
```


```python
## Player Count
total_player = len(purchase_data_df["SN"].unique())
total_player_df = pd.DataFrame({"Total Players": [total_player]})
total_player_df
```




<div>
<style>
    .dataframe thead tr:only-child th {
        text-align: right;
    }

    .dataframe thead th {
        text-align: left;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>Total Players</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>573</td>
    </tr>
  </tbody>
</table>
</div>




```python
## Purchasing Analysis (Total)
total_unique_item = len(purchase_data_df["Item ID"].unique())
purchase_number = len(purchase_data_df["Item ID"])
total_revenue = purchase_data_df["Price"].sum()
average_purchase_price = total_revenue / purchase_number
purchase_df = pd.DataFrame({"Number of Unique Items": [total_unique_item],
                            "Average Purchase Price": [average_purchase_price],
                            "Total Number of Purchases": [purchase_number],
                            "Total Revenue": [total_revenue]     
                             })
purchase_df = purchase_df[["Number of Unique Items",
                           "Average Purchase Price",
                           "Total Number of Purchases",
                           "Total Revenue"
                           ]]
purchase_df["Average Purchase Price"] = purchase_df["Average Purchase Price"].map("${:,.2f}".format)
purchase_df["Total Revenue"] = purchase_df["Total Revenue"].map("${:,.2f}".format)
purchase_df
```




<div>
<style>
    .dataframe thead tr:only-child th {
        text-align: right;
    }

    .dataframe thead th {
        text-align: left;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>Number of Unique Items</th>
      <th>Average Purchase Price</th>
      <th>Total Number of Purchases</th>
      <th>Total Revenue</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>183</td>
      <td>$2.93</td>
      <td>780</td>
      <td>$2,286.33</td>
    </tr>
  </tbody>
</table>
</div>




```python
## Gender Demographics
gender_df = pd.DataFrame({"count": purchase_data_df.groupby("SN")["Gender"].value_counts()})
gender_df.reset_index(inplace=True)
gender_demographics_df = pd.DataFrame({"Total Count": gender_df.groupby("Gender")["SN"].count(),
                                       "Percentage of Players" : gender_df.groupby("Gender")["SN"].count()/total_player*100
                                       })
gender_demographics_df = gender_demographics_df.sort_values("Total Count", ascending=False)
gender_demographics_df["Percentage of Players"] = gender_demographics_df["Percentage of Players"].map("{:.2f}".format)
gender_demographics_df
```




<div>
<style>
    .dataframe thead tr:only-child th {
        text-align: right;
    }

    .dataframe thead th {
        text-align: left;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>Percentage of Players</th>
      <th>Total Count</th>
    </tr>
    <tr>
      <th>Gender</th>
      <th></th>
      <th></th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>Male</th>
      <td>81.15</td>
      <td>465</td>
    </tr>
    <tr>
      <th>Female</th>
      <td>17.45</td>
      <td>100</td>
    </tr>
    <tr>
      <th>Other / Non-Disclosed</th>
      <td>1.40</td>
      <td>8</td>
    </tr>
  </tbody>
</table>
</div>




```python
## Purchasing Analysis (Gender)
gender_purchase_df = pd.DataFrame({"Purchase Count": purchase_data_df.groupby("Gender")["Item ID"].count(),
                                   "Average Purchase Price": purchase_data_df.groupby("Gender")["Price"].mean(),
                                   "Total Purchase Value": purchase_data_df.groupby("Gender")["Price"].sum()
                                    })
gender_purchase_df = pd.merge(gender_purchase_df,gender_demographics_df,left_index=True, right_index=True)
del gender_purchase_df["Percentage of Players"]
gender_purchase_df["Normalized Totals"] = gender_purchase_df["Total Purchase Value"]/gender_purchase_df["Total Count"]
del gender_purchase_df["Total Count"]
gender_purchase_df = gender_purchase_df[["Purchase Count","Average Purchase Price","Total Purchase Value","Normalized Totals"]]
gender_purchase_df["Average Purchase Price"] = gender_purchase_df["Average Purchase Price"].map("${:,.2f}".format)
gender_purchase_df["Total Purchase Value"] = gender_purchase_df["Total Purchase Value"].map("${:,.2f}".format)
gender_purchase_df["Normalized Totals"] = gender_purchase_df["Normalized Totals"].map("${:,.2f}".format)
gender_purchase_df
```




<div>
<style>
    .dataframe thead tr:only-child th {
        text-align: right;
    }

    .dataframe thead th {
        text-align: left;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>Purchase Count</th>
      <th>Average Purchase Price</th>
      <th>Total Purchase Value</th>
      <th>Normalized Totals</th>
    </tr>
    <tr>
      <th>Gender</th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>Female</th>
      <td>136</td>
      <td>$2.82</td>
      <td>$382.91</td>
      <td>$3.83</td>
    </tr>
    <tr>
      <th>Male</th>
      <td>633</td>
      <td>$2.95</td>
      <td>$1,867.68</td>
      <td>$4.02</td>
    </tr>
    <tr>
      <th>Other / Non-Disclosed</th>
      <td>11</td>
      <td>$3.25</td>
      <td>$35.74</td>
      <td>$4.47</td>
    </tr>
  </tbody>
</table>
</div>




```python
## Age Demographics
purchase_data_df["Age"].max()
bins = [0,9,14,19,24,29,34,39,50]
group_names = [' <10', '10-14', '15-19', '20-24','25-29','30-34','35-39','40+']
purchase_data_df["Age Range"] = pd.cut(purchase_data_df["Age"], bins, labels=group_names)
age_df = pd.DataFrame({"Age Count": purchase_data_df.groupby("SN")["Age Range"].value_counts()})
age_df.reset_index(inplace=True)
age_demographics_df = pd.DataFrame({"Total Count": age_df.groupby("Age Range")["SN"].count(),
                                    "Percentage of Players" : age_df.groupby("Age Range")["SN"].count()/total_player*100
                                    })
age_demographics_df["Percentage of Players"] = age_demographics_df["Percentage of Players"].map("{:.2f}".format)
age_demographics_df
```




<div>
<style>
    .dataframe thead tr:only-child th {
        text-align: right;
    }

    .dataframe thead th {
        text-align: left;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>Percentage of Players</th>
      <th>Total Count</th>
    </tr>
    <tr>
      <th>Age Range</th>
      <th></th>
      <th></th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>&lt;10</th>
      <td>3.32</td>
      <td>19</td>
    </tr>
    <tr>
      <th>10-14</th>
      <td>4.01</td>
      <td>23</td>
    </tr>
    <tr>
      <th>15-19</th>
      <td>17.45</td>
      <td>100</td>
    </tr>
    <tr>
      <th>20-24</th>
      <td>45.20</td>
      <td>259</td>
    </tr>
    <tr>
      <th>25-29</th>
      <td>15.18</td>
      <td>87</td>
    </tr>
    <tr>
      <th>30-34</th>
      <td>8.20</td>
      <td>47</td>
    </tr>
    <tr>
      <th>35-39</th>
      <td>4.71</td>
      <td>27</td>
    </tr>
    <tr>
      <th>40+</th>
      <td>1.92</td>
      <td>11</td>
    </tr>
  </tbody>
</table>
</div>




```python
## Purchasing Analysis (age)
age_purchase_df = pd.DataFrame({"Purchase Count": purchase_data_df.groupby("Age Range")["Item ID"].count(),
                                "Average Purchase Price": purchase_data_df.groupby("Age Range")["Price"].mean(),
                                "Total Purchase Value": purchase_data_df.groupby("Age Range")["Price"].sum()
                                    })
age_purchase_df = pd.merge(age_purchase_df,age_demographics_df,left_index=True, right_index=True)
del age_purchase_df["Percentage of Players"]
age_purchase_df["Normalized Totals"] = age_purchase_df["Total Purchase Value"]/age_purchase_df["Total Count"]
del age_purchase_df["Total Count"]
age_purchase_df = age_purchase_df[["Purchase Count","Average Purchase Price","Total Purchase Value","Normalized Totals"]]
age_purchase_df["Average Purchase Price"] = age_purchase_df["Average Purchase Price"].map("${:,.2f}".format)
age_purchase_df["Total Purchase Value"] = age_purchase_df["Total Purchase Value"].map("${:,.2f}".format)
age_purchase_df["Normalized Totals"] = age_purchase_df["Normalized Totals"].map("${:,.2f}".format)
age_purchase_df

```




<div>
<style>
    .dataframe thead tr:only-child th {
        text-align: right;
    }

    .dataframe thead th {
        text-align: left;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>Purchase Count</th>
      <th>Average Purchase Price</th>
      <th>Total Purchase Value</th>
      <th>Normalized Totals</th>
    </tr>
    <tr>
      <th>Age Range</th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>&lt;10</th>
      <td>28</td>
      <td>$2.98</td>
      <td>$83.46</td>
      <td>$4.39</td>
    </tr>
    <tr>
      <th>10-14</th>
      <td>35</td>
      <td>$2.77</td>
      <td>$96.95</td>
      <td>$4.22</td>
    </tr>
    <tr>
      <th>15-19</th>
      <td>133</td>
      <td>$2.91</td>
      <td>$386.42</td>
      <td>$3.86</td>
    </tr>
    <tr>
      <th>20-24</th>
      <td>336</td>
      <td>$2.91</td>
      <td>$978.77</td>
      <td>$3.78</td>
    </tr>
    <tr>
      <th>25-29</th>
      <td>125</td>
      <td>$2.96</td>
      <td>$370.33</td>
      <td>$4.26</td>
    </tr>
    <tr>
      <th>30-34</th>
      <td>64</td>
      <td>$3.08</td>
      <td>$197.25</td>
      <td>$4.20</td>
    </tr>
    <tr>
      <th>35-39</th>
      <td>42</td>
      <td>$2.84</td>
      <td>$119.40</td>
      <td>$4.42</td>
    </tr>
    <tr>
      <th>40+</th>
      <td>17</td>
      <td>$3.16</td>
      <td>$53.75</td>
      <td>$4.89</td>
    </tr>
  </tbody>
</table>
</div>




```python
## Top Spenders
top_spenders_df = pd.DataFrame({"Purchase Count": purchase_data_df.groupby("SN")["Item ID"].count(),
                                "Average Purchase Price": purchase_data_df.groupby("SN")["Price"].mean(),
                                "Total Purchase Value": purchase_data_df.groupby("SN")["Price"].sum()    
                                })
top_spenders_df = top_spenders_df[["Purchase Count",
                                   "Average Purchase Price",
                                   "Total Purchase Value"]]
top_spenders_df = top_spenders_df.sort_values("Total Purchase Value", ascending=False)
top_spenders_df = top_spenders_df.iloc[0:5,:]
top_spenders_df["Average Purchase Price"] = top_spenders_df["Average Purchase Price"].map("${:,.2f}".format)
top_spenders_df["Total Purchase Value"] = top_spenders_df["Total Purchase Value"].map("${:,.2f}".format)
top_spenders_df
```




<div>
<style>
    .dataframe thead tr:only-child th {
        text-align: right;
    }

    .dataframe thead th {
        text-align: left;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>Purchase Count</th>
      <th>Average Purchase Price</th>
      <th>Total Purchase Value</th>
    </tr>
    <tr>
      <th>SN</th>
      <th></th>
      <th></th>
      <th></th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>Undirrala66</th>
      <td>5</td>
      <td>$3.41</td>
      <td>$17.06</td>
    </tr>
    <tr>
      <th>Saedue76</th>
      <td>4</td>
      <td>$3.39</td>
      <td>$13.56</td>
    </tr>
    <tr>
      <th>Mindimnya67</th>
      <td>4</td>
      <td>$3.18</td>
      <td>$12.74</td>
    </tr>
    <tr>
      <th>Haellysu29</th>
      <td>3</td>
      <td>$4.24</td>
      <td>$12.73</td>
    </tr>
    <tr>
      <th>Eoda93</th>
      <td>3</td>
      <td>$3.86</td>
      <td>$11.58</td>
    </tr>
  </tbody>
</table>
</div>




```python
## Most Popular Items
popular_items_df = pd.DataFrame({"Purchase Count": purchase_data_df.groupby(["Item ID","Item Name"])["Price"].count(),
                                 "Item Price": purchase_data_df.groupby(["Item ID","Item Name"])["Price"].mean(),
                                 "Total Purchase Value": purchase_data_df.groupby(["Item ID","Item Name"])["Price"].sum()    
                                })
popular_items_df = popular_items_df[["Purchase Count",
                                     "Item Price",
                                     "Total Purchase Value"]]
popular_items_df = popular_items_df.sort_values("Purchase Count", ascending=False)
popular_items_df = popular_items_df.iloc[0:5,:]
popular_items_df["Item Price"] = popular_items_df["Item Price"].map("${:,.2f}".format)
popular_items_df["Total Purchase Value"] = popular_items_df["Total Purchase Value"].map("${:,.2f}".format)
popular_items_df

```




<div>
<style>
    .dataframe thead tr:only-child th {
        text-align: right;
    }

    .dataframe thead th {
        text-align: left;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th></th>
      <th>Purchase Count</th>
      <th>Item Price</th>
      <th>Total Purchase Value</th>
    </tr>
    <tr>
      <th>Item ID</th>
      <th>Item Name</th>
      <th></th>
      <th></th>
      <th></th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>39</th>
      <th>Betrayal, Whisper of Grieving Widows</th>
      <td>11</td>
      <td>$2.35</td>
      <td>$25.85</td>
    </tr>
    <tr>
      <th>84</th>
      <th>Arcane Gem</th>
      <td>11</td>
      <td>$2.23</td>
      <td>$24.53</td>
    </tr>
    <tr>
      <th>31</th>
      <th>Trickster</th>
      <td>9</td>
      <td>$2.07</td>
      <td>$18.63</td>
    </tr>
    <tr>
      <th>175</th>
      <th>Woeful Adamantite Claymore</th>
      <td>9</td>
      <td>$1.24</td>
      <td>$11.16</td>
    </tr>
    <tr>
      <th>13</th>
      <th>Serenity</th>
      <td>9</td>
      <td>$1.49</td>
      <td>$13.41</td>
    </tr>
  </tbody>
</table>
</div>




```python
## Most Profitable Items
profitable_items_df = pd.DataFrame({"Purchase Count": purchase_data_df.groupby(["Item ID","Item Name"])["Price"].count(),
                                    "Item Price": purchase_data_df.groupby(["Item ID","Item Name"])["Price"].mean(),
                                    "Total Purchase Value": purchase_data_df.groupby(["Item ID","Item Name"])["Price"].sum()    
                                   })
profitable_items_df = profitable_items_df[["Purchase Count",
                                           "Item Price",
                                           "Total Purchase Value"]]
profitable_items_df = profitable_items_df.sort_values("Total Purchase Value", ascending=False)
profitable_items_df = profitable_items_df.iloc[0:5,:]
profitable_items_df["Item Price"] = profitable_items_df["Item Price"].map("${:,.2f}".format)
profitable_items_df["Total Purchase Value"] = profitable_items_df["Total Purchase Value"].map("${:,.2f}".format)
profitable_items_df
```




<div>
<style>
    .dataframe thead tr:only-child th {
        text-align: right;
    }

    .dataframe thead th {
        text-align: left;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th></th>
      <th>Purchase Count</th>
      <th>Item Price</th>
      <th>Total Purchase Value</th>
    </tr>
    <tr>
      <th>Item ID</th>
      <th>Item Name</th>
      <th></th>
      <th></th>
      <th></th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>34</th>
      <th>Retribution Axe</th>
      <td>9</td>
      <td>$4.14</td>
      <td>$37.26</td>
    </tr>
    <tr>
      <th>115</th>
      <th>Spectral Diamond Doomblade</th>
      <td>7</td>
      <td>$4.25</td>
      <td>$29.75</td>
    </tr>
    <tr>
      <th>32</th>
      <th>Orenmir</th>
      <td>6</td>
      <td>$4.95</td>
      <td>$29.70</td>
    </tr>
    <tr>
      <th>103</th>
      <th>Singed Scalpel</th>
      <td>6</td>
      <td>$4.87</td>
      <td>$29.22</td>
    </tr>
    <tr>
      <th>107</th>
      <th>Splitter, Foe Of Subtlety</th>
      <td>8</td>
      <td>$3.61</td>
      <td>$28.88</td>
    </tr>
  </tbody>
</table>
</div>


