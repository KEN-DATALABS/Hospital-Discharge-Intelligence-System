# Synthetic Healthcare Analytics Dataset: Data Dictionary and Business Definition

## Project overview
This dataset is designed for a dual-task healthcare analytics project:
- **Classification task:** predict `readmission_30days`
- **Regression task:** predict `total_treatment_cost`

The data simulates a patient journey from admission through discharge, including demographic, lifestyle, clinical, hospital encounter, and outcome variables. It also includes intentionally messy real-world issues such as missing values, inconsistent data types, outliers, coded values, and near-duplicate rows.

## Business problems

### 1) Classification business problem
Hospitals want to identify patients who are likely to be readmitted within 30 days so that they can:
- strengthen discharge planning,
- schedule follow-up care earlier,
- assign care coordination,
- reduce avoidable readmissions.

**Target:** `readmission_30days`  
**Type:** Binary classification  
**Success focus:** precision and recall for the minority readmission class.

### 2) Regression business problem
Hospitals also want to estimate the total cost of treatment for a patient encounter so that they can:
- forecast budget demand,
- allocate resources,
- compare expensive versus routine care pathways,
- support operational and insurance planning.

**Target:** `total_treatment_cost`  
**Type:** Regression  
**Success focus:** MAE and RMSE.

## Column-by-column explanation

| Column | Meaning | Type / Notes |
|---|---|---|
| `patient_id` | Unique identifier for each encounter record. | Identifier only; not predictive. |
| `encounter_timestamp` | Date and time of the hospital encounter. | Supports drift monitoring and time-based analysis. |
| `age` | Patient age in years. | Includes a small number of unrealistic outliers for cleaning practice. |
| `gender` | Self-reported gender. | Values: `M`, `F`, `Other`. |
| `ethnicity` | Self-reported ethnic group. | Mixed-case intentionally to simulate inconsistent source systems. |
| `income_level` | Approximate household income band. | Ordinal field with missing values to mimic socioeconomic gaps. |
| `education_level` | Highest education attainment. | Ordinal categorical variable. |
| `employment_status` | Current employment state. | Values: `Employed`, `Unemployed`, `Retired`. |
| `smoking_status` | Smoking indicator collected from different source formats. | Mixed types such as `0`, `1`, `yes`, `no`, `current`. |
| `alcohol_consumption` | Approximate alcohol intake. | Mixed numeric and text values such as `12` and `12 units`. |
| `physical_activity` | Approximate weekly physical activity score. | Numeric with a few extreme outliers. |
| `sleep_hours` | Average sleep per night. | Floating-point with missingness. |
| `diet_score` | Diet quality score on a 0–10 scale. | Contains a few invalid values such as `11` and `-2`. |
| `bmi` | Body mass index. | Numeric with missing values and outliers. |
| `systolic_bp` | Systolic blood pressure. | Includes a few impossible values for quality-control testing. |
| `diastolic_bp` | Diastolic blood pressure. | Numeric clinical measurement. |
| `heart_rate` | Resting heart rate during admission. | Numeric clinical measurement. |
| `blood_sugar_fasting` | Fasting blood glucose level. | Numeric with missing values. |
| `cholesterol_total` | Total cholesterol. | Includes coded strings such as `high` and `NULL`. |
| `admission_type` | How the patient entered care. | Messy categories like `Emerg`, `Emergency`, `URGENT`, `elective`. |
| `department` | Treating clinical department. | Contains spelling and case inconsistencies such as `Cardio`, `cardiology`, `CARDIOLOGY`. |
| `length_of_stay` | Length of stay in days. | Strong predictor for both cost and readmission. |
| `procedures_count` | Number of procedures performed. | Integer count. |
| `medications_count` | Number of medications prescribed/administered. | Integer with missing values. |
| `complications` | Whether the patient experienced complications. | Binary field with some missing values. |
| `readmission_30days` | Whether the patient was readmitted within 30 days. | **Primary classification target**. |
| `total_treatment_cost` | Total cost of the encounter. | **Primary regression target**; varies realistically by severity and care intensity. |
| `patient_satisfaction_score` | Patient-reported satisfaction score from 1–10. | Higher values generally reflect better care experience. |
| `discharge_disposition` | Where the patient went after discharge. | Examples: `Home`, `Rehab`, `Skilled Nursing`, `Transfer`, `Against Medical Advice`. |
| `follow_up_appointment_scheduled` | Whether follow-up was booked before discharge. | Binary indicator used in readmission risk analysis. |

## Alignment notes
This dataset is intentionally structured so the variables are not random:
- older patients tend to have more chronic burden and more procedures,
- longer stays tend to increase treatment cost,
- complications raise both readmission risk and cost,
- emergency admissions are generally riskier than elective admissions,
- lower income groups may show slightly higher readmission risk because of access-to-care effects.

## Suggested modeling use
- **Classification:** train a model on `readmission_30days`
- **Regression:** train a model on `total_treatment_cost`
- Use the same preprocessing pipeline, then branch into two model heads or two separate model artifacts behind one FastAPI endpoint.

## Deployment note
A single FastAPI endpoint can expose both predictions in one response:
- probability and class for readmission,
- estimated treatment cost,
- optional risk band for business interpretation.
