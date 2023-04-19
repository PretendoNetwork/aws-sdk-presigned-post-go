# Custom PresignClient for presigned POST using the AWS s3 Go SDK (v2)

## Why?

The official SDK has not had a way to create presigned POST urls, despite having an open [issue](https://github.com/aws/aws-sdk-go-v2/issues/1224) and associated [PR](https://github.com/aws/aws-sdk-go/pull/3287) for over 2 years. This module adds support for creating these presigned POST urls in the cleanest way possible