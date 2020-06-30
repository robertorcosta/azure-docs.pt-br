---
title: Exemplos de modelo do Azure Resource Manager
description: Encontre amostras de modelo do Azure Resource Manager para alguns dos cenários comuns do Serviço de Aplicativo. Saiba como automatizar suas tarefas de implantação ou gerenciamento do Serviço de Aplicativo.
author: tfitzmac
tags: azure-service-management
ms.topic: sample
ms.date: 01/04/2019
ms.author: tomfitz
ms.custom: mvc, fasttrack-edit
ms.openlocfilehash: 2caaadd0da9d62128d04962fa1f2ff7eade907b0
ms.sourcegitcommit: bf99428d2562a70f42b5a04021dde6ef26c3ec3a
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/23/2020
ms.locfileid: "85254422"
---
# <a name="azure-resource-manager-templates-for-app-service"></a>Modelos do Azure Resource Manager para o Serviço de Aplicativo

A tabela a seguir inclui links para modelos do Azure Resource Manager para o Serviço de Aplicativo do Azure. Para obter recomendações sobre como evitar erros comuns ao criar modelos de aplicativo, confira [Diretrizes de implantação de aplicativos com modelos do Azure Resource Manager](deploy-resource-manager-template.md).

Para saber mais sobre a sintaxe JSON e as propriedades dos recursos de Serviços de Aplicativos, confira [Tipos de recurso de Microsoft.Web](/azure/templates/microsoft.web/allversions).

