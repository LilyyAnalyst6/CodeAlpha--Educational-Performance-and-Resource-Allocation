# Educational Performance and Resource Allocation: Power BI Analysis

**Do learning resources explain student results? An analysis of 12,469 student records**

| | |
|---|---|
| **Author** | [Alexander Chinwendu Lilian] |
| **Contact** | linkedin.com/in/lilian-alexander|
| **Date** | October 2026 |
| **Tools** | Power BI Desktop, Power Query (M), DAX, Excel,|
| **Dataset** | Student performance dataset, 12,469 records after cleaning |
| **Deliverable** | 2-page interactive Power BI report (`Educational_Performance.pbix`) |
| **Context** | CodeAlpha Power BI Internship, Task 4: Educational Performance and Resource Allocation |

---

## 1. Executive summary

Education managers have limited budgets for learning resources such as materials, internet access and educational technology. The practical question is where that money should go. This project builds a dashboard that measures student performance, shows where access to resources is thin, and tests whether access is linked to better results.

**Headline findings**

1. **Performance is middling and widely spread.** The average exam score is **70.3 out of 100**, average attendance is **80.2%**, and **49.1%** of students fall into the two lowest grade bands (C and D).
2. **Access gaps exist.** **20.7%** of students sit at the lowest resource level (Level 0), **8.3%** have no internet access, and **29.3%** do not use educational technology.
3. **Access does not move exam scores in this data.** Average scores are within about half a point across resource levels (70.2 to 70.5), internet access (70.7 without, 70.3 with) and EduTech use (70.0 without, 70.4 with).
4. **Study habits do not explain scores either.** Exam score has a correlation of **0.00** with weekly study hours and **-0.01** with attendance.
5. **The only visible signal is small.** Students at stress level 0 average **71.9**, about 2 points above the other levels, which is roughly 0.1 standard deviations.
6. **The decision-making takeaway:** this dataset gives no evidence that expanding resource access alone would raise scores. Access gaps may still matter for equity, but any spending justified by score gains should be tested in a measured pilot first.

> **Important context:** the dataset contains repeated student profiles (about 4,200 distinct profiles across 12,469 rows), so rows are not independent students, and the meaning of several codes is not published. Findings are exploratory. 

---

## 2. Business context and objectives

A school or university department deciding how to allocate learning resources needs to know three things: how students are performing, where access to resources is lacking, and whether closing those gaps is likely to change outcomes.

| Objective from the brief | How it was addressed |
|---|---|
| Visualise academic performance metrics | KPI cards for exam score, attendance and low-grade share, plus grade distribution and score breakdowns |
| Analyse resource usage and identify gaps | Student counts by resource level, internet access and EduTech use, with the share of students at the lowest level of each |
| Support data-driven decisions in education management | Comparisons of average score by resource access, study behaviour and stress level, with an honest reading of how strong each link is |

---

## 3. Data

**Source:** Najem, Ziti and Zaoui Seghroucheni (2025), Zenodo, DOI 10.5281/zenodo.16459132, licensed **CC BY 4.0**. The dataset is credited here as the licence requires.

The cleaned workbook (`Education_Dataset_Cleaned.xlsx`) contains the `Clean_Data` sheet, a lookup sheet for the codes, a data dictionary, the removed duplicate rows and a cleaning log.

### 3.1 Fields

| Group | Fields |
|---|---|
| Study habits | `StudyHours` (5 to 44), `Attendance` (60 to 100%), `OnlineCourses` (0 to 20), `AssignmentCompletion` (50 to 100%) |
| Resources and engagement | `Resources` (code 0 to 2), `Internet` (0/1), `EduTech` (0/1), `Extracurricular` (0/1), `Discussions` (0/1) |
| Student profile | `Gender` (0/1), `Age` (18 to 29), `LearningStyle` (0 to 3), `Motivation` (0 to 2), `StressLevel` (0 to 2) |
| Outcomes | `ExamScore` (40 to 100), `FinalGrade` (0 to 3, derived from exam score) |

`StudentID` is a row number added during cleaning. The source has no student identifier.

### 3.2 Data quality profile

| Check | Result | Treatment |
|---|---|---|
| Rows in the uploaded file | 14,003 | |
| Exact duplicate rows | 1,534 | Removed (first copy kept). Listed in the `Removed_Duplicates` sheet |
| Rows analysed | 12,469 | Reconciled to zero difference |
| Blank cells | 0 | No imputation needed |
| Values outside stated ranges | 0 | None removed |
| Study hours above 36 | 45 rows | Kept as plausible |
| Repeated profiles | 4,199 distinct combinations across 12,469 rows | **Not removed.** Flagged as a limitation because these rows are not independent students |

