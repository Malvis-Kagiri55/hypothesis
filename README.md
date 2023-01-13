{
 "cells": [
  {
   "cell_type": "markdown",
   "metadata": {},
   "source": [
    "# Hypothesis Testing - Cumulative Lab\n",
    "\n",
    "## Introduction\n",
    "\n",
    "In this cumulative lab, you will use pandas to clean up a dataset and perform some EDA, then perform statistical tests and interpret their results in order to answer some business questions.\n",
    "\n",
    "## Objectives\n",
    "\n",
    "You will be able to:\n",
    "\n",
    "* Practice using a data dictionary\n",
    "* Practice using pandas to prepare data for statistical analysis\n",
    "* Practice identifying an appropriate statistical test for a given question\n",
    "* Practice defining the null and alternative hypotheses\n",
    "* Practice executing statistical tests and interpreting their results"
   ]
  },
  {
   "cell_type": "markdown",
   "metadata": {},
   "source": [
    "## Your Task: Analyzing Health Trends\n",
    "\n",
    "![tissues and tea mug](images/sick_day.jpg)\n",
    "\n",
    "Photo by <a href=\"https://unsplash.com/@kellysikkema?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText\">Kelly Sikkema</a> on <a href=\"/?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText\">Unsplash</a>"
   ]
  },
  {
   "cell_type": "markdown",
   "metadata": {},
   "source": [
    "### Business Understanding\n",
    "\n",
    "Flatiron Health Insurance (FHI) is a growing private healthcare insurance provider founded on the premise that using data and analytics can improve the health insurance industry by providing better care and offerings to its patients. Every year, the Center for Disease Control (CDC) conducts surveys to understand the latest demographic, health, and fitness trends. You have been tasked with analyzing the recently published results of the 2017-2018 survey and providing your recommendations back to the Chief Analytics Officer and Chief Marketing Officer. You have been assigned the task of taking a first look at the data and beginning to answer several key questions:\n",
    "\t\t\t\t\t\t\t\t\t\t\t \t\t\t\t\t\t\t\t\n",
    "1. How does health status, represented by average number of days with bad physical health in the past month (`PHYSHLTH`), differ by state?\t\t\t\t\t \t\t\t\t\t\t\t\t\n",
    "2. Digging deeper into the data, what are some factors that impact health (demographics, behaviors, etc.)?\t\n",
    "\n",
    "### Data Understanding\n",
    "\n",
    "To get you started, the IT department formatted the data set into a tab delimited text file for only NY, NJ, and CT (FHI’s primary markets) called `case_study.csv`.\n",
    "\n",
    "There is also a PDF data dictionary called `data_dictionary.pdf`, which explains the meanings of the features and codes contained in this dataset.\n",
    "\n",
    "Both files are located in the `data/` directory of this repository.\n",
    "\n",
    "Prior to each statistical test, you will need to perform some data preparation, which could include:\n",
    "\n",
    "* Filtering out rows with irrelevant values\n",
    "* Transforming data from codes into human-readable values\n",
    "* Binning data to transform it from numeric to categorical\n",
    "* Creating new columns based on queries of the values in other columns\n",
    "\n",
    "For steps 2-5, you will need to select and execute an appropriate statistical test. Recall these tests we have learned so far:\n",
    "\n",
    "1. [Chi-squared test](https://docs.scipy.org/doc/scipy/reference/generated/scipy.stats.chi2_contingency.html): used for comparing a categorical feature against a categorical feature, to determine whether they are independent\n",
    "2. [t-test](https://docs.scipy.org/doc/scipy/reference/generated/scipy.stats.ttest_ind.html): used for comparing two categories of a numeric feature, to determine whether their means are the same across categories\n",
    "3. [ANOVA](https://www.statsmodels.org/stable/generated/statsmodels.stats.anova.anova_lm.html#statsmodels.stats.anova.anova_lm): used for comparing more than two categories of a numeric feature, to determine whether their means are the same across categories"
   ]
  },
  {
   "cell_type": "markdown",
   "metadata": {},
   "source": [
    "### Requirements\n",
    "\n",
    "#### 1. Prepare `PHYSHLTH` Data for Analysis\n",
    "\n",
    "Using the data dictionary, ensure that you understand the meaning of the `PHYSHLTH` column. Then clean the data so that only valid records of `PHYSHLTH` remain.\n",
    "\n",
    "#### 2. Describe the Distribution of Health Status by State\n",
    "\n",
    "Does health status (`PHYSHLTH`) differ by state (`STATE_`)? If so, by how much, and is it statistically significant?\n",
    "\n",
    "#### 3. Describe the Relationship between Health Status and Home Ownership Status\n",
    "\n",
    "Does health status (`PHYSHLTH`) differ between home owners and renters (`RENTHOM1`)? If so, by how much, and is it statistically significant?\n",
    "\n",
    "#### 4. Describe the Relationship between Chronic Sickness and Nicotine Use\n",
    "\n",
    "Does chronic sickness (`PHYSHLTH >= 15`) differ based on nicotine use (various columns)? If so, by how much, and is it statistically significant?\n",
    "\n",
    "#### 5. Choose Your Own Question\n",
    "\n",
    "Thinking about the business case, what is another question that might be useful to answer? Perform all analysis steps to answer this question."
   ]
  },
  {
   "cell_type": "markdown",
   "metadata": {},
   "source": [
    "## 1. Prepare `PHYSHLTH` Data for Analysis\n",
    "\n",
    "In the cells below, we include the relevant imports and load the data into a dataframe called `df`:"
   ]
  },
  {
   "cell_type": "code",
   "execution_count": null,
   "metadata": {},
   "outputs": [],
   "source": [
    "# Run this cell without changes\n",
    "import pandas as pd\n",
    "import matplotlib.pyplot as plt\n",
    "import numpy as np\n",
    "%matplotlib inline"
   ]
  },
  {
   "cell_type": "code",
   "execution_count": null,
   "metadata": {},
   "outputs": [],
   "source": [
    "# Run this cell without changes\n",
    "df = pd.read_csv(\"data/case_study.csv\", index_col=0, low_memory=False)\n",
    "df"
   ]
  },
  {
   "cell_type": "markdown",
   "metadata": {},
   "source": [
    "Our main column of interest is called `PHYSHLTH`. We display summary statistics and plot a distribution below:"
   ]
  },
  {
   "cell_type": "code",
   "execution_count": null,
   "metadata": {},
   "outputs": [],
   "source": [
    "# Run this cell without changes\n",
    "df['PHYSHLTH'].describe()"
   ]
  },
  {
   "cell_type": "code",
   "execution_count": null,
   "metadata": {},
   "outputs": [],
   "source": [
    "# Run this cell without changes\n",
    "fig, ax = plt.subplots()\n",
    "ax.hist(df[\"PHYSHLTH\"], bins=\"auto\")\n",
    "ax.set_xlabel(\"PHYSHLTH\")\n",
    "ax.set_ylabel(\"Count\")\n",
    "ax.set_title(\"Distribution of PHYSHLTH in Raw Data\");"
   ]
  },
  {
   "cell_type": "markdown",
   "metadata": {},
   "source": [
    "This feature is supposed to represent the number of days with bad physical health out of the past 30 days. Do you see anything wrong with what is displayed above? Explain."
   ]
  },
  {
   "cell_type": "code",
   "execution_count": null,
   "metadata": {},
   "outputs": [],
   "source": [
    "\"\"\"\n",
    "If these values represent a number of days out of the past 30 days,\n",
    "we would expect the lowest number to be 0 and the highest number to\n",
    "be 30. Instead we have many numbers in the 70s-90s range, so much so\n",
    "that our mean is an impossible number, higher than 30\n",
    "\"\"\""
   ]
  },
  {
   "cell_type": "markdown",
   "metadata": {},
   "source": [
    "Look in the data dictionary, page 17, to understand what is happening with these values. Then edit the cell below so:\n",
    "\n",
    "* The records where the `PHYSHLTH` value label is `None` are converted to 0\n",
    "* The records where the `PHYSHLTH` value label is `Number of days` are kept as-is\n",
    "* All other records are dropped (i.e. records with `Don't know/Not sure`, `Refused`, and `Not asked or Missing` value labels for `PHYSHLTH` are dropped)"
   ]
  },
  {
   "cell_type": "code",
   "execution_count": null,
   "metadata": {},
   "outputs": [],
   "source": [
    "\n",
    "df.loc[df[\"PHYSHLTH\"]==88, \"PHYSHLTH\"] = 0\n",
    "\n",
    "\n",
    "df = df[df[\"PHYSHLTH\"] <= 30].copy()\n",
    "\n",
    "df"
   ]
  },
  {
   "cell_type": "markdown",
   "metadata": {},
   "source": [
    "Run the code below to ensure you have the correct, cleaned dataframe:"
   ]
  },
  {
   "cell_type": "code",
   "execution_count": null,
   "metadata": {},
   "outputs": [],
   "source": [
    "# Run this cell without changes\n",
    "\n",
    "# We should have fewer rows, the same number of columns\n",
    "assert df.shape == (33747, 358)\n",
    "\n",
    "# The maximum value in this column should now be 30\n",
    "assert df[\"PHYSHLTH\"].max() == 30.0"
   ]
  },
  {
   "cell_type": "markdown",
   "metadata": {},
   "source": [
    "Now we can look at the same descriptive information on our cleaned data:"
   ]
  },
  {
   "cell_type": "code",
   "execution_count": null,
   "metadata": {},
   "outputs": [],
   "source": [
    "# Run this cell without changes\n",
    "df['PHYSHLTH'].describe()"
   ]
  },
  {
   "cell_type": "code",
   "execution_count": null,
   "metadata": {},
   "outputs": [],
   "source": [
    "# Run this cell without changes\n",
    "fig, ax = plt.subplots()\n",
    "ax.hist(df[\"PHYSHLTH\"])\n",
    "ax.set_xlabel(\"PHYSHLTH\")\n",
    "ax.set_ylabel(\"Count\")\n",
    "ax.set_title(\"Distribution of PHYSHLTH in Cleaned Data\");"
   ]
  },
  {
   "cell_type": "markdown",
   "metadata": {},
   "source": [
    "That looks a lot more reasonable. Let's move on to the next step."
   ]
  },
  {
   "cell_type": "markdown",
   "metadata": {},
   "source": [
    "## 2. Describe the Distribution of Health Status by State\n",
    "\n",
    "As mentioned previously, this dataset only includes data from three states."
   ]
  },
  {
   "cell_type": "code",
   "execution_count": null,
   "metadata": {},
   "outputs": [],
   "source": [
    "# Run this cell without changes\n",
    "df[\"_STATE\"].value_counts()"
   ]
  },
  {
   "cell_type": "markdown",
   "metadata": {},
   "source": [
    "Look in the data dictionary, pages 2-3, to determine which states map onto which codes. Then replace the numbers with strings representing the state names."
   ]
  },
  {
   "cell_type": "code",
   "execution_count": null,
   "metadata": {},
   "outputs": [],
   "source": [
    "# __SOLUTION__\n",
    "df[\"_STATE\"].replace({36: \"New York\", 34: \"New Jersey\", 9: \"Connecticut\"}, inplace=True)\n",
    "df"
   ]
  },
  {
   "cell_type": "markdown",
   "metadata": {},
   "source": [
    "Below, we check the values:"
   ]
  },
  {
   "cell_type": "code",
   "execution_count": null,
   "metadata": {},
   "outputs": [],
   "source": [
    "# Run this cell without changes\n",
    "\n",
    "# Shape should be the same\n",
    "assert df.shape == (33747, 358)\n",
    "\n",
    "# Values in state column should be changed\n",
    "assert sorted(list(df[\"_STATE\"].value_counts().index)) == ['Connecticut', 'New Jersey', 'New York']"
   ]
  },
  {
   "cell_type": "code",
   "execution_count": null,
   "metadata": {},
   "outputs": [],
   "source": [
    "# Run this cell without changes\n",
    "\n",
    "ny = df.loc[df[\"_STATE\"] == \"New York\", \"PHYSHLTH\"]\n",
    "nj = df.loc[df[\"_STATE\"] == \"New Jersey\", \"PHYSHLTH\"]\n",
    "ct = df.loc[df[\"_STATE\"] == \"Connecticut\", \"PHYSHLTH\"]\n",
    "\n",
    "fig, ax = plt.subplots(figsize=(15, 6))\n",
    "\n",
    "ax.hist(\n",
    "    x=[ny, nj, ct],\n",
    "    label=[\"New York\", \"New Jersey\", \"Connecticut\"],\n",
    "    bins=range(32),\n",
    "    align=\"left\"\n",
    ")\n",
    "\n",
    "ax.set_xlabel(\"PHYSHLTH\")\n",
    "ax.set_ylabel(\"Count\")\n",
    "ax.set_title(\"Distribution of PHYSHLTH by State\")\n",
    "\n",
    "ax.legend(title=\"State\");"
   ]
  },
  {
   "cell_type": "markdown",
   "metadata": {},
   "source": [
    "Looking at the plot above, does the distribution seem to differ by state?\n",
    "\n",
    "(Just answer based on a visual inspection; we will do the statistical assessment next.)"
   ]
  },
  {
   "cell_type": "code",
   "execution_count": null,
   "metadata": {},
   "outputs": [],
   "source": [
    "\n",
    "\"\"\"\n",
    "Not really, the states all seem to have approximately the same\n",
    "distribution of unhealthy days\n",
    "\"\"\""
   ]
  },
  {
   "cell_type": "markdown",
   "metadata": {},
   "source": [
    "For the statistical test, we will be comparing the *means* of `PHYSHLTH` across states, as a representation of the overall distribution. In other words, when operationalizing the question **does `PHYSHLTH` differ by state?** we want to answer that in terms of the mean `PHYSHLTH`.\n",
    "\n",
    "Let's look at those means:"
   ]
  },
  {
   "cell_type": "code",
   "execution_count": null,
   "metadata": {},
   "outputs": [],
   "source": [
    "# Run this cell without changes\n",
    "df.groupby(\"_STATE\")[\"PHYSHLTH\"].mean()"
   ]
  },
  {
   "cell_type": "markdown",
   "metadata": {},
   "source": [
    "You likely noted that the overall distribution looked about the same, but these means are different. We have a range from Connecticut with about 3.7 days of bad health to New Jersey with about 4.4 days. But is that difference statistically significant?"
   ]
  },
  {
   "cell_type": "markdown",
   "metadata": {},
   "source": [
    "Identify which of the statistical tests you have learned is the most appropriate for this question, and why. Make sure you mention what kinds of variables are being compared (numeric and categorical), and how many categories there are."
   ]
  },
  {
   "cell_type": "code",
   "execution_count": null,
   "metadata": {},
   "outputs": [],
   "source": [
    "\"\"\"\n",
    "ANOVA is the most appropriate technique since we are trying to determine\n",
    "whether there is a difference in PHYSHLTH (a numeric feature) across 3\n",
    "categories. If we only had 2 categories, a t-test would be appropriate,\n",
    "but we want to use ANOVA because there are 3\n",
    "\"\"\""
   ]
  },
  {
   "cell_type": "markdown",
   "metadata": {},
   "source": [
    "Now, identify the null and alternative hypotheses:"
   ]
  },
  {
   "cell_type": "code",
   "execution_count": null,
   "metadata": {},
   "outputs": [],
   "source": [
    "\"\"\"\n",
    "Null hypothesis: the means of PHYSHLTH are the same across states\n",
    "\n",
    "Alternative hypothesis: the means of PHYSHLTH are not the same across states\n",
    "\"\"\""
   ]
  },
  {
   "cell_type": "markdown",
   "metadata": {},
   "source": [
    "In the cell below, we set up and execute the statistical test for you. If this doesn't match your previous answer about which test to perform, look at the solution branch to understand why this is the appropriate test."
   ]
  },
  {
   "cell_type": "code",
   "execution_count": null,
   "metadata": {},
   "outputs": [],
   "source": [
    "# Run this cell without changes\n",
    "import statsmodels.api as sm\n",
    "from statsmodels.formula.api import ols\n",
    "\n",
    "formula = 'PHYSHLTH ~ C(_STATE)'\n",
    "lm = ols(formula, df).fit()\n",
    "sm.stats.anova_lm(lm)"
   ]
  },
  {
   "cell_type": "markdown",
   "metadata": {},
   "source": [
    "Interpret the results of this statistical test below. What is the calculated p-value? Were we able to reject the null hypothesis at an alpha of 0.05? What does this say about how `PHYSHLTH` varies by state? What recommendations would you make to the business?"
   ]
  },
  {
   "cell_type": "code",
   "execution_count": null,
   "metadata": {},
   "outputs": [],
   "source": [
    "\"\"\"\n",
    "Yes, we were able to reject the null hypothesis. The p-value,\n",
    "identified here as PR(>F) for the _STATE category, is much\n",
    "smaller than 0.05.\n",
    "\n",
    "This means that even though the overall distribution seemed\n",
    "similar across states, and the means are all around 3-4 days\n",
    "per month, with this sample size we can say that there is a \n",
    "statistically significant difference between them.\n",
    "\n",
    "Based on this information, we might recommend to the business\n",
    "that they investigate further why people in Connecticut have\n",
    "the best health and why people in New Jersey have the worst\n",
    "health out of these states.\n",
    "\"\"\""
   ]
  },
  {
   "cell_type": "markdown",
   "metadata": {},
   "source": [
    "With that section wrapped up, let's move on to the next step."
   ]
  },
  {
   "cell_type": "markdown",
   "metadata": {},
   "source": [
    "## 3. Describe the Relationship between Health Status and Home Ownership Status\n",
    "\n",
    "This time, we want to categorize respondents by demographic information: specifically, we'll look at whether or not they own their home.\n",
    "\n",
    "Once again, this will require some data preparation. The variable of interest is contained in the `RENTHOM1` column. Currently the values look like this:"
   ]
  },
  {
   "cell_type": "code",
   "execution_count": null,
   "metadata": {},
   "outputs": [],
   "source": [
    "# Run this cell without changes\n",
    "df[\"RENTHOM1\"].value_counts()"
   ]
  },
  {
   "cell_type": "markdown",
   "metadata": {},
   "source": [
    "In the cell below, modify `df` so that we have dropped all records where the `RENTHOM1` value label is neither `Own` nor `Rent`, and we have replaced the numeric codes with `Own` and `Rent` respectively. You can find more information about codes on page 33 of the data dictionary."
   ]
  },
  {
   "cell_type": "code",
   "execution_count": null,
   "metadata": {},
   "outputs": [],
   "source": [
    "\n",
    "df[\"RENTHOM1\"].replace({1: \"Own\", 2: \"Rent\"}, inplace=True)\n",
    "\n",
    "\n",
    "df = df[df[\"RENTHOM1\"].isin([\"Own\", \"Rent\"])].copy()\n",
    "\n",
    "df"
   ]
  },
  {
   "cell_type": "code",
   "execution_count": null,
   "metadata": {},
   "outputs": [],
   "source": [
    "# Run this cell without changes\n",
    "df[\"RENTHOM1\"].value_counts()"
   ]
  },
  {
   "cell_type": "markdown",
   "metadata": {},
   "source": [
    "Below, we check that this was done correctly:"
   ]
  },
  {
   "cell_type": "code",
   "execution_count": null,
   "metadata": {},
   "outputs": [],
   "source": [
    "# Run this cell without changes\n",
    "\n",
    "# Number of rows should be smaller again\n",
    "assert df.shape == (31934, 358)\n",
    "\n",
    "# Only two values should be present in this column\n",
    "assert sorted(list(df[\"RENTHOM1\"].value_counts().index)) == ['Own', 'Rent']"
   ]
  },
  {
   "cell_type": "markdown",
   "metadata": {},
   "source": [
    "Now, similar to the previous step, create a plot that shows the distribution of `PHYSHLTH` for those who own vs. rent their homes, including appropriate axis labels and legend. Because there is more of an imbalance of categories this time (more than twice as many owners as renters, compared to nearly-even numbers from New York, New Jersey, and Connecticut), make sure you add the argument `density=True`, so that the y-axis shows the density (proportion) rather than the count."
   ]
  },
  {
   "cell_type": "code",
   "execution_count": null,
   "metadata": {},
   "outputs": [],
   "source": [
    "own = df.loc[df[\"RENTHOM1\"] == \"Own\", \"PHYSHLTH\"]\n",
    "rent = df.loc[df[\"RENTHOM1\"] == \"Rent\", \"PHYSHLTH\"]\n",
    "\n",
    "fig, ax = plt.subplots(figsize=(15, 6))\n",
    "\n",
    "ax.hist(\n",
    "    x=[own, rent],\n",
    "    label=[\"Own\", \"Rent\"],\n",
    "    bins=range(32),\n",
    "    align=\"left\",\n",
    "    density=True\n",
    ")\n",
    "\n",
    "ax.set_xlabel(\"PHYSHLTH\")\n",
    "ax.set_ylabel(\"Proportion\")\n",
    "ax.set_title(\"Distribution of PHYSHLTH by Home Ownership\")\n",
    "\n",
    "ax.legend(title=\"Own or Rent Home\");"
   ]
  },
  {
   "cell_type": "markdown",
   "metadata": {},
   "source": [
    "Then run this code to find the averages:"
   ]
  },
  {
   "cell_type": "code",
   "execution_count": null,
   "metadata": {},
   "outputs": [],
   "source": [
    "# Run this cell without changes\n",
    "df.groupby(\"RENTHOM1\")[\"PHYSHLTH\"].mean()"
   ]
  },
  {
   "cell_type": "markdown",
   "metadata": {},
   "source": [
    "Now, interpret the plot and averages. Does it seem like there a difference in the number of unhealthy days between those who rent their homes and those who own their homes? How does this compare to the distributions by state?"
   ]
  },
  {
   "cell_type": "code",
   "execution_count": null,
   "metadata": {},
   "outputs": [],
   "source": [
    "\"\"\"\n",
    "While the overall distribution is fairly similar again, there is\n",
    "a clearer difference between renters and owners. Home owners are\n",
    "more likely to report 0 unhealthy days than renters are, whereas\n",
    "renters are more likely to report all other numbers of unhealthy\n",
    "days.\n",
    "\n",
    "The difference in means is also larger: about 3.5 vs. about 5.2\n",
    "unhealthy days per month.\n",
    "\"\"\""
   ]
  },
  {
   "cell_type": "markdown",
   "metadata": {},
   "source": [
    "Now, choose and execute an appropriate statistical test. Make sure you describe why you chose the test, the null and alternative hypotheses, and what the result of the test means."
   ]
  },
  {
   "cell_type": "code",
   "execution_count": null,
   "metadata": {},
   "outputs": [],
   "source": [
    "\"\"\"\n",
    "Here we choose a t-test since we are comparing a numeric feature\n",
    "(PHYSHLTH) vs a categorical feature (RENTHOM1), and the categorical\n",
    "feature contains only two categories.\n",
    "\n",
    "Null hypothesis: there is no difference in mean unhealthy days per\n",
    "month between homeowners and renters\n",
    "\n",
    "Alternative hypothesis: renters have more mean unhealthy days per\n",
    "month than homeowners\n",
    "\n",
    "Because we set the alternative hypothesis to be more and not just\n",
    "that the means are not the same, this is a one-tailed t-test and we\n",
    "need to divide the result of the t-test from SciPy in half\n",
    "\"\"\"\n",
    "\n",
    "import scipy.stats as stats\n",
    "\n",
    "ttest_pvalue = stats.ttest_ind(rent, own, equal_var=False).pvalue / 2\n",
    "print(\"t-statistic p-value:\", ttest_pvalue)"
   ]
  },
  {
   "cell_type": "markdown",
   "metadata": {},
   "source": [
    "## 4. Describe the Relationship between Chronic Sickness and Nicotine Use\n",
    "\n",
    "Once again, this will require some preparation before we can run the statistical test. Create a new column `NICOTINE_USE` with 1 representing someone who uses or has used nicotine in some form, and 0 representing someone who hasn't.\n",
    "\n",
    "We define nicotine use as:\n",
    "\n",
    "* Answered `Yes` to the `SMOKE100` question (*Have you smoked at least 100 cigarettes in your entire life?*, page 43), OR\n",
    "* Answered `Every day` or `Some days` to the `USENOW3` question (*Do you currently use chewing tobacco, snuff, or snus every day, some days, or not at all?*, page 46), OR\n",
    "* Answered `Yes` to the `ECIGARET` question (*Have you ever used an e-cigarette or other electronic vaping product, even just one time, in your entire life?*, page 46)\n",
    "\n",
    "If a record matches one or more of the above criteria, `NICOTINE_USE` should be 1. Otherwise, `NICOTINE_USE` should be 0. Go ahead and keep all of the \"Don't know\" or \"Refused\" answers as 0."
   ]
  },
  {
   "cell_type": "code",
   "execution_count": null,
   "metadata": {},
   "outputs": [],
   "source": [
    "\n",
    "df[\"NICOTINE_USE\"] = 0\n",
    "\n",
    "mask = (\n",
    "    \n",
    "    (df[\"SMOKE100\"] == 1) |\n",
    "    \n",
    "    (df[\"USENOW3\"] == 1) |\n",
    "    (df[\"USENOW3\"] == 2) |\n",
    "\n",
    "    (df[\"ECIGARET\"] == 1)\n",
    ")\n",
    "\n",
    "\n",
    "df.loc[mask, \"NICOTINE_USE\"] = 1\n",
    "\n",
    "\n",
    "df[\"NICOTINE_USE\"].value_counts(normalize=True)"
   ]
  },
  {
   "cell_type": "markdown",
   "metadata": {},
   "source": [
    "This time, let's treat health status as a categorical variable. We'll say that a \"chronically sick\" person is a person who reports that their physical health was not good for 15 or more out of the past 30 days. (This is a simplification but it will work for this analysis.)\n",
    "\n",
    "In the cell below, create a new column of `df` called `CHRONIC`, which is 0 for records where `PHYSHLTH` is less than 15, and 1 for records where `PHYSHLTH` is 15 or more."
   ]
  },
  {
   "cell_type": "code",
   "execution_count": null,
   "metadata": {},
   "outputs": [],
   "source": [
    "df[\"CHRONIC\"] = (df[\"PHYSHLTH\"] >= 15).apply(int)\n",
    "\n",
    "# View the distribution of the newly-created column\n",
    "df[\"CHRONIC\"].value_counts()"
   ]
  },
  {
   "cell_type": "markdown",
   "metadata": {},
   "source": [
    "Now we can view the crosstabs for these two categorical variables, as well as display their distributions:"
   ]
  },
  {
   "cell_type": "code",
   "execution_count": null,
   "metadata": {},
   "outputs": [],
   "source": [
    "# Run this cell without changes\n",
    "contingency_table = pd.crosstab(index=df[\"CHRONIC\"], columns=df[\"NICOTINE_USE\"])\n",
    "contingency_table"
   ]
  },
  {
   "cell_type": "code",
   "execution_count": null,
   "metadata": {},
   "outputs": [],
   "source": [
    "# Run this cell without changes\n",
    "\n",
    "no_nicotine_use = df.loc[df[\"NICOTINE_USE\"] == 0, \"CHRONIC\"]\n",
    "nicotine_use = df.loc[df[\"NICOTINE_USE\"] == 1, \"CHRONIC\"]\n",
    "\n",
    "fig, ax = plt.subplots()\n",
    "\n",
    "ax.hist(\n",
    "    x=[no_nicotine_use, nicotine_use],\n",
    "    label=[\"No Nicotine Use\", \"Nicotine Use\"],\n",
    "    bins=[0,1,2],\n",
    "    align=\"left\"\n",
    ")\n",
    "\n",
    "ax.set_ylabel(\"Count\")\n",
    "ax.set_xticks([0,1])\n",
    "ax.set_xticklabels([\"Not Chronically Sick\", \"Chronically Sick\"])\n",
    "ax.set_title(\"Distribution of Chronic Sickness by Nicotine Use\")\n",
    "\n",
    "ax.legend();"
   ]
  },
  {
   "cell_type": "markdown",
   "metadata": {},
   "source": [
    "Once again, it appears that there is a difference in health outcomes between these categories. In the cell below, select an appropriate statistical test, describe the null and alternative hypotheses, execute the test, and interpret the result."
   ]
  },
  {
   "cell_type": "code",
   "execution_count": null,
   "metadata": {},
   "outputs": [],
   "source": [
    "\"\"\"\n",
    "Interpreting the result above, we can reject the null hypothesis\n",
    "because we have a p-value much smaller than 0.05. In other words,\n",
    "this finding is consistent with a statistically significant\n",
    "relationship between nicotine use and incidence of chronic\n",
    "sickness.\n",
    "\n",
    "We can interpret the results table like this:\n",
    "\n",
    "If nicotine use made no difference in chronic sickness, we would\n",
    "expect to see about 2076 people who did not use nicotine and had\n",
    "chronic sickness, and about 1611 people who did use nicotine and\n",
    "had chronic sickness. The first category is larger because over\n",
    "half (~56%) of people stated no nicotine use.\n",
    "\n",
    "Instead what we found was that 1648 people who did not use\n",
    "nicotine and had chronic sickness, and 2040 people who did use\n",
    "nicotine and had chronic sickness. Thus even though people who\n",
    "used nicotine formed the smaller group, they represented the \n",
    "majority of those with chronic sickness.\n",
    "\n",
    "Of course, this does not prove a causal link. It is possible that\n",
    "people who were already experiencing a large number of unhealthy\n",
    "days actually started using nicotine to treat their symptoms. But\n",
    "this seems to demonstrate that there is an important link between\n",
    "nicotine use and not only long-term health issues, but also\n",
    "short-term health status within the past 30 days.\n",
    "\"\"\""
   ]
  },
  {
   "cell_type": "markdown",
   "metadata": {},
   "source": [
    "## 5. Choose Your Own Question\n",
    "\n",
    "Now that you have investigated physical health and chronic sickness and their relationships with state, home ownership, and nicotine use, you will conduct a similar investigation with variables of your choosing.\n",
    "\n",
    "Select an independent variable based on looking at the information in the data dictionary, and perform any additional transformations needed to use it in an analysis. Then set up, execute, and interpret a statistical test that addresses the relationship between that independent variable and `PHYSHLTH`.\n",
    "\n",
    "(There is no solution branch for this question, and feel free to move on if you have already spent more than 1.5 hours on this lab.)"
   ]
  },
  {
   "cell_type": "markdown",
   "metadata": {},
   "source": [
    "## Conclusion\n",
    "\n",
    "Congratulations, another cumulative lab down! In this lab you practiced reading a data dictionary, performing various data transformations with pandas, and executing statistical tests to address business questions."
   ]
  }
 ],
 "metadata": {
  "kernelspec": {
   "display_name": "Python (learn-env)",
   "language": "python",
   "name": "learn-env"
  },
  "language_info": {
   "codemirror_mode": {
    "name": "ipython",
    "version": 3
   },
   "file_extension": ".py",
   "mimetype": "text/x-python",
   "name": "python",
   "nbconvert_exporter": "python",
   "pygments_lexer": "ipython3",
   "version": "3.8.5"
  }
 },
 "nbformat": 4,
 "nbformat_minor": 4
}
