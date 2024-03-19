# Cloud Custodian #

Cloud Custodian is a tool that unifies the dozens of tools and scripts most organizations use for managing their public cloud accounts into one open source tool. It uses a stateless rules engine for policy definition and enforcement, with metrics, structured outputs and detailed reporting for clouds infrastructure. It integrates tightly with serverless runtimes to provide real time remediation/response with low operational overhead.

Organizations can use Custodian to manage their cloud environments by ensuring compliance to security policies, tag policies, garbage collection of unused resources, and cost management from a single tool.

Cloud Custodian can be bound to serverless event streams across multiple cloud providers that maps to security, operations, and governance use cases. Custodian adheres to a compliance as code principle, so you can validate, dry-run, and review changes to your policies.

Cloud Custodian policies are expressed in YAML and include the following:

- The type of resource to run the policy against
- Filters to narrow down the set of resources
- Actions to take on the filtered set of resources

The CompuCom SRE team has implemented Cloud Custodian on two platforms.  The first implementation, which only provides e-mail notifications has its accounts.yml and policies.yml files located in the BitBucket infra-report-ccustodian repository and is run by a scheduled Drone CI process: (https://drone.sre.compucom.com/compucomtechservices/infra-report-ccustodian).  This implementation uses the Cloud Custodian Docker container.  In the steps of .drone.yml you will notice the image statements.

The second implementation, which is for adhoc executions, is installed on **SPLAWSSREDKR01**.  Following are instructions on how to run an adhoc Cloud Custodian execution on **SPLAWSSREDKR01**:

`sudo su -`

`cd /usr/local/bin`

`source c7n-org/bin/activate`

`c7n-org run -c /home/awsadmin/bg216063/cloud-custodian/accounts.yaml -s /tmp -u /home/awsadmin/bg216063/cloud-custodian/policy.yaml`

The policy is validated automatically when you run it, but you can also validate it separately:

`custodian validate /home/awsadmin/bg216063/cloud-custodian/policy.yaml`

You can also check which resources are identified by the policy, without running any actions on the resources:

`c7n-org run -c /home/awsadmin/bg216063/cloud-custodian/accounts.yaml -s /tmp -u /home/awsadmin/bg216063/cloud-custodian/policy.yaml --dryrun`

In the execution above the **accounts.yaml** and **policy.yaml** are located in my directory (/home/awsadmin/bg216063/cloud-custodian).  You can look there for many examples of policy files and then create policy files in your own directory.  Regardless of the actions specified in the policy file, there will be a **resources.json** file containing the output of every run located in this directory:

**/tmp/{account}/{region}/{name from policy file}, eg. /tmp/us-prod/us-east-1/mcafee-security-groups**

Our accounts.yaml file contains this content:

    accounts:
        - account_id: '472510080448'
        name: us-shared
        regions:
        - us-east-1
        role: arn:aws:iam::472510080448:role/sre-ec2-role-assumed
        - account_id: '995437807815'
        name: us-dev
        regions:
        - us-east-1
        role: arn:aws:iam::995437807815:role/sre-ec2-role-assumed
        - account_id: '300899438431'
        name: us-test
        regions:
        - us-east-1
        role: arn:aws:iam::300899438431:role/sre-ec2-role-assumed
        - account_id: '122639376858'
        name: us-prod
        regions:
        - us-east-1
        role: arn:aws:iam::122639376858:role/sre-ec2-role-assumed
        - account_id: '172507017890'
        name: ca-prod
        regions:
        - ca-central-1
        role: arn:aws:iam::172507017890:role/sre-ec2-role-assumed

A policy file will have a format like:

    policies:
    - name: a-name-you-create
    resource: actual resource name from cc documentation
    description: Some relevant description
    filters:
        - type: a type from cc resource documentation
    actions:
        - type: a type from cc resource documentation