<h2>AWS Regions</h2>
AWS has regions all around the world. It's just what it says, a region somewhere in the world.
Ex us-east-1.

Each region has availability zones. Ex. us-east-1a, us-east-1b. Each availability zone represents
a physical data center in the region. But they're physically separate from one another. Try and 
minimize threat from physical damage. Ex. disasters.

AWS Consoles are region scoped (except IAM and S3). When you perform an action it will be 
performed in the specific region. Choose the closest region to you.

<h2>IAM (Identity and Access Management)</h2>
This is where the AWS security is.
* Users
* Groups
* Roles

Root account should never be used (and shared). Only use it for the very first time to
create an account and then never again.

Users are usually physical people. They are grouped into groups, usually by function, team
or something similar.

Roles are given to machines. They're for internal usage within AWS resources.

Policies are defined as JSON for those above entities. It defines what each of the above
can and cannot do. It's always good to give the users the minimal amount of permissions
they need to perform their job (least privilege principles).

Multi factor authentication can be setup. Additionally there are predefined policies, that 
can be used.

IAM Federation is something that big enterprises can use. It integrates their own repository
of users with IAM. This way people can use their corporate logins in AWS. It uses the SAML
standard (Active Directory).

So the basic rules are:
* One IAM User per PHYSICAL PERSON
* One IAM Role per Application
* IAM credentials should never be shared
* Never write IAM credentials in code
* Never use ROOT account except for initial setup
* Never use ROOT IAM credentials