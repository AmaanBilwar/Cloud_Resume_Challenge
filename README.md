

Table of Contents: 

Coming Soon.....


My "**Resume**" for this challenge is hosted at [this link](https://df351d981p1bv.cloudfront.net) with **AWS S3, Cloudfront and a sweet Github Actions CI/CD pipleine** 😃



# What even is this?

The **Cloud Resume Challenge** is desgined by **Forrest Brazeal** to help people learn "cloud technologies" (AWS in my case) through a hands-on project. The challenge guides u to build and deploy a cloud-base resume using a variety of AWS Services.

Additional Information can be found in this [link](https://cloudresumechallenge.dev/) 

# Requirements for the Challenge



#### Extra work outside of the challenge requirements: 

#### Problem:

 Had to manually update the files in the AWS S3 bucket after every minor change that was made/pushed to github.

#### Solution:
 Made a CI/CD pipeline using Github Actions and AWS IAM to make it easier to have the updates automatically in my AWS S3 bucket.

#### Explanation/Execution: 

1. Made an AWS IAM user and gave full access to my S3 and Cloudfront services.
2. Got the required secret keys and variables needed. 
3. Added the secrets to Github repo secrets to be used later in the yaml file.
4. Create a Github action by making ".github/workflows/deploy.yml" file and writing the required code to allow me to access the AWS services using the CDK.
5. this YAML Code allows me to access and upload only index.html and output.css(generated via tailwind css) to my S3 bucket after verifying authorization. Also invalidates the cache in Cloudfront to make the changes immediate.
``` 
name: Deploy to S3

on:
  push:
    branches:
      - main  # Change this to your branch name if needed

jobs:
  deploy:
    runs-on: ubuntu-latest

    steps:
    - name: Checkout code
      uses: actions/checkout@v2

    - name: Configure AWS Credentials
      uses: aws-actions/configure-aws-credentials@v1
      with:
        aws-access-key-id: ${{ secrets.AWS_ACCESS_KEY_ID }}
        aws-secret-access-key: ${{ secrets.AWS_SECRET_ACCESS_KEY }}
        aws-region: us-east-1

    - name: Copy index.html to S3
      run: aws s3 cp src/index.html s3://${{ secrets.S3_BUCKET }}/index.html
      env:
        S3_BUCKET: ${{ secrets.S3_BUCKET }}

    - name: Copy output.css to S3
      run: aws s3 cp src/output.css s3://${{ secrets.S3_BUCKET }}/output.css
      env:
        S3_BUCKET: ${{ secrets.S3_BUCKET }}

    - name: Create CloudFront Invalidation
      run: aws cloudfront create-invalidation --distribution-id ${{ secrets.CLOUDFRONT_DISTRIBUTION_ID }} --paths "/*"

```
6. **You're Done**. This is all you need. Now every time you push your code Github Actions verifies the code and directly makes the necessary changes to the S3 bucket and Cloudfront services. 

**Would add a unit tests if application requires it**  


Challenges: 

