---
title: Configurar um ambiente de preparo no Azure Spring Cloud | Microsoft Docs
description: Saiba como usar a implantação azul-verde com o Azure Spring Cloud
author: MikeDodaro
ms.service: spring-cloud
ms.topic: conceptual
ms.date: 01/14/2021
ms.author: brendm
ms.custom: devx-track-java, devx-track-azurecli
ms.openlocfilehash: 991a335207fc29cef7b243d7e520dd5f62ff691f
ms.sourcegitcommit: 2dd0932ba9925b6d8e3be34822cc389cade21b0d
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/01/2021
ms.locfileid: "99226094"
---
# <a name="set-up-a-staging-environment-in-azure-spring-cloud"></a>Configurar um ambiente de preparo no Azure Spring Cloud

**Este artigo aplica-se a:** ✔️ Java

Este artigo discute como configurar uma implantação de preparo usando o padrão de implantação azul-verde no Azure Spring Cloud. A implantação em azul / verde é um padrão de entrega contínua do DevOps do Azure que se baseia em manter uma versão existente (azul) ativa, enquanto uma nova (verde) é implantada. Este artigo mostra como colocar essa implantação de preparo em produção sem alterar diretamente a implantação de produção.

## <a name="prerequisites"></a>Pré-requisitos

* Instância do Azure Spring Cloud com **tipo de preço** *Standard* .
* Um aplicativo em execução.  Consulte [início rápido: implantar seu primeiro aplicativo do Azure Spring Cloud](spring-cloud-quickstart.md).
* CLI do Azure [extensão ASC](https://docs.microsoft.com/cli/azure/azure-cli-extensions-overview)

Se você quiser usar um aplicativo diferente para este exemplo, precisará fazer uma alteração simples em uma parte voltada ao público do aplicativo.  Essa alteração diferencia a implantação de preparo da produção.

>[!TIP]
> Azure Cloud Shell é um shell interativo gratuito que você pode usar para executar as instruções neste artigo.  Ele tem ferramentas do Azure já instaladas, incluindo as versões mais recentes do git, do JDK, do Maven e do CLI do Azure. Se você estiver conectado à sua assinatura do Azure, inicie o [Azure cloud Shell](https://shell.azure.com).  Para saber mais, confira [visão geral do Azure cloud Shell](../cloud-shell/overview.md).

Para configurar um ambiente de preparo no Azure Spring Cloud, siga as instruções nas próximas seções.

## <a name="install-the-azure-cli-extension"></a>Instalar a extensão da CLI do Azure

Instale a extensão Azure Spring Cloud para a CLI do Azure usando o seguinte comando:

```azurecli
az extension add --name spring-cloud
```
    
## <a name="view-apps-and-deployments"></a>Exibir aplicativos e implantações

Exiba os aplicativos implantados usando os procedimentos a seguir.

1. Acesse sua instância do Azure Spring Cloud na portal do Azure.

1. No painel de navegação à esquerda, abra **implantações**.

    [![Implantação-preterida](media/spring-cloud-blue-green-staging/deployments.png)](media/spring-cloud-blue-green-staging/deployments.png)

1. Abra a folha "aplicativos" para exibir aplicativos para sua instância de serviço.

    [![Aplicativos-painel](media/spring-cloud-blue-green-staging/app-dashboard.png)](media/spring-cloud-blue-green-staging/app-dashboard.png)

1. Você pode clicar em um aplicativo e exibir detalhes.

    [![Aplicativos-visão geral](media/spring-cloud-blue-green-staging/app-overview.png)](media/spring-cloud-blue-green-staging/app-overview.png)

1. Abra a folha **implantações** para ver todas as implantações do aplicativo. A grade de implantação mostra se a implantação é de produção ou de preparo.

    [![Painel de implantações](media/spring-cloud-blue-green-staging/deployments-dashboard.png)](media/spring-cloud-blue-green-staging/deployments-dashboard.png)

1. Você pode clicar no nome da implantação para exibir a visão geral da implantação. Nesse caso, a única implantação é denominada *padrão*.

    [![Visão geral das implantações](media/spring-cloud-blue-green-staging/deployments-overview.png)](media/spring-cloud-blue-green-staging/deployments-overview.png)
    

## <a name="create-a-staging-deployment"></a>Criar uma implantação de preparo

1. Em seu ambiente de desenvolvimento local, faça uma pequena modificação em seu aplicativo. Isso permite que você diferencie facilmente as duas implantações. Para compilar o pacote jar, execute o seguinte comando: 

    ```console
    mvn clean package -DskipTests
    ```

1. No CLI do Azure, crie uma nova implantação e dê a ela o nome de implantação de preparo "verde".

    ```azurecli
    az spring-cloud app deployment create -g <resource-group-name> -s <service-instance-name> --app <appName> -n green --jar-path gateway/target/gateway.jar
    ```

1. Depois que a implantação da CLI for concluída com êxito, acesse a página do aplicativo no **painel do aplicativo** e exiba todas as suas instâncias na guia **implantações** à esquerda.

   [![Painel de implantações após a implantação verde](media/spring-cloud-blue-green-staging/deployments-dashboard-2.png)](media/spring-cloud-blue-green-staging/deployments-dashboard-2.png)

  
> [!NOTE]
> O status da descoberta é *OUT_OF_SERVICE* para que o tráfego não seja roteado para essa implantação antes de a verificação ser concluída.

## <a name="verify-the-staging-deployment"></a>Verificar a implantação de preparo

Para verificar se o desenvolvimento de preparo verde está funcionando:
1. Vá para **implantações** e clique na `green` **implantação de preparo**.
1. Na página **visão geral** , clique no **ponto de extremidade de teste**.
1. Isso abrirá a compilação de preparo mostrando suas alterações.

>[!TIP]
> * Confirme se o ponto de extremidade de teste termina com uma barra (/) para garantir que o arquivo CSS seja carregado corretamente.  
> * Se o navegador exigir que você insira as credenciais de logon para exibir a página, use [Decodificação de URL](https://www.urldecoder.org/) para decodificar o ponto de extremidade de teste. A decodificação de URL retorna uma URL no formato "https:// \<username> : \<password> @ \<cluster-name> . Test.azureapps.Io/gateway/Green".  Use este formulário para acessar seu ponto de extremidade.

>[!NOTE]    
> As configurações do servidor de configuração se aplicam tanto ao ambiente de preparo quanto à produção. Por exemplo, se você definir o caminho do contexto ( `server.servlet.context-path` ) para o gateway de aplicativo no servidor de configuração como *somepath*, o caminho para a implantação verde será alterado para "https:// \<username> : \<password> @ \<cluster-name> . Test.azureapps.Io/gateway/Green/somepath/...".
 
 Se você visitar seu gateway de aplicativo voltado para o público neste ponto, verá a página antiga sem a nova alteração.
    
## <a name="set-the-green-deployment-as-the-production-environment"></a>Definir a implantação verde como o ambiente de produção

1. Depois de verificar sua alteração no ambiente de preparo, você pode enviá-la por push para produção. Retorne ao **Gerenciamento de implantação** e selecione o aplicativo atualmente em `Production` .

1. Clique nas reticências após o **status do registro** e defina a compilação de produção como `staging` .

   [![Implantações definir implantação de preparo](media/spring-cloud-blue-green-staging/set-staging-deployment.png)](media/spring-cloud-blue-green-staging/set-staging-deployment.png)

1. Retorne à página **Gerenciamento de implantação** . Defina a `green` implantação como `production` . Quando a configuração for concluída, o `green` status da implantação deverá aparecer. Agora, essa é a compilação de produção em execução.

   [![Conjunto de implantações definir resultado da implantação de preparo](media/spring-cloud-blue-green-staging/set-staging-deployment-result.png)](media/spring-cloud-blue-green-staging/set-staging-deployment-result.png)

1. A URL do aplicativo deve exibir suas alterações.

>[!NOTE]
> Depois de definir a implantação verde como o ambiente de produção, a implantação anterior torna-se a implantação de preparo.

## <a name="modify-the-staging-deployment"></a>Modificar a implantação de preparo

Se você não estiver satisfeito com sua alteração, poderá modificar o código do aplicativo, criar um novo pacote jar e carregá-lo em sua implantação verde usando o CLI do Azure.

```azurecli
az spring-cloud app deploy  -g <resource-group-name> -s <service-instance-name> -n gateway -d green --jar-path gateway.jar
```

## <a name="delete-the-staging-deployment"></a>Excluir a implantação de preparo

Para excluir a implantação de preparo da porta do Azure, acesse a página de implantação de preparo e, em seguida, selecione o botão **excluir** .

Como alternativa, exclua a implantação de preparo do CLI do Azure executando o seguinte comando:

```azurecli
az spring-cloud app deployment delete -n <staging-deployment-name> -g <resource-group-name> -s <service-instance-name> --app gateway
```

## <a name="next-steps"></a>Próximas etapas

* [CI/CD para o Azure Spring Cloud](https://review.docs.microsoft.com/azure/spring-cloud/spring-cloud-howto-cicd?branch=pr-en-us-142929&pivots=programming-language-java)
