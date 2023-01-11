# HashiCorp - Terraform

  
  

1. Create a folder

2. Create a file folder for deployment

3. Install Terraform

4. Edito configuration file to

  

Example:

  

```terraform
terraform {

  required_providers {

    docker = {

      source  = "kreuzwerker/docker"

      version = "~> 2.15.0"

    }

  }

}

provider "docker" {}

resource "docker_image" "nginx" {

  name         = "nginx:latest"

  keep_locally = false

}

resource "docker_container" "nginx" {

  image = docker_image.nginx.latest

  name  = "tutorial"

  ports {
    internal = 80

    external = 8000
  }

}

```

  
  

1. Initialize the project issuing the command 
(in this case it download the plugin and allow Terraform to interact with underlying docker)

  

2. Provision resourses stated in the configuration file issuing the command
(in this case an NGINX server container). Note: provisioning can be verified via docker

3. Destroy the deployment issuing the command

```bash

    terraform init

    terraform apply

    docker ps

    terraform destroy

```


## Installation Guide (for Debian/Ubuntu distros)

  1. Install dependecies

	sudo apt-get update && sudo apt-get install -y gnupg software-properties-common
	
2. Download gpg Hashicorp's key

wget -O- https://apt.releases.hashicorp.com/gpg | \

    gpg --dearmor | \

    sudo tee /usr/share/keyrings/hashicorp-archive-keyring.gpg

  

3. Verify downloaded Hashicorp's key

gpg --no-default-keyring \

    --keyring /usr/share/keyrings/hashicorp-archive-keyring.gpg \

    --fingerprint

  

4. Add key to the apt-repository

echo "deb [signed-by=/usr/share/keyrings/hashicorp-archive-keyring.gpg] \

    https://apt.releases.hashicorp.com $(lsb_release -cs) main" | \

    sudo tee /etc/apt/sources.list.d/hashicorp.list

  

5. Update references

sudo apt update

  

6. Install Terraform

sudo apt-get install terraform

  

7. Extra - Install "Terraform autocompletion" (.bashrc file has to exist)

  

    terraform -install-autocomplete

  

%%%%%%%%%%%

% Example %

%%%%%%%%%%%

  
  

    open -a Docker //Open_Docker_Desktop

  

    mkdir learn-terraform-docker-container

    cd learn-terraform-docker-container

    touch main.tf

-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\

  

terraform {

  required_providers {

    docker = {

      source  = "kreuzwerker/docker"

      version = "~> 2.13.0"

    }

  }

}

  

provider "docker" {}

  

resource "docker_image" "nginx" {

  name         = "nginx:latest"

  keep_locally = false

}

  

resource "docker_container" "nginx" {

  image = docker_image.nginx.latest

  name  = "tutorial"

  ports {

    internal = 80

    external = 8000

  }

}

  
  

-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\

  

    terraform init

    terraform apply

    docker ps

    terraform destroy

  

%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%

  

## Terraform Provider Version

  
  

Terraform providers manage resources by communicating between Terraform and target APIs. Whenever the target APIs change or add functionality, provider maintainers may update and version the provider.

  

When multiple users or automation tools run the same Terraform configuration, they should all use the same versions of their required providers. There are two ways for you to manage provider versions in your configuration.

  

1. Specify provider version constraints in your configuration's terraform block.

2. Use the dependency lock file

  

If you do not scope provider version appropriately, Terraform will download the latest provider version that fulfills the version constraint.