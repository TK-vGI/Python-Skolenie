# E-commerce Workflow with Luigi and Faker


In this tutorial, we will:

1. Use the **Faker** library to generate realistic test data for an e-commerce platform.
2. Build a workflow using **Luigi** to process, transform, and analyze the data.
3. Automate the ETL pipeline and generate a report with key analytics.


##  Prerequisites

Make sure you have the following installed:
- **Python 3.6 or later**
- Required Python libraries:
  ```bash
  pip install luigi pandas faker
  ```

## Generate Test Data with Faker

Creating realistic datasets:

- **User Activity**: Actions like viewing products, adding items to the cart, and making purchases.
- **Product Catalog**: A list of products with names and prices.
- **Sales Data**: Transactions linking users and products.

## Data Generator Code 

The `faker_data_generator.py` file: 

```python
from faker import Faker
import random
import csv

fake = Faker()

# Function to generate user activity data
def generate_user_activity(file_path, num_rows=100):
    with open(file_path, mode='w', newline='') as file:
        writer = csv.writer(file)
        writer.writerow(["user_id", "activity", "timestamp"])  # Header
        activities = ["view", "add_to_cart", "purchase"]
        for _ in range(num_rows):
            user_id = random.randint(1, 50)
            activity = random.choice(activities)
            timestamp = fake.date_time_this_year()
            writer.writerow([user_id, activity, timestamp])

# Function to generate product catalog data
def generate_product_catalog(file_path, num_products=20):
    with open(file_path, mode='w', newline='') as file:
        writer = csv.writer(file)
        writer.writerow(["product_id", "product_name", "price"])  # Header
        for product_id in range(1, num_products + 1):
            product_name = fake.word().capitalize()
            price = round(random.uniform(5, 100), 2)
            writer.writerow([product_id, product_name, price])

# Function to generate sales data
def generate_sales_data(file_path, num_sales=100):
    with open(file_path, mode='w', newline='') as file:
        writer = csv.writer(file)
        writer.writerow(["sale_id", "product_id", "user_id", "amount"])  # Header
        for sale_id in range(1, num_sales + 1):
            product_id = random.randint(1, 20)
            user_id = random.randint(1, 50)
            amount = round(random.uniform(5, 100), 2)
            writer.writerow([sale_id, product_id, user_id, amount])

# Generate data
generate_user_activity("user_activity.csv", num_rows=100)
generate_product_catalog("product_catalog.csv", num_products=20)
generate_sales_data("sales_data.csv", num_sales=100)

print("Test data generated using Faker!")
```

Run the Script:  

```bash
python faker_data_generator.py
```

This will create the following CSV files:

- `user_activity.csv`
- `product_catalog.csv`
- `sales_data.csv`


## Build the Luigi Workflow

The Luigi pipeline consists of:

1. **Extract Tasks**: Load the test data generated by Faker.
2. **Transform Task**: Merge and preprocess the data.
3. **Analytics Task**: Perform calculations and generate a report.

## Luigi Workflow Code

The `ecommerce_workflow.py` file:

```python
import luigi
import pandas as pd

class ExtractUserActivityTask(luigi.Task):
    def output(self):
        return luigi.LocalTarget("user_activity.csv")

    def run(self):
        print("User activity data is ready for processing!")

class ExtractProductCatalogTask(luigi.Task):
    def output(self):
        return luigi.LocalTarget("product_catalog.csv")

    def run(self):
        print("Product catalog data is ready for processing!")

class ExtractSalesDataTask(luigi.Task):
    def output(self):
        return luigi.LocalTarget("sales_data.csv")

    def run(self):
        print("Sales data is ready for processing!")

class TransformDataTask(luigi.Task):
    def requires(self):
        return [ExtractUserActivityTask(), ExtractProductCatalogTask(), ExtractSalesDataTask()]

    def output(self):
        return luigi.LocalTarget("transformed_data.csv")

    def run(self):
        # Read the extracted datasets
        user_activity = pd.read_csv(self.input()[0].path)
        product_catalog = pd.read_csv(self.input()[1].path)
        sales_data = pd.read_csv(self.input()[2].path)

        # Transform and merge data
        merged = sales_data.merge(product_catalog, on="product_id").merge(user_activity, on="user_id")
        merged['timestamp'] = pd.to_datetime(merged['timestamp'])  # Normalize date format

        # Save the transformed data
        merged.to_csv(self.output().path, index=False)
        print("Data transformation completed!")

class AnalyticsTask(luigi.Task):
    def requires(self):
        return TransformDataTask()

    def output(self):
        return luigi.LocalTarget("analytics_report.txt")

    def run(self):
        # Read transformed data
        data = pd.read_csv(self.input().path)

        # Perform analytics
        total_revenue = data['amount'].sum()
        popular_product = data['product_name'].value_counts().idxmax()

        # Save analytics report
        with self.output().open('w') as f:
            f.write(f"Total Revenue: {total_revenue}\n")
            f.write(f"Most Popular Product: {popular_product}\n")
        print("Analytics report generated!")

# Optional: Run this task directly via command line
if __name__ == "__main__":
    luigi.run()        
```

The `ecommerce_workflow.py` script is a pipeline built using **Luigi**, designed to automate  
the process of extracting, transforming, and analyzing data for an e-commerce platform. It  
orchestrates a multi-step workflow where each task depends on the successful completion of  
the previous one, ensuring efficient data processing and error handling.

The workflow starts with **extracting data** from three sources: user activity, product catalog,  
and sales data. These are represented as separate tasks (`ExtractUserActivityTask`,  
`ExtractProductCatalogTask`, and `ExtractSalesDataTask`). These tasks simply load pre-generated  
CSV files (created earlier using the Faker library) into the pipeline, simulating how real-world  
workflows would ingest data from databases, APIs, or other systems.

Next, the pipeline moves to the **transformation phase** (`TransformDataTask`). Here, the data from  
all three sources is merged into a unified dataset by linking the `user_id` and `product_id` fields.  
During this step, the pipeline performs essential data cleaning, such as normalizing timestamps and  
ensuring all datasets are compatible for downstream analysis. The transformed data is saved in a  
new CSV file, making it easier to inspect and use for analytics.

Finally, the **analytics phase** (`AnalyticsTask`) takes over. In this task, the pipeline reads the  
transformed dataset and calculates two key insights: the total revenue generated from all sales and  
the most popular product based on the number of occurrences in the dataset. These results are written  
into an analytics report, providing actionable insights that an e-commerce business could use to make  
data-driven decisions.

Overall, the script showcases a robust and modular way to build an ETL (Extract, Transform, Load) pipeline,    
combining data engineering principles with workflow orchestration. It demonstrates the power of Luigi for    
managing dependencies, automating processes, and maintaining a clear structure for even complex workflows.  
  
## Run the Luigi Pipeline

1. Execute the pipeline:
2. 
   ```bash
   python ecommerce_workflow.py AnalyticsTask --local-scheduler
   ```
   
3. Luigi will:
   - Read the test data files generated by Faker.
   - Transform and merge the data into a single structured dataset.
   - Perform analytics and save the results in `analytics_report.txt`.

## Results

After running the pipeline:

- **Transformed Data**: Saved in `transformed_data.csv`.
- **Analytics Report**: Key insights saved in `analytics_report.txt`, such as:
  - Total revenue across all sales.
  - The most popular product.


## Summary

In this tutorial, we:

- Used **Faker** to generate realistic test data.
- Built an automated ETL pipeline using **Luigi** to process the data.
- Conducted analytics and generated a report.

