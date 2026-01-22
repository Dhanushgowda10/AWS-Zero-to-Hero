## Block Public Access (Bucket Settings)

Block Public Access is a safety mechanism in Amazon S3 designed to prevent accidental public exposure of data. It works at both the **bucket level** and the **account level** and overrides other permission configurations such as bucket policies and ACLs.

This feature provides four independent settings:

* **Block public ACLs**
  Prevents new public ACLs from being added to buckets or objects.

* **Ignore public ACLs**
  Existing public ACLs are ignored and do not grant public access.

* **Block public bucket policies**
  Prevents saving bucket policies that allow public access.

* **Restrict public bucket policies**
  Restricts access to only AWS services and authorized users within the account, even if a public policy exists.

**Key points**:

* Strongly recommended to keep all options enabled unless public access is intentionally required.
* Commonly enabled for private buckets such as logs, backups, and internal application data.
* Acts as a guardrail and overrides permissions granted via policies or ACLs.

---

## Object Ownership

Object Ownership controls who owns objects uploaded to an S3 bucket and how ACLs behave. This is especially important in cross-account uploads.

Available modes:

* **Bucket owner enforced (recommended)**

  * The bucket owner automatically owns all objects in the bucket.
  * ACLs are completely disabled.
  * Access control is managed only through IAM policies and bucket policies.

* **Bucket owner preferred**

  * Objects uploaded with the `bucket-owner-full-control` ACL are owned by the bucket owner.
  * ACLs are still enabled.

* **Object writer**

  * The AWS account that uploads the object becomes the owner.
  * ACLs are enabled.

**Key points**:

* AWS recommends **Bucket owner enforced** for simplicity and security.
* Disabling ACLs reduces misconfigurations and permission complexity.
* Required for many modern AWS services and best-practice architectures.

---

## Access Control List (ACL)

ACLs are a legacy access control mechanism in Amazon S3 that grant permissions at the **bucket** or **object** level.

ACLs can grant permissions such as:

* READ
* WRITE
* READ_ACP
* WRITE_ACP
* FULL_CONTROL

Permissions can be granted to:

* Specific AWS accounts
* Predefined groups (for example, *AllUsers* for public access)

**Key points**:

* ACLs are evaluated in addition to bucket policies and IAM policies.
* Public access via ACLs is strongly discouraged.
* When **Object Ownership = Bucket owner enforced**, ACLs are disabled entirely.
* AWS recommends using **IAM and bucket policies** instead of ACLs.

---

## Relationship Between These Concepts

* **Block Public Access** prevents unintended public exposure regardless of ACLs or bucket policies.
* **Object Ownership (Bucket owner enforced)** disables ACLs and simplifies permission management.
* **ACLs** are legacy and should be avoided in modern S3 designs.

---

## Best Practices Summary

* Enable **Block Public Access** at both account and bucket level.
* Use **Object Ownership: Bucket owner enforced**.
* Avoid using ACLs; rely on IAM and bucket policies.
* Only allow public access through controlled bucket policies when absolutely necessary.



# Making an S3 Object Public Using ACL (Console Method)


## Step 1: Verify Object Ownership Settings

* Open **Amazon S3 → Buckets → select your bucket**
* Go to **Permissions → Object Ownership**
* Ensure **ACLs are enabled**

  * The option **Bucket owner enforced** must **NOT** be selected
  * Choose **ACLs enabled (Object writer or Bucket owner preferred)**

Click **Save changes**

---

## Step 2: Configure Block Public Access Settings

* In the same bucket, go to **Permissions**
* Scroll to **Block public access (bucket settings)**
* Click **Edit**
* Disable the Public Access block
* Confirm the warning and save changes

---

## Step 3: Upload or Select the Object

* Go to the **Objects** tab
* Upload the object
  **OR**
* Select an existing object you want to make public

---

## Step 4: Edit Object ACL

* Select the object
* Click on Actions
* Scroll to **Make object public using ACL**
* Click the option

---

## Step 5: Access the Object Publicly

Use the object URL:

```
https://<bucket-name>.s3.<region>.amazonaws.com/<object-key>
```

Example:

```
https://my-bucket.s3.ap-south-1.amazonaws.com/images/logo.png
```

If configured correctly, the object will open without authentication.

---

## Important Exam & Interview Points

* Public ACLs are overridden if **Block Public Access** is enabled
* ACLs are disabled when **Object Ownership = Bucket owner enforced**
* ACLs grant permissions at **object level**
* `public-read` ACL allows anyone to read the object
* ACL-based public access is **not recommended** for production workloads

---












