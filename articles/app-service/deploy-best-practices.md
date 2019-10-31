---
title: Deployment best practices - Azure App Service | Microsoft Docs
description: Learn about the key components of deploying to Azure App Service.
keywords: azure app service, web app, deploy, deployment, pipelines, build
services: app-service
documentationcenter: ''
author: jasonfreeberg
manager: ''
editor: ''
ms.assetid: bb51e565-e462-4c60-929a-2ff90121f41d
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 07/31/2019
ms.author: jafreebe
ms.custom: ''
ms.openlocfilehash: 121ea4b7e29510ef86b61350ed97ffca5d133d56
ms.sourcegitcommit: fa5ce8924930f56bcac17f6c2a359c1a5b9660c9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/31/2019
ms.locfileid: "73199480"
---
# <a name="deployment-best-practices"></a>Deployment Best Practices

Every development team has unique requirements that can make implementing an efficient deployment pipeline difficult on any cloud service. This article introduces the three main components of deploying to App Service: deployment sources, build pipelines, and deployment mechanisms. This article also covers some best practices and tips for specific language stacks.

## <a name="deployment-components"></a>Deployment Components

### <a name="deployment-source"></a>Deployment Source

A deployment source is the location of your application code. For production apps, the deployment source is usually a repository hosted by version control software such as [GitHub, BitBucket, or Azure Repos](deploy-continuous-deployment.md). For development and test scenarios, the deployment source may be [a project on your local machine](deploy-local-git.md). App Service also supports [OneDrive and Dropbox folders](deploy-content-sync.md) as deployment sources. While cloud folders can make it easy to get started with App Service, it is not typically recommended to use this source for enterprise-level production applications. 

### <a name="build-pipeline"></a>Build Pipeline

Once you decide on a deployment source, your next step is to choose a build pipeline. A build pipeline reads your source code from the deployment source and executes a series of steps (such as compiling code, minifying HTML and JavaScript, running tests, and packaging components) to get the application in a runnable state. The specific commands executed by the build pipeline depend on your language stack. These operations can be executed on a build server such as Azure Pipelines, or executed locally.

### <a name="deployment-mechanism"></a>Deployment Mechanism

The deployment mechanism is the action used to put your built application into the */home/site/wwwroot* directory of your web app. The */wwwroot* directory is a mounted storage location shared by all instances of your web app. When the deployment mechanism puts your application in this directory, your instances receive a notification to sync the new files. App Service supports the following deployment mechanisms:

- Kudu endpoints: [Kudu](https://github.com/projectkudu/kudu/wiki) is the open-source developer productivity tool that runs as a separate process in Windows App Service, and as a second container in Linux App Service. Kudu handles continuous deployments and provides HTTP endpoints for deployment, such as zipdeploy.
- FTP and WebDeploy: Using your [site or user credentials](deploy-configure-credentials.md), you can upload files [via FTP](deploy-ftp.md) or WebDeploy. These mechanisms do not go through Kudu.  

Deployment tools such as Azure Pipelines, Jenkins, and editor plugins use one of these deployment mechanisms.

## <a name="language-specific-considerations"></a>Language-Specific Considerations

### <a name="java"></a>Java

Use the Kudu [zipdeploy/](deploy-zip.md) API for deploying JAR applications, and [wardeploy/](deploy-zip.md#deploy-war-file) for WAR apps. If you are using Jenkins, you can use those APIs directly in your deployment phase. Para obter mais informações, consulte [este artigo](../jenkins/execute-cli-jenkins-pipeline.md).

### <a name="node"></a>Nó

By default, Kudu executes the build steps for your Node application (`npm install`). If you are using a build service such as Azure DevOps, then the Kudu build is unnecessary. To disable the Kudu build, create an app setting, `SCM_DO_BUILD_DURING_DEPLOYMENT`, with a value of `false`.

### <a name="net"></a>.NET 

By default, Kudu executes the build steps for your .Net application (`dotnet build`). If you are using a build service such as Azure DevOps, then the Kudu build is unnecessary. To disable the Kudu build, create an app setting, `SCM_DO_BUILD_DURING_DEPLOYMENT`, with a value of `false`.

## <a name="other-deployment-considerations"></a>Other Deployment Considerations

### <a name="use-deployment-slots"></a>Use deployment slots

Whenever possible, use [deployment slots](deploy-staging-slots.md) when deploying a new production build. When using a Standard App Service Plan tier or better, you can deploy your app to a staging environment, validate your changes, and do smoke tests. When you are ready, you can swap your staging and production slots. The swap operation warms up the necessary worker instances to match your production scale, thus eliminating downtime. 

### <a name="local-cache"></a>Cache Local

O conteúdo do Serviço de Aplicativo do Azure é armazenado no Armazenamento do Microsoft Azure e exibido de forma duradoura como um compartilhamento de conteúdo. However, some apps just need a high-performance, read-only content store that they can run with high availability. These apps can benefit from using [local cache](overview-local-cache.md). Local cache is not recommended for content management sites such as WordPress.

Always use local cache in conjunction with [deployment slots](deploy-staging-slots.md) to prevent downtime. See [this section](overview-local-cache.md#best-practices-for-using-app-service-local-cache) for information on using these features together.

### <a name="high-cpu-or-memory"></a>High CPU or Memory

Se o plano do serviço de aplicativo estiver usando mais de 90% da memória ou CPU disponível, a máquina virtual subjacente poderá ter problemas para processar sua implantação. Quando isso acontece, expanda temporariamente sua contagem de instâncias para executar a implantação. Depois que a implantação for concluída, você poderá retornar a contagem de instâncias para seu valor anterior.
