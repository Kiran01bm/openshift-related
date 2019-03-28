# Notes on usage

## Execute any host customisation
```
ansible-playbook -v -i hostInventory -e "user=ec2-user" --become --key-file "keyFilePath" -e "target=all" nodeBootStrap.yml
```

## Host Preperation - PreStorage
```
ansible-playbook -v -i hostInventory -e "user=ec2-user" --become --key-file "keyFilePath" --skip-tags "containerized" -e "target=mastersAndInfrastructures" -e "rh_username=$RHUSERNAME" -e "rh_password=$RHPASSWORD" -e "subscription_poolId=$OCPMASTERSUBS" -e "ocp_rpmrepo=rhel-7-server-ose-3.9-rpms" -e "ansible_rpmrepo=rhel-7-server-ansible-2.4-rpms" ~/assets/prepNodePreStorage.yml 
ansible-playbook -v -i hostInventory -e "user=ec2-user" --become --key-file "keyFilePath"  --skip-tags "containerized" -e "target=workers" -e "rh_username=$RHUSERNAME" -e "rh_password=$RHPASSWORD" -e "subscription_poolId=$OCPWORKERSUBS" -e "ocp_rpmrepo=rhel-7-server-ose-3.9-rpms" -e "ansible_rpmrepo=rhel-7-server-ansible-2.4-rpms"  ~/assets/prepNodePreStorage.yml

```

## Storage tasks
```
ansible-playbook -v -i hostInventory -e "user=ec2-user" --become --key-file "keyFilePath" -e "target=all" -e "blk_dvc_name=/dev/xvdba" -e "pv_name=/dev/xvdba1" -e "mnt_point=/var/lib/docker"  -e "vg_name=docker-vg" -e "lv_name=docker-pool" storage.yml
ansible-playbook -v -i hostInventory -e "user=ec2-user" --become --key-file "keyFilePath" -e "target=all" -e "blk_dvc_name=/dev/xvdbb" -e "pv_name=/dev/xvdbb1" -e "mnt_point=/var/lib/origin/openshift.local.volumes"  -e "vg_name=ocplocalvol-vg" -e "lv_name=ocplocalvol-pool" storage.yml
ansible-playbook -v -i hostInventory -e "user=ec2-user" --become --key-file "keyFilePath" -e "target=masters" -e "blk_dvc_name=/dev/xvdbd" -e "pv_name=/dev/xvdbd1" -e "mnt_point=/var/lib/etcd"  -e "vg_name=etcd-vg" -e "lv_name=etcd-pool" storage.yml
```

## Post Storage Tasks
```
ansible-playbook -v -i hostInventory -e "user=ec2-user" --become --key-file "keyFilePath" -e "target=all" prepNodePostStorage.yml
```

## Copying the aws configs
```
ansible  -v -i hostInventory --become  --key-file "keyFilePath" -a "mkdir -p /etc/origin" all
ansible  -v -i hostInventory --become  --key-file "keyFilePath" -a "mkdir -p /etc/origin/cloudprovider" all

ansible-playbook -v -i hostInventory -e "user=ec2-user" --become --key-file "keyFilePath" -e "target=zone1" -e "fileName=aws.conf.zone1" awsConfig.yml
ansible-playbook -v -i hostInventory -e "user=ec2-user" --become --key-file "keyFilePath" -e "target=zone2" -e "fileName=aws.conf.zone2" awsConfig.yml
ansible-playbook -v -i hostInventory -e "user=ec2-user" --become --key-file "keyFilePath" -e "target=zone3" -e "fileName=aws.conf.zone3" awsConfig.yml
```

## Advanced Installation
```
ansible-playbook -u ec2-user --key-file "keyFilePath" -i /etc/ansible/hosts.multiAZ /usr/share/ansible/openshift-ansible/playbooks/prerequisites.yml 
ansible-playbook -u ec2-user --key-file "keyFilePath" -i /etc/ansible/hosts.multiAZ /usr/share/ansible/openshift-ansible/playbooks/deploy_cluster.yml
```

## Create the requires storage classes
```
oc create -f storageClass.yml
```
