# Australian Beaches Data Analysis
A data cleaning and exploratory analysis project I did on four messy real-world-style datasets about Australian beaches - amenities, climate and beach records split between NSW and the rest of the country. The main goal was to get practice going from "raw, inconsistent data" to "one clean dataset I can actually trust," then use it to answer a real question: does how accessible a beach is actually affect how many people visit it?

Answer: yes, and the stats back it up (ANOVA p < 0.05, correlation = 0.69).

## What's in this repo

| File | Description |
|---|---|
| `beach_analysis.ipynb` | Full code - cleaning, merging, visualisation, hypothesis testing |
| `report.pdf` | Write-up explaining my process and reasoning behind each decision |
| `beach_amenities.csv` | Amenities per beach (BBQ, showers, playground, car spaces, etc.) |
| `climate_summary_by_region.csv` | Regional climate data (rainfall, sea temp, extreme heat days) |
| `data_except_nsw.csv` | Beach records for every state except NSW |
| `data_nsw.csv` | Beach records for NSW (needed a State column added manually) |
| `data_glossary.docx` | Reference doc defining each column, its data type and valid value ranges — this is what I checked everything against during cleaning |

## Datasets & what was wrong with them
Below is some of the issues I ran into:

- **Wrong dtypes** - boolean columns stored as strings (`"Yes"`/`"No"`), rating columns stored as floats instead of ints
- **Inconsistent categories** - `Accessibility` had 9 unique values instead of the expected 4 (`"goood"`, `"G"`, `"Good."` all meant the same thing)
- **Typos** - `Beach_Type` had `"Lagon"` instead of `"Lagoon"`, `"Reeef"` instead of `"Reef"`
- **Invalid entries** - a `"NZ"` value hiding in the `State` column (last I checked, New Zealand isn't an Australian state)
- **Out-of-range values** - annual rainfall as high as 2360mm when the glossary capped it at 2000, visitor counts in the millions above the stated limit
- **Missing data** - `Dogs_Allowed` and `Flags` were both missing 50%+ of values, so I dropped them rather than risk skewing results
- **Duplicates** - a couple of exact duplicate rows, plus a sneakier one where two rows for the same beach had slightly different values (had to Google the real one to figure out which to keep)

## Tools & libraries
- `pandas` - cleaning, type conversion, merging
- `numpy` - encoding accessibility ratings numerically for correlation
- `matplotlib` - all visualisations (pie chart, bar charts, histograms, scatter, box plots)
- `scipy.stats` - ANOVA for hypothesis testing

## Process
1. **Explore each dataset separately** - `.info()`, `.describe()`, `.unique()`, `.isnull().mean()` to catch dtype issues, typos, missing values and outliers before touching anything
2. **Clean each one on its own terms** - fix dtypes, strip whitespace, standardise categories, handle outliers with `.where()`, drop columns that were mostly empty
3. **Merge into one dataset** - `concat()` to stack the NSW/non-NSW records, then `merge()` to bring in amenities (by beach name) and climate data (by region)
4. **Explore the combined data** - pie/bar charts for categorical variables, histograms for numerical ones
5. **Test relationships** - scatter plot + correlation for sea temp vs. rainfall, bar chart + correlation + ANOVA for accessibility vs. visitor numbers

## Key findings
- Most beaches fall into Moderate (47.5%) or Good (39.2%) accessibility - very few are rated Excellent (6.3%)
- Sea temperature and rainfall aren't really related (correlation = -0.23 - weak, and in the opposite direction I expected)
- Accessibility and annual visitors are pretty strongly linked (correlation = 0.69). Ran a one-way ANOVA to confirm this wasn't just noise - p-value came back well under 0.05, so accessibility does seem to genuinely drive visitor numbers
- Bumping up facilities or water quality ratings within the low-accessibility group barely moved the needle on visitor numbers (~16,089 vs. ~15,367 average visitors) - accessibility matters more than amenities, at least in this dataset.

This project is built as part of my Data Analytics coursework.
