# Automating a testing environment setup on AWS

Cloud resources are expensive, you only want to pay for them while your are using them. If you only run tests or work on demos for a couple of hours per day, it makes a sense to shutdown all resources for the rest of the day to save on costs.

If you want to resume work or start fresh, your setup should ideally be re-created consistently and then destroyed with a single click of a button. That's where automation comes in.

<p align="center">
  <img title="Software Download section" src="images/graph.png"><br>
  <b>Automation ROI</b><br>
</p>

In this repo, I will provide an example in AWS to create a virtual machine that can run containerized multi-vendor network topologies automatically. You can modify it to fit your needs.

## Requirements

Ansible 2.9+ needs to be [installed](https://docs.ansible.com/ansible/latest/installation_guide/intro_installation.html#installing-ansible-with-pip) in your computer to run this example. [Python3](https://wiki.python.org/moin/BeginnersGuide/Download) as well.

```bash
python -m pip install --user ansible
```

### Python libraries

As we will interact with AWS, we need a couple of Python libraries to be present in the system.

```bash
pip3 install --user boto3 botocore
```

### Ansible Collections

We will also need the Ansible [Amazon AWS Collection](https://github.com/ansible-collections/amazon.aws#amazon-aws-collection).

```bash
ansible-galaxy collection install -r collections/requirements.yml
```

## Creating the test VM

Let's create a VM to run the examples. You first need to make your AWS credentials (`AWS_ACCESS_KEY` and `AWS_SECRET_KEY`) available and have the dependencies installed the dependencies.

```bash
export AWS_ACCESS_KEY=''
export AWS_SECRET_KEY=''
```

With that we can run the following Playbook and wait.

```bash
ansible-playbook create-EC2-Fedora-34.yml -v
<snip>
TASK [Print out SSH access details] *********************************************************************************************************************************************
ok: [Fedora34] => {
    "msg": "ssh -i Fedora34-private.pem fedora@ec2-3-236-234-XXX.compute-1.amazonaws.com"
}

RUNNING HANDLER [configure_instance : Reboot machine] ***************************************************************************************************************************
changed: [Fedora34] => {"changed": true, "elapsed": 23, "rebooted": true}

PLAY RECAP **********************************************************************************************************************************************************************
Fedora34                   : ok=33   changed=22   unreachable=0    failed=0    skipped=0    rescued=0    ignored=0   
localhost                  : ok=23   changed=2    unreachable=0    failed=0    skipped=1    rescued=0    ignored=0   
```

You can access the VM as displayed in the logs, for example: `ssh -i Fedora34-private.pem fedora@ec2-3-236-234-XXXS.compute-1.amazonaws.com`.

### Instance type

You can select any instance type you prefer, based on the vCPU/Memory requirements, and price constrains. By default it selects `t3.medium`. Check out [On-Demand Plans for Amazon EC2](https://aws.amazon.com/ec2/pricing/on-demand/). Some examples:

Instance name | On-Demand hourly rate | vCPU | Memory
--- | --- | --- | ---
t3.medium | $0.0416 | 2 | 4 GiB
m5.large | $0.096 | 2 | 8 GiB

You would run the Playbook like this instead if you preferred to run a `m5.large` instance. 

```bash
ansible-playbook create-EC2-Fedora-34.yml -v --extra-vars "instance_type=m5.large"
```

### Volume type

The volume type is also configurable, check out [Amazon EBS pricing](https://aws.amazon.com/ebs/pricing/). The default is `gp2`.

Volume Type | Price
--- | --- | --- | ---
General Purpose SSD (gp3) - Storage	| $0.08/GB-month
General Purpose SSD (gp2) Volumes | $0.10 per GB-month of provisioned storage

To select a different type, use the variable `volume_type`.

```bash
ansible-playbook create-EC2-Fedora-34.yml -v --extra-vars "instance_type=m5.large volume_type=gp3"

```

## Deleting the test VM

To delete the VM, run this:

```bash
ansible-playbook delete-EC2-Fedora-34.yml -v
```