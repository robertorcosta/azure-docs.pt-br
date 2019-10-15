---
title: Configurar um ambiente de preparo no Azure Spring Cloud | Microsoft Docs
description: Saiba como usar a implantação azul-verde com o Azure Spring Cloud
services: spring-cloud
author: v-vasuke
manager: gwallace
editor: ''
ms.service: spring-cloud
ms.topic: quickstart
ms.date: 10/07/2019
ms.author: v-vasuke
ms.openlocfilehash: 454eeaa2568891ec35fe698cdb20c5448e10887e
ms.sourcegitcommit: d773b5743cb54b8cbcfa5c5e4d21d5b45a58b081
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/08/2019
ms.locfileid: "72038295"
---
# <a name="how-to-set-up-a-staging-environment"></a>Como configurar um ambiente de preparo

Este artigo mostrará como aproveitar uma implantação de preparo usando o padrão de implantação azul-verde no Azure Spring Cloud. Ele também mostrará como colocar essa implantação de preparo em produção sem alterar diretamente a implantação de produção.

## <a name="prerequisites"></a>Pré-requisitos

Este artigo pressupõe que você já tenha implantado o aplicativo PiggyMetrics do nosso [tutorial sobre como iniciar um aplicativo](spring-cloud-quickstart-launch-app-portal.md). O PiggyMetrics inclui três aplicativos: "gateway", "account-service" e "auth-service".  

Se você tiver um aplicativo diferente que quiser usar para este exemplo, precisará fazer uma alteração simples em uma parte do aplicativo voltada ao público.  Essa alteração diferencia a implantação de preparo da produção.

>[!NOTE]
> Antes de começar este início rápido, verifique se sua assinatura do Azure tem acesso ao Azure Spring Cloud.  Tratando-se de um serviço em versão prévia, pedimos que você entre em contato conosco para que possamos adicionar sua assinatura à nossa lista de permissões.  Se desejar explorar as funcionalidades do Azure Spring Cloud, entre em contato conosco por email: azure-spring-cloud@service.microsoft.com.

