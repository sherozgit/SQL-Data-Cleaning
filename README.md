# SQL Project: Data Cleaning on Layoffs Dataset

## Project Overview
This project focuses on **data cleaning** using SQL, applied to the **Layoffs Dataset 2022** sourced from [Kaggle](https://www.kaggle.com/datasets/swaptr/layoffs-2022). The dataset provides information on company layoffs, including details such as company name, industry, location, and the number of employees laid off.

The goal of this project is to clean and prepare the dataset for analysis by addressing issues such as duplicates, null values, standardizing data, and ensuring consistent formatting.

---

## Objectives
1. **Create a Safe Staging Environment**:
   - Preserve the raw data by creating a staging table for cleaning and transformation.
2. **Remove Duplicates**:
   - Identify and remove duplicate rows while retaining legitimate entries.
3. **Standardize and Fix Errors**:
   - Ensure consistency in fields like `industry` and `country`.
   - Standardize date formats for easier analysis.
4. **Handle Null Values**:
   - Populate null fields where possible or retain nulls for appropriate analysis.
5. **Remove Unnecessary Data**:
   - Drop irrelevant or redundant rows and columns to optimize the dataset.

---

## Dataset Description
The dataset includes the following fields:
- `company`: Name of the company.
- `location`: Company's headquarters.
- `industry`: Industry sector of the company.
- `total_laid_off`: Number of employees laid off.
- `percentage_laid_off`: Percentage of the workforce affected.
- `date`: Date of the layoffs.
- `stage`: Company's funding stage (e.g., Series A, IPO).
- `country`: Company's country of operation.
- `funds_raised_millions`: Total funds raised by the company (in millions).

---

## Steps Performed

### **1. Staging Table Creation**
- A staging table (`layoffs_staging`) was created using the raw data to ensure the original dataset remains untouched.
- Data was copied from the original table to the staging table.

```sql
CREATE TABLE world_layoffs.layoffs_staging LIKE world_layoffs.layoffs;
INSERT INTO world_layoffs.layoffs_staging SELECT * FROM world_layoffs.layoffs;
```

---

### **2. Removing Duplicates**
- Identified duplicates based on all relevant columns using `ROW_NUMBER()` and removed rows with a row number greater than 1.
- A new table (`layoffs_staging2`) was created to assign row numbers and facilitate deletion of duplicates.

```sql
DELETE FROM world_layoffs.layoffs_staging2 WHERE row_num >= 2;
```

---

### **3. Standardizing and Fixing Errors**
- Standardized `industry` values (e.g., consolidated variations of "Crypto").
- Ensured consistency in `country` field by trimming trailing periods (e.g., "United States." → "United States").
- Converted `date` field from string to `DATE` type for proper analysis.

```sql
UPDATE layoffs_staging2 SET industry = 'Crypto' WHERE industry IN ('Crypto Currency', 'CryptoCurrency');
UPDATE layoffs_staging2 SET `date` = STR_TO_DATE(`date`, '%m/%d/%Y');
ALTER TABLE layoffs_staging2 MODIFY COLUMN `date` DATE;
```

---

### **4. Handling Null Values**
- Updated null values in the `industry` field based on matching company names.
- Retained nulls in numeric fields (`total_laid_off`, `funds_raised_millions`) for accurate calculations during analysis.

```sql
UPDATE layoffs_staging2 t1 JOIN layoffs_staging2 t2
ON t1.company = t2.company
SET t1.industry = t2.industry
WHERE t1.industry IS NULL AND t2.industry IS NOT NULL;
```

---

### **5. Removing Unnecessary Data**
- Removed rows where both `total_laid_off` and `percentage_laid_off` were null, as these rows provided no actionable insights.
- Dropped temporary columns (`row_num`) used during the cleaning process.

```sql
DELETE FROM world_layoffs.layoffs_staging2 WHERE total_laid_off IS NULL AND percentage_laid_off IS NULL;
ALTER TABLE layoffs_staging2 DROP COLUMN row_num;
```

---

## Cleaned Dataset
After the cleaning process, the dataset is:
- Free of duplicates and unnecessary rows.
- Standardized for consistent analysis.
- Ready for exploratory data analysis (EDA) or visualization in tools like Python, Tableau, or Power BI.

---

## Key Insights
- The cleaning process revealed several inconsistencies in the dataset, including duplicate rows, inconsistent industry names, and non-standardized date formats.
- Standardizing the dataset ensures accurate and meaningful analysis of layoffs trends across industries and countries.

---

## How to Use the Cleaned Data
1. Import the cleaned data (`layoffs_staging2`) into your database or analysis tool.
2. Use the standardized `industry`, `country`, and `date` fields for grouping and filtering.
3. Perform statistical or visual analysis to uncover patterns in layoffs.

---

## Tools Used
- **Database**: MySQL
- **Commands**: SQL for data manipulation, cleaning, and transformations.

---

## Future Improvements
- Integrate additional datasets (e.g., macroeconomic indicators) for enriched analysis.
- Automate the cleaning process using Python or SQL scripts for future datasets.

---

## References
- Dataset: [Layoffs Dataset 2022](https://www.kaggle.com/datasets/swaptr/layoffs-2022)

Feel free to contribute by submitting pull requests or raising issues for improvements.

---
