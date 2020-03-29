---
title: CI/CD para Nuvem de Primavera Do Azure
description: CI/CD para Nuvem de Primavera Do Azure
author: bmitchell287
ms.service: spring-cloud
ms.topic: conceptual
ms.date: 10/04/2019
ms.author: brendm
ms.openlocfilehash: f329fb5472c5a2eab6f22a2e81b19d90e7045330
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "76278522"
---
# <a name="cicd-for-azure-spring-cloud"></a>CI/CD para Nuvem de Primavera Do Azure

A integração contínua e as ferramentas de entrega contínua permitem que os desenvolvedores implantem rapidamente atualizações para aplicativos existentes com o mínimo de esforço e risco. O Azure DevOps ajuda você a organizar e controlar esses trabalhos-chave. Atualmente, o Azure Spring Cloud não oferece um plugin Azure DevOps específico.  No entanto, você pode integrar seus aplicativos spring cloud com DevOps usando uma [tarefa Azure CLI](https://docs.microsoft.com/azure/devops/pipelines/tasks/deploy/azure-cli?view=azure-devops). Este artigo mostrará como usar uma tarefa azure CLI com o Azure Spring Cloud para se integrar ao Azure DevOps.

## <a name="create-an-azure-resource-manager-service-connection"></a>Crie uma conexão de serviço do Azure Resource Manager

Leia [este artigo](https://docs.microsoft.com/azure/devops/pipelines/library/connect-to-azure?view=azure-devops) para aprender como criar uma conexão de serviço do Azure Resource Manager ao seu projeto Azure DevOps. Certifique-se de selecionar a mesma assinatura que você está usando para a sua instância de serviço Azure Spring Cloud.

## <a name="azure-cli-task-templates"></a>Modelos de tarefas Azure CLI

### <a name="deploy-artifacts"></a>Implantar artefatos

Você pode construir e implantar seus `tasks`projetos usando uma série de . Este trecho define primeiro uma tarefa Maven para construir o aplicativo, seguido de uma segunda tarefa que implanta o arquivo JAR usando a extensão CLI Do Azure Spring Cloud Azure.

```yaml
steps:
- task: Maven@3
  inputs:
    mavenPomFile: 'pom.xml'
- task: AzureCLI@1
  inputs:
    azureSubscription: <your service connection name>
    scriptLocation: inlineScript
    inlineScript: |
      az extension add -y --name spring-cloud
      az spring-cloud app deploy --resource-group <your-resource-group> --service <your-spring-cloud-service> --name <app-name> --jar-path ./target/your-result-jar.jar
      # deploy other app
```

### <a name="deploy-from-source"></a>Implantar a partir da fonte

É possível implantar diretamente no Azure sem uma etapa de compilação separada.

```yaml
- task: AzureCLI@1
  inputs:
    azureSubscription: <your service connection name>
    scriptLocation: inlineScript
    inlineScript: |
      az extension add -y --name spring-cloud
      az spring-cloud app deploy --resource-group <your-resource-group> --service <your-spring-cloud-service> --name <app-name>

      # or if it is a multi-module project
      az spring-cloud app deploy --resource-group <your-resource-group> --service <your-spring-cloud-service> --name <app-name> --target-module relative/path/to/module
```
