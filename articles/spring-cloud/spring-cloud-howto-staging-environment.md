---
title: Configurar um ambiente de preparo no Azure Spring Cloud | Microsoft Docs
description: Saiba como usar a implantação azul-verde com o Azure Spring Cloud
author: bmitchell287
ms.service: spring-cloud
ms.topic: conceptual
ms.date: 02/03/2020
ms.author: brendm
ms.openlocfilehash: 2e29f6a75b303518ac34ecf9b570bd7638cf0c3a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79471023"
---
# <a name="set-up-a-staging-environment-in-azure-spring-cloud"></a>Configure um ambiente de preparação em Azure Spring Cloud

Este artigo discute como configurar uma implantação de preparação usando o padrão de implantação azul-verde no Azure Spring Cloud. A implantação em azul / verde é um padrão de entrega contínua do DevOps do Azure que se baseia em manter uma versão existente (azul) ativa, enquanto uma nova (verde) é implantada. Este artigo mostra como colocar essa implantação de encenação em produção sem alterar a implantação da produção diretamente.

## <a name="prerequisites"></a>Pré-requisitos

Este artigo pressupõe que você já implantou o aplicativo PiggyMetrics a partir do nosso [tutorial sobre o lançamento de um aplicativo Azure Spring Cloud](spring-cloud-quickstart-launch-app-portal.md). A PiggyMetrics é composta por três aplicativos: "gateway", "account-service" e "auth-service".  

Se você quiser usar um aplicativo diferente para este exemplo, você precisa fazer uma simples alteração em uma parte pública do aplicativo.  Essa alteração diferencia a implantação de preparo da produção.

>[!TIP]
> O Azure Cloud Shell é um shell interativo gratuito que você pode usar para executar as instruções neste artigo.  Tem ferramentas azure comuns e pré-instaladas, incluindo as versões mais recentes do Git, JDK, Maven e a Cli do Azure. Se você estiver conectado à sua assinatura do Azure, inicie o [Azure Cloud Shell](https://shell.azure.com).  Para saber mais, consulte [Visão Geral do Azure Cloud Shell](../cloud-shell/overview.md).

Para configurar um ambiente de preparação no Azure Spring Cloud, siga as instruções nas próximas seções.

## <a name="install-the-azure-cli-extension"></a>Instalar a extensão da CLI do Azure

Instale a extensão Azure Spring Cloud para a CLI do Azure usando o seguinte comando:

```azurecli
az extension add --name spring-cloud
```
    
## <a name="view-all-deployments"></a>Exibir todas as implantações

Vá para a instância de serviço no portal Azure e selecione **o gerenciamento de implantação** para exibir todas as implantações. Para ver mais detalhes, você pode selecionar cada implantação.

## <a name="create-a-staging-deployment"></a>Criar uma implantação de preparo

1. Em seu ambiente de desenvolvimento local, faça uma pequena modificação no aplicativo gateway PiggyMetrics. Por exemplo, altere a cor no *arquivo gateway/src/main/resources/static/css/launch.css.* Isso permite diferenciar facilmente as duas implantações. Para construir o pacote de jar, execute o seguinte comando: 

    ```console
    mvn clean package
    ```

1. Na CLI do Azure, crie uma nova implantação e dê-lhe o nome de implantação de preparação "verde".

    ```azurecli
    az spring-cloud app deployment create -g <resource-group-name> -s <service-instance-name> --app gateway -n green --jar-path gateway/target/gateway.jar
    ```

1. Depois que a implantação terminar com sucesso, acesse a página de gateway do Painel de **aplicativos**e visualize todas as instâncias na guia **Instâncias** do aplicativo à esquerda.
  
> [!NOTE]
> O status de descoberta é *OUT_OF_SERVICE* para que o tráfego não seja encaminhado para esta implantação antes que a verificação seja concluída.

## <a name="verify-the-staging-deployment"></a>Verificar a implantação de preparo

1. Retorne à página **de gerenciamento de implantação** e selecione sua nova implantação. O status da implantação agora deve mostrar *Em execução*. O botão **Atribuir/Desatribuir** de domínio deve aparecer em cinza, porque o ambiente é um ambiente de preparação.

1. No **painel Visão Geral,** você deve ver um **ponto final de teste**. Copie e cole-o em uma nova janela do navegador, e a nova página piggymetrics deve ser exibida.

>[!TIP]
> * Confirme se o ponto final do teste termina com uma barra (/) para garantir que o arquivo CSS esteja carregado corretamente.  
> * Se o navegador exigir que você insira as credenciais de logon para exibir a página, use [Decodificação de URL](https://www.urldecoder.org/) para decodificar o ponto de extremidade de teste. A decodificação de URL retorna uma URL no formato "https://\<nome-de-usuário>:\<senha>@\<nome-do-cluster>.test.azureapps.io/gateway/green".  Use este formulário para acessar seu ponto final.

>[!NOTE]    
> As configurações do servidor de configuração aplicam-se tanto ao seu ambiente de preparação quanto à produção. Por exemplo, se você definir`server.servlet.context-path`o caminho de contexto ( ) para o gateway do aplicativo\<no servidor\<de configuração como *somepath,* o caminho para sua implantação verde será alterado para "https:// nome de usuário>: senha>@\<nome de cluster>.test.azureapps.io/gateway/green/somepath/...".
 
 Se você visitar o gateway de aplicativo voltado para o público neste momento, você deve ver a página antiga sem a sua nova alteração.
    
## <a name="set-the-green-deployment-as-the-production-environment"></a>Defina a implantação verde como o ambiente de produção

1. Depois de verificar sua mudança no ambiente de encenação, você pode empurrá-lo para a produção. Retornar ao **gerenciamento de implantação**e selecionar a caixa de seleção de aplicativos de **gateway.**

2. Selecione **'Definir'**
3. Na **lista 'Implantação de produção',** selecione **Verde**e selecione **Aplicar**.
4. Vá para a página **Visão geral** do aplicativo de gateway. Se você já atribuiu um domínio para o seu aplicativo gateway, a URL aparecerá no painel **Visão Geral.** Para visualizar a página PiggyMetrics modificada, selecione a URL e vá para o site.

>[!NOTE]
> Depois de definir a implantação verde como o ambiente de produção, a implantação anterior se tornará a implantação do estágio.

## <a name="modify-the-staging-deployment"></a>Modificar a implantação de preparo

Se você não estiver satisfeito com sua mudança, você pode modificar seu código de aplicativo, construir um novo pacote de jareto e enviá-lo para sua implantação verde usando o Cli do Azure.

```azurecli
az spring-cloud app deploy  -g <resource-group-name> -s <service-instance-name> -n gateway -d green --jar-path gateway.jar
```

## <a name="delete-the-staging-deployment"></a>Exclua a implantação de encenação

Para excluir sua implantação de preparação da porta Azure, vá para a página de implantação de staging e selecione o botão **Excluir.**

Alternativamente, exclua sua implantação de staging do Azure CLI executando o seguinte comando:

```azurecli
az spring-cloud app deployment delete -n <staging-deployment-name> -g <resource-group-name> -s <service-instance-name> --app gateway
```
