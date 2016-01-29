# rexray-cfn
This project exists to house proof-of-concept code for spinning up an AWS instances using CloudFormation, downloading and configuring the most recent `Docker` and `rexray` code, and making the instance available online. The purpose of the proof-of-concept is to allow an end user to 'kick the tires' a bit, without having to invest much time or energy into the setup.

## Usage
A basic familiarity with CloudFormation is expected - you'll want to [install the AWS CloudFormation CLI](http://docs.aws.amazon.com/cli/latest/userguide/installing.html) (hint: most likely `pip install awscli`), or just use the web interface.

There are two files here so far; one that uses IAM to create a keypair for the rexray process to use, and one that wants an access key and secret key passed in on the commandline. Neither of these are particularly useful as actual production code, but they can be used as examples.

To bring up a stack with the CLI tools, clone this repository and enter the `rexray-cfn` directory.

For the IAM version, use the following command:
> `aws cloudformation create-stack --template-body file://./rexray-template-IAM.json --stack-name rexraytesting --parameters ParameterKey=KeyName,ParameterValue=YOURKEY --capabilities=CAPABILITY_IAM`

...where, of course, "YOURKEY" is the name of your own keypair in AWS. Note that the `--capabilities=CAPABILITY_IAM` is needed!

Currently it's worth noting that this template/stack *DOES NOT WORK*. I'm working on it.

For the pass-the-keys-on-the-CLI version, use the following command:
>`aws cloudformation create-stack --template-body file://./rexray-template-cli-params.json --stack-name rexraytesting --parameters ParameterKey=KeyName,ParameterValue=YOURKEY ParameterKey=AWSAccessKey,ParameterValue="YOUR_AWS_ACCESS_KEY" ParameterKey=AWSSecretKey,ParameterValue="YOUR_AWS_SECRET_KEY"`

...and again, swapping out the YOURKEY, YOUR\_AWS\_ACCESS\_KEY and YOUR\_AWS\_SECRET\_KEY for your own values.

This template *does* work, but it's inherently an insecure way of dealing with the keys, so mostly it's just here to prove that everything else works while I try to figure out why the IAM version isn't running.

## Details
These cloudformation templates install a basic AMI, download the latest Docker install script to /tmp and run it, then download the latest REX-ray install script to /tmp and run it, and then reboot. Upon reboot, a user should be able to log into the instance as the user 'ec2-user' (using the keypair specified in the YOURKEY variable) and immediately be able to run containers with the REX-ray volume driver.

The IAM template also creates an IAM keypair for the rexray user to use - that's the 'CAPABILITY\_IAM' part. The script then populates the `/etc/rexray/config.yml` file with the AWS\_ACCESS\_KEY and AWS\_SECRET\_KEY values for rexray to use. Currently, *this doesn't work*. I'm not 100% sure why, but I'm actively working on fixing that... upon bringing up the stack, running `rexray volume help` gives the error "FATA[0000] Error: Driver Volume discovery failed: You are not authorized to perform this operation. (UnauthorizedOperation)". I suspect this is due to some sort of IAM policy oversight, but will have to dig harder into it to be sure.

## TODO

- fix the auth bug
- tighten up the IAM policies
- clean up the documentation, adding better instructions

## Maintainer

- Drew Smith
