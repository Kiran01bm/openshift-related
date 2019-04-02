## General Notes:

Prometheus has two replicas and Alertmanager has three replicas, which amounts to five PVs.
Unless you use dynamically-provisioned storage, you need to make sure you have a persistent volume (PV) ready to be claimed by the PVC, one PV for each replica.
