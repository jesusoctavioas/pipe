---
title: "Configuration reference"
linkTitle: "Configuration reference"
weight: 8
description: >
  This page describes all configurable fields in the piped configuration.
---

``` yaml
apiVersion: pipecd.dev/v1beta1
kind: Piped
spec:
  projectID: ...
  pipedID: ...
  ...
```

## Piped Configuration

| Field | Type | Description | Required |
|-|-|-|-|
| projectID | string | The identifier of the PipeCD project where this piped belongs to. | Yes |
| pipedID | string | The generated ID for this piped. | Yes |
| pipedKeyFile | string | The path to the file containing the generated key string for this piped. | Yes |
| apiAddress | string | The address used to connect to the control-plane's API. | Yes |
| webAddress | string | The address to the control-plane's Web. | No |
| syncInterval | duration | How often to check whether an application should be synced. Default is `1m`. | No |
| git | [Git](/docs/operator-manual/piped/configuration-reference/#git) | Git configuration needed for Git commands.  | No |
| repositories | [][Repository](/docs/operator-manual/piped/configuration-reference/#gitrepository) | List of Git repositories this piped will handle. | No |
| chartRepositories | [][ChartRepository](/docs/operator-manual/piped/configuration-reference/#chartrepository) | List of Helm chart repositories that should be added while starting up. | No |
| cloudProviders | [][CloudProvider](/docs/operator-manual/piped/configuration-reference/#cloudprovider) | List of cloud providers can be used by this piped. | No |
| analysisProviders | [][AnalysisProvider](/docs/operator-manual/piped/configuration-reference/#analysisprovider) | List of analysis providers can be used by this piped. | No |
| notifications | [Notifications](/docs/operator-manual/piped/configuration-reference/#notifications) | Sending notifications to Slack, Webhook... | No |

## Git

| Field | Type | Description | Required |
|-|-|-|-|
| username | string | The username that will be configured for `git` user. | No |
| email | string | The email that will be configured for `git` user. | No |
| sshConfigFilePath | string | Where to write ssh config file. Default is `etc/ssh/ssh_config`. | No |
| host | string | The host name. Default is `github.com`. | No |
| hostName | string | The hostname or IP address of the remote git server. Default is the same value with Host. | No |
| sshKeyFile | string | The path to the private ssh key file. This will be used to clone the source code of the specified git repositories. | No |

## GitRepository

| Field | Type | Description | Required |
|-|-|-|-|
| repoID | string | Unique identifier to the repository. This must be unique in the piped scope. | Yes |
| remote | string | Remote address of the repository used to clone the source code. e.g. `git@github.com:org/repo.git` | Yes |
| branch | string | The branch will be handled. | Yes |

## ChartRepository

| Field | Type | Description | Required |
|-|-|-|-|
| name | string | The name of the Helm chart repository. Note that is not a Git repository but a [Helm chart repository](https://helm.sh/docs/topics/chart_repository/). | Yes |
| address | string | The address to the Helm chart repository. | Yes |
| username | string | Username used for the repository backed by HTTP basic authentication. | No |
| password | string | Password used for the repository backed by HTTP basic authentication. | No |

## CLoudProvider

| Field | Type | Description | Required |
|-|-|-|-|
| name | string | The name of the cloud provider. | Yes |
| type | string | The cloud provider type. Must be one of the following values:<br>`KUBERNETES`, `TERRAFORM`, `CLOUDRUN`, `LAMBDA`. | Yes |
| config | [CloudProviderConfig](/docs/operator-manual/piped/configuration-reference/#cloudproviderconfig) | Specific configuration for the specified type of cloud provider. | Yes |

## CloudProviderConfig

Must be one of the following structs:

### CloudProviderKubernetesConfig

| Field | Type | Description | Required |
|-|-|-|-|
| masterURL | string | The master URL of the kubernetes cluster. Empty means in-cluster. | No |
| kubeConfigPath | string | The path to the kubeconfig file. Empty means in-cluster. | No |
| appStateInformer | [KubernetesAppStateInformer](/docs/operator-manual/piped/configuration-reference/#kubernetesappstateinformer) | Configuration for application resource informer. | No |

### CloudProviderTerraformConfig

| Field | Type | Description | Required |
|-|-|-|-|
| credentialsDirName | string | The name of directory where to put the configured credentials files. Default is `.terraform-crendentials`. | No |
| credentialsFiles | []string | List of credentials files that will be copied to the CredentialsDir before running terraform commands. | No |
| vars | []string | List of variables that will be set directly on terraform commands with `-var` flag. The variable must be formatted by `key=value`. | No |

### CloudProviderCloudRunConfig

| Field | Type | Description | Required |
|-|-|-|-|
| project | string | The GCP project hosting the CloudRun service. | Yes |
| region | string | The region of running CloudRun service. | Yes |
| credentialsFile | string | The path to the service account file for accessing CloudRun service. | No |

### CloudProviderLambdaConfig

| Field | Type | Description | Required |
|-|-|-|-|

## KubernetesAppStateInformer

| Field | Type | Description | Required |
|-|-|-|-|
| namespace | string | Only watches the specified namespace. Empty means watching all namespaces. | No |
| includeResources | [][KubernetesResourcematcher](/docs/operator-manual/piped/configuration-reference/#kubernetesresourcematcher) | List of resources that should be added to the watching targets. | No |
| excludeResources | [][KubernetesResourcematcher](/docs/operator-manual/piped/configuration-reference/#kubernetesresourcematcher) | List of resources that should be ignored from the watching targets. | No |

## KubernetesResourceMatcher

| Field | Type | Description | Required |
|-|-|-|-|
| apiVersion | string | The APIVersion of the kubernetes resource. | Yes |
| kind | string | The kind name of the kubernetes resource. Empty means all kinds are matching. | No |

## AnalysisProvider

| Field | Type | Description | Required |
|-|-|-|-|
| name | string | The unique name of the analysis provider. | Yes |
| kind | string | The provider type. Currently, only PROMETHEUS is available. | Yes |
| prometheus | [AnalysisProviderPrometheus](/docs/operator-manual/piped/configuration-reference/#analysisproviderprometheus) | Configuration needed to connect to Prometheus. | No |

## AnalysisProviderPrometheus
| Field | Type | Description | Required |
|-|-|-|-|
| address | string | The Prometheus server address. | Yes |
| usernameFile | string | The path to the username file. | No |
| passwordFile | string | The path to the password file. | No |

## Notifications

| Field | Type | Description | Required |
|-|-|-|-|
| routes | [][NotificationRoute](/docs/operator-manual/piped/configuration-reference/#notificationroute) | List of notification routes. | No |
| receivers | [][NotificationReceiver](/docs/operator-manual/piped/configuration-reference/#notificationreceiver) | List of notification receivers. | No |

## NotificationRoute

| Field | Type | Description | Required |
|-|-|-|-|
| name | string | The name of the route. | Yes |
| receiver | string | The name of receiver who will receive all matched events. | Yes |
| events | []string | List of events that should be routed to the receiver. | No |
| ignoreEvents | []string | List of events that should be ignored. | No |
| groups | []string | List of event groups should be routed to the receiver. | No |
| ignoreGroups | []string | List of event groups should be ignored. | No |
| apps | []string | List of applications where their events should be routed to the receiver. | No |
| ignoreApps | []string | List of applications where their events should be ignored. | No |
| envs | []string | List of environments where their events should be routed to the receiver. | No |
| ignoreEnvs | []string | List of environments where their events should be ignored. | No |


## NotificationReceiver

| Field | Type | Description | Required |
|-|-|-|-|
| name | string | The name of the receiver. | Yes |
| slack | [NotificationReciverSlack](/docs/operator-manual/piped/configuration-reference/#notificationreceiverslack) | Configuration for slack receiver. | No |
| webhook | [NotificationReceiverWebhook](/docs/operator-manual/piped/configuration-reference/#notificationreceiverwebhook) | Configuration for webhook receiver. | No |

## NotificationReceiverSlack

| Field | Type | Description | Required |
|-|-|-|-|
| hookURL | string | The hookURL of a slack channel. | Yes |

## NotificationReceiverWebhook

| Field | Type | Description | Required |
|-|-|-|-|
