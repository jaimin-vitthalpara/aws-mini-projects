# S3 and CloudFront Distribution Setup

This guide demonstrates the process of hosting a static website using AWS S3 and CloudFront. Follow these steps to create an S3 bucket, configure a CloudFront distribution, and deploy your website.

## Step 1: Create an S3 Bucket and Upload Files

1. Go to the [S3 Console](https://console.aws.amazon.com/s3/).
2. Click **Create bucket**.
3. Enter a **unique bucket name** and choose a region.
4. Upload your website files (HTML, CSS, images, etc.) to the bucket.
5. Ensure that your files are publicly accessible by setting appropriate permissions.

![S3 Bucket Setup](https://github.com/jaimin-vitthalpara/aws-mini-projects/blob/42d4f346756e6ab736b6944b1a6af98f43677df1/1-%20create%20bucket.png)
![S3 Bucket Setup](https://github.com/jaimin-vitthalpara/aws-mini-projects/blob/42d4f346756e6ab736b6944b1a6af98f43677df1/2-uoload%20file%20and%20folder.png
)

## Step 2: Create CloudFront Distribution

1. Go to the [CloudFront Console](https://console.aws.amazon.com/cloudfront/).
2. Click **Create Distribution**.
3. Select the **Web** delivery method for the distribution.

CloudFront is a Content Delivery Network (CDN) that caches your website’s static content at edge locations across the globe. This helps improve performance by serving content from locations closest to the users.

![CloudFront Distribution Creation](https://github.com/jaimin-vitthalpara/aws-mini-projects/blob/42d4f346756e6ab736b6944b1a6af98f43677df1/3-%20create%20bucekt%20choose%20origin.png)

> **Tip:** CloudFront speeds up delivery by using edge locations, meaning users accessing your website will get faster load times since data is fetched from a nearby location, not directly from the S3 bucket.

## Step 3: Select the Origin Domain

1. Under **Origin Settings**, select your S3 bucket from the **Origin Domain** dropdown menu.
2. The S3 bucket you created earlier should appear in the dropdown list.

![Select S3 Bucket](https://github.com/jaimin-vitthalpara/aws-mini-projects/blob/42d4f346756e6ab736b6944b1a6af98f43677df1/4-%20origin%20access.png)

> **Explanation:** In CloudFront, the **origin** refers to where the content is coming from. In this case, the S3 bucket will serve as the origin for your CloudFront distribution. CloudFront fetches content from this origin when users request it.

## Step 4: Configure Origin Access

1. In the **Origin Access Control** section, select **Origin access control settings (recommended)**.
   
    ### 4.1 Create New OAC:
   - Click on **Create new OAC**.
   - Enter your S3 bucket name when prompted.

 ![Origin Access Control](https://github.com/jaimin-vitthalpara/aws-mini-projects/blob/42d4f346756e6ab736b6944b1a6af98f43677df1/5-create%20new%20OAC.png)

2. ### 4.2 Select the Created OAC:
   - In the dropdown, select the OAC you just created.

![Origin Access Control](https://github.com/jaimin-vitthalpara/aws-mini-projects/blob/42d4f346756e6ab736b6944b1a6af98f43677df1/6-select%20origin%20access.png)

> **Explanation:** CloudFront uses **Origin Access Control (OAC)** to securely fetch content from your S3 bucket. By creating and selecting an OAC, you're ensuring that only CloudFront can access your S3 bucket, preventing direct public access to your bucket's files.

## Step 5: Set Cache Policy

- Under **Cache Policy**, you can choose either a custom policy or use the default policy. I have selected the default policy.

![Cache Policy](https://github.com/jaimin-vitthalpara/aws-mini-projects/blob/42d4f346756e6ab736b6944b1a6af98f43677df1/7-Cache%20policy.png)

> **Explanation:** The cache policy defines how CloudFront caches your content at edge locations. By choosing the default policy, CloudFront will cache your content based on standard HTTP headers, improving response time and reducing the load on your S3 bucket.

## Step 6: Disable WAF

- In the **Web Application Firewall (WAF)** section, select **Disable WAF**.

![Disable WAF](https://github.com/jaimin-vitthalpara/aws-mini-projects/blob/42d4f346756e6ab736b6944b1a6af98f43677df1/8-Web%20Application%20Firewall%20(WAF).png)

## Step 7: Create the Distribution

1. After configuring the settings, click **Create Distribution**.
2. A notification will appear saying "Copy Policy".

![Create Distribution](https://github.com/jaimin-vitthalpara/aws-mini-projects/blob/42d4f346756e6ab736b6944b1a6af98f43677df1/9-copy%20policy.png)

> **Tip:** After creating the distribution, CloudFront will start deploying your settings. This process can take some time because CloudFront is propagating your settings across its global edge network.

## Step 8: Update S3 Bucket Policy

1. Copy the **Policy** from the CloudFront notification.
2. Go to your S3 bucket settings.
3. Under **Permissions**, click on **Bucket Policy**.
4. Paste the copied policy and click **Save Changes**.

![S3 Bucket Policy](https://github.com/jaimin-vitthalpara/aws-mini-projects/blob/42d4f346756e6ab736b6944b1a6af98f43677df1/10-add%20policy%20to%20S3.png)

> **Explanation:** This policy allows CloudFront to access the S3 bucket's content securely. By pasting it in your S3 bucket's policy, you're granting CloudFront the necessary permissions to serve the files.

## Step 9: Configure CloudFront Distribution Settings

1. Go back to the **CloudFront Console**.
2. Find the distribution you just created, which will be in the **Deploying** stage.
3. Open the distribution, go to **Settings**, and click **Edit**.
4. In the **Default Root Object** field, enter the name of your default HTML file (e.g., `index.html`).

![Default Root Object](https://github.com/jaimin-vitthalpara/aws-mini-projects/blob/42d4f346756e6ab736b6944b1a6af98f43677df1/11-root%20name.png)

## Step 10: Wait for Distribution Deployment

1. Wait for the distribution to deploy. This process may take several minutes as CloudFront propagates across edge locations worldwide.
2. Once deployed, you will see a timestamp indicating deployment completion.
3. Copy the **Distribution Domain Name** provided by CloudFront.

![Distribution Domain](https://github.com/jaimin-vitthalpara/aws-mini-projects/blob/42d4f346756e6ab736b6944b1a6af98f43677df1/12-deployed.png)

> **Explanation:** CloudFront deploys content to its edge locations around the world. Once deployed, CloudFront will distribute your website from multiple locations to ensure fast access for global users. The **Distribution Domain Name** is the URL that you will use to access your website.

## Step 11: Access the Website

1. Open your browser and paste the **Distribution Domain Name**.
2. Your website should be live!

![Website Output](https://github.com/jaimin-vitthalpara/aws-mini-projects/blob/42d4f346756e6ab736b6944b1a6af98f43677df1/13-website.png)

---

## Conclusion

Congratulations! You have successfully set up a static website hosted on AWS S3 and distributed globally using CloudFront. By following this guide, you’ve learned how to:

- Create and configure an S3 bucket to store your website files.
- Set up a CloudFront distribution to serve your content quickly and securely from AWS's global edge locations.
- Use CloudFront’s caching and security features to optimize the performance and access control of your site.

This setup provides a highly scalable, reliable, and fast hosting solution for static websites. CloudFront's CDN ensures your site loads quickly from any part of the world, making it a great choice for production environments.

Feel free to explore additional CloudFront features like custom domains, HTTPS, and advanced caching strategies to further optimize your website.

Happy hosting, and good luck with your AWS journey!


