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
   - ![Create Bucket](https://github.com/jaimin-vitthalpara/aws-mini-projects/blob/d5b4d69a9ac2ccbe08daa33720ad761559520952/create-bucket-edit.png)
   - ![Create Bucket](https://github.com/jaimin-vitthalpara/aws-mini-projects/blob/6cde468acf07618e2cb350c467ffa92c4c2b3c61/1-bucket-created.png)

---

### **Step 2: Upload Files to Your S3 Bucket**

1. **Select Your Bucket**: Click on the `photography-portfolio-bucket` that you just created.
2. **Upload Website Files**:
   - Inside the bucket, click on **Upload**.
   - Upload all your website files, including HTML, CSS, JS, and images. Make sure to preserve the folder structure if any.
3. **Confirm Upload**: After the upload completes, you should see all your files listed in the bucket.

![Create Bucket](https://github.com/jaimin-vitthalpara/aws-mini-projects/blob/6cde468acf07618e2cb350c467ffa92c4c2b3c61/2-add%20file%20%26%20folders.png)

---

### **Step 3: Set Permissions for the Bucket**

1. **Go to Permissions Tab**: In your S3 bucket, click on the **Permissions** tab.
2. **Click on Bucket Policy**: Scroll down to find the **Bucket Policy** section and click on **Edit**.

![Create Bucket](https://github.com/jaimin-vitthalpara/aws-mini-projects/blob/6cde468acf07618e2cb350c467ffa92c4c2b3c61/3-permission%20TAB.png)

---

### **Step 4: Generate a Bucket Policy**

1. **Click on Policy Generator**: To create a policy, click on the **Policy generator** button.

![Create Bucket](https://github.com/jaimin-vitthalpara/aws-mini-projects/blob/6cde468acf07618e2cb350c467ffa92c4c2b3c61/4-edit%20bucket%20policy.png)

---

### **Step 5: Generated Policy to the Bucket Policy**

1. **Select Policy Type**: In the Policy Generator, select **S3 Bucket Policy**.
2. **Set Permissions**:
   - For **Action**, select `GetObject`. This will allow public read access to all objects in your bucket.
   - For **ARN**, enter: 
     ```
     arn:aws:s3:::photography-portfolio-bucket/*
     ```
     (replace `photography-portfolio-bucket` with your actual bucket name).
3. **Generate the Policy**: After filling in the details, click **Generate Policy**.
4. **Copy the Generated Policy**: After generating the policy, you’ll see it on the screen. Copy the entire generated policy.
5. **Paste into Bucket Policy**: Go back to the Bucket Policy editor (under the **Permissions** tab) and paste the copied policy into the editor.

![Create Bucket](https://github.com/jaimin-vitthalpara/aws-mini-projects/blob/6cde468acf07618e2cb350c467ffa92c4c2b3c61/5-getobject.png)


---

### **Step 6: Add the Generated Policy to the Bucket Policy**

1. **Copy the Generated Policy**: After generating the policy, you’ll see it on the screen. Copy the entire generated policy.
2. **Paste into Bucket Policy**: Go back to the Bucket Policy editor (under the **Permissions** tab) and paste the copied policy into the editor.

![Create Bucket](https://github.com/jaimin-vitthalpara/aws-mini-projects/blob/6cde468acf07618e2cb350c467ffa92c4c2b3c61/6-policy%20generatded.png)


---

### **Step 7: Save the Changes**

1. **Save the Policy**: After pasting the policy, click **Save changes** to apply the policy.

![Create Bucket](https://github.com/jaimin-vitthalpara/aws-mini-projects/blob/6cde468acf07618e2cb350c467ffa92c4c2b3c61/7-copy%20policy.png)

---

### **Step 8: Access the Website**

1. **Find the Website URL**:
   - Go to the **Properties** tab of your S3 bucket.
   - Scroll down to the **Static Website Hosting** section.
   - select the main/default **index.html** page and click on **copy URL** 
   - You will see a URL that looks like this:
     ```
     http://photography-portfolio-bucket.s3-website-us-east-1.amazonaws.com/
     ```
     ![Create Bucket](https://github.com/jaimin-vitthalpara/aws-mini-projects/blob/6cde468acf07618e2cb350c467ffa92c4c2b3c61/8.png)
     
2. **Test Your Website**: Copy the URL and paste it into your browser. You should now see your website live and accessible!

    #### ▶ **Home Page**
   ![Home Page](https://github.com/jaimin-vitthalpara/aws-mini-projects/blob/38072c1d7eb1f75561a30401679610a23d2883eb/home.png)

   #### ▶ **Portfolio Page**
   ![Portfolio Page](https://github.com/jaimin-vitthalpara/aws-mini-projects/blob/38072c1d7eb1f75561a30401679610a23d2883eb/portfolio.png)

   #### ▶ **Gallery Page**
   ![Gallery Page](https://github.com/jaimin-vitthalpara/aws-mini-projects/blob/38072c1d7eb1f75561a30401679610a23d2883eb/Gallery.png)

   #### ▶ **Exhibitions Page**
   ![Exhibitions Page](https://github.com/jaimin-vitthalpara/aws-mini-projects/blob/38072c1d7eb1f75561a30401679610a23d2883eb/exhibitions.png)

   #### ▶ **About Page**
   ![About Page](https://github.com/jaimin-vitthalpara/aws-mini-projects/blob/38072c1d7eb1f75561a30401679610a23d2883eb/about.png)

   #### ▶ **Blog Page**
   ![Blog Page](https://github.com/jaimin-vitthalpara/aws-mini-projects/blob/38072c1d7eb1f75561a30401679610a23d2883eb/blog.png)

---

## **Final Review**

In this guide, we walked through the process of hosting a Photography Portfolio Website on AWS S3. First, we created an S3 bucket with a unique name and selected the appropriate region. Next, we uploaded the website files (HTML, CSS, JS, and images) to the bucket. Then, we configured the bucket permissions by adding a Bucket Policy that grants public access to all the files using the `s3:GetObject` action. After that, we used the Policy Generator to create the policy and applied it to the bucket. Finally, we enabled Static Website Hosting, retrieved the public URL, and tested it in the browser to verify the website was live. 

Following these steps ensures that your static website is publicly accessible, simple to manage, and cost-effective, providing a seamless way to showcase your work online.

---

## **Additional Resources**

- [AWS S3 Documentation](https://docs.aws.amazon.com/s3/)
- [AWS S3 Static Website Hosting](https://docs.aws.amazon.com/AmazonS3/latest/dev/WebsiteHosting.html)


