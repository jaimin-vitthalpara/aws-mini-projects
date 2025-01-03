# **Hosting Your Photography Portfolio Website on AWS S3**

## **Introduction**

This guide will walk you through the process of hosting a Photography Portfolio Website on AWS S3. The website is a static website created using HTML, CSS, and JavaScript, and we will use AWS S3 to host it. Follow these step-by-step instructions to upload the files, set up permissions, and generate a working URL for your site.

---

## **Step-by-Step Guide**

### **Step 1: Create an S3 Bucket**

1. **Log in to AWS Console**: Go to the [AWS Console](https://aws.amazon.com/) and log into your account.
2. **Navigate to S3**: In the AWS Console, search for and select **S3** under the "Services" section.
3. **Create a Bucket**:
   - Click on **Create bucket**.
   - Name your bucket as `photography-portfolio-bucket` (or any other unique name you prefer).
   - Choose the appropriate region for your bucket.
   - Click on **Create**.


---

### **Step 2: Upload Files to Your S3 Bucket**

1. **Select Your Bucket**: Click on the `photography-portfolio-bucket` that you just created.
2. **Upload Website Files**:
   - Inside the bucket, click on **Upload**.
   - Upload all your website files, including HTML, CSS, JS, and images. Make sure to preserve the folder structure if any.
3. **Confirm Upload**: After the upload completes, you should see all your files listed in the bucket.

**Screenshot Tip**: Capture the screen showing your files uploaded in the bucket (HTML, CSS, JS, images).

---

### **Step 3: Set Permissions for the Bucket**

1. **Go to Permissions Tab**: In your S3 bucket, click on the **Permissions** tab.
2. **Click on Bucket Policy**: Scroll down to find the **Bucket Policy** section and click on **Edit**.

**Screenshot Tip**: Capture a screenshot showing the Permissions tab and where to click to open the Bucket Policy section.

---

### **Step 4: Generate a Bucket Policy**

1. **Click on Policy Generator**: To create a policy, click on the **Policy generator** button.
2. **Select Policy Type**: In the Policy Generator, select **S3 Bucket Policy**.
3. **Set Permissions**:
   - For **Action**, select `GetObject`. This will allow public read access to all objects in your bucket.
   - For **ARN**, enter: 
     ```
     arn:aws:s3:::photography-portfolio-bucket/*
     ```
     (replace `photography-portfolio-bucket` with your actual bucket name).
4. **Generate the Policy**: After filling in the details, click **Generate Policy**.

**Screenshot Tip**: Capture the page where you select the action and input the ARN, showing the process of generating the policy.

---

### **Step 5: Add the Generated Policy to the Bucket Policy**

1. **Copy the Generated Policy**: After generating the policy, you’ll see it on the screen. Copy the entire generated policy.
2. **Paste into Bucket Policy**: Go back to the Bucket Policy editor (under the **Permissions** tab) and paste the copied policy into the editor.

**Screenshot Tip**: Capture the editor with the policy pasted, showing the Bucket Policy in the Permissions section.

---

### **Step 6: Save the Changes**

1. **Save the Policy**: After pasting the policy, click **Save changes** to apply the policy.

**Screenshot Tip**: Capture the final step showing the successful save of the Bucket Policy.

---

### **Step 7: Access the Website**

1. **Find the Website URL**:
   - Go to the **Properties** tab of your S3 bucket.
   - Scroll down to the **Static Website Hosting** section.
   - You will see a URL that looks like this:
     ```
     http://photography-portfolio-bucket.s3-website-us-east-1.amazonaws.com/
     ```
2. **Test Your Website**: Copy the URL and paste it into your browser. You should now see your website live and accessible!

**Screenshot Tip**: Capture the live website page in the browser after pasting the URL.

---

## **Final Review/Overview**

In this guide, we walked through the process of hosting a Photography Portfolio Website on AWS S3. First, we created an S3 bucket with a unique name and selected the appropriate region. Next, we uploaded the website files (HTML, CSS, JS, and images) to the bucket. Then, we configured the bucket permissions by adding a Bucket Policy that grants public access to all the files using the `s3:GetObject` action. After that, we used the Policy Generator to create the policy and applied it to the bucket. Finally, we enabled Static Website Hosting, retrieved the public URL, and tested it in the browser to verify the website was live. 

Following these steps ensures that your static website is publicly accessible, simple to manage, and cost-effective, providing a seamless way to showcase your work online.

---

## **Additional Resources**

- [AWS S3 Documentation](https://docs.aws.amazon.com/s3/)
- [AWS S3 Static Website Hosting](https://docs.aws.amazon.com/AmazonS3/latest/dev/WebsiteHosting.html)


