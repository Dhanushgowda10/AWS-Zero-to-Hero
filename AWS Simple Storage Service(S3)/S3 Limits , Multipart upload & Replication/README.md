

# Object Size & Storage Limits in Amazon S3

## 1. Object Size Limits in S3

Amazon S3 stores data as **objects inside buckets**. While storage is virtually unlimited, **individual objects are constrained by size limits**.

---

### 1.1 Maximum Object Size

* **Maximum size of a single S3 object: 5 TB**

Key points:

* This is a **hard limit** and cannot be increased.
* Applies regardless of upload method.
* Objects larger than 5 TB **cannot be stored in S3**.

---

### 1.2 Single PUT Upload Limit

* Maximum size using a **single PUT operation: 5 GB**

Implications:

* Uploading objects **larger than 5 GB requires multipart upload**.
* Attempting a single PUT above 5 GB results in failure.

---

### 1.3 Number of Buckets

* Default limit: **100 buckets per AWS account**

Additional notes:

* Bucket names are **globally unique** across AWS.
* Limit increases can be requested via AWS Support.
* Best practice is to **use fewer buckets with logical prefixes**.

---

### 1.4 Number of Objects per Bucket

* **Unlimited number of objects** per bucket

S3 automatically scales to handle:

* Millions or billions of objects
* No performance degradation due to object count

---

### 1.5 Total Storage per Bucket

* **Unlimited storage per bucket**

Important:

* No need to provision capacity.
* You only pay for **what you store**.
* Growth is automatic and elastic.

---

# 2: Multipart Uploads in Amazon S3

## 2.1 What is Multipart Upload?

Multipart upload is a feature that allows **large objects to be uploaded as multiple smaller parts**, which are then **assembled by S3 into a single object**.

It is:

* **Required** for objects larger than 5 GB
* **Recommended** for objects larger than 100 MB

---

## 2.2 How Multipart Upload Works (Workflow)

1. **Initiate Multipart Upload**

   * S3 returns a unique **Upload ID**.
   * All parts reference this ID.

2. **Upload Parts**

   * Each part is uploaded independently.
   * Parts can be uploaded **in parallel**.
   * Each part has a **part number**.

3. **Complete Multipart Upload**

   * S3 assembles all uploaded parts.
   * Final object becomes available.

4. **Abort Multipart Upload (Optional)**

   * Cancels upload and removes uploaded parts.
   * Prevents unnecessary storage charges.

---

## 2.3 Multipart Upload Size Limits

| Parameter               | Limit                   |
| ----------------------- | ----------------------- |
| Minimum part size       | 5 MB (except last part) |
| Maximum part size       | 5 GB                    |
| Maximum number of parts | 10,000                  |
| Maximum object size     | 5 TB                    |

---

## 2.4 Benefits of Multipart Upload

### Reliability

* Failed uploads require retrying only the failed part.

### Performance

* Parallel uploads significantly increase throughput.

### Network Efficiency

* Handles unstable or slow connections gracefully.

### Resume Capability

* Uploads can resume using the Upload ID.

### Cost Control

* Failed uploads can be aborted to avoid charges.

---

# 3: Object Replication in Amazon S3

## 3.1 What is Object Replication?

Object replication is an S3 feature that **automatically copies objects** from a **source bucket** to one or more **destination buckets**, either within the same region or across regions.

Replication is:

* **Asynchronous**
* **Object-level**
* Based on **replication rules**

---

## 3.2 Types of S3 Replication

### Same-Region Replication (SRR)

* Source and destination buckets are in the **same AWS region**.
* Used for:

  * Data segregation
  * Security isolation
  * Analytics workloads

### Cross-Region Replication (CRR)

* Buckets are in **different AWS regions**.
* Used for:

  * Disaster recovery
  * Compliance
  * Latency reduction

---

## 3.3 Replication Prerequisites

* Versioning **must be enabled** on both buckets.
* Destination bucket must already exist.
* Proper **IAM role and permissions** required.
* Replication applies only to **new objects** by default.

---

## 3.4 Replication – What Gets Copied

| Item                | Replicated                 |
| ------------------- | -------------------------- |
| New object versions | Yes                        |
| Object metadata     | Yes                        |
| Object tags         | Optional                   |
| ACLs                | Optional                   |
| Delete markers      | Optional                   |
| Existing objects    | No (use Batch Replication) |

---

## 3.5 Replication of Deletes

* Deleting an object creates a **delete marker** in versioned buckets.
* Replication of delete markers is **optional**.
* Permanent deletes are **not replicated**.

---

## 3.6 Batch Replication

Batch Replication is used to:

* Replicate **existing objects**
* Retry **failed replications**
* Apply new rules to old data

Common scenarios:

* Replication enabled late
* Compliance-driven data sync
* Backfilling historical data

---

## 3.7 Common Use Cases

* Disaster recovery across regions
* Regulatory compliance
* Multi-region application data
* Centralized log storage
* Data sovereignty requirements

---

## 3.8 Best Practices

* Always enable versioning before replication.
* Use prefixes or tags to control replication scope.
* Monitor replication metrics.
* Secure IAM roles and KMS permissions.
* Combine replication with lifecycle policies.
* Use Batch Replication for historical data.


# **S3 Replication Lab (Same Account)**

### **Objective**

Automatically replicate objects from a source S3 bucket to a destination S3 bucket **within the same AWS account**.

---

###  Create Buckets**

1. Log in to the **AWS Management Console** → go to **S3**.
2. Click **Create bucket**.

   * **Source bucket**: `s3-replication-source`
   * **Destination bucket**: `s3-replication-destination`
   * Keep **Region** same or different (same account).
