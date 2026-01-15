

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

