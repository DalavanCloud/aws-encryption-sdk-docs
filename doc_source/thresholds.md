# Setting Cache Security Thresholds<a name="thresholds"></a>

When you implement data key caching, you need to configure the security thresholds that the [caching CMM](data-caching-details.md#caching-cmm) enforces\. 

The security thresholds help you to limit how long each cached data key is used and how much data is protected under each data key\. The caching CMM returns cached data keys only when the cache entry conforms to all of the security thresholds\. If the cache entry exceeds any threshold, the entry is not used for the current operation and it is evicted from the cache\.

As a rule, use the minimum amount of caching that is required to meet your cost and performance goals\. 

The AWS Encryption SDK only caches data keys that are encrypted by using a [key derivation function](https://en.wikipedia.org/wiki/Key_derivation_function)\. Also, it establishes upper limits for the threshold values\. These restrictions ensure that data keys are not reused beyond their cryptographic limits\. However, because your plaintext data keys are cached \(in memory, by default\), try to minimize the time that the keys are saved \. Also, try to limit the data that might be exposed if a key is compromised\.

For examples of setting cache security thresholds, see [AWS Encryption SDK: How to Decide if Data Key Caching is Right for Your Application](http://aws.amazon.com/blogs/security/aws-encryption-sdk-how-to-decide-if-data-key-caching-is-right-for-your-application/) in the AWS Security Blog\.

**Note**  
The caching CMM enforces all of the following thresholds\. If you do not specify an optional value, the caching CMM uses the default value\.  
To disable data key caching temporarily, do not set the [cache capacity](data-caching-details.md#simplecache) or security thresholds to 0\. Instead, use the *null cryptographic materials cache* \(NullCryptoMaterialsCache\) that the AWS Encryption SDK provides\. The NullCryptoMaterialsCache returns a miss for every get request and does not respond to put requests\. For more information, see the SDK for your[ programming language](programming-languages.md)\.

**Maximum age \(required\)**  
Determines how long a cached entry can be used, beginning when it was added\. This value is required\. Enter a value greater than 0\. There is no maximum value\.  
The LocalCryptoMaterialsCache tries to evict cache entries as soon as possible after they reach the maximum age value\. Other conforming caches might perform differently\.  
Use the shortest interval that still allows your application to benefit from the cache\. You can use the maximum age threshold like a key rotation policy\. Use it to limit reuse of data keys, minimize exposure of cryptographic materials, and evict data keys whose policies might have changed while they were cached\.

**Maximum messages encrypted \(optional\)**  
Specifies the maximum number of messages that a cached data key can encrypt\. This value is optional\. Enter a value between 1 and 2^32 messages\. The default value is 2^32 messages\.  
Set the number of messages protected by each cached key to be large enough to get value from reuse, but small enough to limit the number of messages that might be exposed if a key is compromised\.

**Maximum bytes encrypted \(optional\)**  
Specifies the maximum number of bytes that a cached data key can encrypt\. This value is optional\. Enter a value between 0 and 2^63 \- 1\. The default value is 2^63 \- 1\. A value of 0 lets you encrypt empty message strings\.  
The first use of each data key \(before caching\) is exempt from this threshold\. Also, to enforce this threshold, requests to encrypt data of unknown size, such as streamed data with no length specifier, do not use the data key cache\.  
The bytes in the current request are included when evaluating this threshold\. If the bytes processed, plus current bytes, exceed the threshold, the cached data key is evicted from the cache, even though it might have been used on a smaller request\. 