
# City School Analysis using Pandas
In this exercise I utilized Pandas and Jupyter Notebook to analyze district-wide school's standardized test results. 

District Summary

    Create a high level snapshot (in table form) of the district's key metrics, including:
        Total Schools
        Total Students
        Total Budget
        Average Math Score
        Average Reading Score
        % Passing Math (passing requires a score of 70% or greater)
        % Passing Reading (passing requires a score of 70% or greater)
        Overall Passing Rate (Average of the above two)

School Summary

    Create an overview table that summarizes key metrics about each school, including:
        School Name
        School Type
        Total Students
        Total School Budget
        Per School Budget
        Average Math Score
        Average Reading Score
        % Passing Math
        % Passing Reading
        Overall Passing Rate (Average of the above two)

Top Performing Schools (By Passing Rate)

    Create a table that highlights the top 5 performing schools based on Overall Passing Rate. Include:
        School Name
        School Type
        Total Students
        Total School Budget
        Per School Budget
        Average Math Score
        Average Reading Score
        % Passing Math
        % Passing Reading
        Overall Passing Rate (Average of the above two)

Bottom Performing Schools (By Passing Rate)

    Create a table that highlights the bottom 5 performing schools based on Overall Passing Rate. Include all of the same metrics as above.

Math Scores by Grade

    Create a table that lists the average Math Score for students of each grade level (9th, 10th, 11th, 12th) at each school.

Reading Scores by Grade

    Create a table that lists the average Reading Score for students of each grade level (9th, 10th, 11th, 12th) at each school.

Scores by School Spending

    Create a table that breaks down school performances based on average Spending Ranges (Per Student). Use 4 reasonable bins to group school spending. Include in the table each of the following:
        Average Math Score
        Average Reading Score
        % Passing Math
        % Passing Reading
        Overall Passing Rate (Average of the above two)

Scores by School Size

    Repeat the above breakdown, but this time group schools based on a reasonable approximation of school size (Small, Medium, Large).

Scores by School Type

    Repeat the above breakdown, but this time group schools based on school type (Charter vs. District).

As final considerations:

    Your script must work for both data-sets given.
    You must use the Pandas Library and the Jupyter Notebook.
    You must submit a link to your Jupyter Notebook with the viewable Data Frames.
    You must include an exported markdown version of your Notebook called README.md in your GitHub repository.
    You must include a written description of three observable trends based on the data.
    See Example Solution for a reference on the expected format. Note: the specific fields in that PDF may differ slightly from the instructions. Use the PDF as a guide for the report layout, but refer to the instructions for what fields you should create in each section.


```python
#Import dependencies:
import pandas as pd
import os
from IPython.display import display
```


```python
import warnings
warnings.filterwarnings('ignore')
```


```python
#Import files
schools_file= os.path.join('raw_data', 'schools_complete.csv')
students_file= os.path.join('raw_data', 'students_complete.csv')
schools_df = pd.read_csv(schools_file)
students_df = pd.read_csv(students_file)
#print(schools_df.head())
#print(schools_df.columns)
#print(students_df.head())
#print(students_df.columns)
```


```python
#Rename schools_df "name" column to "school" to match other csv file header for school name
schools_df=schools_df.rename(columns= {'name': 'school'})
#print(schools_df.head())
#schools_df.columns
#print(len(schools_df))
#print(len(students_df))
```


```python
#Merge csv files based on the column 'school'
merge_df = pd.merge(schools_df, students_df, on="school")
#print(merge_df.head())
#print(len(merge_df))
```

# District Summary