| | |
|-|-|
|**Implantando um aplicativo**||
| [Plano do Serviço de Aplicativo e aplicativo básico do Linux](https://github.com/Azure/azure-quickstart-templates/tree/master/101-webapp-basic-linux) | Implanta um aplicativo do Serviço de Aplicativo configurado para o Linux. |
| [Plano do Serviço de Aplicativo e aplicativo básico do Windows](https://github.com/Azure/azure-quickstart-templates/tree/master/101-webapp-basic-windows) | Implanta um aplicativo do Serviço de Aplicativo configurado para o Windows. |
| [Aplicativo vinculado a um repositório GitHub](https://github.com/Azure/azure-quickstart-templates/tree/master/201-web-app-github-deploy)| Implanta um aplicativo do Serviço de Aplicativo que efetua pull do código por meio do GitHub. |
| [Aplicativo com slots de implantação personalizados](https://github.com/Azure/azure-quickstart-templates/tree/master/101-webapp-custom-deployment-slots)| Implanta um aplicativo do Serviço de Aplicativo com ambientes/slots de implantação personalizados. |
|**Configurando um aplicativo**||
| [Certificado de aplicativo do Key Vault](https://github.com/Azure/azure-quickstart-templates/tree/master/201-web-app-certificate-from-key-vault)| Implanta um certificado de aplicativo do Serviço de Aplicativo de um segredo do Azure Key Vault e o usa para uma associação TLS/SSL. |
| [Aplicativo com um domínio personalizado e o SSL](https://github.com/Azure/azure-quickstart-templates/tree/master/201-web-app-custom-domain-and-ssl)| Implanta um aplicativo do Serviço de Aplicativo com um nome do host personalizado e obtém o certificado de aplicativo do Key Vault para uma associação TLS/SSL. |
| [Aplicativo com uma extensão do GoLang](https://github.com/Azure/azure-quickstart-templates/tree/master/101-webapp-with-golang)| Implanta um aplicativo do Serviço de Aplicativo com a extensão de site do Golang. Assim, você pode executar aplicativos Web desenvolvidos em GoLang no Azure. |
| [Aplicativo com Java 8 e Tomcat 8](https://github.com/Azure/azure-quickstart-templates/tree/master/201-web-app-java-tomcat)| Implanta um aplicativo do Serviço de Aplicativo com o Java 8 e o Tomcat 8 habilitados. Assim, você pode executar aplicativos Java no Azure. |
| [Aplicativo com integração VNet regional](https://github.com/Azure/azure-quickstart-templates/tree/master/101-app-service-regional-vnet-integration)| Implanta um aplicativo do Serviço de Aplicativo com integração VNet regional habilitada. |
|**Como proteger um aplicativo**||
| [Aplicativo integrado ao Gateway de Aplicativo do Azure](https://github.com/Azure/azure-quickstart-templates/tree/master/201-web-app-with-app-gateway-v2)| Implanta um aplicativo do Serviço de Aplicativo e um Gateway de Aplicativo e isola o tráfego usando restrições de acesso e ponto de extremidade de serviço. |
|**Aplicativo do Linux com recursos conectados**||
| [Aplicativo no Linux com MySQL](https://github.com/Azure/azure-quickstart-templates/tree/master/101-webapp-linux-managed-mysql) | Implanta um aplicativo do Serviço de Aplicativo no Linux com o Banco de Dados do Azure para MySQL. |
| [Aplicativo no Linux com PostgreSQL](https://github.com/Azure/azure-quickstart-templates/tree/master/101-webapp-linux-managed-postgresql) | Implanta um aplicativo do Serviço de Aplicativo no Linux com o Banco de Dados do Azure para PostgreSQL. |
|**Aplicativo com recursos conectados**||
| [Aplicativo com MySQL](https://github.com/Azure/azure-quickstart-templates/tree/master/101-webapp-managed-mysql)| Implanta um aplicativo do Serviço de Aplicativo no Windows com o Banco de Dados do Azure para MySQL. |
| [Aplicativo com PostgreSQL](https://github.com/Azure/azure-quickstart-templates/tree/master/101-webapp-managed-postgresql)| Implanta um aplicativo do Serviço de Aplicativo no Windows com o Banco de Dados do Azure para PostgreSQL. |
| [Aplicativo com um banco de dados no Banco de Dados SQL do Azure](https://github.com/Azure/azure-quickstart-templates/tree/master/201-web-app-sql-database)| Implanta um aplicativo do Serviço de Aplicativo e um banco de dados no Banco de Dados SQL do Azure no nível de serviço Básico. |
| [Aplicativo com uma conexão do Armazenamento de Blobs](https://github.com/Azure/azure-quickstart-templates/tree/master/201-web-app-blob-connection)| Implanta um aplicativo do Serviço de Aplicativo com uma cadeia de conexão do Armazenamento de Blobs do Azure. Em seguida, você poderá usar o Armazenamento de Blobs no aplicativo. |
| [Aplicativo com um Cache Redis do Azure](https://github.com/Azure/azure-quickstart-templates/tree/master/201-web-app-with-redis-cache)| Implanta um aplicativo do Serviço de Aplicativo com um Cache Redis do Azure. |
|**Ambiente do Serviço de Aplicativo**||
| [Criar um ambiente do Serviço de Aplicativo v2](https://github.com/Azure/azure-quickstart-templates/tree/master/201-web-app-asev2-create) | Cria um ambiente do Serviço de Aplicativo v2 em sua rede virtual. |
| [Criar um ambiente do Serviço de Aplicativo v2 com um endereço ILB](https://github.com/Azure/azure-quickstart-templates/tree/master/201-web-app-asev2-ilb-create/) | Cria um ambiente do Serviço de Aplicativo v2 em sua rede virtual com um endereço de balanceador de carga interno privado. |
| [Configurar o certificado SSL padrão para um ambiente de Serviço de Aplicativo ILB ou um ambiente de Serviço de Aplicativo v2 ILB](https://github.com/Azure/azure-quickstart-templates/tree/master/201-web-app-ase-ilb-configure-default-ssl) | Configura o certificado TLS/SSL padrão para um Ambiente do Serviço de Aplicativo do ILB ou um Ambiente do Serviço de Aplicativo v2 do ILB. |
| | |
