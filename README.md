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
-- Populate Property Address data
-- [Your SQL Code Here]
```

### Breaking out Address into Individual Columns (Address, City, State)

```sql
-- Breaking out Address into Individual Columns (Address, City, State)
-- [Your SQL Code Here]
```

### Parsing Owner Address into Individual Columns (Address, City, State)

```sql
-- Parsing Owner Address into Individual Columns (Address, City, State)
-- [Your SQL Code Here]
```

### Change Y and N to Yes and No in "Sold as Vacant" Field

```sql
-- Change Y and N to Yes and No in "Sold as Vacant" field
-- [Your SQL Code Here]
```

### Remove Duplicates

```sql
-- Remove Duplicates
-- [Your SQL Code Here]
```

### Delete Unused Columns

```sql
-- Delete Unused Columns
-- [Your SQL Code Here]
```

### Delete Unused Column

```sql
-- Delete Unused Column
-- [Your SQL Code Here]
```

## Usage

Execute the SQL queries in the provided order to clean and standardize the NashvilleHousing dataset.

## Repository Structure

- `NashvilleHousing.sql`: SQL script containing the data cleaning steps.

Feel free to customize this README to match your style and provide any additional details about the project.
