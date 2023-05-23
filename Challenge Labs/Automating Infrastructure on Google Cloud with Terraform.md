# Task 1. Create the configuration files

```
touch main.tf
touch variables.tf
mkdir modules
cd modules
mkdir instances
cd instances
touch instances.tf
touch outputs.tf
touch variables.tf
cd ..
mkdir storage
cd storage
touch storage.tf
touch outputs.tf
touch variables.tf
cd
```

Edit all variables.tf file
remember to match *region, zone, project_id* with your own
```
variable "region" {
 default = "us-central1"
}

variable "zone" {
 default = "us-central1-a"
}

variable "project_id" {
 default = "<FILL IN PROJECT ID>"
}
```

Add the following to main.tf
```
#terraform {
#  required_providers {
#    google = {
#      source = "hashicorp/google"
#      version = "3.55.0"
#    }
#  }
#}

provider "google" {
  project     = var.project_id
  region      = var.region
  zone        = var.zone
}


module "instances" {
  source     = "./modules/instances"
}
```

Run terraform init
```
terraform init
```

Get the ID of tf-instance-1 and tf-instance-2

Navigate to Compute Engine > VM Instances. Click on the first instance and copy the Instance ID. Do the same for the second instance.

Next, edit modules/instances/instance.tf
```
resource "google_compute_instance" "tf-instance-1" {
  name         = "tf-instance-1"
  machine_type = "n1-standard-1"

  boot_disk {
    initialize_params {
      image = "debian-cloud/debian-10"
    }
  }

  network_interface {
 network = "default"
  }

metadata_startup_script = <<-EOT
#!/bin/bash
EOT

allow_stopping_for_update = true
}

resource "google_compute_instance" "tf-instance-2" {
  name         = "tf-instance-2"
  machine_type = "n1-standard-1"

  boot_disk {
    initialize_params {
      image = "debian-cloud/debian-10"
    }
  }

  network_interface {
 network = "default"
  }

metadata_startup_script = <<-EOT
#!/bin/bash
EOT

allow_stopping_for_update = true
}
```

Go to root directory
```
cd ~
```

Then import first instance
```
terraform import module.instances.google_compute_instance.tf-instance-1 <FILL IN INSTANCE 1 ID>
```

Then import second instance
```
terraform import module.instances.google_compute_instance.tf-instance-2 <FILL IN INSTANCE 2 ID>
```

Run terraform plan and terraform apply
```
terraform plan
terraform apply
```

# Task 2. Import infrastructure

Add the following to *modules/storage/storage.tf*
```
resource "google_storage_bucket" "storage-bucket" {
  name          = "<FILL IN BUCKET NAME>"
  location      = "US"
  force_destroy = true
  uniform_bucket_level_access = true
}
```

Add the folowing to main.tf in root directory
```
module "storage" {
  source     = "./modules/storage"
}
```

Aplly the changes
```
terraform init
terraform apply
```

Update the main.tf on block terraform like this
```
terraform {
  backend "gcs" {
    bucket  = "<FILL IN BUCKET NAME>"
    prefix  = "terraform/state"
  }
}
```

Initialize the backend
```
terraform init
```

# Task 4. Modify and update infrastructure

Update tf-instance-1 and tf-instance-2 in *modules/instances/instances.tf*, make *machine_type* to *n1-standard-2*

Then add tf-instance-3 in *modules/instances/instances.tf* after tf-instance-2
```
resource "google_compute_instance" "tf-instance-3" {
  name         = "<FILL IN INSTANCE NAME>"
  machine_type = "n1-standard-2"

  boot_disk {
    initialize_params {
      image = "debian-cloud/debian-10"
    }
  }

  network_interface {
    network = "default"
  }

  metadata_startup_script = <<-EOT
#!/bin/bash
EOT

  allow_stopping_for_update = true
}
```

initialize the backend
```
terraform init
terraform apply
```

# Task 5. Destroy resources

remove tf-instance-3 from *modules/instances/instances.tf*

then run terraform init and terraform apply
```
terraform init
terraform apply
```

# Task 6. Use a module from the Registry

add module "vpc" in *main.tf* after module section 
```
module "vpc" {
    source  = "terraform-google-modules/network/google"
    version = "~> 6.0"

    project_id   = var.project_id
    network_name = "<FILL IN NETWORK NAME>"
    routing_mode = "GLOBAL"

    subnets = [
        {
            subnet_name           = "subnet-01"
            subnet_ip             = "10.10.10.0/24"
            subnet_region         = "us-east1"
        },
        {
            subnet_name           = "subnet-02"
            subnet_ip             = "10.10.20.0/24"
            subnet_region         = "us-east1"
        }
    ]
}
```

Then run terraform init and terraform apply
```
terraform init
terraform apply
```

Next, navigate to the instances.tf file and update the configuration resources to connect tf-instance-1 to subnet-01 and tf-instance-2 to subnet-02

Just update the *network_interface* section

tf-instance-1
```
network_interface {
    network = "<YOUR VPC NAME>"
    subnetwork = "subnet-01"
  }
```

tf-instance-2
```
network_interface {
    network = "<YOUR VPC NAME>"
    subnetwork = "subnet-02"
  }
```

Then run terraform init and terraform apply
```
terraform init
terraform apply
```

# Task 7. Configure a firewall

Create a firewall rule resource in the main.tf file, and name it tf-firewall

```
resource "google_compute_firewall" "tf-firewall" {
  name    = "tf-firewall"
  network = projects/<PROJECT_ID>/global/networks/<VPC_NAME>

  allow {
    protocol = "icmp"
  }

  allow {
    protocol = "tcp"
    ports    = ["80", "8080", "1000-2000"]
  }

  source_tags = ["web"]
  source_ranges = ["0.0.0.0/0"]
}
```

Then run terraform init and terraform apply
```
terraform init
terraform apply
```