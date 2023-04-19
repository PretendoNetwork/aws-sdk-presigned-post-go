# Custom PresignClient for presigned POST using the AWS s3 Go SDK (v2)

## Why?

The official SDK has not had a way to create presigned POST urls, despite having an open [issue](https://github.com/aws/aws-sdk-go-v2/issues/1224) and associated [PR](https://github.com/aws/aws-sdk-go/pull/3287) for over 2 years. This module adds support for creating these presigned POST urls in the cleanest way possible

## Usage

```go
package main

import (
	"context"
	"fmt"
	"time"

	presign_post "github.com/PretendoNetwork/aws-sdk-presigned-post-go"
	"github.com/aws/aws-sdk-go-v2/aws"
	"github.com/aws/aws-sdk-go-v2/config"
	"github.com/aws/aws-sdk-go-v2/credentials"
)

func main() {
	s3Endpoint := "https://s3.example.com"
	s3Region := "us-east-1"
	s3AccessKey := "key_id"
	s3AccessSecret := "key_secret"
	bucket := "bucket"
	key := "file.bin"

	staticCredentials := credentials.NewStaticCredentialsProvider(s3AccessKey, s3AccessSecret, "")

	endpointResolver := aws.EndpointResolverWithOptionsFunc(func(service, region string, options ...interface{}) (aws.Endpoint, error) {
		return aws.Endpoint{
			URL: s3Endpoint,
		}, nil
	})

	cfg, err := config.LoadDefaultConfig(
		context.TODO(),
		config.WithRegion(s3Region),
		config.WithCredentialsProvider(staticCredentials),
		config.WithEndpointResolverWithOptions(endpointResolver),
	)

	if err != nil {
		panic(err)
	}

	presignPostClient := presign_post.NewPresignClient(cfg)

	input := &presign_post.PostObjectInput{
		Bucket:    bucket,
		Key:       key,
		ExpiresIn: time.Minute * 15,
	}

	res, _ := presignPostClient.PresignPostObject(input)

	fmt.Printf("%+v\n", res)
}
```