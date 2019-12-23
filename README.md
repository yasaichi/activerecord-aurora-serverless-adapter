[![CI Status](https://github.com/customink/activerecord-aurora-serverless-adapter/workflows/CI/badge.svg)](https://launch-editor.github.com/actions?nwo=customink%activerecord-aurora-serverless-adapter&workflowID=CI)

# Activerecord Aurora Serverless Adapter

<a href="https://github.com/customink/lamby"><img src="https://user-images.githubusercontent.com/2381/59363668-89edeb80-8d03-11e9-9985-2ce14361b7e3.png" alt="Lamby: Simple Rails & AWS Lambda Integration using Rack." align="right" width="300" /></a>**⚠️ WORK IN PROGRESS**<br><br>Simple ActiveRecord Mysql2 adapter extensions to allow Rails to use [AWS Aurora Serverless](https://aws.amazon.com/rds/aurora/serverless/) via the [Aws::RDSDataService::Client](https://docs.aws.amazon.com/sdk-for-ruby/v3/api/Aws/RDSDataService/Client.html) internface. Perfect if you are using [Lamby](https://lamby.custominktech.com) to deploy your Rails applications to AWS Lambda.

**[Lamby: Simple Rails & AWS Lambda Integration using Rack.](https://lamby.custominktech.com)**


## Highlights

This gem allows Rails to seamless use

* No need for the `mysql2` gem at all!
* Developed and tested with Aurora Serverless MySQL v5.6.
* Emoji support via `utf8mb4`. Please configure your cluster's parameter group. See our [CDK Stack](/blob/master/test/aurora-serverless/lib/aurora-serverless-stack.ts) for examples.

Here are some misc features that work differently for the Mysql2 adapter under Aurora Serverless.

* Multiple schemas are not supported.
* We assume that all database times are UTC.
* Prepared statement are not supported.
* Batch statements are not supported.


## Usage

Add the gem to your `Gemfile`. Remember, You **DO NOT** have to add the `mysql2` gem.

```ruby
gem 'activerecord-aurora-serverless-adapter'
```

Assuming you have [created your database](/blob/master/test/aurora-serverless/lib/aurora-serverless-stack.ts) with the Data API enabled and [configured your secrets](https://docs.aws.amazon.com/AmazonRDS/latest/AuroraUserGuide/data-api.html) then configure your `database.yml` file like so.

```yaml
database: 'mydatabase'
adapter: aurora_serverless
secret_arn: arn:aws:secretsmanager:us-east-1:123456789012:secret:Secret-kd2ASwipxeWw-Bdsiww
resource_arn: arn:aws:rds:us-east-1:123456789012:cluster:mydatabase
```

**IMPORTANT:** Please do not use any standard Rails or Mysql2 configuration options here. All or ignored or removed. However, please feel free to use any valid [Aws::RDSDataService::Client](https://docs.aws.amazon.com/sdk-for-ruby/v3/api/Aws/RDSDataService/Client.html#initialize-instance_method) options here. They will all be passed to the Clients `#initialize` method.


## Contributing

Bug reports and pull requests are welcome on GitHub at https://github.com/customink/activerecord-aurora-serverless-adapter. This project is intended to be a safe, welcoming space for collaboration, and contributors are expected to adhere to the [Contributor Covenant](http://contributor-covenant.org) code of conduct.

### Testing

Cloning the repo and running the tests locally is super easy assuming you have:

1. Docker Installed
2. AWS Account Configured

These commands will use Docker to setup a node runtime leveraging [AWD CDK](https://github.com/aws/aws-cdk) to deploy a Aurora Serverless stack. Note, you may be to use/set `AWS_PROFILE` for the deploy command.

```s
$ ./bin/bootstrap
$ export AASA_MASTER_USER=admin
$ export AASA_MASTER_PASS=supersecret
$ ./test/bin/deploy-aurora
```

The outputs of this deployed stack will containt an `AASASecretArn` and a `AASAAuroraClusterArn` value. Please place these into the local `.env` file in the following format where `{{ Value }}` is replaced.

```
AASA_SECRET_ARN={{ AASASecretArn }}
AASA_RESOURCE_ARN2={{ AASAResourceArn }}

AASA_SECRET_ARN={{ AASASecretArn2 }}
AASA_RESOURCE_ARN_2={{ AASAResourceArn2 }}
```

Finally, assuming you have your default AWS account setup with full access to your account, now you can run the tests. The `AWS_PROFILE` can be used here and set in `.env` file as needed or you can use the `AASAUserAccessKeyId` and `AASAUserSecretAccessKey` outputs as `AWS_ACCESS_KEY_ID` and `AWS_SECRET_ACCESS_KEY` environments set in `.env` too.

```s
$ ./bin/test
```

#### Working With The CDK App/Stack

To work with the CDK project within the test directory, run the following commands.

```s
$ docker-compose \
  --project-name aasa \
  run \
  cdk \
  bash

$ cd ./test/aurora-serverless
```

From here you can run `npm`, `tsc`, `cdk` or whatever commands are needed.


## License

The gem is available as open source under the terms of the [MIT License](https://opensource.org/licenses/MIT).


## Code of Conduct

Everyone interacting in the adapter project’s codebases, issue trackers, chat rooms and mailing lists is expected to follow the [code of conduct](https://github.com/customink/activerecord-aurora-serverless-adapter/blob/master/CODE_OF_CONDUCT.md).
