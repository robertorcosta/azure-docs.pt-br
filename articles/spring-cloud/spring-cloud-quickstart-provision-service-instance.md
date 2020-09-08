---
title: Guia de início rápido – Provisionar o serviço Azure Spring Cloud
description: Descreve a criação da instância de serviço Azure Spring Cloud para implantação de aplicativo.
author: MikeDodaro
ms.author: brendm
ms.service: spring-cloud
ms.topic: quickstart
ms.date: 08/03/2020
ms.custom: devx-track-java
ms.openlocfilehash: c91237e3a14c60e477f58be0bf62f634b462960b
ms.sourcegitcommit: e69bb334ea7e81d49530ebd6c2d3a3a8fa9775c9
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/27/2020
ms.locfileid: "88951667"
---
# <a name="quickstart-provision-azure-spring-cloud-service"></a>Início Rápido: Provisionar o serviço Azure Spring Cloud

Você pode criar uma instância do Azure Spring Cloud usando o portal do Azure ou a CLI do Azure.  Ambos os métodos são explicados nos procedimentos a seguir.
## <a name="prerequisites"></a>Pré-requisitos

* [Instalar o JDK 8](https://docs.microsoft.com/java/azure/jdk/?view=azure-java-stable)
* [Inscrever-se em uma assinatura do Azure](https://azure.microsoft.com/free/)
* (Opcional) [Instale a CLI do Azure versão 2.0.67 ou superior](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) e instale a extensão do Azure Spring Cloud com o comando: `az extension add --name spring-cloud`
* (Opcional) [Instale o Azure Toolkit for IntelliJ](https://plugins.jetbrains.com/plugin/8053-azure-toolkit-for-intellij/) e [conecte-se](https://docs.microsoft.com/azure/developer/java/toolkit-for-intellij/create-hello-world-web-app#installation-and-sign-in)

## <a name="provision-an-instance-of-azure-spring-cloud"></a>Provisionar uma instância do Azure Spring Cloud

#### <a name="portal"></a>[Portal](#tab/Azure-portal)

O procedimento a seguir cria uma instância do Azure Spring Cloud usando o portal do Azure.

1. Em uma nova guia, abra o [portal do Azure](https://ms.portal.azure.com/). 

2. Na caixa de pesquisa superior, pesquise **Azure Spring Cloud**.

3. Selecione **Azure Spring Cloud** nos resultados.

    ![Ícone de início do ASC](media/spring-cloud-quickstart-launch-app-portal/find-spring-cloud-start.png)

4. Na página do Azure Spring Cloud, clique em **+ Adicionar**.

    ![Ícone de adição do ASC](media/spring-cloud-quickstart-launch-app-portal/spring-cloud-add.png)

5. Preencha o formulário na página **Criar** do Azure Spring Cloud.  Considere as seguintes diretrizes:
    - **Assinatura**: selecione a assinatura da qual você deseja que este recurso seja cobrado.
    - **Grupo de recursos**: a criação de grupos de recursos para novos recursos é uma melhor prática. Observe que isso será usado em etapas posteriores como **\<resource group name\>** .
    - **Nome/Detalhes do Serviço**: Especifique o **\<service instance name\>** .  O nome deve ter entre 4 e 32 caracteres e pode conter apenas letras minúsculas, números e hifens.  O primeiro caractere do nome do serviço deve ser uma letra e o último caractere deve ser uma letra ou um número.
    - **Localização**: selecione a localização de sua instância de serviço.

    ![Início do portal ASC](media/spring-cloud-quickstart-launch-app-portal/portal-start.png)

6. Clique em **Examinar e criar**.

> [!div class="nextstepaction"]
> [Encontrei um problema](https://www.research.net/r/javae2e?tutorial=asc-cli-quickstart&step=public-endpoint)

#### <a name="cli"></a>[CLI](#tab/Azure-CLI)

O procedimento a seguir usa a extensão da CLI do Azure para provisionar uma instância do Azure Spring Cloud.

1. Faça logon na CLI do Azure e escolha sua assinatura ativa. Escolha a assinatura ativa que está na lista de permissões para o Azure Spring Cloud

    ```azurecli
    az login
    az account list -o table
    az account set --subscription <Name or ID of subscription, skip if you only have 1 subscription>
    ```

1. Prepare um nome para seu serviço do Azure Spring Cloud.  O nome deve ter entre 4 e 32 caracteres e pode conter apenas letras minúsculas, números e hifens.  O primeiro caractere do nome do serviço deve ser uma letra e o último caractere deve ser uma letra ou um número.

1. Crie um grupo de recursos para conter seu serviço do Azure Spring Cloud.

    ```azurecli
    az group create --location eastus --name <resource group name>
    ```

    Saiba mais sobre [grupos de recursos do Azure](../azure-resource-manager/management/overview.md).

1. Abra uma janela da CLI do Azure e execute os seguintes comandos para provisionar uma instância do Azure Spring Cloud.

    ```azurecli
    az spring-cloud create -n <service instance name> -g <resource group name>
    ```

    A instância de serviço levará cerca de cinco minutos para ser implantada.
---

## <a name="next-steps"></a>Próximas etapas
> [!div class="nextstepaction"]
> [Configurar o servidor de configuração](spring-cloud-quickstart-setup-config-server.md)