### 3.3 Code labels

Several fields are stored as numeric codes, and the source does not publish what they mean.

| Field | Label used in the report | Status |
|---|---|---|
| FinalGrade | A (85-100), B (70-84), C (55-69), D (40-54) | Confirmed by the data (derived from exam score) |
| Internet, EduTech, Extracurricular, Discussions | No (0) / Yes (1) | Assumed, to be confirmed |
| Resources, Motivation, StressLevel | Level 0 / 1 / 2 | Not published. Shown as coded to avoid inventing meaning |
| LearningStyle, Gender | Style 0 to 3, Gender 0 / 1 | Not published |

---

## 4. Approach

1. **Acquire and validate.** Loaded the cleaned workbook and reviewed the cleaning log, data dictionary and range checks.
2. **Prepare in Power Query.** Imported `Clean_Data`, promoted headers and set integer types. Added readable label columns with conditional logic while keeping the original numeric columns for calculations.
3. **Model.** A single flat table (`Clean_Data`) plus a measures table. No relationships are needed because the data has no dimension tables.
4. **Enrich.** Added attendance and study-hour bands, with sort-order columns so bands display in the right sequence.
5. **Define measures in DAX.** Nine measures cover the KPIs and the internet score gap, keeping figures consistent across pages and slicers.
6. **Design the report.** Two pages at 1920 x 1080 with shared slicers for age, gender and learning style.
7. **Validate.** The figures in this report were recomputed independently from the dataset using the same measure definitions, and group differences were tested statistically.

### 4.1 Key analytical decisions

| Decision | Reason |
|---|---|
| Keep numeric codes alongside label columns | Measures filter on the numbers, while charts and slicers use the labels, so nothing breaks when labels change |
| Use "Level 0/1/2" and "Style 0 to 3" instead of Low/Medium/High | The source does not say which direction the codes run, and an invented direction would mislead |
| Define "low grade" as final grade C or D (codes 2 and 3) | Gives a clear bottom-half group. Final grade bands come directly from exam score |
| Define "low resources" as Resources code 0 | Assumes code 0 is the lowest level. Flagged as unconfirmed |
| Keep repeated profiles | Removing them would be a judgement call about which rows are real. They are flagged, and results are described as exploratory |
| Test group differences statistically | With 12,469 rows, tiny differences can look meaningful on a chart, so effect sizes are reported alongside |

---

## 5. Data model and measures

**Calculated columns**

| Column | Logic |
|---|---|
| Attendance Band | 60-69%, 70-79%, 80-89%, 90-100% (with `Attendance Band Order`) |
| Study Hours Band | 5-15, 16-20, 21-25, 26+ hours (with `Study Hours Band Order`) |
| Label columns | `FinalGrade Label`, `Resources Label`, `Motivation Label`, `Stress Level Label`, `Internet Label`, `Extracurricular Label`, `Discussions Label`, `EduTech Label`, `LearningStyle Label`, `Gender Label`, created in Power Query |

**Measures**

| Measure | DAX | Purpose |
|---|---|---|
| Total Students | `COUNTROWS(Clean_Data)` | Population size |
| Avg Exam Score | `AVERAGE(Clean_Data[ExamScore])` | Performance |
| Avg Attendance | `AVERAGE(Clean_Data[Attendance])` | Engagement |
| % Low Grades | `DIVIDE(CALCULATE([Total Students], Clean_Data[FinalGrade] >= 2), [Total Students])` | Share in grade C or D |
| % Low Resources | `DIVIDE(CALCULATE([Total Students], Clean_Data[Resources] = 0), [Total Students])` | Resource gap |
| % No Internet | `DIVIDE(CALCULATE([Total Students], Clean_Data[Internet] = 0), [Total Students])` | Connectivity gap |
| Avg Score With Internet | `CALCULATE([Avg Exam Score], Clean_Data[Internet] = 1)` | Comparison group |
| Avg Score No Internet | `CALCULATE([Avg Exam Score], Clean_Data[Internet] = 0)` | Comparison group |
| Internet Score Gap | `[Avg Score With Internet] - [Avg Score No Internet]` | Score difference linked to connectivity |

---

## 6. Dashboard overview

### Page 1: Academic Performance

<img width="897" height="490" alt="Edu Dashboard 1" src="https://github.com/user-attachments/assets/855ccdbd-9669-4a5d-9701-0752cfddd27d" />


- **KPI cards:** Total Students, Avg Attendance, Avg Exam Score, % Low Grades, % Low Resources, % No Internet
- **Charts:** students by final grade, students by resources level, students by internet access, students by EduTech use (donut), and average exam score by attendance band
- **Slicers:** Age, Gender, Learning Style

### Page 2: Resources Usage