3. For **both buckets**, enable **Versioning**:

   * Open the bucket → **Properties** → **Bucket Versioning** → **Enable**
---

###  Configure Replication Rule**

1. Go to **S3 → Source Bucket → Management → Replication Rules → Create replication rule**.
2. **Step 1: Rule scope**

   * Name the rule (e.g., `ReplicateAllObjects`).
   * Choose **Apply to all objects** (or filter by prefix/tag if desired).
3. **Step 2: Destination**

   * Choose **Same AWS account**.
   * Select **Destination bucket**: `s3-replication-destination`.
4. **Step 3: IAM Role**

   * Choose **Create a New role**
5. **Step 4: Additional options**

   * Enable **Replicate delete markers** if you want deletions to replicate.
6. Review → click **Create rule**.

---

### **Step 4: Test Replication**

1. Upload a file to **source bucket**:

2. Check **destination bucket** in the console after a few seconds.

   * The file should appear automatically.

3. Optional: Delete the file in the source bucket and check if delete marker appears in the destination (if enabled).

---

### **Lab Outcome**

* Source bucket: `s3-replication-source`
* Destination bucket: `s3-replication-destination`
* Objects are automatically replicated in near real-time.
* IAM Role handles permissions for replication.

Here’s a **structured lab note for S3 Cross-Account Replication (CAR)** that you can use for teaching or your own reference:

---

# **AWS S3 Cross-Account Replication (CAR) Lab Note**

## **Objective**

Set up S3 replication to automatically replicate objects from a **source bucket in Account A** to a **destination bucket in Account B**.

---

## **Pre-requisites**

1. Two AWS accounts (Account A: Source, Account B: Destination).
2. Two S3 buckets:

   * **Source bucket** (Account A)
   * **Destination bucket** (Account B)
3. Versioning enabled on **both buckets**.
4. IAM roles/policies for replication permissions.

---

## **Step 1: Enable Versioning**

* Versioning must be **enabled** on both source and destination buckets.

**Source Bucket (Account A):**
Console → S3 → Select Bucket → Properties → Bucket Versioning → Enable

**Destination Bucket (Account B):**
Console → S3 → Select Bucket → Properties → Bucket Versioning → Enable

---

## **Step 2: Create IAM Role in Destination Account**

* **Purpose:** Allow the source account to replicate objects into the destination bucket.

1. Go to **Account B → IAM → Roles → Create Role**
2. Choose **Another AWS Account**, enter **Account A ID**
3. Attach this policy (replace bucket name):

```json
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Effect": "Allow",
      "Action": [
        "s3:ReplicateObject",
        "s3:ReplicateDelete",
        "s3:ReplicateTags"
      ],
      "Resource": "arn:aws:s3:::destination-bucket/*"
    }
  ]
}
```

4. Name the role `s3-cross-account-replication-role`
5. Note the **Role ARN** (e.g., `arn:aws:iam::AccountB_ID:role/s3-cross-account-replication-role`)

---

## **Step 3: Attach Bucket Policy to Destination Bucket**

* Destination bucket must **trust the source account**:

```json
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Effect": "Allow",
      "Principal": { "AWS": "arn:aws:iam::AccountA_ID:role/s3-replication-role" },
      "Action": [
        "s3:ReplicateObject",
        "s3:ReplicateDelete",
        "s3:ReplicateTags"
      ],
      "Resource": "arn:aws:s3:::destination-bucket/*"
    }
  ]
}
```

---

## **Step 4: Create IAM Role in Source Account**

* **Purpose:** Source bucket uses this role to perform replication.

1. Go to **Account A → IAM → Roles → Create Role**
2. Choose **S3** → **Replication**
3. Attach policy (replace bucket names):

```json
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Effect": "Allow",
      "Action": [
        "s3:GetObjectVersion",
        "s3:GetObjectVersionAcl",
        "s3:GetObjectVersionTagging"
      ],
      "Resource": "arn:aws:s3:::source-bucket/*"
    },
    {
      "Effect": "Allow",
      "Action": [
        "sts:AssumeRole"
      ],
      "Resource": "arn:aws:iam::AccountB_ID:role/s3-cross-account-replication-role"
    }
  ]
}
```

4. Name it `s3-replication-role`
5. Note the **Role ARN** (used in replication rule).

---

## **Step 5: Configure Replication Rule on Source Bucket**

1. Go to **Account A → S3 → Source Bucket → Management → Replication Rules → Create Rule**
2. **Rule Scope:** Apply to all objects or specific prefix/tag
3. **Destination:**

   * Choose **Another AWS Account**
   * Enter **Account B ID**
   * Enter **Destination Bucket**
   * Choose **IAM Role** → `s3-replication-role`
4. **Additional Options:** Enable replication of **delete markers**, **replicate existing objects** (optional)
5. Review and **Create Rule**

---

## **Step 6: Test Replication**

1. Upload an object to **source bucket**:


2. Check **destination bucket** in Account B → object should appear automatically (replication is near real-time).
3. Optional: Delete the file in source bucket → verify delete marker appears in destination (if enabled).

---

## **Step 7: Lab Outcome**

* Objects in the source bucket (Account A) are **replicated to a destination bucket in Account B** automatically.
* Delete markers can be replicated if enabled.
* IAM roles ensure **secure cross-account replication**.

---

✅ **Notes / Best Practices**

* Use **unique object prefixes** if replicating multiple types of data.
* Replication can be **same-region** or **cross-region**.
* Replication **does not copy existing objects** by default; you need to enable **replicate existing objects** if required.
* For **large-scale replication**, monitor **replication metrics in S3 → Metrics**.

---
