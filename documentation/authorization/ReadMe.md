# General Info about OpenShift Authorization

## Two levels of RBAC control authorization
1. Cluster RBAC: Roles and bindings that are applicable across all projects
2. Local RBAC:: Roles and bindings that are scoped to a given project

## Rolebinding
1. Local role bindings can reference both cluster and local roles
2. Cluster role bindings can only reference cluster roles
3. Both cluster and local bindings used during evaluation

## Order of Authorization evaluation:
1. Cluster-wide allow rules checked
2. Locally bound allow rules checked
3. Deny by default

## Service Account Naming Convention:
1. ServiceAccount username derived from project and name: **system:serviceaccount:project:name**

## Service account Membership:
Every Service account is also a member of two groups:
1. system:serviceaccounts: Includes all service accounts in system
2. system:serviceaccounts:<project>: Includes all service accounts in a specified project

```
To allow all service accounts in all projects to view resources in monitored-project:
$ oc policy add-role-to-group view system:serviceaccounts -n monitored-project
	
To allow all service accounts in monitor project to edit resources in monitored-project:
$ oc policy add-role-to-group edit system:serviceaccounts:monitor -n monitored-project
```

## SCC:
Security context constraints (SCCs), in contrast, control:
1. Actions that pod can perform
2. What pod can access

SCCs let administrator control:
1. Use of host directories as volumes
2. SELinux context of container
3. User ID
4. Use of host namespaces and networking
5. Allocating FSGroup that owns pod’s volumes
6. Configuring allowable supplemental groups
7. Requiring use of read-only root file system
8. Usage of volume types
9. Configuring allowable secure computing mode (seccomp) profiles
10. Capabilities container can request to be added

## Check authorization:
To determine if you can perform a specific verb:
```
oc policy can-i <verb> <resource>
```

To determine which verbs you can perform against all namespace-scoped resources
```
oc policy can-i --list --loglevel=8
```

Indicate which users can perform an action on a resource.
```
oc policy who-can <verb> <resoruce>
```

## Impersonation
User can impersonate another user or a service account if the authorization to impersonate is granted

If User A is requesting to impersonate service account (system:serviceaccount:namespace:name):
1. OpenShift checks to ensure that User A can impersonate serviceaccount named name in namespace
2. If check fails, request fails with 403 (Forbidden) error code

To impersonate:
1. via CLI use --as=username flag
2. via REST use Impersonate-User header


**sudoers** role allows user to impersonate system:admin, which has cluster administrator permissions
1. For example, oc delete nodes --all is forbidden, but oc delete nodes --all as system:admin is allowed
2. Can add user to group using oadm policy add-cluster-role-to-user sudoer <username>

## Ref Diagrams:
![Alt text](rbacbinding.png?raw=true "")
