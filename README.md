## Introduction

Many organizations that have already implemented a DevOps culture are looking to further accelerate their development process by adopting GitOps practices in their environments. There is a lot to take into consideration when planning out your GitOps strategy.

## What is GitOps?

GitOps is a set of best practices applied from the beginning of the development workflow, all the way to deployment.
Developers already use Git for the source code of the application – GitOps extends this practice to an application’s configuration, infrastructure, and operational procedures. All changes to applications and infrastructure are described in a source control system and automatically synchronized with the live environment.

## Trusted by Enterprise Companies

Selecting the right tool for your organization is an important step. One of the most popular GitOps tools you’ll find in your searches is Argo CD. The [2021 CNCF annual survey reported on the 115% year-on-year increase](https://blog.argoproj.io/cncf-argo-project-2022-user-survey-results-f9caf46df7fd) in Argo CD’s in-production use. The increase in popularity of Argo CD means that it comes up a lot when organizations are discussing the adoption of GitOps.

Argo CD is also trusted by various enterprises across all verticals. Companies like Adobe, Blackrock, Capital One, Google, IBM, Red Hat, and many others have used and contributed to Argo CD and the Argo Project.

**Organizations who have officially adopted Argo CD can be found [here](https://github.com/argoproj/argo-cd/blob/master/USERS.md).**

## What Is Argo CD?

Argo CD is a declarative, GitOps continuous delivery tool for Kubernetes. Argo CD can pull updated code from Git repositories and deploy it directly to Kubernetes resources. It enables developers to manage both infrastructure configuration and application updates in one system.

![Argo CD](https://user-images.githubusercontent.com/7479571/232259047-f34941a4-147d-4406-8a69-334f32057edb.png)


### How it works

Argo CD follows the GitOps pattern of using Git repositories as the source of truth for defining the desired application state. Kubernetes manifests can be specified in several ways:

- kustomize applications
- helm charts
- jsonnet files
- Plain directory of YAML/json manifests
- Any custom config management tool configured as a config management plugin

Argo CD automates the deployment of the desired application states in the specified target environments. 

## GitOps with Argo CD

GitOps is a software engineering practice that uses a Git repository as its single source of truth. A basic part of the GitOps process is a pull request. New versions of a configuration are introduced via pull request, merged with the main branch in the Git repository, and then the new version is automatically deployed. The Git repository contains a full record of all changes, including all details of the environment at every stage of the process.

At a high level, the Argo CD process works like this:

- A developer makes changes to an application, pushing a new version of Kubernetes resource definitions to a Git repo.
- Continuous integration is triggered, resulting in a new container image saved to a registry. 
- A developer issues a pull request, changing Kubernetes manifests, which are created either manually or automatically.
- The pull request is reviewed and changes are merged to the main branch. This triggers a webhook which tells Argo CD a change was made.
- Argo CD clones the repo and compares the application state with the current state of the Kubernetes cluster. It applies the required changes to cluster configuration.
- Kubernetes uses its controllers to reconcile the changes required to cluster resources, until it achieves the desired configuration.
- Argo CD monitors progress and when the Kubernetes cluster is ready, reports that the application is in sync.
- ArgoCD also works in the other direction, monitoring changes in the Kubernetes cluster and discarding them if they don’t match the current configuration in Git.


## Argo CD Best Practices

- **Separating Source Code and Configuration Repositories:**

  It is best to use separate Git repositories for Kubernetes manifests and application source code. Maintaining separation between source code and config repos makes them more manageable, enabling modification of one without affecting the other. Another reason to keep repos separate is to maintain cleaner logs for auditing purposes. Separate repos help reduce the noise from regular development activity and make it easier to trace the Git history. 
  
  ![Separate your Git repositories](https://codefresh.io/wp-content/uploads/2022/03/word-image-20220330-174003.png)

- **Suitable Number of Deployment Configuration Repositories:**

  It is important to consider how many repos should house an organization’s deployment configurations.
 
  - Generally, small companies that don’t rely heavily on automation, and where all employees are trusted, can use a mono-repo. 
  - Mid-sized companies that use some automation should use a repository for each team.
  - Larger organizations that require greater control and rely significantly on automation should use repositories for each service.
  
   Teams can often manage themselves if they have their own repository. Each team can decide who has release access, so there is no need for a central team that gives write access to each team, which may create a release bottleneck.

- **Testing Manifests Before Each Commit:**

  Testing changes before pushing them to a manifest helps prevent the introduction of issues into pre-production. Typically, the agent uses a **Helm chart** or other template to generate the manifests. Engineers can run commands locally to test their manifests before they commit any changes. 

- **Role-based access control for different clusters**

   Create an ‘argocd’ folder in your configuration repository for each cluster and create an Argo CD Application manifest for each app in the cluster’s repository. By creating the separate ‘argocd’ folder, you can also implement role-based access control for different clusters if you wish with Git repository permissions.

- **A different repository for every project**

  Never put any independent applications or applications managed by different teams in the same repository. If the project is too large, you should definitely divide your repository. Otherwise, it gets hard to manage.
  
- **Group your applications**

  Group your applications with `ApplicationSets`
  
  ![ApplicationSet](https://user-images.githubusercontent.com/7479571/232258712-771e0413-e4ec-48fb-9cef-e7ed636e26e5.png)

- **Managing ten or fewer applications**

  When managing ten or fewer applications, it’s best to use the `App of Apps` pattern. `App of App`s was the pioneer before `ApplicationSets` that allowed us to deploy multiple applications at once with ease.

- **Externalizing your secrets from your Git repository**
  
  Secrets should not be on the Argo CD. It is not a safe solution. nstead of secret management on Argo CD, it could be good to use secret management tools like Haschicorp Vault.
  [Argo CD Vault plugi](Argo CD Vault plugin): This solution creates debate about whether it’s GitOps or not. Nonetheless, it’s a solution using Kubernetes auth in Vault. The Argo CD repo server authorizes Vault to use the service account token in the secret manifest, substituting the required value and creating a secret for you

- **Increase automation for your system with the other Argo projects**

  Other Argo Projects:
 
  - `Argo Rollouts`: Progressive delivery is a set of practices that roll out new features gradually instead of all at once. Argo Rollouts provide advanced deployment capabilities and rolling updates for progressive delivery approaches you might already know, such as blue-green, canary, etc.
  - `Argo Workflows`: Workflows allow you to build and orchestrate parallel jobs and utilize a pipeline on Kubernetes.
  - `Argo Events`: Events is an event-driven workflow automation framework that is used with Kubernetes.

### Steps:

- You create a feature branch;
- CI/CD system runs tests, lint, and maybe pushes the snapshot version of the image (CI/CD system here is not Argo CD, Argo CD can’t do any of this);
- You merge the branch (after code review, approvals, etc.);
- CI/CD system does the same and pushes the new production-ready image version to the registry;
- As the last step, CI/CD system pushes a new commit into another repo, “my-application-config”, with an update to the current version (here you can customize it to create a Pull Request on this config repo, in case you can’t have continuous deployment);
- Argo CD applies the code from the my-application-config repo to the cluster (after Pull Request to the config repo is merged, in case you can’t have continuous deployment).


## Note 

Argo CD will not replace your Jenkins, Gitlab pipleines, GitHub Actions or or any other job execution system. Argo CD can’t lint, can’t build, can’t run tests. Argo CD is GitOps tool that only used for Continuous Delivery/Deployment.

### Development Status

Argo CD is being actively developed by the community. Our releases can be found [here](https://github.com/argoproj/argo-cd/releases).

### Security Considerations

- [Security](https://argo-cd.readthedocs.io/en/stable/operator-manual/security/)
- [Overview of past and current CVE issues](https://argo-cd.readthedocs.io/en/stable/security_considerations/)
- [Security Advisories](https://github.com/argoproj/argo-cd/security/advisories)
