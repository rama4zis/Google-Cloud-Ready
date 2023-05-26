# 1. Check the firewall rules. Remove the overly permissive rules.

```bash
gcloud compute firewall-rules delete open-access
```

# 2. Navigate to Compute Engine in the Cloud Console and identify the bastion host. The instance should be stopped. Start the instance.

Lookup the instance name in the VM instances and see your bastion server zone. Then start the instance.
```bash
gcloud compute instances start bastion --zone us-east1-b
```

# 3. The bastion host is the one machine authorized to receive external SSH traffic. Create a firewall rule that allows SSH (tcp/22) from the IAP service. The firewall rule must be enabled for the bastion host instance using a network tag of 

Remember to match the zone of the bastion server.
```bash
gcloud compute firewall-rules create <SSH IAP network tag> --allow=tcp:22 --source-ranges 35.235.240.0/20 --target-tags <SSH IAP network tag> --network acme-vpc

gcloud compute instances add-tags bastion --tags=<SSH IAP network tag> --zone=us-east1-b
```

# 4 The juice-shop server serves HTTP traffic. Create a firewall rule that allows traffic on HTTP (tcp/80) to any address. The firewall rule must be enabled for the juice-shop instance using a network tag of <HTTP network tag>.

Match the zone of the juice-shop server.
```bash
gcloud compute firewall-rules create <HTTP network tag> --allow=tcp:80 --source-ranges 0.0.0.0/0 --target-tags <HTTP network tag> --network acme-vpc

gcloud compute instances add-tags juice-shop --tags=<HTTP network tag> --zone=us-east1-b
```

# 5. You need to connect to juice-shop from the bastion using SSH. Create a firewall rule that allows traffic on SSH (tcp/22) from acme-mgmt-subnet network address. The firewall rule must be enabled for the juice-shop instance using a network tag of <SSH internal network tag>.

```bash
gcloud compute firewall-rules create <SSH internal network tag> --allow=tcp:22 --source-ranges 192.168.10.0/24 --target-tags <SSH internal network tag> --network acme-vpc

gcloud compute instances add-tags juice-shop --tags=<SSH internal network tag> --zone=us-east1-b
```

# 6. In the Compute Engine instances page, click the SSH button for the bastion host. Once connected, SSH to juice-shop

1.  Open VM instances in the Compute Engine page.
2.  Click the SSH button for the bastion host.
3.  Once connected, SSH to juice-shop
```bash
ssh <INTERNAL IP OF juice-shop>
```