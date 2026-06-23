# 🚀 Quick Start Guide

## Global Development & Socio-Economic Risk Segmentation Analysis

Get up and running in **5 steps**!

---

## Step 1: Prepare Your Environment

### Windows
```powershell
# Create virtual environment
python -m venv venv
venv\Scripts\activate

# Install dependencies
pip install -r requirements.txt
```

### macOS/Linux
```bash
# Create virtual environment
python3 -m venv venv
source venv/bin/activate

# Install dependencies
pip install -r requirements.txt
```

---

## Step 2: Add Your Data

1. Place your dataset in: `data/country_data.csv`
2. File should contain columns:
   - country, child_mort, exports, health, imports
   - income, inflation, life_expec, total_fer, gdpp

### Sample CSV Structure
```
country,child_mort,exports,health,imports,income,inflation,life_expec,total_fer,gdpp
Afghanistan,91.5,8.2,4.1,14.8,1800.5,2.5,60.2,5.1,2000.3
Albania,24.3,31.8,5.9,54.3,10800.2,3.2,76.8,1.5,11500.5
...
```

---

## Step 3: Run the Analysis Notebook

```bash
# Navigate to notebooks folder
cd notebooks

# Start Jupyter
jupyter notebook

# Open: Global_Development_Analysis.ipynb
# Execute all cells (Cell → Run All)
```

**What happens**:
- ✅ Data is cleaned and validated
- ✅ Outliers are treated
- ✅ Derived features are created
- ✅ EDA visualizations are generated
- ✅ Countries are segmented
- ✅ Cleaned data is exported to: `data/country_data_cleaned.csv`

**Time**: ~2-3 minutes for 195 countries

---

## Step 4: Load Data to SQL (Optional but Recommended)

### Option A: Using Python Script

```bash
# Navigate to dashboard folder
cd dashboard

# Edit database credentials in load_data_to_sql.py
# Update these lines:
#   'host': 'localhost'
#   'user': 'your_mysql_user'
#   'password': 'your_password'
#   'database': 'development_db'

# Run the script
python load_data_to_sql.py
```

### Option B: Manual SQL Import

```bash
# Connect to MySQL
mysql -u username -p database_name

# Run table creation
source ../sql/01_create_tables.sql

# Import CSV (adjust path as needed)
LOAD DATA LOCAL INFILE '/path/to/country_data_cleaned.csv'
INTO TABLE country_stats
FIELDS TERMINATED BY ','
LINES TERMINATED BY '\n'
IGNORE 1 ROWS;
```

---

## Step 5: Launch the Dashboard

```bash
# From project root or dashboard folder
streamlit run dashboard/app.py
```

**Dashboard will open at**: `http://localhost:8501`

---

## 📊 What You Can Do Now

### 1. Explore Global Overview
- View KPI cards (average income, life expectancy, etc.)
- See scatter plots of income vs life expectancy
- Check segment distribution

### 2. Analyze Health & Risk
- Identify top 15 countries by child mortality
- Compare health spending vs mortality
- Spot inflation risks

### 3. View Segmentation
- See segment-wise statistics
- Compare metrics across segments
- Understand country classifications

### 4. Prioritize Aid
- Rank high-risk countries
- See priority distribution
- Download filtered data
- Review recommendations

### 5. Use SQL Queries
```bash
# Connect to SQL and run queries
mysql -u username -p database_name

# Example: Top 10 high-risk countries
SELECT country, child_mort, income, life_expec 
FROM country_stats 
WHERE segment = 'High Risk Country' 
ORDER BY child_mort DESC 
LIMIT 10;
```

---

## 🔧 Customization Guide

### Change Analysis Parameters

**Edit in notebook** (`Global_Development_Analysis.ipynb`):

```python
# Modify segment thresholds (Section 9)
def classify_segment(row):
    if child_mort > 80 and income < 5000:  # ← Change these values
        return 'High Risk Country'
```

### Modify Dashboard Filters

**Edit in dashboard** (`dashboard/app.py`):

```python
# Change filter ranges
income_range = st.sidebar.slider(
    "Income Range:",
    min_value=0,           # ← Change minimum
    max_value=100000,      # ← Change maximum
    value=(1000, 50000)
)
```

### Add More Visualizations

**Add to any tab** in `dashboard/app.py`:

```python
# Example: Add new scatter plot
fig_new = px.scatter(
    filtered_df,
    x='inflation',
    y='development_index',
    color='segment',
    title='Inflation vs Development Index'
)
st.plotly_chart(fig_new, use_container_width=True)
```

---

## 📈 Key Metrics Reference