```python
#District Summary#
#Total Schools
total_schools = schools_df['school'].count()

#Total Students
student_count = students_df['name'].count()

#Total Budget
total_budget = schools_df['budget'].sum()

#Average Math Score -NEED TO ROUND IT TO 2 DECIMAL POINTS - DF.ROUND(2)
avg_math = students_df['math_score'].mean()

#Average Reading Score
avg_read = students_df['reading_score'].mean()

#% Passing Math (passing requires a score of 70% or greater)
passing_math= students_df.loc[(students_df["math_score"] >= 70)]
pass_math_count = passing_math["name"].count()
percMathPass = (pass_math_count/student_count)*100

#% Passing Reading (passing requires a score of 70% or greater)
passing_read= students_df.loc[(students_df["reading_score"] >= 70)]
pass_read_count = passing_read["name"].count()
percReadPass = (pass_read_count/student_count)*100

#Overall Passing Rate (Average of the above two)
overall_pass= (percMathPass+percReadPass)/2

#Output of District Summary
district_summary = pd.DataFrame ({"Total Schools": [total_schools], 
                               "Total Students": [student_count],
                               "Total Budget": [total_budget], 
                              "Average Math Score" : [avg_math],
                              "Average Reading Score" : [avg_read],
                              "% Passing Math": [percMathPass],
                               "% Passing Reading": [percReadPass],
                               "% Overall Passing Rate":[overall_pass]})
#print(district_summary)

#Reorganize the columns using double brackets
organized_dist_summary = district_summary[["Total Schools","Total Students","Total Budget","Average Math Score", 
                                 "Average Reading Score", "% Passing Math", "% Passing Reading", 
                                 "% Overall Passing Rate"]]
display(organized_dist_summary)
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
      <th>Total Schools</th>
      <th>Total Students</th>
      <th>Total Budget</th>
      <th>Average Math Score</th>
      <th>Average Reading Score</th>
      <th>% Passing Math</th>
      <th>% Passing Reading</th>
      <th>% Overall Passing Rate</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>15</td>
      <td>39170</td>
      <td>24649428</td>
      <td>78.985371</td>
      <td>81.87784</td>
      <td>74.980853</td>
      <td>85.805463</td>
      <td>80.393158</td>
    </tr>
  </tbody>
</table>
</div>


# School Summary


```python
#School Summary# 
#School Name
#School Type
#Total Students
#Total School Budget

school_summary = schools_df
school_summary = school_summary.drop(['School ID'], axis=1) #axis 1 indicates drop that column; default is to drop a row

#Per Student Budget
per_student_budget = school_summary["budget"]/school_summary["size"]
school_summary["Per Student Budget"] = per_student_budget
#print(school_summary)

#Average Math Score
#Average Reading Score
group_school = merge_df.groupby (["school"])
group_school.count()
avg_scores=group_school['math_score','reading_score'].mean().reset_index()
#print(avg_scores)

#Merge avg_scores with school summary on school - MERGE DIDN'T WORK UNTIL I RESET THE INDEX FOR AVG_SCORE. WHY?
school_summary2 = pd.merge(school_summary, avg_scores, on='school')

#Calculate number of students per school
group_school_size= group_school["name"].count()
#print(group_school_size)

#% Passing Math 
school_passing_math = passing_math.groupby(["school"])
sch_pass_math_count = school_passing_math['math_score'].count()

perMathPass_sch = sch_pass_math_count/group_school_size*100
PerMathPass_sch_df= pd.DataFrame(perMathPass_sch)
PerMathPass_sch_df = PerMathPass_sch_df.reset_index()

#% Passing Reading
school_passing_read = passing_read.groupby(["school"])
sch_pass_read_count = school_passing_read['reading_score'].count()

perReadPass_sch = sch_pass_read_count/group_school_size*100
PerReadPass_sch_df = pd.DataFrame(perReadPass_sch)
PerReadPass_sch_df = PerReadPass_sch_df.reset_index()

#%Overall Passing Rate (Average of the above two)
overall_pass_sch = (perMathPass_sch+perReadPass_sch)/2
overall_pass_sch_df = overall_pass_sch.to_frame()
overall_pass_sch_df = overall_pass_sch_df.reset_index()
#print (PerMathPass_sch_df)
#print (PerReadPass_sch_df)
#print(overall_pass_sch_df)

#Merge last three columns to school_summary2 -I WAS ONLY ABLE TO MERGE THESE AFTER RESETTING THE INDEX FOR DFS.WHY?
school_summary3=pd.merge(pd.merge(pd.merge(school_summary2,PerMathPass_sch_df,on='school'),
                                  PerReadPass_sch_df,on='school'),overall_pass_sch_df,on='school')

