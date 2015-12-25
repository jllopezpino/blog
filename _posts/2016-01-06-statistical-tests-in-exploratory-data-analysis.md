---
layout: post
title: Exploratory data analysis&#58; statistical tests
excerpt: "This is the first post of a series of posts on exploratory data analysis. It covers one of the most overlooked parts: statistical testing."
tags: [data science, exploratory data analysis, statistical tests]
modified: 2016-01-06
comments: true
image:
    feature: old-days-of-technology.jpg
---


I want to write a series of posts on *exploratory data analysis* and I'll start with one of the most overlooked part during data analysis: statistical testing.


While doing exploratory data analysis, most of the people focus on simple summary statistics using time as an excuse.
However, performing some statistical tests takes little time and lines of code as I will try to show here.


If you don't have any clue on how to start doing statistical tests on your dataset, I recommend to use the following table by [Pamela J. Ludford](http://www-users.cs.umn.edu/~ludford/stat_overview.htm) (University of Minnesota) as a cheatsheet:


<style>
table{
    border-collapse: collapse;
    border-spacing: 0;
    border:2px solid #000000;
}
th{
    border:2px solid #000000;
}

td{
    border:1px solid #000000;
}
</style>
||| Dependent variable |
||| Categorical  | Continuous  |
||| ------------- | ------------- |
|**Independent variable** | Categorical | Chi Square  | t-test, ANOVA  |
|| Continuous | LDA, QDA  | Regression |



<div class="container" id="notebook-container">

<div class="cell border-box-sizing text_cell rendered">
<div class="prompt input_prompt">
</div>
<div class="inner_cell">
<div class="text_cell_render border-box-sizing rendered_html">
<h1 id="Dataset">Dataset<a class="anchor-link" href="#Dataset">&#182;</a></h1>
</div>
</div>
</div>
<div class="cell border-box-sizing text_cell rendered">
<div class="prompt input_prompt">
</div>
<div class="inner_cell">
<div class="text_cell_render border-box-sizing rendered_html">
<p>I have chosen an <a href="http://archive.ics.uci.edu/ml/datasets/Statlog+%28German+Credit+Data%29">old credit dataset</a> from Professor Dr. Hans Hofmann (University of Hamburg) for the simple reasons that I'm more familiar with this domain and the set of attributes is nice.</p>

</div>
</div>
</div>
<div class="cell border-box-sizing code_cell rendered">
<div class="input">
<div class="prompt input_prompt">In&nbsp;[1]:</div>
<div class="inner_cell">
<div class="input_area">
<div class=" highlight hl-ipython2"><pre><span class="kn">import</span> <span class="nn">pandas</span> <span class="kn">as</span> <span class="nn">pd</span>