<img width="903" height="492" alt="Edu Dashboard 2" src="https://github.com/user-attachments/assets/7818da98-c0d9-4356-a1ef-7991e7fe40bf" />


- **Resource comparisons:** average exam score by resources level, and by EduTech use split by internet access
- **Other drivers:** average exam score by stress level, and average exam score by study-hours band
- **Resources by grade:** a matrix of student counts by resources level and final grade
- **Slicers:** Age, Gender, Learning Style (the same three as page 1)

---

## 7. Findings

### 7.1 Headline KPIs (all students, no filters)

| Metric | Value |
|---|---|
| Total students | 12,469 |
| Average exam score | 70.3 (median 70) |
| Average attendance | 80.2% |
| Average weekly study hours | 20.0 |
| Average assignment completion | 74.5% |
| Students in grade C or D | 49.1% |
| Students at resources Level 0 | 20.7% |
| Students with no internet | 8.3% |

### 7.2 Grade distribution

| Grade band | Students | Share |
|---|---|---|
| A (85-100) | 3,401 | 27.3% |
| B (70-84) | 2,943 | 23.6% |
| C (55-69) | 3,221 | 25.8% |
| D (40-54) | 2,904 | 23.3% |

<img width="897" height="490" alt="dashboard_page1_academic_performance (1)" src="https://github.com/user-attachments/assets/6690ce1c-3dc1-4e04-82c0-99ed0b49b44d" />


The distribution is nearly flat: each band holds roughly a quarter of students. There is no dominant grade, and about half of students fall in the bottom two bands. This is partly structural, because final grade is simply exam score grouped into bands.

### 7.3 Where the access gaps are

| Resource | Group | Students | Share |
|---|---|---|---|
| Resources level | Level 0 | 2,585 | 20.7% |
| | Level 1 | 6,035 | 48.4% |
| | Level 2 | 3,849 | 30.9% |
| Internet | No | 1,034 | 8.3% |
| | Yes | 11,435 | 91.7% |
| EduTech | No | 3,651 | 29.3% |
| | Yes | 8,818 | 70.7% |

<img width="903" height="492" alt="dashboard_page2_resources_usage" src="https://github.com/user-attachments/assets/41970393-0ca8-426b-9893-23461f8e5363" />


Connectivity is widely available (92%), so the largest access gap is **EduTech use**, where nearly 3 in 10 students are not using it. About 1 in 5 students sit at the lowest resource level.

### 7.4 Does access change results?

| Group | Students | Average exam score | Share in grade C or D |
|---|---|---|---|
| Resources Level 0 | 2,585 | 70.2 | 49.4% |
| Resources Level 1 | 6,035 | 70.3 | 49.5% |
| Resources Level 2 | 3,849 | 70.5 | 48.3% |
| No internet | 1,034 | 70.7 | 46.8% |
| Internet | 11,435 | 70.3 | 49.3% |
| No EduTech | 3,651 | 70.0 | not tested |
| EduTech | 8,818 | 70.4 | not tested |



- **No resource variable is linked to a meaningful difference.** Resource levels differ by 0.2 points, EduTech users score 0.4 points higher, and students *without* internet score 0.5 points *higher* (the `Internet Score Gap` measure is -0.5).
- None of these differences is statistically distinguishable from zero (p-values of 0.82 for resources level, 0.41 for internet and 0.25 for EduTech).
- The resources-by-grade matrix on page 2 shows the same thing in counts: Level 0 has 692, 616, 675 and 602 students in grades A to D, and Level 2 has 1,069, 920, 951 and 909, which are almost identical proportions.
- Combining EduTech and internet shows the same flat pattern. Averages by group are 69.8 (neither), 70.1 (internet only), 71.2 (EduTech only) and 70.4 (both), but the "neither" group has only 310 students and the "EduTech only" group has 724.

### 7.5 Do study behaviours or stress explain scores?

| Group | Students | Average exam score |
|---|---|---|
| Attendance 60-69% | 2,875 | 71.3 |
| Attendance 70-79% | 3,115 | 69.8 |
| Attendance 80-89% | 3,168 | 70.1 |
| Attendance 90-100% | 3,311 | 70.1 |
| Study 5-15 hrs | 2,977 | 70.6 |
| Study 16-20 hrs | 3,565 | 70.1 |
| Study 21-25 hrs | 3,584 | 70.1 |
| Study 26+ hrs | 2,343 | 70.7 |
| Stress Level 0 | 2,524 | 71.9 |
| Stress Level 1 | 3,614 | 69.7 |
| Stress Level 2 | 6,331 | 70.0 |



