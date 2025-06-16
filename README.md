# Retail Sales Analysis: Enabling Data Based Decisions

A comprehensive end-to-end retail sales analysis project featuring a robust ETL pipeline, advanced data visualisations, and strategic business insights. This project demonstrates proficiency in Python data engineering, statistical analysis, and interactive dashboard development using industry-standard tools.

![CI logo](https://codeinstitute.s3.amazonaws.com/fullstack/ci_logo_small.png)

## Dataset Overview
This project analyses retail sales data from an anonymous superstore chain in the United States, sourced from Kaggle. The dataset comprises three interconnected CSV files totalling approximately 15MB:

### Dataset Content
- **`sales-data-set.csv`** (421,570 records): Weekly sales data spanning 45 stores from February 2010 to October 2012
- **`stores-data-set.csv`** (45 records): Store metadata including type and size
- **`features-data-set.csv`** (8,190 records): Economic and promotional features including temperature, fuel prices, CPI, unemployment rates, and markdown events

## ETL Pipeline & Data Processing

### Data Transformation Architecture
This project implements a comprehensive 6-stage ETL pipeline that transforms raw CSV files into analysis-ready datasets optimized for different use cases:

### Stage 1: Data Extraction & Initial Loading
- **Source**: Kaggle retail dataset via `kagglehub` API
- **Raw datasets loaded**:
  - `sales_df_raw`: 421,570 weekly sales records (Store × Department × Week granularity)
  - `stores_df_raw`: 45 store master records with type and size classifications
  - `features_df_raw`: 8,190 economic/promotional feature records

### Stage 2: Data Profiling & Quality Assessment  
- **Data dictionary generation** for comprehensive column analysis
- **Missing data analysis**: Identified 50-64% missingness in markdown columns (by design post-Nov 2011)
- **Quality issues discovered**: 7% missing economic indicators (CPI, Unemployment)
- **Bias detection**: Store type representation and temporal coverage analysis

### Stage 3: Data Type Standardization & Preparation
- **Date parsing**: Convert DD/MM/YYYY strings → datetime64[ns] format
- **Create transform datasets**: Preserve original raw data integrity
- **Data validation**: Ensure consistent formats across all datasets

### Stage 4: Schema Integration & Relationship Mapping
- **Star schema identification**: Sales (fact) ← Stores/Features (dimensions)
- **Join key mapping**: 
  - Sales ↔ Stores: `Store` (Many-to-One)
  - Sales ↔ Features: `Store` + `Date` composite key (One-to-One)
- **Referential integrity validation**: Zero orphaned keys confirmed
- **Data range alignment**: Trim features to match sales timeline (end 2012-10-26)
- **Duplicate removal**: Drop redundant `IsHoliday` column from features

### Stage 5: Denormalization & Strategic Dataset Creation
- **LEFT JOIN execution**: Create comprehensive denormalized table (421,570 × 16 columns)
- **Strategic dataset splitting**:
  - **`sales_df`**: Full timeline (Feb 2010 - Oct 2012) for trend analysis
  - **`promo_sales_df`**: Nov 2011+ subset (151,432 records) for promotional analysis

### Stage 6: Data Cleaning & Feature Engineering
- **Missing value treatment**:
  - Markdown columns: Impute NaN → 0.0 (no promotional discount)
  - Economic indicators: Forward-fill by store (gradual macro changes)
- **Missing data flags**: Create binary indicators for strategic markdown analysis
- **Feature engineering**: Generate temporal features:
  - Temporal components (Year, Month, Quarter, Week_of_Year)
  - Seasonal indicators (Season categories, binary flags)
  - Holiday periods (extended holiday effects)
  - Cyclical features (Month_Sin/Cos, Week_Sin/Cos)

### Final Output Datasets
- **`sales_df`**: Complete dataset (421,570 × 78 columns) for general analysis
- **`promo_sales_df`**: Promotional dataset (151,432 × 78 columns) with complete markdown features
- **Zero missing values** achieved through strategic imputation
- **Analysis-ready format** optimized for statistical modeling and visualization

## Analysis Questions & Objectives

### Research Questions
1. **Seasonal and Holiday Sales Trends**: How do sales patterns shift across seasons and major holiday periods?
2. **Store Type and Size Benchmarking**: Which store types and sizes deliver the strongest sales performance?
3. **Economic Drivers of Retail Performance**: How do economic factors (unemployment, CPI, fuel prices) influence sales?
4. **Promotional Markdown Impact**: How effective are markdowns in driving sales growth, especially around key holidays?
5. **Regional and Geographic Patterns**: Are there identifiable regional trends in sales performance?

### Business Objectives
- **Performance Benchmarking**: Establish clear metrics to compare stores, identify high performers.
- **Forecast Seasonal Demand**: Generate insights to anticipate sales fluctuations throughout the year.
- **Optimise Resources**: Find opportunities to improve inventory levels and staffing based on demand.
- **Economic Resilience**: Understand retail performance sensitivity to external economic factors
- **Maximise Promotional Effectiveness**: Quantify the impact of markdowns to refine pricing and promotion strategies.

## Key Findings & Strategic Insights

### 1. Promotional Markdown Impact
**Hypothesis Validated**: Markdown promotional activities serve as primary drivers of sales volume, with strategic absence correlating strongly with reduced performance.

#### Core Findings:
- **Direct Impact**: MarkDown1 shows consistent positive correlation (0.086-0.090) with sales across all store types
- **Statistical Significance**: MarkDown1 coefficient of 0.1992 with p < 0.001, indicating strong promotional effectiveness
- **Escalating Benefits**: Higher markdown levels correspond to progressively higher sales, peaking at $22,600 average weekly sales for "High" markdown periods
- **Strategic Missingness**: Missing markdown data represents deliberate strategic decisions rather than random gaps
  - Stores without MD2-4 markdowns show systematically lower sales (-1,600 to -11,376 average weekly impact)
  - Effect persists within store types, confirming strategic rather than resource-based decisions

### 2. Holiday & Seasonal Performance Patterns
#### Holiday Sales Lift:
- **Holiday Boost**: Holiday weeks generate 9.3% higher average sales ($17,600 vs $16,100)
- **Seasonal Peak**: November holiday periods show largest monthly lift ($22,200 vs $16,000 non-holiday)
- **Markdown Synergy**: Combining holidays with markdowns produces optimal results:
  - Non-holiday + No Markdown: ~$8,600
  - Non-holiday + With Markdown: ~$16,100 (87% increase)
  - Holiday + With Markdown: ~$17,600 (best performance)

#### Strategic Implications:
- Holiday periods represent critical revenue opportunities
- Markdown strategies during holidays amplify sales impact
- Weeks without markdowns, especially during holidays, yield lowest performance

### 3. Store Size & Type Performance Hierarchy
#### Store Size Impact:
- **Strongest Predictor**: Store size shows highest correlation with sales (0.245)
- **Performance Hierarchy**: 
  - Extra Large: $20-30M weekly (highest volume, most seasonal variation)
  - Medium: $10-15M weekly (second highest performance)
  - Large: $6-8M weekly (steady mid-range performance)
  - Small: $4-6M weekly (lowest but most stable)

#### Store Type Strategies:
- **Type A Stores**: Rely heavily on comprehensive markdown strategies (-1,600 to -11,376 impact when missing)
- **Type B Stores**: Show moderate markdown dependency with focused approaches (-844 to -2,066 impact)
- **Type C Stores**: Exhibit unique patterns - positive effect when MD4 missing (+1,100), suggesting resource-constrained optimization

### 4. Economic Factors & External Drivers
#### Limited Economic Impact:
- **Weak Correlations**: Most economic indicators show minimal linear relationship with sales:
  - Temperature: -0.008
  - Fuel Price: -0.014
  - CPI: -0.020
  - Unemployment: -0.032
- **Implication**: Internal promotional strategies (markdowns, store size) drive performance more than external economic conditions

### 5. Temporal Stability & Trends
#### Consistent Performance Patterns:
- **Stable Hierarchy**: Store size performance ranking remains consistent (2010-2012)
- **No Decline Trends**: All store categories show stable performance without significant long-term decline
- **Seasonal Reliability**: Holiday patterns repeat consistently across years

### Strategic Recommendations

#### 1. Promotional Strategy Optimization
- **Prioritize MarkDown1**: 95%+ completion rate makes it the most reliable promotional lever
- **Strategic Layering**: Implement multiple markdown levels (MD2-4) for Type A stores to maximize impact
- **Holiday Focus**: Concentrate marketing spend during November-December for maximum ROI

#### 2. Store Performance Management
- **Size-Based Targets**: Set performance expectations based on store size categories
- **Type-Specific Strategies**: 
  - Type A: Aggressive multi-level markdown campaigns
  - Type B: Focused, targeted promotional approach
  - Type C: Resource-efficient strategies avoiding deep discounting (MD4)

#### 3. Resource Allocation
- **Holiday Inventory**: Increase stock levels for holiday periods, especially for larger stores
- **Promotional Budget**: Allocate marketing spend based on store size and type effectiveness
- **Seasonal Staffing**: Scale workforce to match predictable holiday volume increases

#### 4. Performance Monitoring
- **Leading Indicators**: Monitor markdown implementation rates as early predictor of sales performance
- **Store Benchmarking**: Compare performance within size/type categories rather than absolute terms
- **Economic Independence**: Focus on internal levers (promotions, inventory) over external economic factors

## Unfixed Bugs
* Unfixxed bugs and features can be found at https://github.com/users/Julian-Elliott/projects/3

## Credits 

* In this section, you need to reference where you got your content, media and extra help from. It is common practice to use code from other repositories and tutorials, however, it is important to be very specific about these sources to avoid plagiarism. 
* You can break the credits section up into Content and Media, depending on what you have included in your project. 

- [Kaggle API Dataset Download Guide](https://github.com/Kaggle/kagglehub/blob/main/README.md#download-dataset)
- [Jupyter Notebook Data Analysis Template](https://raw.githubusercontent.com/Code-Institute-Org/data-analytics-template/refs/heads/main/jupyter_notebooks/Notebook_Template.ipynb)
- [README.md Data Analysis Template](https://raw.githubusercontent.com/Code-Institute-Solutions/da-README-template/refs/heads/main/README.md)
- [shutil High-level file operations](https://docs.python.org/3/library/shutil.html)
- [Kaggle Data Source](https://www.kaggle.com/datasets/manjeetsingh/retaildataset/data)
