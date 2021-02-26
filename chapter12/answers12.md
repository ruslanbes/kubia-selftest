## 12.1. Understanding authentication

## 12.1.1. Users and groups

## 12.1.2. Introducing ServiceAccounts

## 12.1.3. Creating ServiceAccounts

The token is shown in the `Mountable secrets` list when you inspect a ServiceAccount with `kubectl describe`. Let me explain what that list represents. In chapter 7 you learned how to create Secrets and mount them inside a pod. By default, a pod can mount any Secret it wants. But the pod’s ServiceAccount can be configured to limit which Secrets a pod can mount to the list in `Mountable secrets`. To enable this feature, the ServiceAccount must contain the following annotation: `kubernetes.io/enforce-mountable-secrets="true"`

***

A ServiceAccount’s image pull Secrets behave slightly differently than its mountable Secrets. Unlike mountable Secrets, they don’t determine which image pull Secrets a pod can use, but which ones are automatically added to all pods using the Service-Account

## 12.1.4. Assigning a ServiceAccount to a pod

After you create additional ServiceAccounts, you need to assign them to pods. This is done by setting the name of the ServiceAccount in the `spec.serviceAccountName` field in the pod definition

## 12.2. Securing the cluster with role-based access control

## 12.2.1. Introducing the RBAC authorization plugin

## 12.2.2. Introducing RBAC resources

The RBAC authorization rules are configured through four resources, which can be grouped into two groups:
- Roles and ClusterRoles, which specify which verbs can be performed on which resources.
- RoleBindings and ClusterRoleBindings, which bind the above roles to specific users, groups, or ServiceAccounts.

## 12.2.3. Using Roles and RoleBindings

A Role defines what actions can be performed, but it doesn’t specify who can perform them. To do that, you must bind the Role to a subject, which can be a user, a ServiceAccount, or a group of users or ServiceAccounts

***

Instead of creating the service-reader Role from a YAML file, you could also create it with the special `kubectl create role` command. Let’s use this method to create the Role in the bar namespace:

    $ kubectl create role service-reader --verb=get --verb=list --resource=services -n bar

***

Binding Roles to subjects is achieved by creating a RoleBinding resource. To bind the Role to the default ServiceAccount, run the following command:

    $ kubectl create rolebinding test --role=service-reader --serviceAccount=foo:default -n bar

## 12.2.4. Using ClusterRoles and ClusterRoleBindings

## 12.2.5. Understanding default ClusterRoles and ClusterRoleBindings

## 12.2.6. Granting authorization permissions wisely