#Rename column names
#print (school_summary3.columns) # last column heading is an integer 0, not a string '0'.
school_summary_final = school_summary3.rename(columns = {'school' : '',
                                'type' : 'School Type',
                                'size' : 'Total Students',
                                'budget' : 'Total School Budget',
                                'math_score' : 'Average Math Score',
                                'reading_score' : 'Average Reading Score',
                                '0_x' : '% Passing Math',
                                '0_y' : '% Passing Reading',
                                0 : '% Overall Passing Rate'
                                                        })
#Made school the index for the final output
school_summary_final=school_summary_final.set_index("")

#print as table form
display(school_summary_final) 
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
      <th>School Type</th>
      <th>Total Students</th>
      <th>Total School Budget</th>
      <th>Per Student Budget</th>
      <th>Average Math Score</th>
      <th>Average Reading Score</th>
      <th>% Passing Math</th>
      <th>% Passing Reading</th>
      <th>% Overall Passing Rate</th>
    </tr>
    <tr>
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
      <th>Huang High School</th>
      <td>District</td>
      <td>2917</td>
      <td>1910635</td>
      <td>655.0</td>
      <td>76.629414</td>
      <td>81.182722</td>
      <td>65.683922</td>
      <td>81.316421</td>
      <td>73.500171</td>
    </tr>
    <tr>
      <th>Figueroa High School</th>
      <td>District</td>
      <td>2949</td>
      <td>1884411</td>
      <td>639.0</td>
      <td>76.711767</td>
      <td>81.158020</td>
      <td>65.988471</td>
      <td>80.739234</td>
      <td>73.363852</td>
    </tr>
    <tr>
      <th>Shelton High School</th>
      <td>Charter</td>
      <td>1761</td>
      <td>1056600</td>
      <td>600.0</td>
      <td>83.359455</td>
      <td>83.725724</td>
      <td>93.867121</td>
      <td>95.854628</td>
      <td>94.860875</td>
    </tr>
    <tr>
      <th>Hernandez High School</th>
      <td>District</td>
      <td>4635</td>
      <td>3022020</td>
      <td>652.0</td>
      <td>77.289752</td>
      <td>80.934412</td>
      <td>66.752967</td>
      <td>80.862999</td>
      <td>73.807983</td>
    </tr>
    <tr>
      <th>Griffin High School</th>
      <td>Charter</td>
      <td>1468</td>
      <td>917500</td>
      <td>625.0</td>
      <td>83.351499</td>
      <td>83.816757</td>
      <td>93.392371</td>
      <td>97.138965</td>
      <td>95.265668</td>
    </tr>
    <tr>
      <th>Wilson High School</th>
      <td>Charter</td>
      <td>2283</td>
      <td>1319574</td>
      <td>578.0</td>
      <td>83.274201</td>
      <td>83.989488</td>
      <td>93.867718</td>
      <td>96.539641</td>
      <td>95.203679</td>
    </tr>
    <tr>
      <th>Cabrera High School</th>
      <td>Charter</td>
      <td>1858</td>
      <td>1081356</td>
      <td>582.0</td>
      <td>83.061895</td>
      <td>83.975780</td>
      <td>94.133477</td>
      <td>97.039828</td>
      <td>95.586652</td>
    </tr>
    <tr>
      <th>Bailey High School</th>
      <td>District</td>
      <td>4976</td>
      <td>3124928</td>
      <td>628.0</td>
      <td>77.048432</td>
      <td>81.033963</td>
      <td>66.680064</td>
      <td>81.933280</td>
      <td>74.306672</td>
    </tr>
    <tr>
      <th>Holden High School</th>
      <td>Charter</td>
      <td>427</td>
      <td>248087</td>
      <td>581.0</td>
      <td>83.803279</td>
      <td>83.814988</td>
      <td>92.505855</td>
      <td>96.252927</td>
      <td>94.379391</td>
    </tr>
    <tr>
      <th>Pena High School</th>
      <td>Charter</td>
      <td>962</td>
      <td>585858</td>
      <td>609.0</td>
      <td>83.839917</td>
      <td>84.044699</td>
      <td>94.594595</td>
      <td>95.945946</td>
      <td>95.270270</td>
    </tr>
    <tr>
      <th>Wright High School</th>
      <td>Charter</td>
      <td>1800</td>
      <td>1049400</td>
      <td>583.0</td>
      <td>83.682222</td>
      <td>83.955000</td>
      <td>93.333333</td>
      <td>96.611111</td>
      <td>94.972222</td>
    </tr>
    <tr>
      <th>Rodriguez High School</th>
      <td>District</td>
      <td>3999</td>
      <td>2547363</td>
      <td>637.0</td>
      <td>76.842711</td>
      <td>80.744686</td>
      <td>66.366592</td>
      <td>80.220055</td>
      <td>73.293323</td>
    </tr>
    <tr>
      <th>Johnson High School</th>
      <td>District</td>
      <td>4761</td>
      <td>3094650</td>
      <td>650.0</td>
      <td>77.072464</td>
      <td>80.966394</td>
      <td>66.057551</td>
      <td>81.222432</td>
      <td>73.639992</td>
    </tr>
    <tr>
      <th>Ford High School</th>
      <td>District</td>
      <td>2739</td>
      <td>1763916</td>
      <td>644.0</td>
      <td>77.102592</td>
      <td>80.746258</td>
      <td>68.309602</td>
      <td>79.299014</td>
      <td>73.804308</td>
    </tr>
    <tr>
      <th>Thomas High School</th>
      <td>Charter</td>
      <td>1635</td>
      <td>1043130</td>
      <td>638.0</td>
      <td>83.418349</td>
      <td>83.848930</td>
      <td>93.272171</td>
      <td>97.308869</td>
      <td>95.290520</td>
    </tr>
  </tbody>