<span class="n">german_dataset_columns</span> <span class="o">=</span> <span class="p">[</span><span class="s">&#39;checking_account&#39;</span><span class="p">,</span> <span class="s">&#39;duration&#39;</span><span class="p">,</span> <span class="s">&#39;credit_history&#39;</span><span class="p">,</span> <span class="s">&#39;purpose&#39;</span><span class="p">,</span> <span class="s">&#39;credit_amount&#39;</span><span class="p">,</span> <span class="s">&#39;savings&#39;</span><span class="p">,</span>
                      <span class="s">&#39;employment_since&#39;</span><span class="p">,</span> <span class="s">&#39;instalment_rate&#39;</span><span class="p">,</span> <span class="s">&#39;status_and_sex&#39;</span><span class="p">,</span> <span class="s">&#39;other_credits&#39;</span><span class="p">,</span> <span class="s">&#39;residence_since&#39;</span><span class="p">,</span>
                      <span class="s">&#39;property&#39;</span><span class="p">,</span> <span class="s">&#39;age&#39;</span><span class="p">,</span> <span class="s">&#39;other_instalment&#39;</span><span class="p">,</span> <span class="s">&#39;housing&#39;</span><span class="p">,</span> <span class="s">&#39;number_of_credits&#39;</span><span class="p">,</span> <span class="s">&#39;job&#39;</span><span class="p">,</span> <span class="s">&#39;maintenace&#39;</span><span class="p">,</span>
                      <span class="s">&#39;telephone&#39;</span><span class="p">,</span> <span class="s">&#39;foreign&#39;</span><span class="p">]</span>

<span class="n">df</span> <span class="o">=</span> <span class="n">pd</span><span class="o">.</span><span class="n">read_csv</span><span class="p">(</span><span class="s">&quot;german.data.txt&quot;</span><span class="p">,</span>
             <span class="n">header</span><span class="o">=</span><span class="bp">None</span><span class="p">,</span>  <span class="c"># The textfile has no header row</span>
             <span class="n">delimiter</span><span class="o">=</span><span class="s">r&quot;\s+&quot;</span><span class="p">,</span>  <span class="c"># Columns are delimited by an undetermined number of spaces</span>
             <span class="n">names</span><span class="o">=</span><span class="n">german_dataset_columns</span><span class="p">,</span>
             <span class="n">index_col</span><span class="o">=</span><span class="bp">False</span><span class="p">)</span>  <span class="c"># There is no index column</span>
</pre></div>

</div>
</div>
</div>

</div>
<div class="cell border-box-sizing text_cell rendered">
<div class="prompt input_prompt">
</div>
<div class="inner_cell">
<div class="text_cell_render border-box-sizing rendered_html">
<h1 id="Chi-square">Chi-square<a class="anchor-link" href="#Chi-square">&#182;</a></h1>
</div>
</div>
</div>
<div class="cell border-box-sizing text_cell rendered">
<div class="prompt input_prompt">
</div>
<div class="inner_cell">
<div class="text_cell_render border-box-sizing rendered_html">
<p>We are going to conduct a chi-square test for independence.
The purpose of applying this test to two categorical variables is determining whether there is a noteworthy association between the two variables.</p>
<p>In the code below, we are going to check whether there is an association between the loan purpose and the situation of the bank account of the potential borrower.</p>

</div>
</div>
</div>
<div class="cell border-box-sizing code_cell rendered">
<div class="input">
<div class="prompt input_prompt">In&nbsp;[2]:</div>
<div class="inner_cell">
<div class="input_area">
<div class=" highlight hl-ipython2"><pre><span class="kn">from</span> <span class="nn">scipy.stats</span> <span class="kn">import</span> <span class="n">chi2_contingency</span>
<span class="n">df_observed</span> <span class="o">=</span> <span class="n">pd</span><span class="o">.</span><span class="n">crosstab</span><span class="p">(</span><span class="n">df</span><span class="o">.</span><span class="n">checking_account</span><span class="p">,</span> <span class="n">df</span><span class="o">.</span><span class="n">purpose</span><span class="p">)</span>
<span class="n">chi2</span><span class="p">,</span> <span class="n">p</span><span class="p">,</span> <span class="n">dof</span><span class="p">,</span> <span class="n">expected</span> <span class="o">=</span> <span class="n">chi2_contingency</span><span class="p">(</span><span class="n">df_observed</span><span class="p">)</span>
<span class="k">print</span><span class="p">(</span><span class="s">&quot;</span><span class="si">%.8f</span><span class="s">&quot;</span> <span class="o">%</span> <span class="n">p</span><span class="p">)</span>
</pre></div>

</div>
</div>
</div>

<div class="output_wrapper">
<div class="output">


<div class="output_area"><div class="prompt"></div>
<div class="output_subarea output_stream output_stdout output_text">
<pre>0.00003171
</pre>
</div>
</div>

</div>
</div>

</div>
<div class="cell border-box-sizing text_cell rendered">
<div class="prompt input_prompt">
</div>
<div class="inner_cell">
<div class="text_cell_render border-box-sizing rendered_html">
<p>Given the p-value for the hypothesis test of independence obtained above (0.00003171), this is interpreted as a 99.9968% likelihood that these two variables are dependent.</p>
<p>Now we can do a deep dive into the numbers and compare the expected values and the observed values:</p>

</div>
</div>
</div>
<div class="cell border-box-sizing code_cell rendered">
<div class="input">
<div class="prompt input_prompt">In&nbsp;[3]:</div>
<div class="inner_cell">
<div class="input_area">
<div class=" highlight hl-ipython2"><pre><span class="n">df_expected</span> <span class="o">=</span> <span class="n">pd</span><span class="o">.</span><span class="n">DataFrame</span><span class="p">(</span><span class="n">expected</span><span class="p">,</span> <span class="n">index</span><span class="o">=</span><span class="n">df_observed</span><span class="o">.</span><span class="n">index</span><span class="p">,</span> <span class="n">columns</span><span class="o">=</span><span class="n">df_observed</span><span class="o">.</span><span class="n">columns</span><span class="p">)</span>
<span class="n">df_expected</span>
</pre></div>

</div>
</div>
</div>

<div class="output_wrapper">
<div class="output">


<div class="output_area"><div class="prompt output_prompt">Out[3]:</div>

<div class="output_html rendered_html output_subarea output_execute_result">
<div>
<table border="1" class="dataframe">
<thead>
<tr style="text-align: right;">
  <th>purpose</th>
  <th>A40</th>
  <th>A41</th>
  <th>A410</th>
  <th>A42</th>
  <th>A43</th>
  <th>A44</th>
  <th>A45</th>
  <th>A46</th>
  <th>A48</th>
  <th>A49</th>
</tr>
<tr>
  <th>checking_account</th>
  <th></th>
  <th></th>
  <th></th>
  <th></th>
  <th></th>
  <th></th>
  <th></th>
  <th></th>
  <th></th>
  <th></th>
</tr>
</thead>
<tbody>
<tr>
  <th>A11</th>
  <td>64.116</td>
  <td>28.222</td>
  <td>3.288</td>
  <td>49.594</td>
  <td>76.72</td>
  <td>3.288</td>
  <td>6.028</td>
  <td>13.70</td>
  <td>2.466</td>
  <td>26.578</td>
</tr>
<tr>
  <th>A12</th>
  <td>62.946</td>
  <td>27.707</td>
  <td>3.228</td>
  <td>48.689</td>
  <td>75.32</td>
  <td>3.228</td>
  <td>5.918</td>
  <td>13.45</td>
  <td>2.421</td>
  <td>26.093</td>
</tr>
<tr>
  <th>A13</th>
  <td>14.742</td>
  <td>6.489</td>
  <td>0.756</td>
  <td>11.403</td>
  <td>17.64</td>
  <td>0.756</td>
  <td>1.386</td>
  <td>3.15</td>
  <td>0.567</td>
  <td>6.111</td>
</tr>
<tr>
  <th>A14</th>
  <td>92.196</td>
  <td>40.582</td>
  <td>4.728</td>
  <td>71.314</td>
  <td>110.32</td>
  <td>4.728</td>
  <td>8.668</td>
  <td>19.70</td>
  <td>3.546</td>
  <td>38.218</td>
</tr>
</tbody>
</table>
</div>
</div>

</div>

</div>
</div>

</div>
<div class="cell border-box-sizing code_cell rendered">
<div class="input">
<div class="prompt input_prompt">In&nbsp;[4]:</div>
<div class="inner_cell">
<div class="input_area">
<div class=" highlight hl-ipython2"><pre><span class="n">df_observed</span>
</pre></div>

</div>
</div>
</div>

<div class="output_wrapper">
<div class="output">


<div class="output_area"><div class="prompt output_prompt">Out[4]:</div>

<div class="output_html rendered_html output_subarea output_execute_result">
<div>
<table border="1" class="dataframe">
<thead>
<tr style="text-align: right;">
  <th>purpose</th>
  <th>A40</th>
  <th>A41</th>
  <th>A410</th>
  <th>A42</th>
  <th>A43</th>
  <th>A44</th>
  <th>A45</th>
  <th>A46</th>
  <th>A48</th>
  <th>A49</th>
</tr>
<tr>
  <th>checking_account</th>
  <th></th>
  <th></th>
  <th></th>
  <th></th>
  <th></th>
  <th></th>
  <th></th>
  <th></th>
  <th></th>
  <th></th>
</tr>
</thead>
<tbody>
<tr>
  <th>A11</th>
  <td>78</td>
  <td>26</td>
  <td>4</td>
  <td>72</td>
  <td>54</td>
  <td>5</td>
  <td>5</td>
  <td>13</td>
  <td>2</td>
  <td>15</td>
</tr>
<tr>
  <th>A12</th>
  <td>60</td>
  <td>21</td>
  <td>7</td>
  <td>39</td>
  <td>75</td>
  <td>2</td>
  <td>9</td>
  <td>13</td>
  <td>3</td>
  <td>40</td>
</tr>
<tr>
  <th>A13</th>
  <td>16</td>
  <td>2</td>
  <td>0</td>
  <td>11</td>
  <td>24</td>
  <td>1</td>
  <td>0</td>
  <td>4</td>
  <td>0</td>
  <td>5</td>
</tr>
<tr>
  <th>A14</th>
  <td>80</td>
  <td>54</td>
  <td>1</td>
  <td>59</td>
  <td>127</td>
  <td>4</td>
  <td>8</td>
  <td>20</td>
  <td>4</td>
  <td>37</td>
</tr>
</tbody>
</table>
</div>
</div>

</div>

</div>
</div>

</div>
<div class="cell border-box-sizing text_cell rendered">
<div class="prompt input_prompt">
</div>
<div class="inner_cell">
<div class="text_cell_render border-box-sizing rendered_html">
<p>It might be easier to compare both tables by doing some simple visualisations like the following one:</p>

</div>
</div>
</div>
<div class="cell border-box-sizing code_cell rendered">
<div class="input">
<div class="prompt input_prompt">In&nbsp;[5]:</div>
<div class="inner_cell">
<div class="input_area">
<div class=" highlight hl-ipython2"><pre><span class="o">%</span><span class="k">pylab</span> inline
<span class="kn">import</span> <span class="nn">matplotlib.pyplot</span> <span class="kn">as</span> <span class="nn">plt</span>
<span class="n">plt</span><span class="o">.</span><span class="n">figure</span><span class="p">(</span><span class="n">figsize</span><span class="o">=</span><span class="p">(</span><span class="mi">500</span><span class="p">,</span><span class="mi">100</span><span class="p">))</span>

<span class="c"># Join observed and expected values in a single dataframe</span>
<span class="n">df_plot</span> <span class="o">=</span> <span class="n">df_observed</span><span class="o">.</span><span class="n">join</span><span class="p">(</span><span class="n">df_expected</span><span class="p">,</span> <span class="n">how</span><span class="o">=</span><span class="s">&#39;outer&#39;</span><span class="p">,</span> <span class="n">lsuffix</span><span class="o">=</span><span class="s">&#39; observed&#39;</span><span class="p">,</span> <span class="n">rsuffix</span><span class="o">=</span><span class="s">&#39; expected&#39;</span><span class="p">,</span> <span class="n">sort</span><span class="o">=</span><span class="bp">True</span><span class="p">)</span>

<span class="c"># We sort the columns alphabetically</span>
<span class="n">df_plot</span> <span class="o">=</span> <span class="n">df_plot</span><span class="o">.</span><span class="n">reindex_axis</span><span class="p">(</span><span class="nb">sorted</span><span class="p">(</span><span class="n">df_plot</span><span class="o">.</span><span class="n">columns</span><span class="p">),</span> <span class="n">axis</span><span class="o">=</span><span class="mi">1</span><span class="p">)</span>

<span class="c"># We plot </span>
<span class="n">df_plot</span><span class="o">.</span><span class="n">transpose</span><span class="p">()</span><span class="o">.</span><span class="n">plot</span><span class="p">(</span><span class="n">kind</span><span class="o">=</span><span class="s">&#39;bar&#39;</span><span class="p">,</span> <span class="n">stacked</span><span class="o">=</span><span class="bp">True</span><span class="p">,</span> <span class="n">title</span><span class="o">=</span><span class="s">&#39;Expected and Observed values&#39;</span><span class="p">,</span> <span class="n">figsize</span><span class="o">=</span><span class="p">(</span><span class="mi">10</span><span class="p">,</span> <span class="mi">10</span><span class="p">))</span>
</pre></div>

</div>
</div>
</div>

<div class="output_wrapper">
<div class="output">


<div class="output_area"><div class="prompt"></div>
<div class="output_subarea output_stream output_stdout output_text">
<pre>Populating the interactive namespace from numpy and matplotlib
</pre>
</div>
</div>

<div class="output_area"><div class="prompt output_prompt">Out[5]:</div>


<div class="output_text output_subarea output_execute_result">
<pre>&lt;matplotlib.axes._subplots.AxesSubplot at 0x107fe3550&gt;</pre>
</div>

</div>

<div class="output_area"><div class="prompt"></div>


<div class="output_text output_subarea ">
<pre>&lt;matplotlib.figure.Figure at 0x10391a090&gt;</pre>
</div>

</div>

<div class="output_area"><div class="prompt"></div>


<div class="output_png output_subarea ">
<img src="data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAAlIAAAKoCAYAAAC81hEMAAAABHNCSVQICAgIfAhkiAAAAAlwSFlz
AAALEgAACxIB0t1+/AAAIABJREFUeJzs3X28VWWd///XB9BCsZApjbQbxtTRElETIXM8p4L0a6mZ
YWSF400zU45MU406ZR2ynBqrn01969eNJseyxBoZbLKwOBSWZt6EqZRjE5makJOoKOBRru8fa4Eb
POdwznX22nufzev5eOwHe6291/5c116LzZtr3UVKCUmSJA3dqGY3QJIkaaQySEmSJGUySEmSJGUy
SEmSJGUySEmSJGUySEmSJGUySEkalIg4JSKWNajW0og4bRjLvzQiNkbEiPqNK9v8l3X+zBH5XUgj
hX+xpIpExMqIeDwiHq15/HsT2zOscNJgqXz0KSL2j4hFEbEmIh6JiCURMb2B7ZMkwCAlVSkBb0gp
7VLzOKvJ7RnxImIv4KfAcuClwETgKmBxRExrYDsiIqJR9SS1JoOU1AQR8cWI+HbN9Ccj4ofl846I
uDcizo2IP0XE7yLibTXvfVZEfCoifh8RD5Sf9eya14+LiF9GxMMRcXdEvD4iPg4cAXy+dmQsIv4q
Iq6NiP+NiF9HxFtqPucvylGfhyPi58Be2+jTlRHxx3KU6McRsX/Na5dGxP+NiO+WI0g31O7CiogZ
Zf01EfE5IMpHX7qAn6aUzksprUkpPZZS+hxwGfDJrd57WkTcFxH3R8T7aupNjYibyr49EBGfrnlt
WkT8LCIeKr/HI2teWxoRH4uInwKPAR+IiF9s9T28NyL+c5Dr6gNl2+6NiFMH+G5P2kadYyLi1rI/
90TERwb4rJUR8dqa6a6IuGyQ/T8lIn5brsP/qd0upe1WSsmHDx8VPIDfAa/t57WxwG+AORQB50/A
C8vXOoBe4FPADsBfA2uBfcrX/z9gITAeGAcsAi4oX5sKrNlUF3ghsG/5vAc4taYNOwN/KNswCphS
tmO/8vVvlY+xwMuBe4GfDNDfU8rP3KFs4601r10KPAi8EhgNfB34Zvna84BHgBPK1/6x7P+p/dT5
IzCnj/mdwJPAsyhGqjYC3yjb/wpgdc33cj1wcvl8J+Cw8vkeZTuPKqdfV07/RTm9FFgJ7Fd+Z88p
2/6ymnb8Apg1iHV1FPAAsH/ZhsvLNv9lP9vLQHWOBF5ePj+g/NzjyulN38Womu3yNTWf8xHgsm31
v1y3DwN7l6/tDuzf7L9nPnw0++GIlFSdABaW/7Pf9DgNIKW0DngHxT+0lwFnppTu32r581JKvSml
nwD/BcwqdyWdAfxTKkZj1gL/Cry1XOY04OKU0o/KOvenlH6zVZs2eQPwu5TS/JTSxpTSL4H/AN4S
EaMpgs2HU0rrUkp3APPpf5SIlNKlqRgd6gXmAQdGxC6bXgb+I6V0U0rpKYqAM6V87f8At6eU/iOl
9FRK6SKKINCf51GEqa39kSLcTKiZN69s/+3A14DZ5fwngL0j4nkppcdTSj8v578d+F5K6ftln34I
3AQcU9OPS1NKK8rv7BHgPzd9bkTsDewLLBrEupoFXJJSujOl9DhFoOlTub30Wad8/cflOiKl9CuK
AHxk35/2DLXrdKD+J4pAdkBEjE0prUop3TnIGlLbMkhJ1UkUowK71jwu3vxiSjcC/1NOXrnVsg+V
/3hu8nuKY4GeRzF6cfOmcAZcU84H2BP47TbatMlLgMNqgx7wNoqRhucBYyhGrDa5p78PjYhREfGJ
clfiwxSjHtS0C2BVzfN1FCM0UIya3bvVR/6B/j1YLrO1iRT/0D/Uz+fcU7PcacA+wIqIuDEiNgWl
l1AEydrv5HDgBQO07XKeDmhvA65KKa0Hns/A62piH+0bSH91iIjDIqInIlZHxBrgbylGkYaq3/6X
Ye8k4O+A+8vdtPtm1JDaikFKapKIeA+wI3A/8M9bvbxrROxUM/2S8n0PUoSQ/WvC2fiU0nPK9/0B
eFk/Jbc+2Pwe4MdbBb1dUkrvKes8Cby45v0vpn8nA8dS7Dp7LjBpUzcHWGaT+4EXbZooR3Je1P/b
+SHwlj7mzwJ+tilc9NHmFwP3AaSU7k4pvS2l9HyK46q+XX7f91Ds5tr6O/m3ms/Z+nv8IfD8iDiQ
YrTp8nL+ttbVH/to30D6q0P5fCGwZ0ppPPD/0//v+2MUu+k2eUFNnwbsf0ppcUppZrnMr4GvbKPN
UtszSEnV6jNIRMQ+wPkUAeSdwD+X/0DWmhcRO0TEERS7Vq5MKSWKf7wuiojnl5+1R0TMLJe5GPib
iHhNOUq0R82owSq2PGD8u8A+EfH2ss4OEXFoRPxVufvtP4CuiBgbxYHjc+j/zL9xwAbgzxGxM3DB
YL6H0veAl0fEmyJiDHAWW44AbW0e8KryoO9dI2KXiPgHil2lZ2/13g+V7X85xTFcVwCUfX5++Z6H
y349RXHs1hsjYmZEjI6IZ0dx8P8e/fWl3JV5JcUxbbsC15bzNzLwuloAnBIR+5Uhrt9dewPVKY2j
GMV8IiKmUoxY9beufgm8NSLGRMQrgTfXvNZv/yNityhOZNiZ4hi2x8rvTNquGaSkal0dW15H6jvl
8UeXAZ9IKf0qpXQ38C/AZRGxQ7ncAxS7qO4v3/u3KaW7ytfOBu4Gbih3o11LsZuKlNIvgL+hOPZq
DcXB0ZtGOj4LnBgRf46Ii8pjdmZSjG7cRzFC8q8Uo2QAZ1L8A/0AcEn56E83xe7H+4DbKQ7mrv2H
vK/rQqWyzQ9SjDB9gmIU52XAdf0VKr+vVwMHUhz4fT/wJmBmSun6rT7/xxTf1Q+BC8tjfgBeD9we
EY9SfFdvTSltSCndCxxHsT5WU4zQvI8tw1NfAeVy4LUUYXdjzfyB1tX3gYuAJcBdwI/6+ezB1Hk3
8NGIeAQ4jzIw9tPm8ygC9UMUZ0B+Y/ObBu7/KOC9FOv4fylOkvj7bbRXantR/Ae3nxeL03R/THEW
zBjg2ymlroiYQPEX9SUUP2SzUkprymXOBU6l+J/KWSmlxZX2QGozEdFBsXtloN1bkqQWMOCIVHms
QWdKaQrFGTZHRcRhwDnAtSmlfSj+F3UOFFcbpjgYcX+KU3u/EN6WQJIktalthpzyTA0ohvt3oBgi
PpbiVGjKP48vnx9HcW2Y3pTSSooh7an1bLC0nWiLq5BLUrvbZpAqD1j9JcWBqovLU7Z3TyltOpV5
FcXp0vDM05jvpbjAm6RBSiktTSlt6wwuSVILGLOtN5QHNE6JiOcCV0XEK7Z6PUXEQP97fsZr23i/
JElSS0kp9Xn28aCPX0opPUxxi4nXA6si4gUAETGR4uwOKM7mqD1Ads9yXl+fN+THRz7ykYZe9r2d
67Vz36xnPes1r147981622+9gQwYpCLieRExvnw+FpgBrKC4LcGc8m1zKC4ERzn/rRGxY0RMAvYG
bhywBZIkSSPUtnbtTQTml9e9GQVckVL6XkTcACyI4r5hKymuKExK6c6IWADcSXFV5HenbUU5SZKk
EWrAIJWKm18e3Mf8P1PcFbyvZS7gmVc1rouOjo4qPna7rNfOfbOe9azXvHrt3DfrWa8vA16QsyoR
4UCVJEkaESKC1M/B5ts8a0+SJFWnuE+3WsVQB3oMUpIkNZl7aVpDTqj19i2SJEmZDFKSJEmZDFKS
JEmZDFKSJEmZDFKSJEmZDFKSJI0Ap5xyCt/5zncq+5wzzjiDFStWDPvzW9Xy5cu55ppr6v65BilJ
kkaAel1vqr/P+cpXvsJ+++1Xlxqt6NZbb+V73/te3T/XICVJUgvq7u7mwAMPZMqUKbzzne8E4Cc/
+QmHH344e+211xajShdeeCFTp07lwAMPpKurq8/PmDNnzub5m8LUeeedx6mnnsrGjRvp6Ojglltu
AWDcuHF86EMfYsqUKUyfPp3Vq1cD8Nvf/pZp06YxefJkPvShD7HLLrv02/7HHnuM173udRxyyCFM
njyZRYsWDdi3VatW8aY3vYkpU6YwZcoUbrjhBgA+85nPcMABB3DAAQfw2c9+FoCVK1dywAEHbP68
T33qU8ybNw8obgNzzjnncNhhh7Hvvvty3XXX0dvby4c//GGuuOIKDjroIK688sohro3+eUFOSZJa
zB133MHHP/5xrr/+eiZMmMBDDz3EP/3TP/HAAw/w05/+lBUrVnDsscfy5je/mcWLF3P33Xdz4403
snHjRo477jiWLVvGhAkTtviMNWvWbP78lBIf+MAHeOyxx7jkkkuALUeqHn/8caZPn87HPvYxzj77
bL7yla/wwQ9+kLlz5/Le976Xk046iS996UsD9uHZz342V111FbvssgsPPvgg06dP59hjj31G3za1
66yzzqKzs5OrrrqKlBKPPvooN998M5deeunmvh122GEceeSRjB8/fotaEbG5/RHBU089xc9//nOu
ueYa5s2bx7XXXsv555/PzTffzL//+7/XZR1t4oiUJEktZsmSJcyaNYsJEyYAsOuuuwJw/PHHA7Df
fvuxatUqABYvXszixYs56KCDOOSQQ/jNb37D3XffTU9PzxafsSl8pJQ4//zzeeSRR/jCF77QZ/0d
d9yRY445BoBDDjmElStXAnDDDTfwlre8BYDZs2cP2IeNGzdy7rnncuCBBzJjxgzuv/9+Vq1a9Yy+
bWpXT08Pf//3fw8UYeg5z3kO1113HSeccAJjx45l55135oQTTmDZsmV97p6svTr8CSecAMDBBx+8
ue0ppUquIO+IlCRJLaa8Se4z5u+4446bn9e+fu655/Kud71ri/d+/vOf7/MzIoJDDz2Um2++mYce
emhzSKu1ww47bH4+atQonnzyySH34Rvf+AYPPvggt9xyC6NHj2bSpEmsX7++375t3adNba2dl1Ii
IhgzZgwbN27cPH/dunVbhKtnPetZAIwePTqr7UPhiJQkSS3mNa95DVdeeSV//vOfATb/2ZfXv/71
XHLJJTz22GMA3HffffzpT396xmc89NBDm5c56qijOOecczjmmGNYu3btoNs1bdo0vv3tbwPwrW99
a8D3PvLII+y2226MHj2anp4efv/73xMR/bbrta99LV/84hcBeOqpp3jkkUc44ogjWLhwIevWreOx
xx5j4cKFHHHEEey2226sXr2aP//5z2zYsIHvfve722z7c57zHB599NFB93WwDFKSJLWY/fffnw9+
8IMceeSRTJkyhfe9731bHAcETx/TNGPGDN72trcxffp0Jk+ezKxZs1i7dm2fn1G77IknnsgZZ5zB
sccey/r167eov3WdTdMXXXQRn/nMZ5gyZQq//e1vee5zn9tvH04++WRuuukmJk+ezGWXXbb5jMD+
2vXZz36Wnp4eJk+ezCtf+UpWrFjBQQcdxCmnnMLUqVOZNm0aZ5xxBgceeCA77LADH/7wh5k6dSoz
Z85k//3377cdm9re2dnJnXfeWfeDzaMZd5yOiOSdriVJ6n83Xitat24dY8eOBYoRqSuuuIKrrrqq
ya2qn/7WRTm/z+tGeIyUJEkalJtvvpkzzzyTlBK77rrr5jP+tmeOSEmS1EQjaUSqL7/61a82Xwtq
k2c/+9lcf/31TWpRvpwRKYOUJElNNNKDVDvJCVIebC5JkpTJICVJkpTJICVJkpTJICVJkpTJICVJ
kpTJICVJUpvZdDXyKh9D1dHRwYQJE3jiiSc2z+vp6aGzs5Px48czadKkZyxz3nnnccABB7DDDjsw
b968YX0nVTFISZLUllKFj6FZuXIly5YtY9SoUSxatGjz/HHjxnH66adz4YUX9rnc3nvvzYUXXsgx
xxyTFd4awSAlSZIq1d3dzfTp05kzZw7z58/fPP/QQw/l5JNP7nM0CuCd73wnRx11FLvsskvLXmvL
W8RIkqRKdXd38/73v3/zzYdXr17Nbrvt1uxm1YUjUpIkqTLXXXcd99xzD7NmzeLggw9mr7324vLL
L292s+rGICVJkiozf/58Zs6cyYQJEwCYPXv2Frv3Rjp37UmSpEqsW7eOBQsWsHHjRiZOnAjAhg0b
WLNmDbfddhuTJ08e9Gd5sLkkSdquLFy4kDFjxrBixQqWL1/O8uXLWbFiBUcccQTd3d2klFi/fj29
vb2klNiwYcMWl0d48sknWb9+PU899RS9vb2sX7+ejRs3NrFHzxTNOAo+IlKrHn0vba+G87+9nL/P
ja4ntaqIqPs23YjRm8G0+eijj+YVr3jFMy5vcOWVVzJ37lwuu+wyZsyYATz9PXR0dLBkyRIATjnl
FLq7u7dY9tJLL+Wd73xnnXqxpf7WRTm/zy/VICUJKH94e3qGvmBnZ36QamA9qVVVEaSUJydIuWtP
kiQpk0FKkiQpk0FKkiQpk0FKkiQpk0FKkiQpk0FKkiQpk0FKkiQpk0FKkiQpk0FKkiQpk0FKkqQ2
ExGVP4aqo6ODCRMmbHEvvZ6eHjo7Oxk/fjyTJk3a4v1/+tOfmD17NnvssQfjx4/n1a9+NTfeeOOw
v5t6G9PsBkiSpAp0tc5nr1y5kmXLlrHrrruyaNEiTjzxRADGjRvH6aefzuOPP84FF1ywxTJr167l
sMMO46KLLmK33Xbjq1/9KscccwwrV65k5513rlNHhs8RKUmSVKnu7m6mT5/OnDlzmD9//ub5hx56
KCeffPIzRqMAJk2axD/+4z+y++67ExGcccYZPPHEE9x1112NbPo2OSIlSZIq1d3dzfvf/36mTp3K
tGnTWL16NbvtttuQPuOXv/wlTzzxBC972csqamUeR6QkSVJlrrvuOu655x5mzZrFwQcfzF577cXl
l18+pM945JFHeMc73kFXVxe77LJLRS3NY5CSJEmVmT9/PjNnzmTChAkAzJ49e4vde9uybt063vjG
N/KqV72Ks88+u6pmZnPXniRJqsS6detYsGABGzduZOLEiQBs2LCBNWvWcNtttzF58uQBl9+wYQPH
H388L37xi/nSl77UiCYPmSNSkiSpEgsXLmTMmDGsWLGC5cuXs3z5clasWMERRxxBd3c3KSXWr19P
b28vKSU2bNiw+fIIvb29nHjiiey0005ceumlze3IAByRkiSpHXU1uwHFQeannnoqe+655xbzzzzz
TObOncvRRx/NjBkzgOLaV2PHjqWjo4MlS5bws5/9jP/6r/9ip512Yvz48ZuX/f73v8/hhx/e0H4M
JFJKjS8akZpRV1L/IgJ6eoa+YGcnOX+fG11PalUR4TbdIvpbF+X8Pq9C6q49SZKkTAYpSZKkTAYp
SZKkTAYpSZKkTAYpSZKkTAYpSZKkTAYpSZKkTAYpSZKkTAYpSZKkTAYpSZLaTERU/hiqjo4OJkyY
sPleegA9PT10dnYyfvx4Jk2a9IxlOjs72W233Xjuc5/LlClTWLRo0bC+lyoYpCRJakOpwsdQrVy5
kmXLljFq1KgtwtC4ceM4/fTTufDCC/tc7nOf+xwPPPAADz/8MF/+8pd5+9vfzqpVqzJaUB2DlCRJ
qlR3dzfTp09nzpw5zJ8/f/P8Qw89lJNPPrnP0SiAV7ziFYwa9XRU6e3t5Q9/+EPl7R0Kg5QkSapU
d3c3b3/72zn55JP5wQ9+wOrVqwe97Bve8AbGjh3LtGnT6Ozs5JWvfGWFLR06g5QkSarMddddxz33
3MOsWbM4+OCD2Wuvvbj88ssHvfx3v/td1q5dy/e+9z1mzJhRYUvzGKQkSVJl5s+fz8yZM5kwYQIA
s2fP3mL33mCMHj2ao446isWLF3P11VdX0cxsY5rdAEmS1J7WrVvHggUL2LhxIxMnTgRgw4YNrFmz
httuu43JkycP6fN6e3v5n//5nyqams0RKUmSVImFCxcyZswYVqxYwfLly1m+fDkrVqzgiCOOoLu7
m5QS69evp7e3l5QSGzZs2Hx5hN/85jdcc801rFu3jt7eXr7+9a+zbNkyjjzyyCb3akuOSEmS1IaG
fqWn+uvu7ubUU09lzz333GL+mWeeydy5czn66KM3H/cUEYwdO5aOjg6WLFlCSol58+Zx0kknMXr0
aPbZZx8WLFjAlClTmtGVfkVKOVeEGGbRiNSMupL6FxHQ0zP0BTs7yfn73Oh6UquKCLfpFtHfuijn
95lN3bUnSZKUySAlSZKUySAlSZKUySAlSZKUySAlSZKUySAlSZKUySAlSZKUySAlSZKUySAlSZKU
ySAlSVKbiYjKH0PV0dHBhAkTNt9LD6Cnp4fOzk7Gjx/PpEmT+l32xz/+MaNGjeK8887L+j6q5L32
JElqRzm3YBqszs4hvX3lypUsW7aMXXfdlUWLFnHiiScCMG7cOE4//XQef/xxLrjggj6X7e3tZe7c
uUybNi0rwFVtwBGpiHhRRPRExB0RcXtEnFXO74qIeyPi1vJxdM0y50bEf0fEryNiZtUdkCRJra27
u5vp06czZ84c5s+fv3n+oYceysknnzzgaNSnP/1pjjrqKPbdd9+WvCfhtkakeoH3ppR+GRHjgJsj
4logAZ9JKX2m9s0RsT9wErA/sAfww4jYJ6W0sYK2S5KkEaC7u5v3v//9TJ06lWnTprF69Wp22223
bS73+9//nq997WvccsstvOc972lAS4duwBGplNIDKaVfls/XAisoAhJAX+NrxwHfTCn1ppRWAncD
U+vXXEmSNJJcd9113HPPPcyaNYuDDz6Yvfbai8svv3xQy5511ll87GMfY+edd84+Nqtqgz7YPCJe
ChwE3FDOOjMilkfExRExvpz3QuDemsXu5engJUmStjPz589n5syZTJgwAYDZs2dvsXuvP1dffTVr
167lLW95CwAppRG5aw+Acrfet4G5KaW1EfFF4KPly+cDnwZO62fxPnvd1dW1+XlHRwcdHR2Da7Ek
SRoR1q1bx4IFC9i4cSMTJ04EYMOGDaxZs4bbbruNyZMn97vskiVLuOmmmzYv9/DDDzN69Ghuv/12
rrrqqkrbvXTpUpYuXTqo924zSEXEDsB3gK+nlBYCpJRW17z+VeDqcvI+4EU1i+9ZznuG2iAlSZLa
z8KFCxkzZgzLly9nxx13BIqRpVmzZtHd3c2FF17Ihg0b6O3tJaXEhg0biAh23HFHzj//fM4999zN
y8ydO5c99tijIZdA2HqAZ968ef2+d8AgFcXOyIuBO1NKF9XMn5hS+mM5+SbgV+XzRcDlEfEZil16
ewM3Dr0LkiRpWIZ4iYIqdHd3c+qpp7LnnntuMf/MM89k7ty5HH300cyYMQMorn01duxYOjo6WLJk
CePGjWPcuHGblxk7diw777wz48ePp5XEQPsbI+LVwE+A23h6F92/ALOBKeW83wF/m1JaVS7zL8Cp
wJMUuwJ/0MfnplbczyltzyIi77oznZ1Zxy00up7UqiLCbbpF9Lcuyvl9Huk+4IhUSuk6+j4g/ZoB
lrkA6PuqWpIkSW3EW8RIkiRlMkhJkiRlMkhJkiRlMkhJkiRlMkhJkiRlGtSVzSVJUnVa8R5yGhyD
lCRJTeQ1pEY2d+1JkiRlMkhJkiRlMkhJkiRlMkhJkiRlMkhJkiRlMkhJkiRlMkhJkiRlMkhJkiRl
MkhJkiRlMkhJkiRlMkhJkiRlMkhJkiRlMkhJkiRlMkhJkiRlMkhJkiRlMkhJkiRlMkhJkiRlMkhJ
kiRlGtPsBmwtIrKXTSm1fD1JktQ+Wi5IAdDTM/RlOjtHTj1JktQW3LUnSZKUySAlSZKUySAlSZKU
ySAlSZKUySAlSZKUySAlSZKUySAlSZKUySAlSZKUySAlSZKUySAlSZKUySAlSZKUySAlSZKUySAl
SZKUySAlSZKUySAlSZKUySAlSZKUySAlSZKUySAlSZKUySAlSZKUySAlSZKUySAlSZKUySAlSZKU
ySAlSZKUySAlSZKUySAlSZKUySAlSZKUySAlSZKUySAlSZKUySAlSZKUySAlSZKUySAlSZKUySAl
SZKUySAlSZKUySAlSZKUySAlSZKUySAlSZKUySAlSZKUySAlSZKUySAlSZKUySAlSZKUySAlSZKU
ySAlSZKUySAlSZKUySAlSZKUySAlSZKUySAlSZKUySAlSZKUySAlSZKUySAlSZKUySAlSZKUySAl
SZKUySAlSZKUySAlSZKUySAlSZKUySAlSZKUySAlSZKUySAlSZKUacAgFREvioieiLgjIm6PiLPK
+RMi4tqIuCsiFkfE+Jplzo2I/46IX0fEzKo7IEmS1CzbGpHqBd6bUno5MA14T0TsB5wDXJtS2gf4
UTlNROwPnATsDxwFfCEiHPWSJEltacCQk1J6IKX0y/L5WmAFsAdwLDC/fNt84Pjy+XHAN1NKvSml
lcDdwNQK2i1JktR0gx4tioiXAgcBPwd2TymtKl9aBexePn8hcG/NYvdSBC9JkqS2M2Ywb4qIccB3
gLkppUcjYvNrKaUUEWmAxft8raura/Pzjo4OOjo6BtMUSZKkSi1dupSlS5cO6r3bDFIRsQNFiLos
pbSwnL0qIl6QUnogIiYCq8v59wEvqll8z3LeM9QGKUmSpFax9QDPvHnz+n3vts7aC+Bi4M6U0kU1
Ly0C5pTP5wALa+a/NSJ2jIhJwN7AjUNsvyRJ0oiwrRGpw4G3A7dFxK3lvHOBTwALIuI0YCUwCyCl
dGdELADuBJ4E3p1SGmi3nyRJ0og1YJBKKV1H/6NWr+tnmQuAC4bZLkmSpJbnNZ4kSZIyGaQkSZIy
GaQkSZIyGaQkSZIyGaQkSZIyGaQkSZIyGaQkSZIyGaQkSZIyGaQkSZIyGaQkSZIyGaQkSZIyGaQk
SZIyGaQkSZIyGaQkSZIyGaQkSZIyGaQkSZIyGaQkSZIyGaQkSZIyGaQkSZIyGaQkSZIyGaQkSZIy
GaQkSZIyGaQkSZIyGaQkSZIyGaQkSZIyGaQkSZIyGaQkSZIyGaQkSZIyGaQkSZIyGaQkSZIyGaQk
SZIyjWl2A6SRIiKyl00ptXw9SdLQGaSkoejpGfoynZ0jp54kaUjctSdJkpTJICVJkpTJICVJkpTJ
ICVJkpTJICVJkpTJICVJkpTJICVJkpTJICVJkpTJICVJkpTJICVJkpTJICVJkpTJICVJkpTJICVJ
kpTJICViay8zAAAgAElEQVRJkpTJICVJkpTJICVJkpTJICVJkpTJICVJkpTJICVJkpRpTLMbIKmF
dHY2uwWSNKIYpCRtljKWibq3QpJGDnftSZIkZTJISZIkZTJISZIkZTJISZIkZTJISZIkZTJISZIk
ZTJISZIkZTJISZIkZTJISZIkZTJISZIkZTJISZIkZTJISZIkZTJISZIkZTJISZIkZTJISZIkZTJI
SZIkZTJISZIkZTJISZIkZTJISZIkZTJISZIkZTJISZIkZTJISZIkZTJISZIkZTJISZIkZTJISZIk
ZTJISZIkZTJISZIkZRrT7Aa0hM7OZrdAkiSNQAYpIGUsE3VvhSRJGmkMUpKax9FgSSPcNo+RiohL
ImJVRPyqZl5XRNwbEbeWj6NrXjs3Iv47In4dETOrarikkS9lPCSplQzmYPOvAUdtNS8Bn0kpHVQ+
rgGIiP2Bk4D9y2W+EBEe0C6NEJHxkKTt2TZ37aWUlkXES/t4qa/f0OOAb6aUeoGVEXE3MBW4YTiN
lNQgXQ1aRpLaxHBGi86MiOURcXFEjC/nvRC4t+Y99wJ7DKOGJElSy8o92PyLwEfL5+cDnwZO6+e9
fR7W0NXVtfl5R0cHHR0dmU2RJEmqn6VLl7J06dJBvTcrSKWUVm96HhFfBa4uJ+8DXlTz1j3Lec9Q
G6QkSZJaxdYDPPPmzev3vVm79iJiYs3km4BNZ/QtAt4aETtGxCRgb+DGnBqSJEmtbpsjUhHxTeBI
4HkR8QfgI0BHREyh2G33O+BvAVJKd0bEAuBO4Eng3Sklz1iWJEltaTBn7c3uY/YlA7z/AuCC4TRK
kiRpJPAaT5IkSZkMUpIkSZkMUpIkSZkMUpIkSZkMUpIkSZkMUpIkSZkMUpIkSZkMUpIkSZkMUpIk
SZkMUpIkSZkMUpIkSZm2ea+9pujsbHYLJEmStqklg1TKWCbq3gpJkqSBuWtPkiQpk0FKkiQpk0FK
kiQpk0FKkiQpk0FKkiQpU0uetddonvEnSZJyGKQAuhq0jCRJaivu2pMkScpkkJIkScpkkJIkScpk
kJIkScpkkJIkScpkkJIkScpkkJIkScpkkJIkScpkkJIkScpkkJIkScpkkJIkScpkkJIkScpkkJIk
ScpkkJIkScpkkJIkSco0ptkNkLT9imY3QJKGySAlqXm6GrSMJFXEXXuSJEmZWnJEyuF+SZI0ErRk
kHK4X5IkjQTu2pMkScpkkJIkScpkkJIkScpkkJIkScpkkJIkScpkkJIkScpkkJIkScpkkJIkScrU
mhfklFTo7Gx2CyRJAzBISS0sZSzjLZYkqXHctSdJkpTJICVJkpTJICVJkpTJICVJkpTJICVJkpTJ
ICVJkpTJICVJkpTJICVJkpTJICVJkpTJICVJkpTJICVJkpTJICVJkpTJICVJkpTJICVJkpTJICVJ
kpTJICVJkpTJICVJkpTJICVJkpRpTLMbIKl/0ewGSJIGZJCSWllXg5aRJGUxSElD0dnZ7BZIklqI
QUoagpSxjLvnJKl9ebC5JElSJoOUJElSJoOUJElSJoOUJElSJoOUJElSJoOUJElSJoOUJElSJoOU
JElSJoOUJElSJoOUJElSJoOUJElSJu+112AR+XdeS2nod3rLrZdTS5Kk7c02g1REXAIcA6xOKR1Q
zpsAXAG8BFgJzEoprSlfOxc4FXgKOCultLiapo9gPT1DX6azs3H1hlNLkqTtyGB27X0NOGqreecA
16aU9gF+VE4TEfsDJwH7l8t8ISLcfShJktrSNkNOSmkZ8NBWs48F5pfP5wPHl8+PA76ZUupNKa0E
7gam1qepkiRJrSV3tGj3lNKq8vkqYPfy+QuBe2vedy+wR2YNSZKkljbsg81TSikiBjoyuc/Xurq6
Nj/v6Oigo6NjuE2RJEkatqVLl7J06dJBvTc3SK2KiBeklB6IiInA6nL+fcCLat63ZznvGWqDlCRJ
UqvYeoBn3rx5/b43d9feImBO+XwOsLBm/lsjYseImATsDdyYWUOSJKmlDebyB98EjgSeFxF/AD4M
fAJYEBGnUV7+ACCldGdELADuBJ4E3p28IJEkSWpT2wxSKaXZ/bz0un7efwFwwXAaJbWq/MupSpLa
kVc2l4aiq0HLSJJGBIOUJEmqzEi5NVpuPYOUJEmqVqvfGm0Y9bx9iyRJUiaDlCRJUiaDlCRJUiaD
lCRJUiaDlCRJUiaDlCRJUiaDlCRJUiaDlCRJUiaDlCRJUiaDlCRJUiaDlCRJUiaDlCRJUiaDlCRJ
UiaDlCRJUiaDlCRJUiaDlCRJUiaDlCRJUiaDlCRJUiaDlCRJUiaDlCRJUiaDlCRJUiaDlCRJUiaD
lCRJUiaDlCRJUiaDlCRJUiaDlCRJUiaDlCRJUiaDlCRJUiaDlCRJUiaDlCRJUiaDlCRJUiaDlCRJ
UiaDlCRJUqYxzW5AS+hqdgMkSdJIZJACIGUsE3VvhSRJGlnctSdJkpTJESlJT+tqdgMkaWQxSEmq
4W5uSRoKd+1JkiRlMkhJkiRlMkhJkiRlMkhJkiRlMkhJkiRlMkhJkiRlMkhJkiRl8jpSUivranYD
JEkDMUhJLc0LZEpSK3PXniRJUiaDlCRJUiaDlCRJUiaDlCRJUiaDlCRJUiaDlCRJUiaDlCRJUiaD
lCRJUiaDlCRJUiaDlCRJUiaDlCRJUiaDlCRJUqbWvGlxV7MbIEmStG2tGaS8470kSRoB3LUnSZKU
qUVHpNpcZ2ezWyBJkurAINUE7riUJKk9uGtPkiQpk0FKkiQpk0FKkiQpk8dISZKk9tLAk7oMUpIk
qa008qQug5QkSapWG1/2xyAlSZIq1c6X/fFgc0mSpEwGKUmSpEwGKUmSpEweI9UEI2W/ryRJGphB
qhm6GrSMJEmqlEFKkiS1lUbu+TFISZKk9tLVoGUwSElqpq5mN0CShscgJamJ2vkyfZK2B17+QJIk
KdOwRqQiYiXwCPAU0JtSmhoRE4ArgJcAK4FZKaU1w2ynJEkaodp5HHm4u/YS0JFS+nPNvHOAa1NK
/xYRZ5fT5wyzjiRJGqm6GrRME9Rj197WQfNYYH75fD5wfB1qSJIktZzhBqkELI6ImyLijHLe7iml
VeXzVcDuw6whSZLUkoa7a+/wlNIfI+L5wLUR8evaF1NKKSL6PC2nq6tr8/OOjg46OjqG2RRJUjuK
yD/CJqWhnxna6HpqPUuXLmXp0qWDeu+wglRK6Y/ln3+KiKuAqcCqiHhBSumBiJgIrO5r2dogJY0Y
Xc1ugLSd6ukZ+jKdnSOnnlrK1gM88+bN6/e92UEqInYCRqeUHo2InYGZwDxgETAH+GT558LcGqoT
/3LXkdc9kiQ9bTgjUrsDV5VDoGOAb6SUFkfETcCCiDiN8vIHw26lhmWo//T7z74kSYOTHaRSSr8D
pvQx/8/A64bTKEmSpJHAK5tLkiRl8l572wF31UmSVA2D1Pagq+L3S5K0nXLXniRJUiaDlCRJUiaD
lCRJUiaDlCRJUiaDlCRJUiaDlCRJUiaDlCRJUiaDlCRJUiaDlCRJUiaDlCRJUiaDlCRJUiaDlCRJ
UiaDlCRJUiaDlCRJUiaDlCRJUiaDlCRJUiaDlCRJUiaDlCRJUiaDlCRJUiaDlCRJUiaDlCRJUiaD
lCRJUiaDlCRJUiaDlCRJUiaDlCRJUiaDlCRJUiaDlCRJUiaDlCRJUiaDlCRJUqYxzW6AJEnb1NnZ
7BZIfTJISZJaXspYJoZT0OCmQTJISZK0lYYHN41YHiMlSZKUyRGpZuhqdgMkSVI9GKSawkFjSZLa
gUFKktTy/K+kWpVBSpLU+roatIyq0dXsBlTHILU96Gp2AyRpZHEErN7a95AWg9R2Yagb8MjYeCWp
Ml0NWkYjnpc/kCRJymSQkiRJymSQkiRJymSQkiRJymSQkiRJymSQkiRJyuTlDySpDUTkX7YkpaFf
4ye3Xk4tqZUZpCSpXfT0DH2Zzs7G1RtOLalFuWtPkiQpk0FKkiQpk0FKkiQpk0FKkiQpk0FKkiQp
k0FKkiQpk0FKkiQpk0FKkiQpk0FKkiQpk0FKkiQpk0FKkiQpk/fakyRJ7aWrcaUMUpKkPN6EWC0r
ZSwTWZUMUpKkPF0Vv79ey0oVMkhJUrto6xGixo0wAAY3DZpBSpLaRIOjRpvz29TgGKRUNxH5PyIp
Df1Hq9H1NLJtD9un/4xLjWeQUn319Ax9meHsjmh0PY1s7b59djVoGUmbGaQ0shmKJElNZJDSyNbV
oGUkSeqDVzaXJEnKZJCSJEnK5K49SZKaaHs4o7SdGaQkSWq2dj+jtI0ZpCSpKv7DI7U9g5Tqy384
1MrcPiXVmUFKkqrS1aBlJDWNQUr11dWgZaQcXQ1aplm6mt0AaftjkJKktuGNdkcsdzuPWAYpSapK
V7MbULGuZjdA2QxudWOQkqTKtPsI0VD7N5L61mBdDVqmWfXamEFKI1tXsxsgSdqeGaRUX12NLtju
/+NXXXU1uwGS2o1BSnVmsFErc/uUVF+VBKmIOAq4CBgNfDWl9Mn6fPJSoKM+H7Xd12tkLetZz3rb
T71G1mqjel31/8gsvwMmNbLgUkb6+qt7kIqI0cDngdcB9wG/iIhFKaUVw//0pYz0L7x16jWylvWs
Z73tp14ja7VTvf5GS7voP2UNY7S0v49suKWM9PU3qq6fVpgK3J1SWplS6gW+BRxXQR1JkpQl9fP4
yACvqS9VBKk9gD/UTN9bzpMkSWorkVJ9U2ZEvBk4KqV0Rjn9duCwlNI/1LzHaCtJkkaMlFKf+1Kr
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
"
>
</div>

</div>

</div>
</div>

</div>
<div class="cell border-box-sizing text_cell rendered">
<div class="prompt input_prompt">
</div>
<div class="inner_cell">
<div class="text_cell_render border-box-sizing rendered_html">
<h1 id="ANOVA">ANOVA<a class="anchor-link" href="#ANOVA">&#182;</a></h1>
</div>
</div>
</div>
<div class="cell border-box-sizing text_cell rendered">
<div class="prompt input_prompt">
</div>
<div class="inner_cell">
<div class="text_cell_render border-box-sizing rendered_html">
<p>We are now going to use ANOVA to find what are the categorical variables that impact the loan amount requested by the borrower.</p>
<p>Our null hypothesis is that all groups are samples of the same population. Rejecting the null hypothesis would imply that different values of the categorical variable result in significantly different loan amounts.</p>

</div>
</div>
</div>
<div class="cell border-box-sizing code_cell rendered">
<div class="input">
<div class="prompt input_prompt">In&nbsp;[6]:</div>
<div class="inner_cell">
<div class="input_area">
<div class=" highlight hl-ipython2"><pre><span class="kn">from</span> <span class="nn">scipy.stats</span> <span class="kn">import</span> <span class="n">f_oneway</span>

<span class="k">def</span> <span class="nf">p_value_anova_from_df</span><span class="p">(</span><span class="n">df</span><span class="p">,</span> <span class="n">categorical_var</span><span class="p">,</span> <span class="n">continuous_var</span><span class="p">):</span>
<span class="c"># Get all the possible values that the categorical variable could take</span>
<span class="n">categorical_var_values</span> <span class="o">=</span> <span class="nb">set</span><span class="p">(</span><span class="n">df</span><span class="p">[</span><span class="n">categorical_var</span><span class="p">]</span><span class="o">.</span><span class="n">values</span><span class="p">)</span>

<span class="c"># Get the loan amounts for each of those values, creating a matrix</span>
<span class="n">loan_amounts</span> <span class="o">=</span> <span class="p">[</span><span class="n">df</span><span class="p">[</span><span class="n">df</span><span class="p">[</span><span class="n">categorical_var</span><span class="p">]</span> <span class="o">==</span> <span class="n">v</span><span class="p">][</span><span class="n">continuous_var</span><span class="p">]</span><span class="o">.</span><span class="n">values</span> <span class="k">for</span> <span class="n">v</span> <span class="ow">in</span> <span class="n">categorical_var_values</span><span class="p">]</span>

<span class="c"># Performs 1-way ANOVA.</span>
<span class="n">f</span><span class="p">,</span> <span class="n">p</span> <span class="o">=</span> <span class="n">f_oneway</span><span class="p">(</span><span class="o">*</span><span class="n">loan_amounts</span><span class="p">)</span>

<span class="k">return</span> <span class="n">p</span>


<span class="n">continuous_var</span> <span class="o">=</span> <span class="s">&#39;credit_amount&#39;</span>

<span class="k">for</span> <span class="n">categorical_var</span> <span class="ow">in</span> <span class="p">[</span><span class="s">&#39;checking_account&#39;</span><span class="p">,</span> <span class="s">&#39;credit_history&#39;</span><span class="p">,</span> <span class="s">&#39;purpose&#39;</span><span class="p">,</span> <span class="s">&#39;savings&#39;</span><span class="p">,</span> <span class="s">&#39;status_and_sex&#39;</span><span class="p">,</span> <span class="s">&#39;foreign&#39;</span><span class="p">]:</span>
<span class="c"># Get the p-value of the 1-way ANOVA</span>
<span class="n">p</span> <span class="o">=</span> <span class="n">p_value_anova_from_df</span><span class="p">(</span><span class="n">df</span><span class="p">,</span> <span class="n">categorical_var</span><span class="p">,</span> <span class="n">continuous_var</span><span class="p">)</span>

<span class="c"># Compute the means for visualisation purposes</span>
<span class="n">df_means</span> <span class="o">=</span> <span class="n">df</span><span class="o">.</span><span class="n">groupby</span><span class="p">(</span><span class="n">categorical_var</span><span class="p">)[</span><span class="n">continuous_var</span><span class="p">]</span><span class="o">.</span><span class="n">mean</span><span class="p">()</span>

<span class="c"># Plot the means and use the p-value as title</span>
<span class="n">plt</span><span class="o">.</span><span class="n">figure</span><span class="p">()</span>
<span class="n">ax</span> <span class="o">=</span> <span class="n">df_means</span><span class="o">.</span><span class="n">plot</span><span class="p">(</span><span class="n">kind</span><span class="o">=</span><span class="s">&#39;bar&#39;</span><span class="p">,</span> <span class="n">title</span><span class="o">=</span><span class="s">&quot;p=</span><span class="si">%.8f</span><span class="s">&quot;</span> <span class="o">%</span> <span class="n">p</span><span class="p">)</span>
<span class="n">ax</span><span class="o">.</span><span class="n">set_ylabel</span><span class="p">(</span><span class="s">&#39;loan amount&#39;</span><span class="p">)</span>
</pre></div>

</div>
</div>
</div>

<div class="output_wrapper">
<div class="output">


<div class="output_area"><div class="prompt"></div>


<div class="output_png output_subarea ">
<img src="data:image/png;base64,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
AElFTkSuQmCC
"
>
</div>

</div>

<div class="output_area"><div class="prompt"></div>


<div class="output_png output_subarea ">
<img src="data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAAYgAAAEjCAYAAAAomJYLAAAABHNCSVQICAgIfAhkiAAAAAlwSFlz
AAALEgAACxIB0t1+/AAAHeBJREFUeJzt3XuYXXV97/H3hwQkQjCANRAuBSU5EqqiKKlHrCNwaLwU
8MJNpajRqukRaq0abD2MPmqJfaoH6gEfq0BQocZWPHDASAzZiohEEEggRIISNCMJGEFuXhL4nj/W
b7KW428yey5r1p49n9fzrCdr/X7r8t3fyezvrLsiAjMzs4F2ajoAMzPrTC4QZmaW5QJhZmZZLhBm
ZpblAmFmZlkuEGZmluUCYWZmWS4QNmlJOkbSOkmPS7pO0oE7mHcvSVdIekzSBkmnDWddkhZL+mUa
zh3Qd5CklWnZuyQdM6D/TZLuS9u+QtKelb6nSbpI0q8l3S/pfQOW/XyK60lJZwzoO0PSzWnZn6cY
p7SfQet2LhA2KUl6JvBfwD8CewI3A1/dwSL/B/gt8CzgzcCFkua2sy5J7wJOAJ6fhr9Kbf0uB24B
9krr+M+0TiQdBnwubXMm8ARwQWXZXuA5wIHAK4EPSvrLSv9twELgR8DAu2KnAWcBewPzgGOAf9hB
DmyyiQgPHibcAGwAFgF3Ar8CLgKeNozl/wb4XmX66RRfvnMy8+4G/A44pNJ2KfDP7awL+D7wjkr/
24Eb0/gcisKzW6X/u8C70vgngS9X+p6dYtktTfcBx1b6PwZcnvkM1wN/PURO3gdc2fTP1kPnDN6D
sInsTcBxFH9BzwH+SdIBkh6W9NAgw6lp2cOA2/tXFBFPAD8B/iyznTnAtoi4p9J2e1rHjtbV3z+3
2g+sHrDsTyPi8TbX/VPg98CcdKhp3x2se7heAdwxwmWtC01tOgCzEQrgsxHRByDpE8C/RcRHgBlt
LL8b8OCAtl8Du2fm3R14ZEDbI8D0Sv8DmXVV+389yHYG9vWve99KnAP7+9e9e2U6t922SXo78CKK
vRszwOcgbGL7eWX8Z8CsYSz7GLDHgLY9gEeHMW9/0Xh0iHUNXH6P1NZOHI8Bzxik/7HK9FCfYVCS
TqQ4lPWqiPjVcJa17uYCYRPZgQPG+9IhpsckPTrI0H/10Z3AC/oXlrQbxaGqOzPbuRuYKumQStsL
KvMOta47gcMHLHtHpe/ZknYf0D/Yup8NPA24OyIeAu7fwbqHJGk+8HngtRGR++w2mTV9EsSDh5EM
FCepbwf2o7j653vAx4ex/DOBh4HXA7sCi4Hv72D+y4HLKE5Avywte2g76wLeBayl2MOZRfEF/jeV
/huBf0nLvg54CNg79c2lOGx0FMXhpi8Dl1WW/WegRXFY7bnAL4DjKv07p/XeALwjjSv1HQ1sAY5q
+ufpoTOHxgPw4GEkA3Av8CGKv7AfAi4Gdh3mOo4B7qK44ug64MBK34eBayrTewJXUBzW2QCc2u66
Uv/i9GW8BTh3QN+fAivTsncBRw/oPw24L237CmBGpW8X4IupiGwC/m7Asi3gKeDJ9O9TwF+kvuso
Tng/Whmubvpn66Fzhv6/JGojaQbwBYorKwJ4G7Ce4jrxP6X4ZTs5Ih5O859NcaLsSeDMiLg2tR8B
XELxF9A1EXFWrYFbR5N0L7AgIq5rOhazbjUe5yDOo/hCP5TiJqF1FNevL4+IOcCKNE268egUit3q
+cAFkpTWcyHFF8JsYHY6dmpmZjWptUBIegbw8oi4CCAitkXEr4HjgSVptiXAiWn8BIqbfLZGxAbg
HmCepH2B6RGxKs13aWUZMzOrQd33QRwMPCjpYoqrK24B/g6YGRGb0zybKR4hAMUJvB9Ult9IcRJy
axrv15fabZKKiIObjsGs29V9iGkqxc03F0TEi4DHSYeT+kVxEqTeEyFmZjZsde9BbAQ2RsQP0/R/
AmcDmyTtExGb0uGj/rtQ+4ADKsvvn9bRl8ar7X0DNybJhcbMbJgiQrn2WvcgImIT8HNJc1LTsRSX
JV4F9D96+AzgG2n8SuBUSbtIOhiYDaxK63lE0rx00vr0yjIDt9nocM455zQeQ6cMzoVz4Vx0fi52
ZDyexfRe4CuSdqF4gNnbgCnAUkkLSJe5AkTEWklLKW4q2gYsjPITLKS4zHUaxVVRy8YhdjOzSav2
AhERtwMvyXQdO8j8n6R4LszA9luA541tdGZmNhg/i2mM9fT0NB1Cx3AuSs5FybkodXouar+TejxJ
im76PGZmdZNENHGS2szMJi4XCDMzy3KBMDOzLBcIMzPLcoEwM7MsFwgzM8tygTAzsywXCDMzy3KB
MDOzLBcIMzPLcoEwM7MsFwgzM8tygTAzsywXCDMzy3KBMDOzLBcIMzPLcoEwM7MsFwgzM8tygTAz
sywXCDMzy5radACdRMq+t7sREdF0CGY2yblA/JFO+GLunEJlZpOXDzGZmVmWC4SZmWW5QJiZWZYL
hJmZZdVeICRtkLRa0q2SVqW2vSQtl3S3pGslzajMf7ak9ZLWSTqu0n6EpDWp77y64zYzm+zGYw8i
gJ6IeGFEHJnaFgHLI2IOsCJNI2kucAowF5gPXKDy2tMLgQURMRuYLWn+OMRuZjZpjdchpoHXbR4P
LEnjS4AT0/gJwOURsTUiNgD3APMk7QtMj4hVab5LK8uYmVkNxmsP4lpJN0t6Z2qbGRGb0/hmYGYa
nwVsrCy7Edgv096X2s3MrCbjcaPcyyLifkl/AiyXtK7aGREhqRPuTjMzs4raC0RE3J/+fVDSFcCR
wGZJ+0TEpnT46IE0ex9wQGXx/Sn2HPrSeLW9L7e93t7e7eM9PT309PSMzQcxM+sCrVaLVqvV1ryq
85k/kp4OTImIRyXtBlwLfBQ4FtgSEYslLQJmRMSidJL6Mooish/wbeCQtJdxE3AmsAq4Gjg/IpYN
2F6M5vMU58M7YWdGfhaTmY0LSURE9vk+de9BzASuSBciTQW+EhHXSroZWCppAbABOBkgItZKWgqs
BbYBCyvf+AuBS4BpwDUDi4OZmY2tWvcgxpv3IMzMhmdHexC+k9rMzLJcIMzMLMsFwszMslwgzMws
ywXCzMyyXCDMzCzLBcLMzLJcIMzMLMsFwszMslwgzMwsywXCzMyyXCDMzCzLBcLMzLJcIMzMLMsF
wszMslwgzMwsywXCzMyyXCDMzCzLBcLMzLJcIMzMLMsFwszMslwgzMwsa2rTAZjZxCGp6RC2i4im
Q+h6LhBmNkyd8MXcOYWqm/kQk5mZZblAmJlZlguEmZlluUCYmVmWC4SZmWXVXiAkTZF0q6Sr0vRe
kpZLulvStZJmVOY9W9J6SeskHVdpP0LSmtR3Xt0xm5nZ+OxBnAWspbw2bhGwPCLmACvSNJLmAqcA
c4H5wAUqL7q+EFgQEbOB2ZLmj0PcZmaTWq0FQtL+wKuBL1BeuHw8sCSNLwFOTOMnAJdHxNaI2ADc
A8yTtC8wPSJWpfkurSxjZmY1qXsP4jPAB4CnKm0zI2JzGt8MzEzjs4CNlfk2Avtl2vtSu5mZ1ai2
O6klvRZ4ICJuldSTmyciQtKY3pbZ29u7fbynp4eenuymzcwmpVarRavVamte1fU8E0mfBE4HtgG7
AnsAXwdeAvRExKZ0+GhlRDxX0iKAiDg3Lb8MOAe4L81zaGo/DXhFRLw7s80YzecpTnl0xmME/JwZ
60T+Hek+koiI7LNLajvEFBEfjogDIuJg4FTguog4HbgSOCPNdgbwjTR+JXCqpF0kHQzMBlZFxCbg
EUnz0knr0yvLmJlZTcbzYX395f5cYKmkBcAG4GSAiFgraSnFFU/bgIWV3YGFwCXANOCaiFg2jnGb
mU1KtR1iaoIPMZnVy78j3aeRQ0xmZjaxuUCYmVnWkAVC0uJ22szMrLu0swdxXKbt1WMdiJmZdZZB
r2KS9B6Kq4eeI2lNpWs6cEPdgZmZWbMGvYpJ0jOAPSkuS/0Q5bOUHo2ILeMT3vD4Kiazevl3pPvs
6Cqmti5zlTSF4plJ2/c4IuJnYxbhGHGBMKuXf0e6z44KxJA3ykl6L8UjLx4Anqx0PW9swjMzs040
5B6EpJ8AR3bqYaUq70GY1cu/I91ntDfK/Qx4ZGxDMjOzTtfOs5juBVZKuhr4fWqLiPh0fWGZmVnT
2ikQP0vDLmnolH1MMzOrkR/W94fL0xm1z8dXrTP5d6T7jPYqppWZ5oiIo0cdmZmZdax2DjF9oDK+
K/AGivc1mJlZFxvRISZJP4yIl9QQz6j4EJNZvfw70n1Ge4hpr8rkTsCLKd4vbWZmXaydQ0w/ovyT
YRvFa0IX1BWQmZl1Bl/F9IfL491ns8H5d6T7jPYQ0y7Ae4C/oPif8R3gcxGxdUyjNDOzjtLOs5i+
SFFIllDcJHc6sC0i3lF/eMPjPYgxjEDZPyga0XQurOTfke4zqj0I4CUR8fzK9ApJq8cmNOtsnfAL
2DmFymyyaedhfdskHdI/Iek5+D4IM7Ou1+6NctdJujdNHwS8rbaIzMysI7T7RrldgTlp8scR8bta
oxohn4MYwwicC8vw/4vuM6pXjkqaCryGYs+hf4+jIx/37QIxhhE4F5bh/xfdZ7Qnqa8CfgOsAZ4a
y8DMzKxztVMg9htwFZOZmU0C7VzFtEzSXw53xZJ2lXSTpNsk3SGpN7XvJWm5pLslXStpRmWZsyWt
l7RO0nGV9iMkrUl95w03FjMzG752CsSNwNcl/VbSo2kY8h3VEfFb4JURcThwODBf0jxgEbA8IuYA
K9I0kuYCpwBzgfnABSrv1roQWBARs4HZkuYP72OamY0tSR0z1KWdAvEZ4KXA0yNiehraepprRDyR
RncBdqY4u3U8xV3ZpH9PTOMnAJdHxNaI2ADcA8yTtC8wPSJWpfkurSxjZtag6IChPu2+k/rOiBj2
CWpJO1E8DfY5wGcjYpWkmRGxOc2yGZiZxmcBP6gsvhHYD9iaxvv1pXazceHHjthk1U6BuBdYKemb
wO9TW1uXuaaicrikZwBXSPqzAf0haUz/x/f29m4f7+npoaenZyxXb5NWJ3wxd06hsomr1WrRarXa
mred+yB602j/jKL4bv/ocIKS9BHgCeCdQE9EbEqHj1ZGxHMlLaJY8blp/mXAOcB9aZ5DU/tpwCsi
4t2Zbfg+iLGKwLkoI3AuygicizKCLsnFqG6UG8VGn0nx1NeHJU0DvgWcC/QAWyJicSoKMyJiUTpJ
fRlwJMUhpG8Dh6S9jJuAM4FVwNXA+RGxLLNNF4ixisC5KCNwLsoInIsygi7JxWjfB/Es4IMUVxdN
S80REUcPsei+wBJJUyhOhn81Iq6R9ANgqaQFFG+nOzmtcK2kpcBaiocBLqx82y8ELknbvyZXHMzM
bGy1c4hpOfBV4B+AdwFvBR6MiA/WHt0weQ9iDCNwLsoInIsyAueijKBLcrGjPYh2LnPdOyK+APw+
Ir4TEW8Dhtp7MDOzCa6dq5j6r1zaJOm1wC+APesLyczMOkE7BeIT6XEY7wf+DdgDeF+tUZmZWeNq
u4qpCT4HMYYROBdlBM5FGYFzUUbQJbkY7TkIMzObhFwgzMwsywXCzMyy2rlRblfgDfzxK0c/VmNc
ZmbWsHauYvq/wMPALcBv6w3HzMw6RbuvHB32G+XMzGxia+ccxPcl+Z3UZmaTTDvPYroLOITivRC/
S80RER1XNHwfxBhG4FyUETgXZQTORRlBl+RiVE9zBV414i2bmdmENWSBSO+H7n/s9651B2RmZp1h
yHMQko6XtJ7iENN3KN7h8M2a4zIzs4a1c5L648BLgbsj4mDgGOCmWqMyM7PGtVMgtkbEL4GdJE2J
iJXAi2uOy8zMGtbOSeqHJE0Hrge+IukB4LF6wzIzs6a1c5nr7sBvAAFvoXgfxFciYkv94Q2PL3Md
wwicizIC56KMwLkoI+iSXIzqMteIeEzSPsBLgC3ANZ1YHMzMbGy1cxXTyRQnpU9KwypJJ9UdmJmZ
NaudQ0yrgWMj4oE0/SfACt9JXSfvPpeci5JzUXIuSs2+UU7Ag5XpLanNzMy6WDtXMS0DviXpMorC
cAq+Uc7MrOu1c4hJwOuBoyj2p66PiCvGIbZh8yGmMYzAuSgjcC7KCJyLMoIuycWODjENWSAmEheI
MYzAuSgjcC7KCJyLMoIuycWILnOV9BiDf/qIiD1GHJGZmXW8QQtEROw+noGYmVlnaecqphGTdICk
lZLulHSHpDNT+16Slku6W9K1kmZUljlb0npJ6yQdV2k/QtKa1HdenXGbmVnNBQLYCrwvIg4D/hz4
W0mHAouA5RExB1iRppE0l+IqqbnAfOCCdJIc4EJgQUTMBmZLml9z7GZmk1qtBSIiNkXEbWn8MeAu
YD/geGBJmm0JcGIaPwG4PCK2phcV3QPMk7QvMD0iVqX5Lq0sY2ZmNah7D2I7SQcBL6R4bMfMiNic
ujYDM9P4LGBjZbGNFAVlYHtfajczs5qMS4FIT4T9L+CsiHi02peuS+2Ea8XMzKyinTupR0XSzhTF
4UsR8Y3UvFnSPhGxKR0+eiC19wEHVBbfn2LPoS+NV9v7ctvr7e3dPt7T00NPT88YfAozs+7QarVo
tVptzVvrjXLpBPMSYEtEvK/S/qnUtljSImBGRCxKJ6kvA46kOIT0beCQiAhJNwFnAquAq4HzI2LZ
gO35RrmxisC5KCNwLsoInIsygi7JRWN3Uks6CvgusJoyk2dTfMkvBQ4ENgAnR8TDaZkPA28HtlEc
kvpWaj8CuASYRvFOijMz23OBGKsInIsyAueijMC5KCPoklz4URvtL083/MDHJALnoozAuSgjcC7K
CLokF6N93LeZmU1CLhBmZpblAmFmZlkuEGZmluUCYWZmWS4QZmaW5QJhZmZZLhBmZpblAmFmZlku
EGZmluUCYWZmWS4QZmaW5QJhZmZZLhBmZpblAmFmZlkuEGZmluUCYWZmWS4QZmaW5QJhZmZZLhBm
ZpblAmFmZlkuEGZmluUCYWZmWS4QZmaW5QJhZmZZLhBmZpblAmFmZlkuEGZmllVrgZB0kaTNktZU
2vaStFzS3ZKulTSj0ne2pPWS1kk6rtJ+hKQ1qe+8OmM2M7NC3XsQFwPzB7QtApZHxBxgRZpG0lzg
FGBuWuYCSUrLXAgsiIjZwGxJA9dpZmZjrNYCERHXAw8NaD4eWJLGlwAnpvETgMsjYmtEbADuAeZJ
2heYHhGr0nyXVpYxM7OaNHEOYmZEbE7jm4GZaXwWsLEy30Zgv0x7X2o3M7MaNXqSOiICiCZjMDOz
vKkNbHOzpH0iYlM6fPRAau8DDqjMtz/FnkNfGq+29w228t7e3u3jPT099PT0jE3UZmZdoNVq0Wq1
2ppXxR/x9ZF0EHBVRDwvTX8K2BIRiyUtAmZExKJ0kvoy4EiKQ0jfBg6JiJB0E3AmsAq4Gjg/IpZl
thWj+TzFOfFO2KERdf9chozAuSgjcC7KCJyLMoIuyYUkIkK5vlr3ICRdDrwCeKaknwP/CzgXWCpp
AbABOBkgItZKWgqsBbYBCyvf9guBS4BpwDW54mBmZmOr9j2I8eQ9iDGMwLkoI3AuygicizKCLsnF
jvYgfCe1mZlluUCYmVmWC4SZmWW5QJiZWZYLhJmZZblAmJlZlguEmZlluUCYmVmWC4SZmWW5QJiZ
WZYLhJmZZblAmJlZlguEmZlluUCYmVmWC4SZmWW5QJiZWZYLhJmZZblAmJlZlguEmZlluUCYmVmW
C4SZmWW5QJiZWZYLhJmZZblAmJlZlguEmZlluUCYmVmWC4SZmWW5QJiZWdaEKhCS5ktaJ2m9pA81
HY+ZWTebMAVC0hTgs8B8YC5wmqRDm40qp9V0AB2k1XQAHaTVdAAdpNV0AB2k1XQAOzRhCgRwJHBP
RGyIiK3AfwAnNBxTRqvpADpIq+kAOkir6QA6SKvpADpIq+kAdmgiFYj9gJ9XpjemNjMzq8FEKhDR
dABmZpOJIibG966kPwd6I2J+mj4beCoiFlfmmRgfxsysg0SEcu0TqUBMBX4MHAP8AlgFnBYRdzUa
mJlZl5radADtiohtkv4n8C1gCvBFFwczs/pMmD0IMzMbXxPpJLWZmY0jFwgzM8tygRghSTtLerek
ZZLWpGFZatu56fg6haTPNx3DeJI0Nf0f+Liklw3o+6em4mqCpN0kfUjSByTtKumtkq6S9ClJuzcd
X9Mk3d10DEPxOYgRkvQfwEPAEqAvNe8PnAHsGRGnNBXbeJO012BdwOqImDQ3NEr6IjAN+CHwFuA7
EfH3qe/WiHhhk/GNJ0lfA34GPB2YA6wDvgocD8yMiNMbDG9cSXqU4l6u6uWkTweeACIi9mgksCG4
QIyQpPURMXu4fd1I0lPAfYN07xcRu4xnPE2StCYinpfGdwYuAPYG3gTcOMkKxO0R8QJJAu4HZkXE
U2l6dX+eJgNJ5wMzgA9GxKaUg59GxMENh7ZDPsQ0cr+SdLKk7TmUtJOkU4BfNRhXE34K9ETEwQMH
YHPTwY2z7YcXI2JrRLwTuB1YAUy2wyoBxZ/HwDcj4qnK9KT6yzQizgTOBy6TdBYT5Lt3QgTZoU4F
3ghsTo8fX0/xZfiG1DeZ/G9gz0H6/mU8A+kAt0h6VbUhIj4KXAwc1EhEzblF0nSAiHhbf6OkQ4BH
GouqIRFxM/A/0mQL2LW5aNrjQ0yjlHYV906TW8IJNRuSpJ369ygmI0mzgMMj4pqmY9kR70GMgqR9
KE62/ZIil6+TdFjDYTVC0j4pH0h6lqQ3OBfOxWC5mIzFoZoLYBswrdP/X7hAjJCkdwE3AjdJeg9w
FfAa4OuS3tFocONskFy8GufCuXAugImbCx9iGiFJdwDzKC5pvA84JCLul7Qn0IqIFzQa4DhyLkrO
Rcm5KE3UXEyYh/V1oK0R8TjwuKSfRMT9ABHx0CR87LhzUXIuSs5FaULmwoeYRu6pyh3Tr+lvlDSN
P7wZZjJwLkrORcm5KE3IXLhAjNzr+0ciovoq1BdTuRZ+knAuSs5FybkoTchc+BDTCEXE9juHJb0I
OA04GbgXuLCpuJrgXJSci5JzUZqouXCBGCFJ/43ih3wqsIXiGTOKiJ4m42qCc1FyLkrORWmi5sJX
MY1Qev7Q9cA7ImJ9aru305+tUgfnouRclJyL0kTNhc9BjNzrgU3ASkn/LulYOvhkU82ci5JzUXIu
ShMyF96DGCUVz7U/gWL38ZXApcAVEXFto4E1wLkoORcl56I00XLhAjGGVLwX4Y3AqRFxdNPxNMm5
KDkXJeeiNBFy4QJhZmZZPgdhZmZZLhBmZpblAmFmZlkuEGZmluUCYdYmSY+lf2dJ+loaf8HAV4xm
luuV9P5B+m4YYtkPjzRes9FygbBJTdKUYcweABHxi4g4KbW9kOLFL0Mul+2IeNkQy57dfnjFK3DT
a3DNRs0FwrqepL+WdLuk2yRdKuliSZ+T9ANgsaTnSPqmpJslfTc9NwdJB0u6UdJqSR+vrO8gSWvS
45s/Bpwi6VZJJw0SAsBcSSsl/UTSeyvr6t8r2Tdt+9a07qMknUvxWspbJX0pzff3qX+NpLMq8fxY
0hJgDfARSZ+pbOOdkj49Zgm1ySMiPHjo2gE4DPgxsFea3hO4GLiS8j6gFRRv+ILirV8r0viVwFvS
+ELg0TR+ELAmjZ8BnD9EDL3ADRSPdd4b+CUwJfX1r/P9wIfT+E7A7tX+NH4EsJrirWS7AXcAh6d4
ngSOTPPtBtxT2cYNwGFN/yw8TLzBT3O1bnc0sDQifgXb3+AF8LWIiPTog5cCX6scmdkl/fvfgdel
8S8DizPrF0M/UyeA/xcRW4Etkh4AZgK/qMyzCrgo7ZV8IyJuz6znKODrEfEbAElfB15OUcjui4hV
6TM+Luk64K8krQN2jog7h4jR7I+4QFi3C/Jf4E+kf3cCHo6IF9Ycx+8r408y4HcvIq6X9HLgtcAl
kj4dEV8asI6Bn0WU5zceHzDvF4B/BO4CLhpl7DZJ+RyEdbvrgJPSc2/6n3+zXUQ8Atwr6Y2pX5Ke
n7pvoHh+P8CbB1n/I8D00QYp6UDgwYj4AvBFipPfAFsl9ReT64ETJU2TtBtwYmr7owKY9ib2B94E
XD7a+GxycoGwrhYRa4FPAN+RdBvwrxR/dVevLHozsCD13wEcn9rPAv5W0mpg1oBl+sdXUpyAHuok
9WBXMvW3vxK4TdKPgJOA81L754HVkr4UEbcCl1AcjvoB8O+VQ1G59S8FvhcRv95BXGaD8sP6zLqU
pKuAT0fEyqZjsYnJexBmXUbSDEk/Bp5wcbDR8B6E2RiR9FaKw1JV34uI92ZmN+t4LhBmZpblQ0xm
ZpblAmFmZlkuEGZmluUCYWZmWS4QZmaW9f8BHP5ro0P/QUMAAAAASUVORK5CYII=
"
>
</div>

</div>

<div class="output_area"><div class="prompt"></div>


<div class="output_png output_subarea ">
<img src="data:image/png;base64,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
"
>
</div>

</div>

<div class="output_area"><div class="prompt"></div>


<div class="output_png output_subarea ">
<img src="data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAAYgAAAEiCAYAAADjxEWuAAAABHNCSVQICAgIfAhkiAAAAAlwSFlz
AAALEgAACxIB0t1+/AAAH2FJREFUeJzt3Xu8XWV95/HPl0skYBApGkOEghIGYrFQxNjRyhYsjdZy
sS0XR0SljpphtM54CY6Wo7UW66tYrIWZVhSYkZRYB4s1RBCzgbbKqZRLIERACJpIAoMgoWhJ5Dt/
rOewtod1kp1zzj77XL7v12u9svazbr/zS7J/Zz3Pusg2ERERw+3U7wAiImJySoGIiIhGKRAREdEo
BSIiIhqlQERERKMUiIiIaJQCERERjVIgYsaSdKyktZL+TdK3JO2/jXX3lnSFpMclrZN0Wrf7kvQB
SaslPSbpXknvH7btAZJWlW3vlHRsx7IPS9rcMT0h6eeS9i7LnyXpC5J+IukBSe8btu+/LnH9XNIZ
w5a9tbR37v/Vo8tmTEcpEDEjSdoH+ArwP4DnAt8FLt/GJn8F/Ax4PvCfgAslLdyBfZ0O7AUsBs6S
dErHsmXATcDeZR9/V/aJ7U/anjM0AZ8CVtn+cdl2AHgxsD/wGuCDkn6rY9+3AEuAfwWa7or9p879
275+GzmImcZ2pkxTbgLWAUuBO4AfA18AnrUD2/9n4B87Pu8OPAEc3LDuHsC/Awd1tF0K/OmO7qss
Px/4bJk/mKrw7NGx/HrgnQ3bCbgXOL2jbQPw2o7PHweWNWx7A/CWYW1vBW7o999lpsk75QwiprI3
AcdR/QZ9MPARSftJelTSIyNMp5ZtXwLcOrQj208A3wd+peE4BwNbbd/T0XZr2ccO7UuSgFcDt3ds
e6/tfxth351+A3ge1dkKkp4LzOs8NnDbCNs2MXCEpIckfU/SRyTt3OW2MQPs0u8AIkbJwOdsbwCQ
9CfAX9r+KFVXzvbsATw0rO0nwLMb1n028NiwtseAOR3LH+xyXwPlzy92bPuThm3nN2x7BvDlUoCG
th1av3PbOXTneuAltu+X9CtU3WJbgXO73D6muZxBxFT2w475HwD77sC2jwN7DmvbE9i8A+sOFY3N
3exL0lnAm4Hftr1lG/t+DsMKkqTdgd8DLhkW19CxtvczPIPt+2zfX+Zvp+qe+r1uto2ZIQUiprL9
h81vKF1Mjw+7MqdzGrr66A7gV4c2lrQHVVfVHQ3HuQvYRdJBHW2/2rHudvcl6e3AB4Fjbf+oYz93
AC+S1Hm20bnvIScBD9u+bqjB9iPAA8Dhw7a9ndHTGLaN6abfgyCZMo1mohqkvpWqK2Zv4B+BT+zA
9vsAjwJvBHajujron7ex/jLgMqoB6FeWbQ/tZl9UVz09ABwywr6/DXy6bHsS8AjwS8PWuRoYaNj2
T4E2VbfaIcCPgOM6lu9a9vtPwB+UeZVlrwPmlvlDgNXAR/v9d5tp8kx9DyBTptFMwH3Ah6h+036E
qk9/tx3cx7HAnVRXHH0L2L9j2YeBFR2fnwtcQdWtsw44dQf2dS/VVVCbO6YLOpb/MrCqbHsncMyw
fc8HngRe1PAzzAIuohp72Aj84bDlbeAp4Oflz6eAV5dlny7bPE41qD4A7Nzvv9tMk2ca+k2iZ8pV
Ed8F1tv+nXKDz+XlP8U64GTbj5Z1zwbeXv4xv8f21aX9SOBiqt9+Vth+b0+DjklP0n3Amba/1e9Y
IqariRiDeC+whvomnaXANbYPBq4tnyk3HZ0CLKS6meiCckkgwIVUXwYLgAWSFk9A3BERM1pPC4Sk
FwKvBz5PPfh1PPWVGJcAJ5b5E6hu8Nliex1wD7BI0jxgju3Bst6lHdtERESP9Po+iM8AH+AXL8Ob
a3tTmd8EzC3z+wLf6VhvPVXf65YyP2QDzdeIxwxi+8B+xxAx3fXsDELSG4AHbd/MCJfOuRoA6e0g
SEREjEovzyD+I3C8pNdTDS7vKel/A5skvcD2xtJ9NHQH6gZgv47tX0h15rChzHe2b2g6oKQUm4iI
HWS78Zf4np1B2P6w7f1KV8CpwLdsnw5cSfXIAMqfXy3zVwKnSpol6UBgATBoeyPwmKRFZdD69I5t
mo7b1+mcc87pewyTZUoukovkYvLnYlsm8llMQ5GcCyyXdCblMlcA22skLae64mkrsMR19EuoLnOd
TXWZ68oJjDsiYkaakALh6vEA15X5HwOvHWG9TwKfbGi/CTislzFGRMQvyrOYxlmr1ep3CJNGclFL
LmrJRW2y56Lnd1JPJEmeTj9PRExe9X28/TeW7z1JeIRB6rwPIiJi1CbDL6S9K1TpYoqIiEYpEBER
0SgFIiIiGqVAREREoxSIiIholAIRERGNUiAiIqJRCkRERDRKgYiIiEYpEBER0SgFIiIiGqVARERE
oxSIiIholAIRERGNUiAiIqJRCkRERDTqWYGQtJukGyXdIul2SQOlfUDSekk3l+l1HducLeluSWsl
HdfRfqSk1WXZ+b2KOSIiaj195aik3W0/IWkX4B+B9wKLgc22zxu27kLgMuAoYD7wTWCBbUsaBM6y
PShpBfBZ2ysbjpdXjkbEhKheOToZvm/Us1eO9rSLyfYTZXYWsCt1NpuCOQFYZnuL7XXAPcAiSfOA
ObYHy3qXAif2LuqIiIAeFwhJO0m6BdgEXN3xJX+WpFslXSRpr9K2L7C+Y/P1VGcSw9s3lPaIiOih
Xp9BPGX7cOCFVGcDLwEuBF4EHA48APx5L2OIiIjR2WUiDmL7J5JWAYttP10QJH0e+Fr5uAHYr2Oz
F1KdOWwo853tG0Y61sDAwNPzrVaLVqs1xugjIqaPdrtNu93uat2eDVJL2gfYavtRSbOBbwDnAv9q
e2NZ533AUbbf1DFI/XLqQeqDyiD1jcB7gEHg62SQOiL6bCYMUvfyDGIecImknam6si63vULSpZIO
p8rsfcA7AWyvkbQcWANsBZZ0fNsvAS4GZgMrmopDRESMr55e5jrRcgYRERNlJpxB5E7qiIholAIR
ERGNJuQqpph6qtPnySHdhhH9kQIR2zAZvpgnT6GKmGnSxRQREY1SICIiolEKRERENEqBiIiIRikQ
ERHRKAUiIiIapUBERESjFIiIiGiUAhEREY1yJ3WHPF4iIqKWAvEMk+GLefIUqoiYudLFFBERjVIg
IiKiUQpEREQ0SoGIiIhGPSsQknaTdKOkWyTdLmmgtO8t6RpJd0m6WtJeHducLeluSWslHdfRfqSk
1WXZ+b2KOSIiaj0rELZ/BrzG9uHA4cBiSYuApcA1tg8Gri2fkbQQOAVYCCwGLlB93emFwJm2FwAL
JC3uVdwREVHpaReT7SfK7CxgV6prSI8HLintlwAnlvkTgGW2t9heB9wDLJI0D5hje7Csd2nHNhER
0SM9LRCSdpJ0C7AJuLp8yc+1vamssgmYW+b3BdZ3bL4emN/QvqG0R0RED/X0RjnbTwGHS3oOcIWk
Xxm23JLG9c60gYGBp+dbrRatVms8dx8RMaW1223a7XZX62qiHukg6aPAE8A7gJbtjaX7aJXtQyQt
BbB9bll/JXAOcH9Z59DSfhpwtO13NRzDY/l5qiGPyXEndb8ftZFcRGzbdPk/IgnbjY9v6OVVTPsM
XaEkaTbwm8CdwJXAGWW1M4CvlvkrgVMlzZJ0ILAAGLS9EXhM0qIyaH16xzYREdEjveximgdcImln
qkJ0ue0Vkr4DLJd0JrAOOBnA9hpJy4E1wFZgScfpwBLgYmA2sML2yh7GHRERTGAX00RIF9M4RpBc
RGzTdPk/0pcupoiImNpSICIiolEKRERENEqBiIiIRikQERHRKAUiIiIapUBERESjFIiIiGiUAhER
EY1SICIiolEKRERENEqBiIiIRikQERHRKAUiIiIapUBERESjFIiIiGiUAhEREY1SICIiolEKRERE
NOppgZC0n6RVku6QdLuk95T2AUnrJd1cptd1bHO2pLslrZV0XEf7kZJWl2Xn9zLuiE6SJs3Ub/3+
+SdTLmYC9fKF8JJeALzA9i2Sng3cBJwInAxstn3esPUXApcBRwHzgW8CC2xb0iBwlu1BSSuAz9pe
OWx7j/Xl3dPhJeTjEkFyUUeQXNQRJBd1BNMkF5Kw3Vhxt3sGIelT3bQ1sb3R9i1l/nHgTqovfoCm
gE4AltneYnsdcA+wSNI8YI7twbLepVSFJiIieqSbLqbjGtpev6MHknQAcATwndJ0lqRbJV0kaa/S
ti+wvmOz9VQFZXj7BupCExERPbDLSAskvRtYArxY0uqORXOAf9qRg5Tupb8D3mv7cUkXAh8vi/8Y
+HPgzB3Z50gGBgaenm+1WrRarfHYbUTEtNBut2m3212tO+IYhKTnAM8FzgU+RN0ltNn2w90GI2lX
4B+Aq2z/RcPyA4Cv2T5M0lIA2+eWZSuBc4D7gVW2Dy3tpwFH237XsH1lDGK8Ikgu6giSizqC5KKO
YJrkYlRjELZ/Ynud7VOpuneeBJ4C9pC0f5cHFnARsKazOJQxhSEnAUNnKFcCp0qaJelAYAEwaHsj
8JikRWWfpwNf7SaGiIgYnRG7mIZI+q9Uv8U/CPy8Y9FhXez/lcCbgdsk3VzaPgycJulwqvJ7H/BO
ANtrJC0H1gBbgSUdpwRLgIuB2cCK4VcwRUTE+NruZa6Svg+8fEe6lfolXUzjGEFyUUeQXNQRJBd1
BNMkF2O6zBX4AfDYqI8eERFT0na7mKi6gFZJ+jrVOASAh9/kFhER00s3BeIHZZpVpslyXhURET3U
00dtTLSMQYxjBMlFHUFyUUeQXNQRTJNcbGsMopurmFY1NNv2MaOOKCIiJr1uupg+0DG/G/C7VJeg
RkTENDaqLiZJ/2L7qB7EMybpYhrHCJKLOoLkoo4guagjmCa5GGsX094dH3cCXgbsOepoIiJiSuim
i+lfqcvkVmAd4/RgvYiImLy2WyBsHzABcURExCTTTRfTLODdwKupziSuA/6n7S09ji0iIvqom2cx
XURVSC6huknudGCr7T/ofXg7JoPU4xhBclFHkFzUESQXdQTTJBdjGqQGjrL90o7P10q6bdTRRETE
lNDNw/q2Sjpo6IOkF5P7ICIipr1ub5T7lqT7yucDgLf1LKKIiJgUurpRTtJuwMHl4/ds/3tPoxql
jEGMYwTJRR1BclFHkFzUEUyTXGxrDKKbQepdgN+mOnMYOuOYlI/7ToEYxwiSizqC5KKOILmoI5gm
uRjrIPXXgJ9SvTf6qVFHERERU0o3BWL+sKuYIiJiBujmKqaVkn5rNDuXtJ+kVZLukHS7pPeU9r0l
XSPpLklXS9qrY5uzJd0taa2k4zraj5S0uiw7fzTxRERE97opEN8G/q+kn0naXKZu31G9BXif7ZcA
rwD+i6RDgaXANbYPBq4tn5G0EDgFWAgsBi5Q1dEHcCFwpu0FwAJJi7uMISIiRqGbAvEZ4NeB3W3P
KVNXT3O1vdH2LWX+ceBOYD5wPNWd2ZQ/TyzzJwDLbG+xvQ64B1gkaR4wx/ZgWe/Sjm0iIqIHuikQ
PwDusD2mAWpJBwBHADcCc21vKos2AXPL/L7A+o7N1lMVlOHtG0p7RET0SDeD1PcBqyRdBTxZ2nbo
MldJzwa+ArzX9ua616jakaRxu1ZsYGDg6flWq0Wr1RqvXUdETHntdpt2u93Vut3cBzFQZodWFNX3
+se6OoC0K/APwFW2/6K0rQVatjeW7qNVtg+RtJRq5+eW9VYC5wD3l3UOLe2nAUfbftewY+U+iPGK
ILmoI0gu6giSizqCaZKLMd0HYXtgDAcWcBGwZqg4FFcCZwCfKn9+taP9MknnUXUhLQAGy1nGY5IW
AYNUT5T97GjjioiI7evmDOL5wAepriyaXZpt+5jt7lx6FXA9cBt1qT2b6kt+ObA/1RvqTrb9aNnm
w8DbqR4I+F7b3yjtRwIXlxhW2H5Pw/FyBjFeESQXdQTJRR1BclFHME1yMdZHbVwDXA68H3gn8Fbg
IdsfHHVEPZICMY4RJBd1BMlFHUFyUUcwTXKxrQLRzVVMv2T788CTtq+z/TZgu2cPERExtXVzFdPQ
lUsbJb0B+BHw3N6FFBERk0E3BeJPyqMw/jvwl8CewPt6GlVERPRdV++DmCoyBjGOESQXdQTJRR1B
clFHME1yMdYxiIiImIFSICIiolEKRERENNruIHV5H/Xv8sxXjn68h3FFRESfdXMV098DjwI3AT/r
bTgRETFZdPvK0VG9US4iIqaubsYg/llS3kkdETHDdPMspjuBg6jeC/Hvpdm2J13RyH0Q4xhBclFH
kFzUESQXdQTTJBdjetw38LpRHzkiIqasbt4HsQ6efuz3br0OKCIiJoftjkFIOl7S3VRdTNdRvb/h
qh7HFRERfdbNIPUngF8H7rJ9IHAscGNPo4qIiL7rpkBssf3/gJ0k7Wx7FfCyHscVERF91s0g9SOS
5gA3AF+S9CDweG/DioiIfuvmMtdnAz8FBLyZ6n0QX7L9cO/D2zG5zHUcI0gu6giSizqC5KKOYJrk
YkyP+7b9OPA8qstdHwb+ttviIOkLkjZJWt3RNiBpvaSby/S6jmVnS7pb0lpJx3W0HylpdVl2fjfH
joiIsenmKqaTqQalf79Mg5J+v8v9fxFYPKzNwHm2jyjTVeU4C4FTgIVlmwtUlWiAC4EzbS8AFkga
vs+IiBhn3YxBfAQ4yvaDAJKeB1wLfHl7G9q+QdIBDYuaTmdOAJbZ3gKsk3QPsEjS/cAc24NlvUuB
E4GVXcQeERGj1M1VTAIe6vj8MM1f8DviLEm3SrqovO8aYF9gfcc664H5De0bSntERPRQN2cQK4Fv
SLqMqjCcwthulLsQGHqXxB8Dfw6cOYb9/YKBgYGn51utFq1Wa7x2HREx5bXbbdrtdlfrdnMVk4A3
Aq+iGj+4wfYV3QZTupi+ZvuwbS2TtBTA9rll2UrgHOB+YJXtQ0v7acDRtt/VsL9cxTReESQXdQTJ
RR1BclFHME1yMaaH9ZVv3K+UacwkzbP9QPl4EjB0hdOVwGWSzqPqQloADNq2pMckLQIGgdOBz45H
LBERMbIRC4Skxxm5PNr2ntvbuaRlwNHAPpJ+SHVG0JJ0eNn3fcA7yw7XSFoOrAG2Aks6TgeWABcD
s4EVtjNAHRHRY9vtYppK0sU0jhEkF3UEyUUdQXJRRzBNcjGmG+UiImJmSoGIiIhGKRAREdEoBSIi
IhqlQERERKMUiIiIaJQCERERjVIgIiKiUQpEREQ0SoGIiIhGKRAREdEoBSIiIhqlQERERKMUiIiI
aJQCERERjVIgIiKiUQpEREQ0SoGIiIhGKRAREdGopwVC0hckbZK0uqNtb0nXSLpL0tWS9upYdrak
uyWtlXRcR/uRklaXZef3MuaIiKj0+gzii8DiYW1LgWtsHwxcWz4jaSFwCrCwbHOBqreCA1wInGl7
AbBA0vB9RkTEOOtpgbB9A/DIsObjgUvK/CXAiWX+BGCZ7S221wH3AIskzQPm2B4s613asU1ERPRI
P8Yg5treVOY3AXPL/L7A+o711gPzG9o3lPaIiOihXfp5cNuW5PHc58DAwNPzrVaLVqs1nruPiJjS
2u027Xa7q3Vlj+v38zMPIB0AfM32YeXzWqBle2PpPlpl+xBJSwFsn1vWWwmcA9xf1jm0tJ8GHG37
XQ3H8lh+nmrIo7f56I7o9d/LdiNILuoIkos6guSijmCa5EISttW0rB9dTFcCZ5T5M4CvdrSfKmmW
pAOBBcCg7Y3AY5IWlUHr0zu2iYiIHulpF5OkZcDRwD6Sfgj8EXAusFzSmcA64GQA22skLQfWAFuB
JR2nA0uAi4HZwArbK3sZd0RETEAX00RKF9M4RpBc1BEkF3UEyUUdwTTJxWTrYoqIiCkgBSIiIhql
QERERKMUiIiIaJQCERERjVIgIiKiUQpEREQ0SoGIiIhGKRAREdEoBSIiIhqlQERERKMUiIiIaJQC
ERERjVIgIiKiUQpEREQ0SoGIiIhGKRAREdEoBSIiIhr1rUBIWifpNkk3SxosbXtLukbSXZKulrRX
x/pnS7pb0lpJx/Ur7oiImaKfZxAGWraPsP3y0rYUuMb2wcC15TOSFgKnAAuBxcAFknL2ExHRQ/3+
kh3+ouzjgUvK/CXAiWX+BGCZ7S221wH3AC8nIiJ6pt9nEFdL+q6kd5S2ubY3lflNwNwyvy+wvmPb
9cD8iQkzImJm2qWPx36l7QckPQ+4RtLazoW2Lcnb2H5byyIiYoz6ViBsP1D+fEjSFVRdRpskvcD2
RknzgAfL6huA/To2f2Fpe4aBgYGn51utFq1Wa/yDj4iYotrtNu12u6t1ZU/8L+KSdgd2tr1Z0h7A
1cDHgNcCD9v+lKSlwF62l5ZB6suoish84JvAQR4WvKThTTsaF5PjxET04+/lFyJILuoIkos6guSi
jmCa5EIStoePBwP9O4OYC1xRJZhdgC/ZvlrSd4Hlks4E1gEnA9heI2k5sAbYCiwZUyWIiIjt6ssZ
RK/kDGIcI0gu6giSizqC5KKOYJrkYltnEP2+zDUiIiapFIiIiGiUAhEREY1SICIiolEKRERENEqB
iIiIRikQERHRKAUiIiIapUBERESjFIiIiGiUAhEREY1SICIiolEKRERENEqBiIiIRikQERHRKAUi
IiIapUBERESjFIiIiGiUAhEREY2mVIGQtFjSWkl3S/pQv+OJiJjOpkyBkLQz8DlgMbAQOE3Sof2N
qkm73wFMIu1+BzCJtPsdwCTS7ncAk0i73wFs05QpEMDLgXtsr7O9Bfhb4IQ+x9Sg3e8AJpF2vwOY
RNr9DmASafc7gEmk3e8AtmkqFYj5wA87Pq8vbRER0QNTqUC43wFERMwksqfG966kVwADtheXz2cD
T9n+VMc6U+OHiYiYRGyrqX0qFYhdgO8BxwI/AgaB02zf2dfAIiKmqV36HUC3bG+VdBbwDWBn4KIU
h4iI3pkyZxARETGxptIgdURETKAUiIiIaJQC0QOS/rrfMUwkSbtIepekT0h65bBlH+lXXP0gaQ9J
H5L0AUm7SXqrpK9J+jNJz+53fP0m6a5+x9APkl7aMT9L0kfLv4tPStq9n7FtS8YgRknS3iMtAm6z
PWNu4pN0ETAb+BfgzcB1tv9bWXaz7SP6Gd9EkvRl4AfA7sDBwFrgcuB4YK7t0/sY3oSStJnq/qXO
Syh3B54AbHvPvgTWB53/DySdB+wNfBE4Cdjb9lv6Gd9IUiBGSdJTwP0jLJ5ve9ZExtNPklbbPqzM
7wpcAPwS8Cbg2zOsQNxq+1clCXgA2Nf2U+XzbUN5mgkkfRbYC/ig7Y0lB/faPrDPoU24YQXiVuAo
209O9n8XU+Yy10noXuBY288oEpJ+2LD+dLbr0Ex5TtY7JJ0DXAvMtG4VQ/XrsaSrbD/V8XlG/TZm
+z2SXgZcJunvqR62OVM9R9Ibqc6mnmX7SZj8/y4yBjF6fwE8d4Rln57IQCaBmyS9rrPB9seoTqEP
6EtE/XOTpDkAtt821CjpIOCxvkXVJ7a/C/xm+dgGdutfNH11PfA7wBuAb0t6AYCkecBD/QxsW9LF
FDFBJO00dEYxE0naFzjc9op+xxLdSRfTKEnaH3jI9k8l7QS8Ffg14A7gb2xv7Wd8Eym5qG0vF8CM
KRCSfhl4sCMXi4FfK+35dzEF/o+ki2n0rqK+OuNc4PXAd6jeWzGjLnMlueiUXNRWkFwMmZL/LnIG
MXqy/USZfy3VVQk/B/6PpNv6GFc/JBe15KKWXNSmZC5yBjF66yUdW+bvA/YDkLQPM+/dFclFLbmo
JRe1KZmLDFKPUulTvJTqybKPAr8B3EJ13ff7bX+zj+FNqOSillzUkovaVM1FCsQYSVoILKDqrtsA
zAJOtb2kr4H1QXJRSy5qyUVtquUiYxBjZHuNpN2A04CTqU4fv9LfqPojuaglF7XkojbVcpECMUqS
/gPVX/KpwMNUz9uR7VY/4+qH5KKWXNSSi9pUzUW6mEapPIvpBuAPbN9d2u6boc+ZSS6K5KKWXNSm
ai5yFdPovRHYCKyS9DeSXssvPrVyJkkuaslFLbmoTclc5AxijMoz/k+gOn18DdWVClfYvrqvgfVB
clFLLmrJRW2q5SIFYhyVd0T8HtVVCcf0O55+Si5qyUUtuahNhVykQERERKOMQURERKMUiIiIaJQC
ERERjVIgInqoXNJ4aL/jiBiNDFJHRESjnEFEjEDSHpK+LukWSaslnSzpo5IGy+f/VdY7RNKNHdsd
MPSMf0ltSb9W5h+X9Imyv29Len5pf7Gk70i6rSzfXNrnSbpe0s3leK+a+CzETJYCETGyxcAG24fb
PgxYCXzO9svL59mS3mB7LTBL0gFlu1OAvy3znafouwPftn041Uvs31Hazwc+Y/ulwA87tnkTsNL2
EcBLqR4PHTFhUiAiRnYb8JuSzpX0KtuPAccM/bYPHAMsLOsupyoMUD2l8/KG/T1p++tl/ibggDL/
CuDLZX4Z9SMYBoG3SToHeKntx8fp54roSgpExAjKQ9WOAFYDn5D0R8BfAb9bftv/G2B2Wf1y4GRJ
C6pN/f2GXW7pmH+K7TxN2fYNVC+W2QBcLOn0sfw8ETsqBSJiBJLmAT+z/SXg01TFwsDD5Zk6v18+
Y/te4OfAR6m7l7r1HapHLkD1OOih4+8PPGT788Dny/EjJkzeBxExssOAT5dHNT8JvBs4Cbid6smc
Nw5b/3Lgz4CPjLA/D5sf+vyHVC+v/zDwDeAnpf01wPslbQE2A28Z008TsYNymWtEn0mabfunZf5U
4BTbJ/U5rIicQURMAkdK+hzV4PQjwNv7HE8EkDOIiIgYQQapIyKiUQpEREQ0SoGIiIhGKRAREdEo
BSIiIhqlQERERKP/D+gLUNjFm4tkAAAAAElFTkSuQmCC
"
>
</div>

</div>

<div class="output_area"><div class="prompt"></div>


<div class="output_png output_subarea ">
<img src="data:image/png;base64,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
BGFmZoX+C4o2q7DSSMD9AAAAAElFTkSuQmCC
"
>
</div>

</div>

<div class="output_area"><div class="prompt"></div>


<div class="output_png output_subarea ">
<img src="data:image/png;base64,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
"
>
</div>

</div>

</div>
</div>

</div>
<div class="cell border-box-sizing text_cell rendered">
<div class="prompt input_prompt">
</div>
<div class="inner_cell">
<div class="text_cell_render border-box-sizing rendered_html">
<p>With a significance level alpha=0.05, we would conclude that there is a depency between the continuous variable studied (loan amount) and all the categorical variables studied except "foreign".</p>
<p>We can understand the value of performing statistical tests from the last example. Although the difference between both averages look significant in our plot, the ANOVA analysis proofs this difference not to be significant.</p>

</div>
</div>
</div>
