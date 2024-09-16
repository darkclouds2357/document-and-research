Here’s a cheat sheet for the Argo CD CLI commands (`argocd`). Argo CD is a declarative GitOps tool for Kubernetes.

### 1. **General Commands**

- **Login to Argo CD**:
  ```bash
  argocd login <argocd-server>
  ```

- **Logout from Argo CD**:
  ```bash
  argocd logout <argocd-server>
  ```

- **View Argo CD version**:
  ```bash
  argocd version
  ```

- **Get the current user info**:
  ```bash
  argocd account get-user-info
  ```

- **Change password**:
  ```bash
  argocd account update-password
  ```

- **Display help for any command**:
  ```bash
  argocd help <command>
  ```

---

### 2. **Applications**

- **List all applications**:
  ```bash
  argocd app list
  ```

- **Get application details**:
  ```bash
  argocd app get <app-name>
  ```

- **Create a new application**:
  ```bash
  argocd app create <app-name> \
    --repo <repo-url> \
    --path <path-in-repo> \
    --dest-server <k8s-cluster-url> \
    --dest-namespace <namespace> \
    --sync-policy automated
  ```

- **Sync an application** (apply desired state from Git):
  ```bash
  argocd app sync <app-name>
  ```

- **Refresh an application's state**:
  ```bash
  argocd app refresh <app-name>
  ```

- **Delete an application**:
  ```bash
  argocd app delete <app-name>
  ```

- **Update an application**:
  ```bash
  argocd app set <app-name> --<parameter>=<value>
  ```

- **Pause an application** (stop auto-sync):
  ```bash
  argocd app set <app-name> --sync-policy none
  ```

- **Resume an application** (start auto-sync):
  ```bash
  argocd app set <app-name> --sync-policy automated
  ```

- **View application logs**:
  ```bash
  argocd app logs <app-name>
  ```

- **Terminate in-progress sync or operation**:
  ```bash
  argocd app terminate-op <app-name>
  ```

---

### 3. **Repositories**

- **List all repositories**:
  ```bash
  argocd repo list
  ```

- **Add a new repository**:
  ```bash
  argocd repo add <repo-url> --username <username> --password <password>
  ```

- **Remove a repository**:
  ```bash
  argocd repo rm <repo-url>
  ```

- **Test repository connection**:
  ```bash
  argocd repo get <repo-url>
  ```

---

### 4. **Clusters**

- **List all clusters**:
  ```bash
  argocd cluster list
  ```

- **Add a new cluster**:
  ```bash
  argocd cluster add <context-name>
  ```

- **Remove a cluster**:
  ```bash
  argocd cluster rm <server-url>
  ```

---

### 5. **Projects**

- **List all projects**:
  ```bash
  argocd proj list
  ```

- **Get project details**:
  ```bash
  argocd proj get <proj-name>
  ```

- **Create a new project**:
  ```bash
  argocd proj create <proj-name>
  ```

- **Delete a project**:
  ```bash
  argocd proj delete <proj-name>
  ```

- **Allow a project to use a specific repository**:
  ```bash
  argocd proj allow-cluster-resource <proj-name> <group> <kind>
  ```

---

### 6. **User Management**

- **List all users**:
  ```bash
  argocd account list
  ```

- **Create a user** (for local users):
  ```bash
  argocd account create <username>
  ```

- **Delete a user**:
  ```bash
  argocd account delete <username>
  ```

- **Add user to a project**:
  ```bash
  argocd proj role add-policy <proj-name> <role-name> --action <action> --permission allow
  ```

- **Change a user's password**:
  ```bash
  argocd account update-password --account <username>
  ```

---

### 7. **Sync and Operations**

- **Manually sync an application**:
  ```bash
  argocd app sync <app-name>
  ```

- **Force sync an application** (overrides manual changes in the cluster):
  ```bash
  argocd app sync <app-name> --force
  ```

- **Rollback to a previous revision**:
  ```bash
  argocd app rollback <app-name> <revision>
  ```

- **View history of an application**:
  ```bash
  argocd app history <app-name>
  ```

- **Terminate an in-progress operation (sync, rollback, etc.)**:
  ```bash
  argocd app terminate-op <app-name>
  ```

---

### 8. **Access Control**

- **Create an access policy** (RBAC):
  ```bash
  argocd proj role add-policy <proj-name> <role-name> --action <action> --permission allow
  ```

- **List access policies**:
  ```bash
  argocd proj role list-policies <proj-name>
  ```

- **Remove access policy**:
  ```bash
  argocd proj role remove-policy <proj-name> <role-name> --action <action>
  ```

- **Bind a role to a user**:
  ```bash
  argocd account add-token --account <user> --role <role>
  ```

---

### 9. **Web Interface & Authentication**

- **Open the Argo CD UI** (browser):
  ```bash
  argocd app open <app-name>
  ```

- **Authenticate using SSO (Single Sign-On)**:
  ```bash
  argocd account generate-token --sso
  ```

---

### 10. **Miscellaneous**

- **Watch the live status of an application**:
  ```bash
  argocd app wait <app-name>
  ```

- **View application manifests** (YAML):
  ```bash
  argocd app manifests <app-name>
  ```

- **Edit an application configuration**:
  ```bash
  argocd app edit <app-name>
  ```

---

### 11. **Troubleshooting and Debugging**

- **Get logs of an application**:
  ```bash
  argocd app logs <app-name>
  ```

- **Check health status of a resource**:
  ```bash
  argocd app health <app-name>
  ```

- **Get the diff between the live and desired state**:
  ```bash
  argocd app diff <app-name>
  ```

---

This cheat sheet should cover most of the commonly used Argo CD CLI commands to manage applications, clusters, repositories, users, and access control. These commands help you operate and troubleshoot GitOps workflows with ease!