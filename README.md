# Student Placement Analytics Dashboard

A Power BI project for exploring student employability, placement outcomes, skills, and salary patterns. The report is intended to help college and university placement cells, EdTech companies, and training institutes identify where career-readiness support may be useful.

## Business problem

Institutions need a clear view of placement outcomes and the factors they can support: academic performance, attendance, technical and communication skills, practical experience, interview readiness, and self-learning. This dashboard brings those indicators together to help stakeholders ask:

- How do placement outcomes vary by branch and college tier?
- Which skills and experiences should students strengthen?
- How do average salaries differ across branches and company types?
- Where should placement teams focus training and employer outreach?

The report is descriptive: it summarizes the supplied dataset and does not establish that any particular skill or student characteristic causes placement success.

## Repository structure

```text
.
├── README.md
├── Student Placements Dashboard.pbix
├── Student Placement Dashboard.png
└── student_placement_career_success_dataset.csv
```
<img width="2880" height="1700" alt="image" src="https://github.com/user-attachments/assets/4c51c1fa-28d9-4455-8fc2-c16a55a9f04e" />

| File | Purpose |
| --- | --- |
| `Student Placements Dashboard.pbix` | Power BI report and data model |
| `Student Placement Dashboard.png` | Preview of the report page |
| `student_placement_career_success_dataset.csv` | Kaggle dataset used by the project: 25,000 rows and 44 columns |

The dataset is modeled as a single student-level table; there are no table relationships to maintain.

## Technical architecture and workflow

```text
Kaggle CSV
    ↓
Power BI Desktop / Power Query (extract, transform, load)
    ↓
Single student-level model table
    ↓
DAX aggregations and report visuals
    ↓
Placement overview dashboard
```

1. **Extract:** Load `student_placement_career_success_dataset.csv` into Power BI.
2. **Transform:** In Power Query, review column types, ensure numeric fields are numeric, inspect blanks, and confirm the student identifier is unique. The CSV has blank values in `GitHub_repos`, `mock_interview_score`, and `sleep_hours` (500 rows each); decide how to handle these before analyses that use those fields.
3. **Load/model:** Load the data as one table. For the DAX examples below, name the table `StudentPlacements` (or replace that name with the actual table name in the PBIX).
4. **Analyze:** Use measures for student counts, placement outcomes, offers, and average placed-student salary. Slice or group the measures by branch, college tier, and company type.
5. **Present:** The report page includes placement distribution, placement by branch, salary by company type and branch, placement rate by college tier, CGPA versus package, and skills by placement status.

## DAX measures

The PBIX contains the report visuals, but the measure definitions are not separately documented in the repository. These measures reproduce the key dashboard metrics; create them in Power BI if they are not already present. They assume the table is named `StudentPlacements` and that `placement_status` contains `Placed` and `Not Placed`.

```DAX
Total Students =
DISTINCTCOUNT ( StudentPlacements[student_id] )

Placed Students =
CALCULATE (
    [Total Students],
    StudentPlacements[placement_status] = "Placed"
)

Unplaced Students =
CALCULATE (
    [Total Students],
    StudentPlacements[placement_status] = "Not Placed"
)

Placement Rate =
DIVIDE ( [Placed Students], [Total Students], 0 )

Total Offers =
SUM ( StudentPlacements[offer_count] )

Average Salary (Placed) =
CALCULATE (
    AVERAGE ( StudentPlacements[salary_lpa] ),
    StudentPlacements[placement_status] = "Placed"
)
```

Format `Placement Rate` as a percentage and salary measures as LPA. These measures respect the current report filter context, so they can be used in visuals grouped by branch, college tier, or company type.

## Key analytical findings

Calculated from the checked-in CSV:

- **Placement:** 24,596 of 25,000 students are marked placed (98.38%); 404 are not placed.
- **Offers:** The sum of `offer_count` is 49,185. Student IDs are unique in the CSV.
- **Salary:** The mean `salary_lpa` among placed students is approximately **19.32 LPA**. The 404 not-placed records have zero salary and zero offers, so exclude them from salary averages.
- **Branch:** Placement rates range from **97.98% in Electronics** to **98.61% in Mechanical**. These rates are close; this dataset does not support describing a branch as having clearly poor placement performance.
- **College tier:** Placement rates are **99.57% for tier 1**, **98.61% for tier 2**, and **97.88% for tier 3**.
- **Company type:** Among placed students, Product-Based companies have the highest mean salary in this dataset at approximately **19.36 LPA**. The differences among company types are modest.

Treat these as descriptive results for this dataset, not guarantees of future outcomes. Validate definitions and data quality with the institution before using the metrics for operational decisions.

## Business recommendations

- Use branch and college-tier comparisons to prioritize further investigation, not to label students or departments. The observed branch placement-rate differences are small.
- Offer targeted support in technical practice, project and internship experience, communication, aptitude, and mock interviews; track participation and outcomes over time to learn what helps.
- Help students compare employer types using salary alongside role, location, work mode, and career-growth opportunities.
- Have placement cells review student-level data with appropriate privacy controls and provide supportive, actionable guidance rather than using a dashboard score as a high-stakes decision.
- Validate the zero-salary convention, missing values, and the definition of “placed” before publishing institutional reports.

## Run locally in Power BI

1. Clone or download this repository, keeping the PBIX and CSV together.
2. Install and open **Microsoft Power BI Desktop**.
3. Open `Student Placements Dashboard.pbix`.
4. If Power BI prompts for a missing CSV or the data source path is invalid, go to **Transform data** and update the CSV source to the local `student_placement_career_success_dataset.csv` file.
5. Select **Refresh** and confirm the report visuals load. If setting up the data source from scratch, use **Get data → Text/CSV**, select the CSV, inspect the preview and data types, then **Load**.

Power BI Desktop is required to edit or refresh the report. The PBIX is the easiest way to view the existing dashboard; Power BI Service publishing may require an account and appropriate permissions.

## Future enhancements

- Develop and validate an AI-based placement prediction model using an appropriate held-out dataset and clear evaluation metrics.
- Create an at-risk student support workflow that explains its indicators and routes students to human advisors; avoid treating predictions as definitive.
- Add scheduled weekly or monthly placement reports, with email notifications where the organization’s Power BI licensing and service setup allow.
- Add longitudinal data, institution and employer reference tables, data-quality checks, and documented measures to support trend analysis and maintainability.

## Author

**Thota Sumith** — Data Analytics Trainee, **Anudip Foundations**
