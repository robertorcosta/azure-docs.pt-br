---
title: Configure as configurações do aplicativo de função no Azure
description: Saiba como definir configurações de aplicativos do Azure Functions.
ms.assetid: 81eb04f8-9a27-45bb-bf24-9ab6c30d205c
ms.topic: conceptual
ms.date: 08/14/2019
ms.custom: cc996988-fb4f-47
ms.openlocfilehash: 662a04dbcc39f3fa95b0098eb8fe556b18b3495b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79276940"
---
# <a name="manage-your-function-app"></a>Gerencie seu aplicativo de função 

No Azure Functions, um aplicativo de funções fornece o contexto de execução para suas funções individuais. Os comportamentos do aplicativo de funções se aplicam a todas as funções hospedadas por um aplicativo função de determinada. Todas as funções em um aplicativo de função devem ser da mesma [língua](supported-languages.md). 

Funções individuais em um aplicativo de função são implantadas em conjunto e são dimensionadas juntas. Todas as funções na mesma função de compartilhamento de recursos do aplicativo, por exemplo, como o aplicativo de função escala. 

As seqüências de conexão, variáveis de ambiente e outras configurações do aplicativo são definidas separadamente para cada aplicativo de função. Todos os dados que devem ser compartilhados entre aplicativos de função devem ser armazenados externamente em um armazenamento persistido.

Este artigo descreve como configurar e gerenciar seus aplicativos de função. 

> [!TIP]  
> Muitas opções de configuração também podem ser gerenciadas usando o [Azure CLI]. 

## <a name="get-started-in-the-azure-portal"></a>Introdução ao portal do Azure

Acesse o [portal do Azure] e entre usando sua conta do Azure. Na barra de pesquisa na parte superior do portal, digite o nome de seu aplicativo de funções e selecione-o na lista. Depois de selecionar o aplicativo de funções, você deverá ver esta página:

![Visão geral do aplicativo de funções no portal do Azure](./media/functions-how-to-use-azure-function-app-settings/azure-function-app-main.png)

