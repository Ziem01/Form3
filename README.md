# Form3 Platform Interview

Platform engineers at Form3 build highly available distributed systems using infrastructure as code. Our take home test is designed to evaluate real world activities that are involved with this role. We recognise that this may not be as mentally challenging and may take longer to implement than some algorithmic tests that are often seen in interview exercises. Our approach however helps ensure that you will be working with a team of engineers with the necessary practical skills for the role (as well as a diverse range of technical wizardry).


## 🧪 Sample application
The sample application consists of four services:

```
┌─────────────┐     ┌──────────────┐    ┌──────────────┐    ┌──────────────┐
│             │     │              │    │              │    │              │
│   payment   │     │   account    │    │   gateway    │    │   frontend   │
│             │     │              │    │              │    │              │
└─────────┬───┘     └──────┬───────┘    └──────┬───────┘    └──────────────┘
          │                │                   │
          │                │                   │
          │                ▼                   │
          │         ┌──────────────┐           │
          │         │              │           │
          └────────►│    vault     │◄──────────┘
                    │              │
                    └──────────────┘
```                    

Three of those services connect to [vault](https://www.vaultproject.io/) to retrieve database credentials. The frontend container serves a static file.

The project structure is as follows:

```
.
├── docker-compose.yml
├── form3.crt
├── README.md
├── run.sh
├── Vagrantfile
├── services
│   ├── account
│   ├── gateway
│   └── payment
└── tf
    └── main.tf
```
1. Refactoring the Terraform code found in the [tf](./tf) directory is the primary focus of this test.
1. `Vagrantfile`, `run.sh` and `docker-compose.yml` are used to bootstrap this sample application; refactoring these files is not part of the test, but these files may be modified if your solution requires it.
1. `form3.crt` is used to ease sandboxed running of the submission by Form3 staff and can be ignored.
1. The `services` code is used to simulate a microservices architecture that connects to vault to retrieve database credentials. The code and method of connecting to vault can be ignored for the purposes of this test.

## Using an M1 Mac?
If you are using an M1 Mac then you need to install some additional tools:
- [Multipass](https://github.com/canonical/multipass/releases) install the latest release for your operating system
- [Multipass provider for vagrant](https://github.com/Fred78290/vagrant-multipass)
    - [Install the plugin](https://github.com/Fred78290/vagrant-multipass#plugin-installation)
    - [Create the multipass vagrant box](https://github.com/Fred78290/vagrant-multipass#create-multipass-fake-box)

## 👟 Running the sample application
- Make sure you have installed the [vagrant prerequisites](https://learn.hashicorp.com/tutorials/vagrant/getting-started-index#prerequisites)
- In a terminal execute `vagrant up`
- Once the vagrant image has started you should see a successful terraform apply:
```
default: vault_audit.audit_dev: Creation complete after 0s [id=file]
    default: vault_generic_endpoint.account_production: Creation complete after 0s [id=auth/userpass/users/account-production]
    default: vault_generic_secret.gateway_development: Creation complete after 0s [id=secret/development/gateway]
    default: vault_generic_endpoint.gateway_production: Creation complete after 0s [id=auth/userpass/users/gateway-production]
    default: vault_generic_endpoint.payment_production: Creation complete after 0s [id=auth/userpass/users/payment-production]
    default: vault_generic_endpoint.gateway_development: Creation complete after 0s [id=auth/userpass/users/gateway-development]
    default: vault_generic_endpoint.account_development: Creation complete after 0s [id=auth/userpass/users/account-development]
    default: vault_generic_endpoint.payment_development: Creation complete after 1s [id=auth/userpass/users/payment-development]
    default: 
    default: Apply complete! Resources: 30 added, 0 changed, 0 destroyed.
    default: 
    default: ~
```
*Verify the services are running*

- `vagrant ssh`
- `docker ps` should show all containers running:

```
CONTAINER ID   IMAGE                                COMMAND                  CREATED          STATUS          PORTS                                       NAMES
6662939321b3   nginx:latest                         "/docker-entrypoint.…"   3 seconds ago    Up 2 seconds    0.0.0.0:4080->80/tcp                        frontend_development
b7e1a54799b0   nginx:1.22.0-alpine                  "/docker-entrypoint.…"   5 seconds ago    Up 4 seconds    0.0.0.0:4081->80/tcp                        frontend_production
4a636fcd2380   form3tech-oss/platformtest-payment   "/go/bin/payment"        16 seconds ago   Up 9 seconds                                                payment_development
3f609757e28e   form3tech-oss/platformtest-account   "/go/bin/account"        16 seconds ago   Up 12 seconds                                               account_production
cc7f27197275   form3tech-oss/platformtest-account   "/go/bin/account"        16 seconds ago   Up 10 seconds                                               account_development
caffcaf61970   form3tech-oss/platformtest-payment   "/go/bin/payment"        16 seconds ago   Up 8 seconds                                                payment_production
c4b7132104ff   form3tech-oss/platformtest-gateway   "/go/bin/gateway"        16 seconds ago   Up 13 seconds                                               gateway_development
2766640654f3   form3tech-oss/platformtest-gateway   "/go/bin/gateway"        16 seconds ago   Up 11 seconds                                               gateway_production
96e629f21d56   vault:1.8.3                          "docker-entrypoint.s…"   2 minutes ago    Up 2 minutes    0.0.0.0:8301->8200/tcp, :::8301->8200/tcp   vagrant-vault-production-1
a7c0b089b10c   vault:1.8.3                          "docker-entrypoint.s…"   2 minutes ago    Up 2 minutes    0.0.0.0:8201->8200/tcp, :::8201->8200/tcp   vagrant-vault-development-1
```

## ⚙️ Task
Imagine the following scenario, your company is growing quickly 🚀 and increasing the number services being deployed and configured.
It's been noticed that the code in `tf/main.tf` is not very easy to maintain 😢.

We would like you to complete the following tasks:

- [ ] Improve the Terraform code to make it easier to add/update/remove services
- [ ] Add a new environment called `staging` that runs each microservice
- [ ] Add a README detailing: 
  - [ ] Your design decisions, if you are new to Terraform let us know
  - [ ] How your code would fit into a CI/CD pipeline
  - [ ] Anything beyond the scope of this task that you would consider when running this code in a real production environment


## 📝 Candidate instructions
1. Create a private [GitHub](https://help.github.com/en/articles/create-a-repo) repository containing the content of this repository
2. Complete the [Task](#task) :tada:
3. [Invite](https://help.github.com/en/articles/inviting-collaborators-to-a-personal-repository) [@form3tech-interviewer-1](https://github.com/form3tech-interviewer-1) to your private repo
4. Let us know you've completed the exercise using the link provided at the bottom of the email from our recruitment team


## Submission Guidance

### Shoulds
- Only use plain Terraform in your solution
- Only modify files in the `tf/` directory, `run.sh`, and `docker-compose.yml`
- Keep the current versions of the services running in `development` and `production` environments
- Structure your code in a way that will segregate environments
- 🚨 All environments (including staging) should be created when you run `vagrant up` and the apps should print `service started` and the secret data in their logs 🚨
- Structure your code in a way that allows engineers to run different versions of services in different environments

### Should Nots
- Use tools that extend Terraform such as Terragrunt


## Design Decisions

- In my solution, I decided to use terraform workspace because it allows one change between different environments, like development, production, and staging, by using different variable values in the tfvars file for each environment. This lets one create multiple versions of the same infrastructure for different environments with one Terraform configuration, which makes it easier to manage and update infrastructure.

- Also the for_each meta-argument was also used. This lets one create multiple versions of a resource with different attribute values using one resource block. In other words, it’s a way of simplifying resource blocks to avoid repeating code.

- I used the for_each meta-argument to create three versions of each resource block for three different services: gateway, account and payment. This avoids repeating resource blocks and lets one define the resource attributes based on the service name and environment in the variable values. This makes the code simpler and easier to manage and update (add/update/remove services).

- The docker-compose.yml and run.sh used to bootstrap this application was modified as follows: 
- In the run.sh bash script, the terraform commands to switch workspaces, initialize and apply were added.
- In the docker-compose.yml file was modified to define the staging service and network.


## Fit into CICD pipeline
- The Terraform code can be automated for deployment and management of the infrastructure using a CI/CD pipeline. This can be done by creating a pipeline in a CI/CD tool, such as Jenkins, that has the following steps:

- After commiting the terraform code to github the version control system, Get the Terraform code from github, Check the Terraform code using the “terraform validate” command. Show the changes to the infrastructure using the “terraform plan” command, which will display the changes to be done. Make the changes to the infrastructure using the “terraform apply” command, which will create or update the infrastructure resources. Save the Terraform state file in a secure backend, such as Amazon S3 or HashiCorp Consul, to ensure state integrity and allow for teamwork.

## Considerations in real production environment
- The Vault token used for authentication should be stored safely and changed regularly. 
- The Docker images used for the containerized services should be checked for vulnerabilities and updated regularly. 
- The secrets managed by Vault should be encrypted and access should be limited to authorized users only. 
- The network configuration should be checked to ensure that it meets security and performance requirements. 
- The Terraform state file should be stored in a secure backend that is highly available and backed up regularly. 
- The infrastructure should be monitored and logged to detect and respond to any problems or anomalies.