</table>
</div>


# Top Performing Schools (By Overall Passing Rate)


```python
Top5_Schools=school_summary_final
Top5_Schools=Top5_Schools.nlargest(5,'% Overall Passing Rate')
display(Top5_Schools)
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
      <th>School Type</th>
      <th>Total Students</th>
      <th>Total School Budget</th>
      <th>Per Student Budget</th>
      <th>Average Math Score</th>
      <th>Average Reading Score</th>
      <th>% Passing Math</th>
      <th>% Passing Reading</th>
      <th>% Overall Passing Rate</th>
    </tr>
    <tr>
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
      <th>Cabrera High School</th>
      <td>Charter</td>
      <td>1858</td>
      <td>1081356</td>
      <td>582.0</td>
      <td>83.061895</td>
      <td>83.975780</td>
      <td>94.133477</td>
      <td>97.039828</td>
      <td>95.586652</td>
    </tr>
    <tr>
      <th>Thomas High School</th>
      <td>Charter</td>
      <td>1635</td>
      <td>1043130</td>
      <td>638.0</td>
      <td>83.418349</td>
      <td>83.848930</td>
      <td>93.272171</td>
      <td>97.308869</td>
      <td>95.290520</td>
    </tr>
    <tr>
      <th>Pena High School</th>
      <td>Charter</td>
      <td>962</td>
      <td>585858</td>
      <td>609.0</td>
      <td>83.839917</td>
      <td>84.044699</td>
      <td>94.594595</td>
      <td>95.945946</td>
      <td>95.270270</td>
    </tr>
    <tr>
      <th>Griffin High School</th>
      <td>Charter</td>
      <td>1468</td>
      <td>917500</td>
      <td>625.0</td>
      <td>83.351499</td>
      <td>83.816757</td>
      <td>93.392371</td>
      <td>97.138965</td>
      <td>95.265668</td>
    </tr>
    <tr>
      <th>Wilson High School</th>
      <td>Charter</td>
      <td>2283</td>
      <td>1319574</td>
      <td>578.0</td>
      <td>83.274201</td>
      <td>83.989488</td>
      <td>93.867718</td>
      <td>96.539641</td>
      <td>95.203679</td>
    </tr>
  </tbody>
</table>
</div>


# Bottom Performing Schools (By Overall Passing Rate)


```python
Bottom5_Schools=school_summary_final
Bottom5_Schools=Bottom5_Schools.nsmallest(5,'% Overall Passing Rate')
display(Bottom5_Schools)
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
      <th>School Type</th>
      <th>Total Students</th>
      <th>Total School Budget</th>
      <th>Per Student Budget</th>
      <th>Average Math Score</th>
      <th>Average Reading Score</th>
      <th>% Passing Math</th>
      <th>% Passing Reading</th>
      <th>% Overall Passing Rate</th>
    </tr>
    <tr>
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
      <th>Rodriguez High School</th>
      <td>District</td>
      <td>3999</td>
      <td>2547363</td>
      <td>637.0</td>
      <td>76.842711</td>
      <td>80.744686</td>
      <td>66.366592</td>
      <td>80.220055</td>
      <td>73.293323</td>
    </tr>
    <tr>
      <th>Figueroa High School</th>
      <td>District</td>
      <td>2949</td>
      <td>1884411</td>
      <td>639.0</td>
      <td>76.711767</td>
      <td>81.158020</td>
      <td>65.988471</td>
      <td>80.739234</td>
      <td>73.363852</td>
    </tr>
    <tr>
      <th>Huang High School</th>
      <td>District</td>
      <td>2917</td>
      <td>1910635</td>
      <td>655.0</td>
      <td>76.629414</td>
      <td>81.182722</td>
      <td>65.683922</td>
      <td>81.316421</td>
      <td>73.500171</td>
    </tr>
    <tr>
      <th>Johnson High School</th>
      <td>District</td>
      <td>4761</td>
      <td>3094650</td>
      <td>650.0</td>
      <td>77.072464</td>
      <td>80.966394</td>
      <td>66.057551</td>
      <td>81.222432</td>
      <td>73.639992</td>
    </tr>
    <tr>
      <th>Ford High School</th>
      <td>District</td>
      <td>2739</td>
      <td>1763916</td>
      <td>644.0</td>
      <td>77.102592</td>
      <td>80.746258</td>
      <td>68.309602</td>
      <td>79.299014</td>
      <td>73.804308</td>
    </tr>
  </tbody>
