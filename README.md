# Summary

## Project Overview

The SQL Data Cleaning project for the NashvilleHousing dataset involved a comprehensive cleaning process to enhance data quality and prepare it for analysis. The dataset, comprising various columns such as UniqueID, ParcelID, SaleDate, SalePrice, and more, required several steps to standardize, enrich, and refine its content.

## Cleaning Steps

1. **Standardize Date Format:**
   - Converted the SaleDate column to a standardized date format.

2. **Populate Missing Property Address Data:**
   - Identified and filled missing PropertyAddress values through a self-join operation.

3. **Break Down Address into Individual Columns:**
   - Split PropertyAddress into Address and City columns, creating new fields for improved data organization.

4. **Parse Owner Address into Individual Columns:**
   - Utilized PARSENAME and REPLACE functions to break down OwnerAddress into Address, City, and State columns.

5. **Change Y and N to Yes and No in "Sold as Vacant" Field:**
   - Transformed the SoldAsVacant column values from 'Y' and 'N' to 'Yes' and 'No' using a CASE statement.

6. **Remove Duplicates:**
   - Implemented a Common Table Expression (CTE) with ROW_NUMBER() to identify and eliminate duplicate records based on specified criteria.

7. **Delete Unused Columns:**
   - Removed unused columns, including OwnerAddress, TaxDistrict, PropertyAddress, and SaleDate, to streamline the dataset.

--

## Detailed Cleaning Steps

### Standardize Date Format

```sql
-- Standardize Date Format
select SaleDateConverted, CONVERT(Date, SaleDate)
FROM PortfolioProject.dbo.NashvilleHousing
```

### Populate Property Address Data

```sql
-- Populate Property Address data where missing
-- Initial inspection of the NashvilleHousing dataset
-- SELECT statement retrieves all records to identify missing PropertyAddress entries

SELECT *
FROM PortfolioProject.dbo.NashvilleHousing
-- WHERE PropertyAddress IS NULL
ORDER BY ParcelID

-- Using a self-join to populate missing PropertyAddress values
-- Selects a.ParcelID, a.PropertyAddress, b.ParcelID, b.PropertyAddress, and utilizes ISNULL to replace null values
-- Only considers records where a.PropertyAddress is null

SELECT a.ParcelID, a.PropertyAddress, b.ParcelID, b.PropertyAddress, ISNULL(a.PropertyAddress, b.PropertyAddress)
FROM PortfolioProject.dbo.NashvilleHousing a
JOIN PortfolioProject.dbo.NashvilleHousing b
    ON a.ParcelID = b.ParcelID
    AND a.[UniqueID ] <> b.[UniqueID]
WHERE a.PropertyAddress IS NULL

-- Updating records with missing PropertyAddress based on the results of the previous query
-- SETs PropertyAddress to the non-null value from the self-join

UPDATE a
SET PropertyAddress = ISNULL(a.PropertyAddress, b.PropertyAddress)
FROM PortfolioProject.dbo.NashvilleHousing a
JOIN PortfolioProject.dbo.NashvilleHousing b
    ON a.ParcelID = b.ParcelID
    AND a.[UniqueID ] <> b.[UniqueID]
WHERE a.PropertyAddress IS NULL;
```

### Breaking out Address into Individual Columns (Address, City, State)

```sql
-- Breaking out Address into Individual Columns (Address, City, State)
-- Initial inspection of PropertyAddress for breaking it down

SELECT PropertyAddress
FROM PortfolioProject.dbo.NashvilleHousing
-- WHERE PropertyAddress IS NULL
-- ORDER BY ParcelID

-- Using SUBSTRING and CHARINDEX to split PropertyAddress into Address and City
-- Creates new columns PropertySplitAddress and PropertySplitCity

SELECT 
    SUBSTRING(PropertyAddress, 1, CHARINDEX(',', PropertyAddress) - 1) as Address,
    SUBSTRING(PropertyAddress, CHARINDEX(',', PropertyAddress) + 1, LEN(PropertyAddress)) as City
FROM PortfolioProject.dbo.NashvilleHousing

-- Adding new columns PropertySplitAddress and PropertySplitCity to the NashvilleHousing table

ALTER TABLE NashvilleHousing
ADD PropertySplitAddress Nvarchar(255);

ALTER TABLE NashvilleHousing
ADD PropertySplitCity Nvarchar(255);

-- Updating the newly added columns with split values

UPDATE NashvilleHousing
SET PropertySplitAddress = SUBSTRING(PropertyAddress, 1, CHARINDEX(',', PropertyAddress) - 1),
    PropertySplitCity = SUBSTRING(PropertyAddress, CHARINDEX(',', PropertyAddress) + 1, LEN(PropertyAddress));

-- Retrieving the updated NashvilleHousing table with the new columns

SELECT *
FROM PortfolioProject.dbo.NashvilleHousing
```

