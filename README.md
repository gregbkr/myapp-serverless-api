# MyApp: a demo serverless API in aws

`Gitrepo: demo-appy-s3-hosting-api-serverless`

## Overview
- APi: SAM serverless API in python
- CI: Codebuild
- Infra: Cloudformation in AWS


## Init 

- Optional: create a certificate for this API e.g.: `*.appy.cloudlabs.link`
- Set AWS to deploy in `eu-west-1`: `nano ~/.aws/config`
- First create the infra with cloudformation: 
  - API: Codebuild project & IAM role 
  - Front: S3 to store static reactjs code

```
cd cloudformation
nano main.yml <-- edit with your needs
aws cloudformation create-stack --stack-name myapp-demo-api-infra-init --template-body file://main.yml --capabilities CAPABILITY_NAMED_IAM
... update-stack ... <-- if already created
```

## Deploy API

### Deploy manually

- Follow code in buildspec.yml to deploy manually (or use local build, see in annexes).
- You will get the url from the output of `sam deploy...`
- Test with your own URL and API key (you will find the key in aws console apigateway -> apikeys): 
```
curl -H "x-api-key: CsYjYxhWiNaKho6Bqt9EW2UbQulgd5475H947qyb" https://kl4q7u9lg3.execute-api.eu-west-1.amazonaws.com/Prod/hellojs/
{"message": "hello world"}%
```

### Deploy via CI
- Edit ci with your needs: `nano buildspec.yml`
- Push code to master or develop for auto CI/CD


### Deploy/test locally
```
serverless offline
curl http://localhost:3000/develop/hellojs
```

## Destroy all
- Destroy using the right region: `aws cloudformation delete-stack --stack-name demo-appy-develop --region eu-west-1`

Todo:
- [x] Secure with APIkey
- [x] Dev + Prod
- [x] Cloudformation init
- [x] Ci create + delete stack
- [ ] Add route53 domain + Cert
- [ ] Parameters SSM [here](https://www.youtube.com/watch?v=mDzjTe9WMnY&list=PLGyRwGktEFqe3-M1EfbpRX_syICmytNWx&index=8)
- [x] Codebuild locally


## Annexes
- SAM Init for new app: `sam init -r python3.8 -n api --app-template "hello-world"`


### Codebuild locally: 
- Dowload + build docker [here](https://github.com/aws/aws-codebuild-docker-images/tree/master/ubuntu/standard/3.0)
- Dowload codebuild_build.sh [here](https://github.com/aws/aws-codebuild-docker-images/blob/master/local_builds/codebuild_build.sh)
- Edit `.env` to set the version to deploy [develop|master]
- Run local build: `./codebuild_build.sh -i aws/codebuild/standard:3.0 -a /tmp/artifacts -s . -e .env -c`