</table>
</div>


# Math and Reading Scores by Grade


```python
Scores_byGrade=merge_df

#Set school as index and select columns of interest
#Scores_byGrade=Scores_byGrade.set_index('school')
Scores_byGrade= Scores_byGrade[["school","grade", "reading_score", "math_score"]]
#print(Scores_byGrade.head())

#Group by school and grade
Grouped_byGrade=Scores_byGrade.groupby(["school", "grade"])

#Output Math Averages by Grade for each school
Math_byGrade=Grouped_byGrade.mean()
del Math_byGrade['reading_score']
New_Math_byGrade=Math_byGrade.reset_index().pivot(index='school', columns='grade')
New_Math_byGrade.columns= New_Math_byGrade.columns.set_levels(['Average Math Score'], level=0)
display(New_Math_byGrade)

#Output Reading Averages by Grade for each school
Reading_byGrade=Grouped_byGrade.mean()
del Reading_byGrade['math_score']
New_Reading_byGrade=Reading_byGrade.reset_index().pivot(index='school', columns='grade')
#print(New_Reading_byGrade.columns)
#print(New_Reading_byGrade.columns.levels[1])
New_Reading_byGrade.columns= New_Reading_byGrade.columns.set_levels(['Average Reading Score '], level=0)
display(New_Reading_byGrade)

#Reorganize Order of grades in both outputs - use loc method to select range and sort columns?
#New_Math_byGrade= New_Math_byGrade([["9th","10th","11th", "12th"]], level=1, axis=1) 
#x=New_Reading_byGrade.sort_index(level=1)
# DOESN'T WORK - multiIndexed
#x1=New_Reading_byGrade.sort_index(level=1, axis=1)      
#print(x1)
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
    <tr>
      <th></th>
      <th colspan="4" halign="left">Average Math Score</th>
    </tr>
    <tr>
      <th>grade</th>
      <th>10th</th>
      <th>11th</th>
      <th>12th</th>
      <th>9th</th>
    </tr>
    <tr>
      <th>school</th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>Bailey High School</th>
      <td>76.996772</td>
      <td>77.515588</td>
      <td>76.492218</td>
      <td>77.083676</td>
    </tr>
    <tr>
      <th>Cabrera High School</th>
      <td>83.154506</td>
      <td>82.765560</td>
      <td>83.277487</td>
      <td>83.094697</td>
    </tr>
    <tr>
      <th>Figueroa High School</th>
      <td>76.539974</td>
      <td>76.884344</td>
      <td>77.151369</td>
      <td>76.403037</td>
    </tr>
    <tr>
      <th>Ford High School</th>
      <td>77.672316</td>
      <td>76.918058</td>
      <td>76.179963</td>
      <td>77.361345</td>
    </tr>
    <tr>
      <th>Griffin High School</th>
      <td>84.229064</td>
      <td>83.842105</td>
      <td>83.356164</td>
      <td>82.044010</td>
    </tr>
    <tr>
      <th>Hernandez High School</th>
      <td>77.337408</td>
      <td>77.136029</td>
      <td>77.186567</td>
      <td>77.438495</td>
    </tr>
    <tr>
      <th>Holden High School</th>
      <td>83.429825</td>
      <td>85.000000</td>
      <td>82.855422</td>
      <td>83.787402</td>
    </tr>
    <tr>
      <th>Huang High School</th>
      <td>75.908735</td>
      <td>76.446602</td>
      <td>77.225641</td>
      <td>77.027251</td>
    </tr>
    <tr>
      <th>Johnson High School</th>
      <td>76.691117</td>
      <td>77.491653</td>
      <td>76.863248</td>
      <td>77.187857</td>
    </tr>
    <tr>
      <th>Pena High School</th>
      <td>83.372000</td>
      <td>84.328125</td>
      <td>84.121547</td>
      <td>83.625455</td>
    </tr>
    <tr>
      <th>Rodriguez High School</th>
      <td>76.612500</td>
      <td>76.395626</td>
      <td>77.690748</td>
      <td>76.859966</td>
    </tr>
    <tr>
      <th>Shelton High School</th>
      <td>82.917411</td>
      <td>83.383495</td>
      <td>83.778976</td>
      <td>83.420755</td>
    </tr>
    <tr>
      <th>Thomas High School</th>
      <td>83.087886</td>
      <td>83.498795</td>
      <td>83.497041</td>
      <td>83.590022</td>
    </tr>
    <tr>
      <th>Wilson High School</th>
      <td>83.724422</td>
      <td>83.195326</td>
      <td>83.035794</td>
      <td>83.085578</td>
    </tr>
    <tr>
      <th>Wright High School</th>
      <td>84.010288</td>
      <td>83.836782</td>
      <td>83.644986</td>
      <td>83.264706</td>
    </tr>
  </tbody>
</table>
</div>



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
    <tr>
      <th></th>
      <th colspan="4" halign="left">Average Reading Score</th>
    </tr>
    <tr>
      <th>grade</th>
      <th>10th</th>
      <th>11th</th>
      <th>12th</th>
      <th>9th</th>
    </tr>
    <tr>
      <th>school</th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>Bailey High School</th>
      <td>80.907183</td>
      <td>80.945643</td>
      <td>80.912451</td>
      <td>81.303155</td>
    </tr>
    <tr>
      <th>Cabrera High School</th>
      <td>84.253219</td>
      <td>83.788382</td>
      <td>84.287958</td>
      <td>83.676136</td>
    </tr>
    <tr>
      <th>Figueroa High School</th>
      <td>81.408912</td>
      <td>80.640339</td>
      <td>81.384863</td>
      <td>81.198598</td>
    </tr>
    <tr>
      <th>Ford High School</th>
      <td>81.262712</td>
      <td>80.403642</td>
      <td>80.662338</td>
      <td>80.632653</td>
    </tr>
    <tr>
      <th>Griffin High School</th>
      <td>83.706897</td>
      <td>84.288089</td>
      <td>84.013699</td>
      <td>83.369193</td>
    </tr>
    <tr>
      <th>Hernandez High School</th>
      <td>80.660147</td>
      <td>81.396140</td>
      <td>80.857143</td>
      <td>80.866860</td>
    </tr>
    <tr>
      <th>Holden High School</th>
      <td>83.324561</td>
      <td>83.815534</td>
      <td>84.698795</td>
      <td>83.677165</td>
    </tr>
    <tr>
      <th>Huang High School</th>
      <td>81.512386</td>
      <td>81.417476</td>
      <td>80.305983</td>
      <td>81.290284</td>
    </tr>
    <tr>
      <th>Johnson High School</th>
      <td>80.773431</td>
      <td>80.616027</td>
      <td>81.227564</td>
      <td>81.260714</td>
    </tr>
    <tr>
      <th>Pena High School</th>
      <td>83.612000</td>
      <td>84.335938</td>
      <td>84.591160</td>
      <td>83.807273</td>
    </tr>
    <tr>
      <th>Rodriguez High School</th>
      <td>80.629808</td>
      <td>80.864811</td>
      <td>80.376426</td>
      <td>80.993127</td>
    </tr>
    <tr>
      <th>Shelton High School</th>
      <td>83.441964</td>
      <td>84.373786</td>
      <td>82.781671</td>
      <td>84.122642</td>
    </tr>
    <tr>
      <th>Thomas High School</th>
      <td>84.254157</td>
      <td>83.585542</td>
      <td>83.831361</td>
      <td>83.728850</td>
    </tr>
    <tr>
      <th>Wilson High School</th>
      <td>84.021452</td>
      <td>83.764608</td>
      <td>84.317673</td>
      <td>83.939778</td>
    </tr>
    <tr>
      <th>Wright High School</th>
      <td>83.812757</td>
      <td>84.156322</td>
      <td>84.073171</td>
      <td>83.833333</td>
    </tr>
  </tbody>
</table>
</div>


# Scores by School Spending


```python
Scores_bySpending = school_summary_final
Scores_bySpending = Scores_bySpending[["Per Student Budget", "Average Math Score", "Average Reading Score",
                                        "% Passing Math", "% Passing Reading", "% Overall Passing Rate"]]
