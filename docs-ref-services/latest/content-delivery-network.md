---
title: Azure Content Delivery Network SDK for Java
description: Reference for Azure Content Delivery Network SDK for Java
author: joshfree
ms.author: jfree
ms.data: 03/16/2023
ms.topic: reference
ms.devlang: java
ms.service: contentdeliverynetwork
ms.date: 07/08/2022
---
# Azure CDN libraries for Java

## Overview

Cache static web content at strategically placed locations to provide maximum throughput for users with [Azure Content Delivery Network](/azure/cdn/cdn-overview) (CDN).

To get started with Azure CDN, see [Getting started with Azure CDN](/azure/cdn/cdn-create-new-endpoint).

## Management API

Create CDN profiles, define endpoints, and add content to the CDN using the management API.

[Add a dependency](https://maven.apache.org/guides/getting-started/index.html#How_do_I_use_external_dependencies) to your Maven `pom.xml` file to use the management API in your project.

```XML
<dependency>
    <groupId>com.microsoft.azure</groupId>
    <artifactId>azure-mgmt-cdn</artifactId>
    <version>1.3.0</version>
</dependency>
```   

### Example

Create a CDN profile, assign endpoints, and load content into the CDN.

```java
CdnProfile profile = azure.cdnProfiles().define("testCDN")
        .withRegion(Region.US_EAST)
        .withNewResourceGroup()
        .withStandardVerizonSku()
        .withNewEndpoint("webAppHostname1")
        .withNewEndpoint("webAppHostname2")
        .create();

List<String> contentList = new ArrayList<String>();
contentList.add("/client.js");
contentList.add("/media/fabrikam_logo.png");

for (CdnEndpoint endpoint : profile.endpoints().values()) {
    endpoint.loadContent(contentList);
}
```

> [!div class="nextstepaction"]
> [Explore the Management APIs](/java/api/overview/azure/cdn/management)

## Samples

[Manage CDNs with Java](https://github.com/Azure-Samples/cdn-java-manage-cdn)