### Parsing Owner Address into Individual Columns (Address, City, State)

```sql
-- Parsing Owner Address into Individual Columns (Address, City, State)
-- Using PARSENAME and REPLACE to split OwnerAddress

SELECT
    PARSENAME(REPLACE(OwnerAddress, ',', '.'), 3) as State,
    PARSENAME(REPLACE(OwnerAddress, ',', '.'), 2) as City,
    PARSENAME(REPLACE(OwnerAddress, ',', '.'), 1) as Address
FROM PortfolioProject.dbo.NashVilleHousing

-- Adding new columns OwnerSplitAddress, OwnerSplitCity, and OwnerSplitState to the NashvilleHousing table

ALTER TABLE NashvilleHousing
ADD OwnerSplitAddress Nvarchar(255);

ALTER TABLE NashvilleHousing
ADD OwnerSplitCity Nvarchar(255);

ALTER TABLE NashvilleHousing
ADD OwnerSplitState Nvarchar(255);

-- Updating the newly added columns with parsed values

UPDATE NashvilleHousing
SET OwnerSplitAddress = PARSENAME(REPLACE(OwnerAddress, ',', '.'), 3),
    OwnerSplitCity = PARSENAME(REPLACE(OwnerAddress, ',', '.'), 2),
    OwnerSplitState = PARSENAME(REPLACE(OwnerAddress, ',', '.'), 1)

```

### Change Y and N to Yes and No in "Sold as Vacant" Field

```sql
-- Change Y and N to Yes and No in "Sold as Vacant" field
-- Identifying distinct values and their counts in the SoldAsVacant column

SELECT DISTINCT SoldAsVacant, COUNT(SoldAsVacant)
FROM [PortfolioProject].dbo.NashvilleHousing
GROUP BY SoldAsVacant
ORDER BY 2

-- Updating the SoldAsVacant column with 'Yes' for 'Y' and 'No' for 'N'
-- Using a CASE statement to handle the transformation

UPDATE NashvilleHousing
SET SoldAsVacant = 
    CASE 
        WHEN SoldAsVacant = 'Y' THEN 'Yes'
        WHEN SoldAsVacant = 'N' THEN 'No'
        ELSE SoldAsVacant
    END
```

### Remove Duplicates

```sql
-- Remove Duplicates
-- Remove Duplicates
-- Using a Common Table Expression (CTE) with ROW_NUMBER() to identify and number duplicate records

WITH RowNumCTE AS (
    SELECT *,
        ROW_NUMBER() OVER (
            PARTITION BY ParcelID,
                         PropertyAddress,
                         SalePrice,
                         SaleDate,
                         LegalReference
            ORDER BY 
                UNIQUEID
        ) AS row_num
    FROM [PortfolioProject].dbo.NashvilleHousing
    -- ORDER BY ParcelID
)

-- Selecting records from the CTE where row number is greater than 1, indicating duplicates
-- ORDER BY can be used here based on the criteria for better visibility

SELECT *
FROM RowNumCTE
WHERE row_num > 1
-- ORDER BY PropertyAddress
```

### Delete Unused Columns

```sql
-- Delete Unused Columns
-- Viewing the current state of the NashvilleHousing table

SELECT * 
FROM [PortfolioProject].dbo.NashvilleHousing

-- Dropping unused columns: OwnerAddress, TaxDistrict, PropertyAddress, and SaleDate
-- This action may impact data integrity, ensure it aligns with your requirements

ALTER TABLE [PortfolioProject].dbo.NashvilleHousing
DROP COLUMN OwnerAddress, TaxDistrict, PropertyAddress

ALTER TABLE [PortfolioProject].dbo.NashvilleHousing
DROP COLUMN SaleDate

```

## Usage

Execute the SQL queries in the provided order to clean and standardize the NashvilleHousing dataset.

## Repository Structure

- `NashvilleHousing.sql`: SQL script containing the data cleaning steps.