#print(Scores_bySpending["Per Student Budget"].quantile([0.25, 0.5, 0.75,1]))

#Create bins based on quantiles and create names for each bin
bins = [0, 592, 628, 642, 655]
bin_names= [' < 592', '593-628', '629-642', '643-655']
pd.cut(Scores_bySpending["Per Student Budget"], bins, labels= bin_names)

#Add Spending Ranges per student column - WHAT DOES THE ERROR BELOW MEAN?
Scores_bySpending["Spending Ranges Per Student ($)"] = pd.cut(Scores_bySpending["Per Student Budget"], bins, labels= bin_names)
del Scores_bySpending["Per Student Budget"]

#Perform a groupby based on newly created Spending Ranges column
Grouped_byRange = Scores_bySpending.groupby("Spending Ranges Per Student ($)")
display(Grouped_byRange.mean())
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
      <th>Average Math Score</th>
      <th>Average Reading Score</th>
      <th>% Passing Math</th>
      <th>% Passing Reading</th>
      <th>% Overall Passing Rate</th>
    </tr>
    <tr>
      <th>Spending Ranges Per Student ($)</th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>&lt; 592</th>
      <td>83.455399</td>
      <td>83.933814</td>
      <td>93.460096</td>
      <td>96.610877</td>
      <td>95.035486</td>
    </tr>
    <tr>
      <th>593-628</th>
      <td>81.899826</td>
      <td>83.155286</td>
      <td>87.133538</td>
      <td>92.718205</td>
      <td>89.925871</td>
    </tr>
    <tr>
      <th>629-642</th>
      <td>78.990942</td>
      <td>81.917212</td>
      <td>75.209078</td>
      <td>86.089386</td>
      <td>80.649232</td>
    </tr>
    <tr>
      <th>643-655</th>
      <td>77.023555</td>
      <td>80.957446</td>
      <td>66.701010</td>
      <td>80.675217</td>
      <td>73.688113</td>
    </tr>
  </tbody>
