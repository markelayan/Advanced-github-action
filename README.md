# Advanced GitHub Actions

Advanced GitHub Action file to create backup zip from repo & build NPM project & Sync it to S3 with the ZIP file.

This has been created by merging multiple repos, so the thanks is for them :)


# GitHub Action to Sync S3 Bucket 🔄

#Crdits https://github.com/jakejarvis/s3-sync-action



### EXAMPLE:


```name: Build and Upload dev

on:
  push:
    branches:
    - developer           # name of the branch you need to build/upload

jobs:
  build:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v1
      - name: Zip Folder
        run: mkdir v && zip -r ./v/"$(date +"%Y-%m-%d %H-%M-%S").zip" .       # your can change the name of the zip file, it is now named as the todays date and placed in a directory called v.
      
      - name: Upload zipfile to S3
        uses: jakejarvis/s3-sync-action@v0.5.0

        env:
          AWS_S3_BUCKET: ######           # Required: Your S3 Bucket NAME not ARN
          AWS_ACCESS_KEY_ID:              # Required: Your AWS Access key https://docs.aws.amazon.com/general/latest/gr/aws-sec-cred-types.html#access-keys-and-secret-access-keys
          AWS_SECRET_ACCESS_KEY:          # Required: Your AWS Secret key
          AWS_REGION: '#######'   # optional: defaults to us-east-1
          SOURCE_DIR: 'v'      # optional: folder ot file you want to sync defaults to entire repository

      - name: Use Node.js 10.14.1         # These configurations will use NodeJs 10.14.1 you can change the version in node-version
        uses: actions/setup-node@v1
        with:
          node-version: 10.14.1 
      - name: Installing dependencies
        run: npm install

      - name: Building the Application
        run: npm run build

      - name: Upload to S3
        uses: jakejarvis/s3-sync-action@v0.5.0
        with:
          args: --acl public-read --follow-symlinks --delete
        env:
          AWS_S3_BUCKET: ######           # Required: Your S3 Bucket NAME not ARN
          AWS_ACCESS_KEY_ID:              # Required: Your AWS Access key https://docs.aws.amazon.com/general/latest/gr/aws-sec-cred-types.html#access-keys-and-secret-access-keys
          AWS_SECRET_ACCESS_KEY:          # Required: Your AWS Secret key
          AWS_REGION: '#######'   # optional: defaults to us-east-1
          SOURCE_DIR: '$$$$$$$'      # optional: folder ot file you want to sync defaults to entire repository

```

### Configuration

The following settings must be passed as environment variables as shown in the example. Sensitive information, especially `AWS_ACCESS_KEY_ID` and `AWS_SECRET_ACCESS_KEY`, should be [set as encrypted secrets](https://help.github.com/en/articles/virtual-environments-for-github-actions#creating-and-using-secrets-encrypted-variables) — otherwise, they'll be public to anyone browsing your repository's source code and CI logs.

| Key | Value | Suggested Type | Required | Default |
| ------------- | ------------- | ------------- | ------------- | ------------- |
| `AWS_ACCESS_KEY_ID` | Your AWS Access Key. [More info here.](https://docs.aws.amazon.com/general/latest/gr/managing-aws-access-keys.html) | `secret env` | **Yes** | N/A |
| `AWS_SECRET_ACCESS_KEY` | Your AWS Secret Access Key. [More info here.](https://docs.aws.amazon.com/general/latest/gr/managing-aws-access-keys.html) | `secret env` | **Yes** | N/A |
| `AWS_S3_BUCKET` | The name of the bucket you're syncing to. For example, `jarv.is` or `my-app-releases`. | `secret env` | **Yes** | N/A |
| `AWS_REGION` | The region where you created your bucket. Set to `us-east-1` by default. [Full list of regions here.](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/using-regions-availability-zones.html#concepts-available-regions) | `env` | No | `us-east-1` |
| `AWS_S3_ENDPOINT` | The endpoint URL of the bucket you're syncing to. Can be used for [VPC scenarios](https://aws.amazon.com/blogs/aws/new-vpc-endpoint-for-amazon-s3/) or for non-AWS services using the S3 API, like [DigitalOcean Spaces](https://www.digitalocean.com/community/tools/adapting-an-existing-aws-s3-application-to-digitalocean-spaces). | `env` | No | Automatic (`s3.amazonaws.com` or AWS's region-specific equivalent) |
| `SOURCE_DIR` | The local directory (or file) you wish to sync/upload to S3. For example, `public`. Defaults to your entire repository. | `env` | No | `./` (root of cloned repository) |
| `DEST_DIR` | The directory inside of the S3 bucket you wish to sync/upload to. For example, `my_project/assets`. Defaults to the root of the bucket. | `env` | No | `/` (root of bucket) |