- **More attendance and more study time do not show a higher score.** Correlations with exam score are -0.01 for attendance and 0.00 for study hours. The group that attends least (60-69%) has the *highest* average, which is the opposite of what you would expect and a sign the data may not reflect real student behaviour (see limitations).
- **Stress level is the only variable with a visible pattern**: Level 0 averages about 2 points above Levels 1 and 2. This difference is statistically detectable, but it is small (around 0.1 standard deviations), and the direction of the stress codes is not published, so it should not be read as "low stress means higher scores".
- **Other numeric fields behave the same way.** Assignment completion (0.03) and online courses taken (0.02) also have near-zero correlation with exam score.

### 7.6 Statistical check

| Variable | Test | Result |
|---|---|---|
| Resources level | One-way ANOVA | p = 0.82 |
| Internet access | Welch t-test | p = 0.40 |
| EduTech use | One-way ANOVA | p = 0.25 |
| Study hours band | One-way ANOVA | p = 0.39 |
| Attendance band | One-way ANOVA | p = 0.008 (difference of about 1.5 points) |
| Stress level | One-way ANOVA | p < 0.001 (difference of about 2 points) |

With 12,469 rows, even tiny gaps can reach statistical significance, and repeated profiles mean the real sample is smaller than it looks. The attendance and stress results are therefore best read as "small and worth confirming", not as established effects.

---

## 8. Recommendations

These are decision-support points, not conclusions about causes.

1. **Do not justify resource spending on score gains using this data alone.** No access measure is linked to a meaningful score difference. If the goal is to raise exam results, run a controlled pilot, for example giving EduTech to a group of non-users and comparing outcomes against a similar group.
2. **Treat access gaps as an equity question, separate from scores.** Roughly 1 in 5 students sit at the lowest resource level and 3 in 10 do not use EduTech. Closing these gaps may be worthwhile for fairness or readiness, even if scores do not respond in this data.
3. **Investigate stress as a lead, after confirming what the codes mean.** It is the one variable with a visible pattern. Confirming the labels and testing it in a better dataset is a low-cost next step.
4. **Look beyond the measured variables for what explains the 49% in grades C and D.** Study hours, attendance, resources and engagement explain almost none of the variation. Prior attainment, course difficulty, teaching quality and assessment design are candidates to collect.
5. **Improve data collection before the next allocation decision.** Publish the code labels, add a real student ID, record the time period for study hours, and capture repeated measures so progress over time can be tracked.

---

## 9. Limitations and assumptions

| Limitation | Impact | How it is handled |
|---|---|---|
| **Repeated profiles** (about 4,200 distinct combinations across 12,469 rows) | Rows are not independent students, so the effective sample is smaller and p-values are too optimistic | Flagged in the cleaning log and here. Results are called exploratory |
| **`StudentID` is not a real identifier** | Individual students cannot be tracked | Used only to count rows |
| **Unpublished code labels** (resources, motivation, stress, learning style, gender) | The direction of each scale is unknown | Shown as Level 0/1/2 and Style 0 to 3. "Low resources" assumes Level 0 is lowest |
| **Assumed Yes/No labels** | Internet, EduTech, discussions and extracurricular codes are assumed to be 0 = No, 1 = Yes | Stated in the lookup sheet and here |
| **Final grade is derived from exam score** | The two are not independent measures | Used for banding and display only |
| **Floors in the source** (exam 40, attendance 60, assignments 50) | The lowest values are truncated, not true minima | Noted. Averages near the floor may be slightly overstated |
| **Near-zero relationships across the board** | Unusual for real education data, which suggests the data may be synthetic or heavily combined | Treated with caution. Findings are not generalised to real institutions |
| **Cross-sectional, correlational data** | No causal claims can be made | Recommendations are framed as tests, not conclusions |
| **Study hours period not stated** | Weekly is assumed in the labels | Stated as a limitation |
| **Age range** | Data covers 18 to 29, though the source describes 18 to 30 | Noted |

---


## 10. Repository structure and how to open

```
CodeAlpha_EducationalPerformance/
|-- README.md
|-- Educational_Performance.pbix
|-- data/
|   `-- Education_Dataset_Cleaned.xlsx
`-- images/
    |-- dashboard_page1_academic_performance.png
    |-- dashboard_page2_resources_usage.png
    |-- 01_grade_distribution.png
    |-- 02_access_gaps.png
    |-- 03_scores_by_access.png
    `-- 04_scores_by_behaviour.png
```

---

## 11. Data source and licence

Najem, Ziti and Zaoui Seghroucheni (2025). Zenodo. DOI: 10.5281/zenodo.16459132. Licensed under [CC BY 4.0](https://creativecommons.org/licenses/by/4.0/). The dataset was cleaned for this project (duplicates removed, ranges checked, code labels documented). No other changes were made to the values.