</table>
</div>


# Scores by School Size


```python
Scores_bySchoolSize = school_summary_final
Scores_bySchoolSize = Scores_bySchoolSize[["Total Students", "Average Math Score", "Average Reading Score",
                                        "% Passing Math", "% Passing Reading", "% Overall Passing Rate"]]

#Create bins based on quantiles and create names for each bin
bins2 = [0, 1786, 2925, 4976]
bin_names2= ['  Small (<1786)', ' Medium (1787-2925)', 'Large (2926-4976)']
pd.cut(Scores_bySchoolSize["Total Students"], bins2, labels= bin_names2)

#Add Spending Ranges per student column - WHAT DOES THE ERROR BELOW MEAN?
Scores_bySchoolSize["School Size"] = pd.cut(Scores_bySchoolSize["Total Students"], bins2, labels= bin_names2)
del Scores_bySchoolSize["Total Students"]
#print(Scores_bySchoolSize.columns)

#Perform a groupby based on newly created School Size column
Grouped_bySize = Scores_bySchoolSize.groupby("School Size")
display(Grouped_bySize.mean())
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
      <th>Average Math Score</th>
      <th>Average Reading Score</th>
      <th>% Passing Math</th>
      <th>% Passing Reading</th>
      <th>% Overall Passing Rate</th>
    </tr>
    <tr>
      <th>School Size</th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>Small (&lt;1786)</th>
      <td>83.554500</td>
      <td>83.850220</td>
      <td>93.526422</td>
      <td>96.500267</td>
      <td>95.013345</td>
    </tr>
    <tr>
      <th>Medium (1787-2925)</th>
      <td>80.750065</td>
      <td>82.769850</td>
      <td>83.065610</td>
      <td>90.161203</td>
      <td>86.613407</td>
    </tr>
    <tr>
      <th>Large (2926-4976)</th>
      <td>76.993025</td>
      <td>80.967495</td>
      <td>66.369129</td>
      <td>80.995600</td>
      <td>73.682364</td>
    </tr>
  </tbody>
</table>
</div>


# Scores by School Type (Charter or District)


```python
Scores_bySchoolType = school_summary_final
Scores_bySchoolType = Scores_bySchoolType[["School Type", "Average Math Score", "Average Reading Score",
                                        "% Passing Math", "% Passing Reading", "% Overall Passing Rate"]]
