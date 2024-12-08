# **Wikipedia Graph Connected Components**

## **Project Overview**
This project focuses on analyzing the structure of a Wikipedia link graph to identify **connected components** in the graph. Using Spark for distributed computation, the project processes four input datasets (pages, pagelinks, linktargets, and redirects) to extract **mutual links** and computes **connected components** iteratively.

### **Goals**
1. **Extract Mutual Links**:
   - Identify pairs of pages that link to each other (bidirectional links).
2. **Find Connected Components**:
   - Identify groups of pages that are connected through mutual links.
   - Each connected component is assigned a unique identifier.
   - Provide a summary of the connected components, including the largest ones.

3. **Deliverables**:
   - Mutual links stored in Parquet format.
   - Connected components stored in Parquet format, with component ID and page ID.

---

## **How to Run the Code**

### **Setting Up the Environment**
Ensure you have the required dependencies installed. Follow these steps:
1. Install Python dependencies (e.g., PySpark, boto3, unittest).
2. Configure the `CS535_S3_WORKSPACE` environment variable to point to the desired S3 bucket.

### **Running the Entry Point**
To run the code that computes mutual links and connected components, execute the following command in your terminal:

```bash
spark-submit entry_point_file.py
```

The script performs the following:
1. Loads the datasets from S3.
2. Extracts mutual links.
3. Computes connected components iteratively.
4. Saves the results back to S3 in Parquet format.

### **Running the Test Suite**
The automated test suite uses synthetic datasets for validation. To run the test suite:

```bash
python -m unittest test_suite_file.py
```

The test suite validates:
1. Correct mutual links are extracted from synthetic data.
2. Connected components are accurately computed.
3. Expected outputs match actual outputs for the synthetic dataset.

---

## **Details of Operations**

### **Extracting Mutual Links**
#### **Description**
- Mutual links are pairs of pages (`page_a`, `page_b`) where:
  - `page_a` links to `page_b` **AND**
  - `page_b` links to `page_a`.

#### **Computation Steps**
1. Filter `page_df` to only include pages in the main namespace.
2. Resolve redirects in the `redirect_df`.
3. Join `pagelinks_df` with `linktarget_df` to find linked pages.
4. Combine all data to identify mutual links.

#### **Output**
- A DataFrame with two columns:
  - `page_a`: ID of the first page.
  - `page_b`: ID of the second page.

### **Finding Connected Components**
#### **Description**
- A connected component is a maximal set of vertices where every pair is connected by a path of mutual links.

#### **Computation Steps**
1. Initialize each vertex with its own ID as its component ID.
2. Iteratively propagate smaller component IDs across edges.
3. Check for convergence when no more component IDs change.

#### **Output**
- A DataFrame with two columns:
  - `vertex`: Page ID.
  - `component`: Connected component ID.

---

## **Test Cases**
The synthetic dataset ensures coverage of common scenarios. Below are the test cases included in the suite:

### **Test Case 1: Mutual Links Extraction**
- **Input**: Synthetic `page`, `pagelinks`, `linktarget`, and `redirect` datasets.
- **Expected Output**: A set of known mutual links.
- **Why?**: Verifies bidirectional linking logic.

### **Test Case 2: Connected Components**
- **Input**: Synthetic mutual links dataset.
- **Expected Output**: Groups of pages that form connected components.
- **Why?**: Verifies that the iterative algorithm converges to correct connected components.

---

## **Creating a ZIP Archive for Viewers**
To create a ZIP file containing all project files (including code, synthetic data, and README), use the following command:

```bash
zip -r project_files.zip /path/to/project/files
```

Replace `/path/to/project/files` with the directory containing your files.

Alternatively, for GitHub:
1. Upload all files to your repository.
2. Use the "Download ZIP" option from the GitHub repository interface.

---

## **Additional Notes**
- **Intermediate Checkpoints**: Checkpoints are saved every few iterations to S3 for recovery and tracking progress.
- **Scalability**: The code is optimized for Spark’s distributed computation model, making it suitable for large datasets.
- **Logging**: Iterative progress is logged for debugging and evaluation.