>[!TIP]
> O Azure Cloud Shell é um shell interativo grátis que pode ser usado para executar as etapas neste artigo.  Ele tem ferramentas comuns do Azure pré-instaladas, incluindo as versões mais recentes do Git, do JDK, do Maven e da CLI do Azure. Se você fez logon na assinatura do Azure, inicie o [Azure Cloud Shell](https://shell.azure.com) em shell.azure.com.  Você pode saber mais sobre o Azure Cloud Shell [lendo nossa documentação](../cloud-shell/overview.md)

Para concluir este artigo, será preciso:

1. [Instalar o Git](https://git-scm.com/)
1. [Instalar o JDK 8](https://docs.microsoft.com/java/azure/jdk/?view=azure-java-stable)
1. [Instalar o Maven 3.0 ou posterior](https://maven.apache.org/download.cgi)
1. [Instalar a CLI do Azure](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)
1. [Inscrever-se em uma assinatura do Azure](https://azure.microsoft.com/free/)

## <a name="install-the-azure-cli-extension"></a>Instalar a extensão da CLI do Azure

Instalar a extensão do Azure Spring Cloud para a CLI do Azure usando o seguinte comando

```azurecli
az extension add -y --source https://azureclitemp.blob.core.windows.net/spring-cloud/spring_cloud-0.1.0-py2.py3-none-any.whl
```
    
## <a name="view-all-deployments"></a>Exibir todas as implantações

Vá para sua instância de serviço no portal do Azure e selecione **Gerenciamento de Implantação** para exibir todas as implantações. Você pode selecionar cada implantação para obter mais detalhes.

## <a name="create-a-staging-deployment"></a>Criar uma implantação de preparo

1. Em seu ambiente de desenvolvimento local, faça uma pequena modificação no aplicativo de gateway do Piggy Metrics. Por exemplo, altere a cor em `gateway/src/main/resources/static/css/launch.css`. Isso permitirá que você diferencie facilmente as duas implantações. Execute o comando a seguir para criar o pacote jar: 

    ```azurecli
    mvn clean package
    ```

1. Crie uma nova implantação com CLI do Azure, dando a ela o nome de implantação de preparo "verde".

    ```azurecli
    az spring-cloud app deployment create -g <resource-group-name> -s <service-instance-name> --app gateway -n green --jar-path gateway/target/gateway.jar
    ```

1. Depois que a implantação for concluída com êxito, acesse a página do gateway no **Painel do Aplicativo** e veja todas as instâncias na guia **Instâncias do Aplicativo** à esquerda.
  
> [!NOTE]
> O status da descoberta é "OUT_OF_SERVICE" para que o tráfego não seja roteado para essa implantação antes da conclusão da verificação.

## <a name="verify-the-staging-deployment"></a>Verificar a implantação de preparo

1. Retorne à página **Gerenciamento de implantação** e selecione a nova implantação. O status da implantação agora deve mostrar **Em execução**. O botão "Atribuir/Cancelar atribuição de domínio" será desabilitado, pois ele é um ambiente de preparo.

1. Na página **Visão geral**, você deverá ver um **Ponto de Extremidade de Teste**. Copie-a e cole-a em uma nova página do navegador e você deverá ver a nova página do Piggy Metrics.

>[!TIP]
> * Confirme se o ponto de extremidade de teste termina com "/" para garantir que o CSS seja carregado corretamente.  
> * Se o navegador exigir que você insira as credenciais de logon para exibir a página, use [Decodificação de URL](https://www.urldecoder.org/) para decodificar o ponto de extremidade de teste. A decodificação de URL retorna uma URL no formato "https://\<nome-de-usuário>:\<senha>@\<nome-do-cluster>.test.azureapps.io/gateway/green".  Use isso para acessar seu ponto de extremidade.

>[!NOTE]    
> As configurações do servidor de configuração se aplicam ao ambiente de preparo, bem como à produção. Por exemplo, se você definir o caminho do contexto (`server.servlet.context-path`) para seu gateway de aplicativo no servidor de configuração como *somepath*, o caminho para suas alterações de implantação verdes se altera: "https://\<nome-de-usuário>:\<senha>@\<nome-do-cluster>.test.azureapps.io/gateway/green/somepath/..."
 
 Se visitar seu gateway de aplicativo voltado para o público neste ponto, você verá a página antiga sem a nova alteração.
    
## <a name="set-the-green-as-production-deployment"></a>Definir a verde como a implantação de produção

1. Depois de verificar sua alteração no ambiente de preparo, você pode enviá-la por push para produção. Retorne ao **Gerenciamento de implantação** e marque a caixa de seleção antes do aplicativo de "gateway".
2. Selecione "Definir implantação".
3. Selecione "verde" no menu "IMPLANTAÇÃO DE PRODUÇÃO" e selecione **Aplicar**
4. Vá para a página **Visão geral** do aplicativo de gateway. Se você já tiver atribuído um domínio para o aplicativo de gateway, a URL será exibida na página **Visão geral**. Visite a URL e você verá a página do Piggy Metrics modificada.

>[!NOTE]
> Depois que a implantação verde for definida como ambiente de produção, a implantação anterior se tornará a implantação de preparo.

## <a name="modify-the-staging-deployment"></a>Modificar a implantação de preparo

Se não estiver satisfeito com a alteração, você poderá modificar o código do aplicativo, criar um novo pacote jar e fazer upload dele na implantação verde usando a CLI do Azure.

```azurecli
az spring-cloud app deploy  -g <resource-group-name> -s <service-instance-name> -n gateway -d green --jar-path gateway.jar
```

## <a name="delete-a-staging-deployment"></a>Excluir uma implantação de preparo

Exclua sua implantação de preparo do portal do Azure navegando até a página de implantação de preparo e selecionando o botão **Excluir**.

Como alternativa, exclua sua implantação de preparo da CLI do Azure com o seguinte comando:

```azurecli
az spring-cloud app deployment delete -n <staging-deployment-name> -g <resource-group-name> -s <service-instance-name> --app gateway
```