#print(Scores_bySchoolType)

#Perform a groupby based on School Type column
Grouped_bySchoolType = Scores_bySchoolType.groupby("School Type")
display(Grouped_bySchoolType.mean())
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
      <th>Average Math Score</th>
      <th>Average Reading Score</th>
      <th>% Passing Math</th>
      <th>% Passing Reading</th>
      <th>% Overall Passing Rate</th>
    </tr>
    <tr>
      <th>School Type</th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>Charter</th>
      <td>83.473852</td>
      <td>83.896421</td>
      <td>93.620830</td>
      <td>96.586489</td>
      <td>95.103660</td>
    </tr>
    <tr>
      <th>District</th>
      <td>76.956733</td>
      <td>80.966636</td>
      <td>66.548453</td>
      <td>80.799062</td>
      <td>73.673757</td>
    </tr>
  </tbody>
</table>
</div>


# Three observable trends from the data above
1.	District-wise kids score higher, on average, in reading (81.88%) compared to math (74.98%).  
2.	The difference between average math and reading scores is about the same (within a one point difference) for the top 5 performing schools, whereas the difference between average math and reading scores for the bottom 5 performing schools is about 3-4 points. Interestingly the top 5 performing schools are all charter schools, whereas the bottom performing schools are all district schools. The overall passing rate for these top 5 schools is ~95.1%, whereas the passing rate for the bottom 5 schools is 73.7% (see Scores by School Type table). Initially I thought that the difference in performance was related to funding, but that is not the case. The top 5 schools actually have smaller per student budgets ranging from 578-638 dollars, whereas the bottom 5 schools have larger budgets of 637-655 dollars per student. The ‘Scores by School Spending’ table confirms that higher spending per student does not translate to higher overall passing rate, in fact it is the opposite. 
3.	The highest overall passing rate is strongly associated with school size, with students succeeding at higher rates in smaller schools (with <1786 students) compared to larger schools (with 2926-4976 students). In this dataset, school size is a big factor influencing the higher success rate of Charter schools compared to the lower success rate in District schools, with the latter having two to four times as many students as the former.

