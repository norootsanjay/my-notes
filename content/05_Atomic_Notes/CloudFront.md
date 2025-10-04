When delivering content globally, your web server may be situated in one region (for example, North America). If a user in India sends a request, the data must travel a long distance, resulting in high latency. To mitigate this, AWS uses edge locations: smaller, ==geographically dispersed sites that cache data from your origin== (such as a web server or an S3 bucket). _Users receive content from the nearest edge location, ensuring minimal delay._

![[Pasted image 20250812113044.png]]

For instance, if users in Australia access your application, they connect to a local edge location instead of the distant origin server. This proximity reduces the interaction time with your application, making it more responsive. Essentially, ==Amazon CloudFront improves performance by caching content closer to users.==

# What is CloudFront?

CloudFront is a web service that accelerates the distribution of ==both static and dynamic== content, including HTML, CSS, JavaScript, images, videos, and music, by ==delivering it from the closest edge location==. This global network of edge locations ==minimizes latency== by serving cached copies of your content rather than always querying a centralized server. 
![[Screenshot 2025-08-12 at 1.16.43 PM.png]]
![[Screenshot 2025-08-12 at 1.18.00 PM.png]]

# CloudFront Architecture

CloudFront's architecture is built on several core components that work together to deliver content efficiently. The following sections explain each component in detail.

## 1. Origin

The origin is the source of the content that CloudFront caches. This can be an S3 bucket storing images and files, or a custom origin like a load balancer or an HTTP server running on an EC2 instance. Once CloudFront fetches the content from the origin, it caches the data at nearby edge locations.

![The image illustrates the architecture of Amazon CloudFront, showing the flow of content from origin sources to CloudFront and then to end users. It highlights the caching process and the distribution of content.](https://kodekloud.com/kk-media/image/upload/v1752865490/notes-assets/images/AWS-Solutions-Architect-Associate-Certification-CloudFront/amazon-cloudfront-architecture-diagram.jpg)

## 2. Distribution

A distribution in CloudFront is a configuration block where you define the origin settings. CloudFront generates a unique domain name (e.g., xyz.cloudfront.net) that users leverage to access cached content. Upon receiving a request, CloudFront checks for a cached version at the closest edge location. _If available, it serves the file immediately; otherwise, it retrieves the content from the origin, caches it, and then delivers it to the user._

![The image illustrates the architecture of Amazon CloudFront, showing the flow of data from a source to edge locations for caching and fast retrieval by users. It includes elements like distribution configuration, edge locations, and user access paths.](https://kodekloud.com/kk-media/image/upload/v1752865492/notes-assets/images/AWS-Solutions-Architect-Associate-Certification-CloudFront/amazon-cloudfront-architecture-diagram-2.jpg)

## 3. Time to Live (TTL)

The Time to Live (TTL) defines how long content remains cached at an edge location. By default, the TTL is set to 24 hours, meaning content is served from the cache for that duration before it is considered stale. If a user requests the content after the TTL expires, CloudFront must fetch a fresh copy from the origin and update its cache. You have the flexibility to modify the TTL based on your content freshness requirements.

![The image explains CloudFront's Time to Live (TTL), detailing how cached content remains at an edge location for a set time, with a default TTL of 24 hours, and the ability to set specific expiration times.](https://kodekloud.com/kk-media/image/upload/v1752865493/notes-assets/images/AWS-Solutions-Architect-Associate-Certification-CloudFront/cloudfront-ttl-cached-content-explanation.jpg)

## 4. Cache Invalidation

Sometimes you need to update content before the TTL expires. CloudFront enables manual cache invalidation to remove outdated content from all edge locations. For example, if you update a file (from version one to version two), users might still retrieve the outdated version until the TTL lapses. By initiating a cache invalidation, CloudFront removes the old version so that the new version is fetched on subsequent requests.

![The image illustrates cache invalidation, showing how content cached at edge locations can be invalidated, with a TTL of 24 hours, and users receiving outdated versions before expiration.](https://kodekloud.com/kk-media/image/upload/v1752865494/notes-assets/images/AWS-Solutions-Architect-Associate-Certification-CloudFront/cache-invalidation-edge-locations-ttl.jpg)

Invalidations can be applied at the distribution level using a wildcard (e.g., "/*"), specific directories, or individual file paths.

![The image is a slide titled "Cache Invalidations," explaining how invalidations are performed on a distribution, with examples for entire distributions, individual files, and specific directories.](https://kodekloud.com/kk-media/image/upload/v1752865495/notes-assets/images/AWS-Solutions-Architect-Associate-Certification-CloudFront/cache-invalidations-distribution-examples.jpg)

# Integration with Other AWS Services

CloudFront integrates seamlessly with several AWS services to enhance its functionality:

- **SSL/TLS:** HTTPS is enabled by default. AWS provides a default SSL certificate (e.g., *.cloudfront.net), and you can use AWS Certificate Manager to customize certificates for your domains.
- **CloudWatch:** CloudFront automatically pushes operational metrics to CloudWatch, allowing you to monitor performance. You can also opt for additional metrics at an extra cost.

![The image is a diagram about CloudWatch, highlighting that it automatically publishes operational metrics for distributions and can enable extra metrics for an additional cost.](https://kodekloud.com/kk-media/image/upload/v1752865497/notes-assets/images/AWS-Solutions-Architect-Associate-Certification-CloudFront/cloudwatch-operational-metrics-diagram.jpg)

# Use Cases for CloudFront

CloudFront is versatile and supports a wide range of real-world use cases:

|Use Case|Description|Example|
|---|---|---|
|Static Websites|Ideal for hosting websites without dynamic server-side logic.|Delivering HTML, CSS, and JavaScript files.|
|Video on Demand|Efficiently caches and delivers video content for on-demand streaming.|Streaming pre-recorded video content to users.|
|Media File Delivery|Ensures fast distribution of images, documents, and other media libraries.|Distributing downloadable files such as PDFs or images.|

![The image shows three use cases: static websites, video on demand, and streaming, each represented by an icon and a number.](https://kodekloud.com/kk-media/image/upload/v1752865498/notes-assets/images/AWS-Solutions-Architect-Associate-Certification-CloudFront/use-cases-static-video-streaming.jpg)