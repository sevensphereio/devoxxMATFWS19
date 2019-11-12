# LAB06 - Provisioners

## Steps

There are multiple provisioner types. For this example we will use the easier one, which is to use the Azure native
'Custom Data'.

**1\. Add this option to our module**

Modify the `virtual.machines.tf` file and add the following after the `admin_password` property:

```tf
custom_data    = "${base64encode(file("${path.module}/templates/${var.custom_data_file}"))}"
```

Add this new variable `custom_data` to our module variables:

```tf
variable "custom_data_file" {}
```

Create a `templates` folder inside the module with some init scripts, for example:

`myapp.sh`

```bash
#!/bin/bash
export HOME=/root
apt-get update
apt-get upgrade -y
apt-get install -y wget curl build-essential libssl-dev git unattended-upgrades
cd /root
curl -o- https://raw.githubusercontent.com/creationix/nvm/v0.33.4/install.sh | bash
. ~/.nvm/nvm.sh
nvm install 6.11.3
npm install pm2 -g
git clone https://github.com/heroku/node-js-sample.git
cd node-js-sample
npm install
pm2 start index.js
```

And add it to the params in the `azure-instances.tf` file:

```tf
custom_data_file  = "myapp.sh"
```

**2\. Plan and execute**

Now create a plan and execute it:

```bash
terraform plan -out=my.plan
```

Why is telling us 'No changes'? This is because Azure will run that script only once when the machine is created, but you can destroy and recreate them to see it in action, up to you.
If you want better software provisioning experience try provisioning your VMs with Packer, Ansible, Chef, Puppet or similar.

You can check the official provisioners in the [following link](https://www.terraform.io/docs/provisioners/index.html)
or search for plugins with other provisioners.

Workshop finished!

Don't forget to destroy all the resources or you will burn your money:

```bash
terraform destroy
```

---


