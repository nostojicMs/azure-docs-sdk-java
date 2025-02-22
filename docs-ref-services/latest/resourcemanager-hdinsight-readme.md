---
title: Azure Resource Manager HDInsight client library for Java
keywords: Azure, java, SDK, API, azure-resourcemanager-hdinsight, hdinsight
author: joshfree
ms.author: jfree
ms.date: 11/17/2022
ms.topic: reference
ms.devlang: java
ms.service: hdinsight
---
# Azure Resource Manager HDInsight client library for Java - version 1.0.0 


Azure Resource Manager HDInsight client library for Java.

This package contains Microsoft Azure SDK for HDInsight Management SDK. HDInsight Management Client. Package tag package-2021-06. For documentation on how to use this package, please see [Azure Management Libraries for Java](https://aka.ms/azsdk/java/mgmt).

## We'd love to hear your feedback

We're always working on improving our products and the way we communicate with our users. So we'd love to learn what's working and how we can do better.

If you haven't already, please take a few minutes to [complete this short survey][survey] we have put together.

Thank you in advance for your collaboration. We really appreciate your time!

## Documentation

Various documentation is available to help you get started

- [API reference documentation][docs]

## Getting started

### Prerequisites

- [Java Development Kit (JDK)][jdk] with version 8 or above
- [Azure Subscription][azure_subscription]

### Adding the package to your product

[//]: # ({x-version-update-start;com.azure.resourcemanager:azure-resourcemanager-hdinsight;current})
```xml
<dependency>
    <groupId>com.azure.resourcemanager</groupId>
    <artifactId>azure-resourcemanager-hdinsight</artifactId>
    <version>1.0.0</version>
</dependency>
```
[//]: # ({x-version-update-end})

### Include the recommended packages

Azure Management Libraries require a `TokenCredential` implementation for authentication and an `HttpClient` implementation for HTTP client.

[Azure Identity][azure_identity] and [Azure Core Netty HTTP][azure_core_http_netty] packages provide the default implementation.

### Authentication

By default, Azure Active Directory token authentication depends on correct configuration of the following environment variables.

- `AZURE_CLIENT_ID` for Azure client ID.
- `AZURE_TENANT_ID` for Azure tenant ID.
- `AZURE_CLIENT_SECRET` or `AZURE_CLIENT_CERTIFICATE_PATH` for client secret or client certificate.

In addition, Azure subscription ID can be configured via `AZURE_SUBSCRIPTION_ID` environment variable.

With above configuration, `azure` client can be authenticated using the following code:

```java
AzureProfile profile = new AzureProfile(AzureEnvironment.AZURE);
TokenCredential credential = new DefaultAzureCredentialBuilder()
    .authorityHost(profile.getEnvironment().getActiveDirectoryEndpoint())
    .build();
HDInsightManager manager = HDInsightManager
    .authenticate(credential, profile);
```

The sample code assumes global Azure. Please change `AzureEnvironment.AZURE` variable if otherwise.

See [Authentication][authenticate] for more options.

## Key concepts

See [API design][design] for general introduction on design and key concepts on Azure Management Libraries.

## Examples

```java
// network
Network network = networkManager.networks().define("vn1")
    .withRegion(REGION)
    .withExistingResourceGroup(resourceGroupName)
    .withAddressSpace("10.0.0.0/24")
    .withSubnet("default", "10.0.0.0/24")
    .create();
Subnet subnet = network.subnets().values().iterator().next();

// storage account
com.azure.resourcemanager.storage.models.StorageAccount storageAccount = storageManager.storageAccounts().define(storageAccountName)
    .withRegion(REGION)
    .withExistingResourceGroup(resourceGroupName)
    .create();
final String storageAccountKey = storageAccount.getKeys().iterator().next().value();

// container
final String containerName = "hdinsight";
storageManager.blobContainers().defineContainer(containerName)
    .withExistingBlobService(resourceGroupName, storageAccountName)
    .withPublicAccess(PublicAccess.NONE)
    .create();

Map<String, Map<String, String>> clusterDefinition = new HashMap<>(1);
Map<String, String> clusterProperties = new HashMap<>(3);
clusterProperties.put("restAuthCredential.isEnabled", "true");
clusterProperties.put("restAuthCredential.username", "admin");
clusterProperties.put("restAuthCredential.password", "Pa$s" + randomPadding());
clusterDefinition.put("gateway", Collections.unmodifiableMap(clusterProperties));

// cluster
Cluster cluster = manager.clusters().define("cluster" + randomPadding())
    .withExistingResourceGroup(resourceGroupName)
    .withRegion(REGION)
    .withProperties(new ClusterCreateProperties()
        .withClusterVersion("3.6")
        .withOsType(OSType.LINUX)
        .withTier(Tier.STANDARD)
        .withClusterDefinition(new ClusterDefinition()
            .withKind("Spark")
            .withConfigurations(Collections.unmodifiableMap(clusterDefinition))
        )
        .withComputeProfile(new ComputeProfile()
            .withRoles(Collections.unmodifiableList(new LinkedList<>(Arrays.asList(
                new Role().withName("headnode")
                    .withTargetInstanceCount(2)
                    .withHardwareProfile(new HardwareProfile()
                        .withVmSize("Large")
                    )
                    .withOsProfile(new OsProfile()
                        .withLinuxOperatingSystemProfile(
                            new LinuxOperatingSystemProfile()
                                .withUsername("sshuser")
                                .withPassword("Pa$s" + randomPadding())
                        )
                    )
                    .withVirtualNetworkProfile(new VirtualNetworkProfile()
                        .withId(network.id())
                        .withSubnet(subnet.id())
                    ),
                new Role().withName("workernode")
                    .withTargetInstanceCount(3)
                    .withHardwareProfile(new HardwareProfile()
                        .withVmSize("Large")
                    )
                    .withOsProfile(new OsProfile()
                        .withLinuxOperatingSystemProfile(
                            new LinuxOperatingSystemProfile()
                                .withUsername("sshuser")
                                .withPassword("Pa$s" + randomPadding())
                        )
                    )
                    .withVirtualNetworkProfile(new VirtualNetworkProfile()
                        .withId(network.id())
                        .withSubnet(subnet.id())
                    )
            ))))
        )
        .withStorageProfile(new StorageProfile()
            .withStorageaccounts(Collections.unmodifiableList(Arrays.asList(
                new StorageAccount()
                    .withName(new URL(storageAccount.endPoints().primary().blob()).getHost())
                    .withKey(storageAccountKey)
                    .withContainer(containerName)
                    .withIsDefault(true)
            )))
        ))
    .create();
```
[Code snippets and samples](https://github.com/Azure/azure-sdk-for-java/blob/azure-resourcemanager-hdinsight_1.0.0/sdk/hdinsight/azure-resourcemanager-hdinsight/SAMPLE.md)


## Troubleshooting

## Next steps

## Contributing

For details on contributing to this repository, see the [contributing guide][cg].

This project welcomes contributions and suggestions. Most contributions require you to agree to a Contributor License Agreement (CLA) declaring that you have the right to, and actually do, grant us the rights to use your contribution. For details, visit <https://cla.microsoft.com>.

When you submit a pull request, a CLA-bot will automatically determine whether you need to provide a CLA and decorate the PR appropriately (e.g., label, comment). Simply follow the instructions provided by the bot. You will only need to do this once across all repositories using our CLA.

This project has adopted the [Microsoft Open Source Code of Conduct][coc]. For more information see the [Code of Conduct FAQ][coc_faq] or contact <opencode@microsoft.com> with any additional questions or comments.

<!-- LINKS -->
[survey]: https://microsoft.qualtrics.com/jfe/form/SV_ehN0lIk2FKEBkwd?Q_CHL=DOCS
[docs]: https://azure.github.io/azure-sdk-for-java/
[jdk]: /java/azure/jdk/
[azure_subscription]: https://azure.microsoft.com/free/
[azure_identity]: https://github.com/Azure/azure-sdk-for-java/blob/azure-resourcemanager-hdinsight_1.0.0/sdk/identity/azure-identity
[azure_core_http_netty]: https://github.com/Azure/azure-sdk-for-java/blob/azure-resourcemanager-hdinsight_1.0.0/sdk/core/azure-core-http-netty
[authenticate]: https://github.com/Azure/azure-sdk-for-java/blob/azure-resourcemanager-hdinsight_1.0.0/sdk/resourcemanager/docs/AUTH.md
[design]: https://github.com/Azure/azure-sdk-for-java/blob/azure-resourcemanager-hdinsight_1.0.0/sdk/resourcemanager/docs/DESIGN.md
[cg]: https://github.com/Azure/azure-sdk-for-java/blob/azure-resourcemanager-hdinsight_1.0.0/CONTRIBUTING.md
[coc]: https://opensource.microsoft.com/codeofconduct/
[coc_faq]: https://opensource.microsoft.com/codeofconduct/faq/

