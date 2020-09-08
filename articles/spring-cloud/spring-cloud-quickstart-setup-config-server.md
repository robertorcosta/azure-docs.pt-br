---
title: Guia de início rápido – Configurar o servidor de configuração do Azure Spring Cloud
description: Descreve a configuração do Azure Spring Cloud Config Server para implantação de aplicativo.
author: MikeDodaro
ms.author: brendm
ms.service: spring-cloud
ms.topic: quickstart
ms.date: 08/03/2020
ms.custom: devx-track-java
ms.openlocfilehash: 47f74b551919177b13f5a72d6eedeae3c77b9f14
ms.sourcegitcommit: e69bb334ea7e81d49530ebd6c2d3a3a8fa9775c9
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/27/2020
ms.locfileid: "88951656"
---
# <a name="quickstart-set-up-azure-spring-cloud-configuration-server"></a>Início Rápido: Configurar o servidor de configuração do Azure Spring Cloud

O Azure Spring Cloud Config Server é um serviço de configuração centralizado para sistemas distribuídos. Ele usa uma camada de repositório conectável que atualmente dá suporte a armazenamento local, Git e Subversion.  Configure o Config Server para implantar aplicativos de microsserviço no Azure Spring Cloud.

## <a name="prerequisites"></a>Pré-requisitos

* [Instalar o JDK 8](https://docs.microsoft.com/java/azure/jdk/?view=azure-java-stable)
* [Inscrever-se em uma assinatura do Azure](https://azure.microsoft.com/free/)
* (Opcional) [Instale a CLI do Azure versão 2.0.67 ou superior](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) e instale a extensão do Azure Spring Cloud com o comando: `az extension add --name spring-cloud`
* (Opcional) [Instale o Azure Toolkit for IntelliJ](https://plugins.jetbrains.com/plugin/8053-azure-toolkit-for-intellij/) e [conecte-se](https://docs.microsoft.com/azure/developer/java/toolkit-for-intellij/create-hello-world-web-app#installation-and-sign-in)

## <a name="azure-spring-cloud-config-server-procedures"></a>Procedimentos do Azure Spring Cloud Config Server

#### <a name="portal"></a>[Portal](#tab/Azure-portal)

O procedimento a seguir configura o Config Server usando o portal do Azure para implantar o [exemplo de Piggymetrics](spring-cloud-quickstart-sample-app-introduction.md).

1. Acesse a página **Visão Geral** do serviço e selecione o **Servidor de Configuração**.

2. Na seção **Repositório padrão**, defina **URI** como "https://github.com/Azure-Samples/piggymetrics-config".

3. Selecione **Aplicar** para salvar as alterações.

    ![Captura de tela do portal do ASC](media/spring-cloud-quickstart-launch-app-portal/portal-config.png)

#### <a name="cli"></a>[CLI](#tab/Azure-CLI)

O procedimento a seguir usa a CLI do Azure para configurar o Config Server para implantar o [exemplo de Piggymetrics](spring-cloud-quickstart-sample-app-introduction.md).

Configurar o config-server com a localização do repositório git do projeto:

```azurecli
az spring-cloud config-server git set -n <service instance name> --uri https://github.com/Azure-Samples/piggymetrics-config
```

---

## <a name="next-steps"></a>Próximas etapas
> [!div class="nextstepaction"]
> [Compilar e implantar aplicativos](spring-cloud-quickstart-deploy-apps.md)