Você pode navegar para tudo o que precisa para gerenciar seu aplicativo de função a partir da página de visão geral, em particular as **[configurações do aplicativo](#settings)** e **[os recursos da plataforma](#platform-features)**.

## <a name="application-settings"></a><a name="settings"></a>Configurações do aplicativo

A **guia Configurações do aplicativo** mantém as configurações usadas pelo aplicativo de função. Essas configurações são armazenadas criptografadas, e você deve selecionar **Mostrar valores** para ver os valores no portal. Você também pode acessar as configurações do aplicativo usando o Azure CLI.

### <a name="portal"></a>Portal

Para adicionar uma configuração no portal, selecione **Nova configuração de aplicativo** e adicione o novo par de valor-chave.

![Funde as configurações do aplicativo no portal Azure.](./media/functions-how-to-use-azure-function-app-settings/azure-function-app-settings-tab.png)

### <a name="azure-cli"></a>CLI do Azure

O [`az functionapp config appsettings list`](/cli/azure/functionapp/config/appsettings#az-functionapp-config-appsettings-list) comando retorna as configurações de aplicativo existentes, como no exemplo a seguir:

```azurecli-interactive
az functionapp config appsettings list --name <FUNCTION_APP_NAME> \
--resource-group <RESOURCE_GROUP_NAME>
```

O [`az functionapp config appsettings set`](/cli/azure/functionapp/config/appsettings#az-functionapp-config-appsettings-set) comando adiciona ou atualiza uma configuração do aplicativo. O exemplo a seguir cria `CUSTOM_FUNCTION_APP_SETTING` uma configuração `12345`com uma chave nomeada e um valor de :


```azurecli-interactive
az functionapp config appsettings set --name <FUNCTION_APP_NAME> \
--resource-group <RESOURCE_GROUP_NAME> \
--settings CUSTOM_FUNCTION_APP_SETTING=12345
```

### <a name="use-application-settings"></a>Use configurações de aplicativos

[!INCLUDE [functions-environment-variables](../../includes/functions-environment-variables.md)]

Quando você desenvolve um aplicativo de função localmente, você deve manter cópias locais desses valores no arquivo de projeto local.settings.json. Para saber mais, consulte [arquivo de configurações locais](functions-run-local.md#local-settings-file).

## <a name="platform-features"></a>Recursos da plataforma

![Guia Recursos da plataforma do aplicativo de funções.](./media/functions-how-to-use-azure-function-app-settings/azure-function-app-features-tab.png)

Aplicativos de funções executados e mantidos pela plataforma de Serviço de Aplicativo do Azure. Dessa forma, seus aplicativos de funções têm acesso à maioria dos recursos da principal plataforma de hospedagem na Web do Azure. Na guia **Recursos da plataforma** você acessa vários recursos da plataforma de Serviço de Aplicativo para uso em seus aplicativos de funções. 

> [!NOTE]
> Nem todos os recursos do Serviço de Aplicativo estão disponíveis quando um aplicativo de funções é executado no plano de hospedagem de Consumo.

O resto deste artigo se concentra nos seguintes recursos do App Service no portal Azure que são úteis para funções:

+ [Editor do Serviço de Aplicativo](#editor)
+ [Console](#console)
+ [Ferramentas avançadas (Kudu)](#kudu)
+ [Opções de implantação](#deployment)
+ [CORS](#cors)
+ [Autenticação](#auth)

Para saber mais sobre como trabalhar com configurações da Serviço de Aplicativo, confira [Definir configurações do Serviço de Aplicativo do Azure](../app-service/configure-common.md).

### <a name="app-service-editor"></a><a name="editor"></a>Editor de serviços de aplicativos

![O Editor de Serviço de Aplicativo](./media/functions-how-to-use-azure-function-app-settings/configure-function-app-appservice-editor.png)

O Editor do Serviço de Aplicativo é um editor avançado dentro do portal que você pode usar para modificar arquivos de configuração JSON e arquivos de código. Escolher essa opção inicia uma nova guia do navegador com um editor básico. Isso permite que você se integre ao repositório do Git, execute e depure código, além de modificar as configurações do aplicativos de funções. Este editor fornece um ambiente de desenvolvimento aprimorado para suas funções em comparação com o editor de funções incorporado.  

Recomendamos que você considere desenvolver suas funções em seu computador local. Quando você desenvolve localmente e publica no Azure, seus arquivos de projeto são somente leitura no portal. Para saber mais, consulte [Código e teste funções do Azure localmente](functions-develop-local.md).

### <a name="console"></a><a name="console"></a>Console

![Console do aplicativo de funções](./media/functions-how-to-use-azure-function-app-settings/configure-function-console.png)

O console no portal é uma ferramenta ideal de desenvolvimento quando você preferir interagir com seu aplicativo de funções na linha de comando. Os comandos comuns incluem criação e navegação de diretório e arquivo, bem como execução scripts e arquivos em lote. 

Ao desenvolver localmente, recomendamos o uso das [Ferramentas Principais de Funções do Azure](functions-run-local.md) e da [CLI do Azure].

### <a name="advanced-tools-kudu"></a><a name="kudu"></a>Ferramentas avançadas (Kudu)

![Configurar Kudu](./media/functions-how-to-use-azure-function-app-settings/configure-function-app-kudu.png)

As ferramentas avançadas para o Serviço de Aplicativo (também conhecidas como Kudu) fornecem acesso a recursos administrativos avançados de seu aplicativo de funções. No Kudu, você pode gerenciar informações do sistema, configurações do aplicativo, variáveis de ambiente, extensões do site, cabeçalhos HTTP e variáveis de servidor. Você também pode iniciar o **Kudu** navegando até o ponto de extremidade SCM de seu aplicativo de funções, como`https://<myfunctionapp>.scm.azurewebsites.net/` 


### <a name="deployment-center"></a><a name="deployment"></a>Centro de Implantação

Quando você usa uma solução de controle de origem para desenvolver e manter seu código de funções, o Centro de Implantação permite que você construa e implante a partir do controle de origem. Seu projeto é construído e implantado no Azure quando você faz atualizações. Para obter mais informações, consulte [tecnologias de implantação em Funções Azure](functions-deployment-technologies.md).

### <a name="cross-origin-resource-sharing"></a><a name="cors"></a>Compartilhamento de recursos entre origens

Para evitar a execução de códigos maliciosos no cliente, navegadores modernos bloqueiam solicitações de aplicativos web para recursos executados em um domínio separado. [O Cors (Cross-Origin Resource Sharing, compartilhamento de recursos de origem cruzada)](https://developer.mozilla.org/docs/Web/HTTP/CORS) permite que um `Access-Control-Allow-Origin` cabeçalho declare quais origens podem chamar pontos finais em seu aplicativo de função.

#### <a name="portal"></a>Portal

Quando você configura a lista **de origens permitidas** para o seu aplicativo de função, o `Access-Control-Allow-Origin` cabeçalho é automaticamente adicionado a todas as respostas de pontos finais HTTP em seu aplicativo de função. 

![Configure a lista cors do aplicativo de função](./media/functions-how-to-use-azure-function-app-settings/configure-function-app-cors.png)

Quando o curinga é`*`usado, todos os outros domínios são ignorados. 

Use [`az functionapp cors add`](/cli/azure/functionapp/cors#az-functionapp-cors-add) o comando para adicionar um domínio à lista de origens permitidas. O exemplo a seguir adiciona o domínio contoso.com:

```azurecli-interactive
az functionapp cors add --name <FUNCTION_APP_NAME> \
--resource-group <RESOURCE_GROUP_NAME> \
--allowed-origins https://contoso.com
```

Use [`az functionapp cors show`](/cli/azure/functionapp/cors#az-functionapp-cors-show) o comando para listar as origens permitidas atuais.

### <a name="authentication"></a><a name="auth"></a>Autenticação

![Configurar a autenticação para um aplicativo de funções](./media/functions-how-to-use-azure-function-app-settings/configure-function-app-authentication.png)

Quando as funções usam um gatilho HTTP, você pode exigir que as chamadas sejam autenticadas primeiro. O App Service suporta a autenticação do Azure Active Directory e o login com provedores sociais, como Facebook, Microsoft e Twitter. Para obter detalhes sobre como configurar provedores de autenticação específicos, consulte [Visão geral de autenticação do serviço de aplicativo do Azure](../app-service/overview-authentication-authorization.md). 


## <a name="next-steps"></a>Próximas etapas

+ [Definir configurações do Serviço de Aplicativo do Azure](../app-service/configure-common.md)
+ [Implantação contínua para Azure Functions](functions-continuous-deployment.md)

[Azure CLI]: /cli/azure/
[Portal Azure]: https://portal.azure.com
