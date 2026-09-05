# Customer Segmentation and Retail Recommendation Analysis

This project analyzes online retail transactions to identify customer segments, discover products that are frequently purchased together, and generate product recommendations.

The analysis is implemented in [`customer_seg.ipynb`](customer_seg.ipynb) using the Online Retail dataset.

## Objectives

- Identify customer groups with similar purchasing behavior.
- Find product combinations that support cross-selling and bundling.
- Recommend similar products to improve customer engagement and average order value.
- Translate analytical results into targeted marketing and retention strategies.

## Dataset

The project uses transaction-level online retail data with fields such as:

- `InvoiceNo`: invoice or transaction identifier
- `StockCode`: product code
- `Description`: product description
- `Quantity`: number of units purchased
- `InvoiceDate`: transaction date and time
- `UnitPrice`: price per unit
- `CustomerID`: customer identifier
- `Country`: customer country

The dataset is expected in the `data` folder. The notebook reads `data/online_retail.csv`; it also contains an earlier optional step that converts `data/online_retail.xlsx` to CSV.

## Methods

### 1. Data exploration and cleaning

The raw data is inspected using previews, shape and type information, descriptive statistics, missing-value counts, and duplicate counts.

The cleaning process then:

1. Removes duplicate transaction rows.
2. Removes rows without a `CustomerID`, since they cannot be assigned to a customer profile.
3. Removes cancelled invoices whose invoice number begins with `C`.
4. Keeps only rows with positive `Quantity` and `UnitPrice` values.
5. Converts `InvoiceDate` to datetime format.
6. Creates `TotalSum` as `Quantity * UnitPrice` for transaction revenue.

### 2. Customer segmentation with RFM and K-Means

Customers are summarized using RFM analysis:

- **Recency**: days since the customer's most recent purchase.
- **Frequency**: number of distinct invoices.
- **Monetary**: total customer spend.

The snapshot date is set to one day after the latest transaction. RFM features are standardized with `StandardScaler` so that monetary values do not dominate distance calculations.

The notebook evaluates cluster counts from 1 to 10 with the Elbow Method using K-Means inertia. It then fits a K-Means model with `K = 4`, using `random_state=42` and `n_init=10`, and summarizes the average RFM values and customer counts for each cluster.

### 3. PCA visualization

Principal Component Analysis reduces the three standardized RFM dimensions to two components. A scatter plot displays the four K-Means clusters, and the explained variance of the two components is reported.

### 4. Market Basket Analysis with Apriori

For a manageable basket matrix, transactions from the United Kingdom are selected. The data is transformed into an invoice-by-product matrix and encoded as binary purchase indicators.

The Apriori algorithm finds frequent itemsets using a minimum support of `0.02`. Association rules are generated with a minimum lift of `1.0` and ranked by confidence and lift. These rules can support product bundling, checkout suggestions, and store layout decisions.

### 5. Item-item collaborative filtering

A binary customer-product interaction matrix is created from purchase quantities. The matrix is transposed so that products can be compared by their customer purchase patterns.

Cosine similarity is calculated between products. The `recommend_products()` function returns the top five products most similar to a supplied product, excluding the product itself.

## Business Applications

- **High-value and loyal customers:** offer loyalty rewards, early access, premium bundles, or free shipping.
- **At-risk customers:** use win-back campaigns and personalized offers based on previous purchases.
- **Frequently associated products:** create bundles and place related products together during checkout.
- **Product recommendations:** display similar-product suggestions on product pages.

## Project Structure

```text
customer_segmentation project/
├── customer_seg.ipynb
├── README.md
└── data/
	└── online_retail.csv
```

## Requirements

Install the Python packages used by the notebook:

```bash
pip install pandas numpy matplotlib seaborn scikit-learn mlxtend openpyxl jupyter
```

Open and run the notebook with:

```bash
jupyter notebook customer_seg.ipynb
```

Run the cells in order because later steps depend on the cleaned dataframe, RFM table, clusters, basket matrix, and similarity matrix created earlier in the notebook.

## Limitations

- The cluster personas are inferred from the RFM averages and should be validated with business stakeholders.
- The current notebook fixes the number of clusters at four after visual Elbow Method inspection.
- Market Basket Analysis is limited to United Kingdom transactions and uses a minimum support of 2%.
- The recommendation model is a simple item-item similarity model and does not address cold-start products or users.
- The notebook is analytical rather than a deployed production recommendation service.

## Conclusion

This project combines RFM analysis, K-Means clustering, PCA, Apriori association rules, and cosine-similarity recommendations to turn raw retail transactions into actionable customer and product insights.
