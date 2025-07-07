# Data-Cleaning-in-SQL

**-- Create duplicate table--**

CREATE TABLE layoff_stagging
LIKE layoffs

Insert layoff_stagging
SELECT 
    *
FROM
    layoffs

**-- As we don't have any unique ID to differentiate, we will give row number by taking some columns as reference--**

CREATE TABLE layoff_stagging
LIKE layoffs

Select * from layoff_stagging

Insert layoff_stagging
SELECT 
    *
FROM
    layoffs

**-- As we don't have any unique ID to differentiate, we will give row number by taking all columns as reference--**
 With duplicate_cte as (
Select *, 
Row_number() over(partition by company,location,industry,total_laid_off,percentage_laid_off,'date',stage,country,funds_raised_millions) as row_num
from layoff_stagging
)
select * from duplicate_cte
where row_num>1

**-- Before Deleting all duplicate create duplicate table with all Row Number--**

CREATE TABLE `layoff_stagging2` (
  `company` text,
  `location` text,
  `industry` text,
  `total_laid_off` int DEFAULT NULL,
  `percentage_laid_off` text,
  `date` text,
  `stage` text,
  `country` text,
  `funds_raised_millions` int DEFAULT NULL,
  `row_num`int
) ENGINE=InnoDB DEFAULT CHARSET=utf8mb4 COLLATE=utf8mb4_0900_ai_ci;

Insert into layoff_stagging2
Select *, 
Row_number() over(partition by company,location,industry,total_laid_off,percentage_laid_off,'date',stage,country,funds_raised_millions) as row_num
from layoff_stagging

**-- disable safe update mode--**

SET SQL_SAFE_UPDATES = 0;

**-- Delete Duplicate Rows--**

DELETE from layoff_stagging2 
WHERE row_num >1

**-- Standardizing Data --**

Update layoff_stagging2
Set company=Trim(company)

Update layoff_stagging2
set industry='Crypto'
where industry like 'Crypto%'

Select distinct country from layoff_stagging2 where country like 'United States%'

Update layoff_stagging2
set country ='United States'
where country like 'United States%'

Update layoff_stagging2
set country = Trim('United States')

**-- Change data type of date ---**

select `date`, STR_TO_DATE (`date`, '%m/%d/%Y')
from layoff_stagging2

Update layoff_stagging2
set `date` = STR_TO_DATE (`date`, '%m/%d/%Y')

Alter table layoff_stagging2
modify column `date` Date;


**-- Updating Industry Null Value with the other present value --**

update layoff_stagging2
set industry = Null
where industry =''

update layoff_stagging2 t1
join layoff_stagging2 t2 on t1.company=t2.company
SET t1.industry=t2.industry
where t1.industry is Null
and t2.industry is not null


**-- Handling Null Value --**

delete from  layoff_stagging2
where total_laid_off is Null 
and percentage_laid_off is Null 


**-- Remove unwanted Row --**

alter table layoff_stagging2
drop column row_num