| Metric | What It Means | Ideal Range |
|--------|---------------|------------|
| **Child Mortality** | Deaths per 1000 children under 5 | < 25 |
| **Life Expectancy** | Average years lived | > 75 |
| **Income** | Average annual income per capita | > $25,000 |
| **GDP per Capita** | Economic productivity | > $15,000 |
| **Inflation** | Annual price increase | 2-4% |
| **Fertility Rate** | Children per woman | 2-2.5 |
| **Development Index** | Composite score 0-100 | > 70 |

---

## 🛠️ Troubleshooting

### Issue: Jupyter Not Starting
```bash
# Try updating jupyter
pip install --upgrade jupyter

# Or use alternative
jupyter lab
```

### Issue: Streamlit Not Found
```bash
# Reinstall streamlit
pip install --upgrade streamlit

# Or run with python module
python -m streamlit run dashboard/app.py
```

### Issue: "No data in dashboard"
```bash
# Verify file path exists
ls data/country_data_cleaned.csv

# Or run notebook again to regenerate
jupyter notebook notebooks/Global_Development_Analysis.ipynb
```

### Issue: SQL Connection Error
```bash
# Test MySQL connection
mysql -u root -p

# Check database exists
SHOW DATABASES;

# Run table creation again
source sql/01_create_tables.sql
```

### Issue: Missing Column in SQL
```sql
-- Add missing columns
ALTER TABLE country_stats ADD COLUMN development_index FLOAT;
ALTER TABLE country_stats ADD COLUMN health_impact_ratio FLOAT;
```

---

## 📚 File Descriptions

| File | Purpose | When to Use |
|------|---------|-----------|
| `notebooks/Global_Development_Analysis.ipynb` | Data analysis & cleaning | First run, data updates |
| `dashboard/app.py` | Interactive dashboard | Exploration, presentations |
| `sql/01_create_tables.sql` | Database schema | SQL setup |
| `sql/02_analytical_queries.sql` | Business queries | Advanced analysis |
| `docs/PROJECT_REPORT.md` | Detailed findings | Decision making |
| `README.md` | Complete guide | Reference |

---

## 🎯 Common Tasks

### Export Filtered Data
1. Apply filters in dashboard
2. Scroll to bottom of "Aid Priority Analysis" tab
3. Click "📥 Download Filtered Data as CSV"
4. Use in Excel, Python, R, or other tools

### Compare Two Segments
1. Go to "Segmentation Insights" tab
2. Look at segment-wise KPI summary table
3. Scroll to visualizations for detailed comparison

### Find Countries by Criteria
1. Use SQL queries in `sql/02_analytical_queries.sql`
2. Or use dashboard filters
3. Or export data and analyze in Excel/Python

### Generate Report
1. Take screenshots from dashboard tabs
2. Reference findings in `docs/PROJECT_REPORT.md`
3. Compile into presentation slides

---

## 📞 Support

### Check Documentation
1. **For setup**: See `README.md` → Installation & Setup
2. **For findings**: See `docs/PROJECT_REPORT.md`
3. **For queries**: See `sql/02_analytical_queries.sql`
4. **For code**: Check comments in notebook and app.py

### Debug Issues
1. Check terminal output for error messages
2. Verify file paths are correct
3. Ensure all packages are installed: `pip list`
4. Clear cache: `streamlit cache clear`

---

## ✅ Verification Checklist

- [ ] Python environment created and activated
- [ ] All packages installed: `pip install -r requirements.txt`
- [ ] Data file placed in `data/country_data.csv`
- [ ] Notebook executed successfully
- [ ] Cleaned data generated: `data/country_data_cleaned.csv`
- [ ] SQL database created and data loaded
- [ ] Dashboard launches without errors
- [ ] All 4 tabs load properly
- [ ] Filters work correctly
- [ ] Data exports successfully

---

## 🎓 Learning Path

**Day 1**: Data Cleaning
- Run notebook cells 1-5
- Learn data validation techniques
- Understand outlier treatment

**Day 2**: EDA & Analysis
- Run notebook cells 6-8
- Explore correlations
- Understand relationships

**Day 3**: Segmentation
- Run notebook cells 9-10
- Learn classification logic
- See segment patterns

**Day 4**: Dashboard
- Launch dashboard
- Explore all 4 tabs
- Practice filtering

**Day 5**: SQL & Reporting
- Run analytical queries
- Read project report
- Present findings

---

## 🚀 Next Steps

1. ✅ Complete this Quick Start
2. 📊 Explore the dashboard
3. 📈 Review the Project Report
4. 🔍 Run SQL queries for your questions
5. 📋 Export data for presentations
6. 🎓 Customize for your needs

---

**Happy Analyzing!** 🌍📊

For detailed information, see: `README.md` and `docs/PROJECT_REPORT.md`