# AWS-S3-Bucket-Cost-Optimization-
S3 Bucket Cost Optimization 

S3 Lifecycle Policies 

Decide your policy (example — quick, common pattern)
A simple, widely used policy:
* After 30 days → move to Standard-IA (lower storage cost for infrequently accessed objects).
* After 90 days → move to Glacier Flexible Retrieval (archive; lower cost, slower retrieval).
* After 365 days → move to Glacier Deep Archive (cheapest for long-term).
* After 1095 days (3 years) → delete. 

Some storage classes (Glacier tiers / Deep Archive) have minimum storage durations and retrieval fees for early deletes. Before you move objects, ensure your transition timing avoids unnecessary early-delete charges. If you’re unsure, consider Intelligent-Tiering which auto-moves objects based on access patterns (good if access is unpredictable).

Test in a non-production bucket
Create a test bucket and upload a few representative objects (small files). Apply the rule there first so you see behavior and billing effects before applying to production.


Console: Create a Lifecycle Rule (easy)
1. Sign in to the AWS Console → open S3.
2. Choose the bucket → go to Management tab → Lifecycle rules → Create lifecycle rule.
3. Give the rule a name, choose scope (All objects or use prefix/tags to limit).
4. Add Transition actions: set days and target storage class (Standard-IA, Intelligent-Tiering, Glacier Flexible, Deep Archive, etc.).
5. (Optional) Add Expiration — number of days to permanently delete.
6. Review & Create rule.



 Example lifecycle JSON (for CLI / API)
You can choose from any of the lifecycle below and  apply a lifecycle config via the AWS CLI or SDK. Save this to lifecycle.json and apply it with the CLI:
NOTE: After choosing the right one, make sure to delete the others and leave only the lifecycle policie that you will be using on your S3 Bucket.
```sh
{
  "Rules": [
    {
      "ID": "cost-optimization-rule",
      "Status": "Enabled",
      "Filter": {
        "Prefix": "" 
      },
      "Transitions": [
        {
          "Days": 30,
          "StorageClass": "STANDARD_IA"
        },
        {
          "Days": 90,
          "StorageClass": "GLACIER"
        },
        {
          "Days": 365,
          "StorageClass": "DEEP_ARCHIVE"
        }
      ],
      "Expiration": {
        "Days": 1095
      }
    }
  ]
}

```
Apply it (replace my-bucket):

```sh
aws s3api put-bucket-lifecycle-configuration \
  --bucket my-bucket \
  --lifecycle-configuration file://lifecycle.json
```
