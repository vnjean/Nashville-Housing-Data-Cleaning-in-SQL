# SQL Data Cleaning - NashvilleHousing

This project focuses on cleaning and standardizing the NashvilleHousing dataset using SQL. The dataset includes columns such as UniqueID, ParcelID, LandUse, PropertyAddress, SaleDate, SalePrice, LegalReference, SoldAsVacant, OwnerName, OwnerAddress, Acreage, TaxDistrict, LandValue, BuildingValue, TotalValue, YearBuilt, Bedrooms, FullBath, and HalfBath.

### Cleaning Steps:

1. **Standardize Date Format:**
   - Created a new column, `SaleDateConverted`, and standardized the date format using SQL queries.

2. **Populate Property Address Data:**
   - Filled missing property addresses by merging data from duplicate ParcelIDs.

3. **Breaking out Address into Individual Columns (Address, City, State):**
   - Split the `PropertyAddress` column into separate columns for address, city, and state.

4. **Parsing Owner Address into Individual Columns (Address, City, State):**
   - Parsed the `OwnerAddress` column into separate columns for owner address, city, and state.

5. **Change Y and N to Yes and No in "Sold as Vacant" Field:**
   - Updated the 'SoldAsVacant' field to display 'Yes' for 'Y' and 'No' for 'N'.

6. **Remove Duplicates:**
   - Removed duplicate records based on specific criteria using ROW_NUMBER().

7. **Delete Unused Columns:**
   - Removed unnecessary columns such as 'OwnerAddress', 'TaxDistrict', and 'PropertyAddress'.

8. **Delete Unused Column:**
   - Removed the 'SaleDate' column.

### Usage:

- Execute the SQL queries in the provided order to clean and standardize the NashvilleHousing dataset.

### Repository Structure:

- `NashvilleHousing.sql`: SQL script containing the data cleaning steps.




Certainly, here's a refined version structured more closely to your original format:

# SQL Data Cleaning - NashvilleHousing

## Introduction

In this project, we focused on cleaning the NashvilleHousing dataset, which is available on GitHub and has been uploaded to this repository. The dataset encompasses various columns, including UniqueID, ParcelID, LandUse, PropertyAddress, SaleDate, SalePrice, LegalReference, SoldAsVacant, OwnerName, OwnerAddress, Acreage, TaxDistrict, LandValue, BuildingValue, TotalValue, YearBuilt, Bedrooms, FullBath, and HalfBath.

## Cleaning Steps

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
