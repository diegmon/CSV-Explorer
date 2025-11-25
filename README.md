# CSV Explorer V0.11.0 Documentation

## Overview
CSV Explorer is an interactive and lightweight command-line tool for exploring and analyzing CSV datasets. It enables quick filtering, cross-tabulation, value exploration, and drill-down analysis without loading entire datasets into memory. The tool uses lazy column loading and caching for efficiency with large files.

**Key Features:**
- Column value exploration with counts/percentages
- Multi-column filtering
- Cross-tabulation with row/column percentages
- Cell-level drill-down from cross-tabs
- Transpose views for better readability
- Indexed navigation for large tables
- Filter stack management

## Requirements
- Python 3.7+
- pandas (`pip install pandas`)

## Usage Guide

### 1. Launching the Tool
Run the script directly (active virtual environment recommended):
```
python3 csv_explorer.py
```

### 2. File Selection
On startup, the tool scans the current directory for CSV files:
```
CSV files found:
1: sales_data.csv (1.2 MB)
2: customer_records.csv (450.3 KB)

Options:
  • <number> select file (1-2)
  • 'q' to quit

Your choice: 
```
- Enter file number to load dataset
- Press 'q' to exit

### 3. Main Session View
After loading a file, you'll see dataset status:
```
============================================================
Current filters:
  1. Region = West

File: sales_data.csv
Data: 1,250/10,000 rows (12.5%) | 12 cols
```
**Available Commands:**
- 'c' - List/select columns
- 'f' - Add new filter
- 'r' - Reset all filters (appears when filters exist)
- 'b' - Return to file selection
- <number> - Explore column by index (e.g., '3')

### 4. Column Exploration
When exploring a column (e.g., 'Product Category'):
```
EXPLORING: 'Product Category'
============================================================
  # | VALUE          | COUNT |     %
---------------------------------------------
  1 | Electronics    |  850  |  68.0%
  2 | Furniture      |  300  |  24.0%
  3 | Office Supplies|  100  |   8.0%
---------------------------------------------
Total              | 1250  | 100.0%
---------------------------------------------
```
**Commands:**
- `<number(s)>` - Filter by value(s) (e.g., `1,3` keeps Electronics and Office Supplies)
- 'x' - Cross-tabulate with another column
- 'd' - Drill-down to another column
- 'c' - Change column to explore
- 'b' - Back to main view

### 5. Cross-Tabulation
Select two columns to cross-analyze (e.g., 'Region' vs 'Product Category'):
```
Cross-Tab (Count): Region vs Product Category
============================================================
     | [1]    | [2]    | [3]       | Total
---------------------------------------------
[#]  | VALUE  |Electro.|Furniture  |Office Su.
---------------------------------------------
[1]  | East   |  200   |   150     |    50    | 400
[2]  | West   |  600   |   100     |    50    | 750
---------------------------------------------
Total        |  800   |   250     |   100    | 1150
---------------------------------------------
```
**Commands:**
- `<row>,<col>` - Drill-down on specific cell (e.g., `2,1` for West/Electronics)
- 'd' - Prompt for cell coordinates
- '%' - Toggle between counts and percentages
- 't' - Transpose rows/columns
- 'b' - Return to column exploration

### 6. Drill-Down Analysis
After selecting a cell in cross-tab view:
```
--- Drill-Down View ---
============================================================
Current filters:
  1. Region = West
  2. Product Category = Electronics

File: sales_data.csv
Data: 600/10,000 rows (6.0%) | 12 cols
```
Explore filtered data using same commands as main view. Press 'b' to return to cross-tab.

### 7. Global Filtering
Use 'f' in main view to add filters:
1. Select column to filter by
2. Choose values to keep (multiple selection supported)
3. Filters persist across views until reset with 'r'

## Technical Notes

### Architecture
- **Model Layer (`Dataset` class)**:
  - Lazy-loads columns only when needed
  - Maintains filter stack and cached columns
  - Applies filters incrementally
- **View Layer (`TableRenderer` class)**:
  - ASCII table rendering with automatic truncation
  - Context-aware formatting (percentages, totals)
  - Indexed navigation for large tables
- **Controller Layer (`ExplorerSession`)**:
  - Manages UI state transitions
  - Handles command routing
  - Coordinates drill-down workflows

### Performance Optimizations
1. **Column Caching**: Frequently accessed columns remain in memory
2. **Index Preservation**: Uses pandas index alignment to avoid full reloads
3. **Partial Loading**: Reads only required columns using `usecols`
4. **Stream Processing**: Counts rows without loading full dataset

### Workflow Examples
**Analyze West Region Sales:**
1. Load `sales_data.csv`
2. Press 'f' → Select 'Region' column → Choose 'West'
3. Press '3' to explore 'Product Category'
4. Press 'x' → Select 'Sales Amount' for cross-tab

**Find Top Customers:**
1. Cross-tab 'Customer ID' vs 'Order Count'
2. Press '%' to see percentages
3. Press '2,1' to drill-down on top customer

## License
GNU General Public License v3.0 - Free to use, modify, and distribute. Improvements must remain open source.

> **Note**: Press `Ctrl+C` anytime to exit the application safely. Works best with datasets under 1GB. For extremely large files, pre-filter using command-line tools like `awk` or `csvkit`.
