---
title: Exemplo de destaque de chamada de grupo
titleSuffix: An Azure Communication Services sample overview
description: Visão geral do exemplo de destaque de chamada usando os Serviços de Comunicação do Azure para permitir que os desenvolvedores aprendam mais sobre o funcionamento interno do exemplo.
author: ddematheu
manager: nimag
services: azure-communication-services
ms.author: dademath
ms.date: 07/20/2020
ms.topic: overview
ms.service: azure-communication-services
ms.openlocfilehash: caee5686695594604f49dcbade54342a9134abc0
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/22/2020
ms.locfileid: "90943164"
---
# <a name="get-started-with-the-group-calling-hero-sample"></a>Introdução ao exemplo de destaque de chamadas em grupo

[!INCLUDE [Public Preview Notice](../includes/public-preview-include.md)]

<!----
> [!WARNING]
> Add links to our Hero Sample repo when the sample is publicly available.
---->

O **Exemplo de Destaque de Chamadas em Grupo** dos Serviços de Comunicação do Azure demonstra como a biblioteca de clientes Web de Chamadas dos Serviços de Comunicação pode ser usada para criar uma experiência de chamada em grupo.

Neste guia de início rápido de Exemplo, aprenderemos como o exemplo funciona antes de o executarmos no computador local. Em seguida, implantaremos o exemplo no Azure usando seus próprios recursos de Serviços de Comunicação do Azure.

> [!IMPORTANT]
> [Baixar o exemplo do GitHub](https://github.com/Azure/Communication/tree/master/samples)

## <a name="overview"></a>Visão geral

O exemplo tem um aplicativo do lado do cliente e um aplicativo do lado do servidor. O **aplicativo do lado do cliente** é um aplicativo Web React/Redux que usa a estrutura de interface do usuário Fluent da Microsoft. Esse aplicativo envia solicitações a um **aplicativo do lado do servidor** ASP.NET Core que ajuda o aplicativo do lado do cliente a se conectar ao Azure. 

Aqui está um exemplo:

:::image type="content" source="./media/calling/landing-page.png" alt-text="Captura de tela mostrando a página de aterrissagem do aplicativo de exemplo.":::

Quando você pressiona o botão "Iniciar uma chamada", o aplicativo Web busca um token de acesso do usuário do aplicativo do lado do servidor. Esse token é usado para conectar o aplicativo cliente aos Serviços de Comunicação do Azure. Depois que o token é recuperado, você é solicitado a especificar a câmera e o microfone que deseja usar. Você pode desabilitar/habilitar seus dispositivos com controles de alternância:

:::image type="content" source="./media/calling/pre-call.png" alt-text="Captura de tela mostrando a tela pré-chamada do aplicativo de exemplo.":::

Depois de configurar o nome de exibição e os dispositivos, você pode ingressar na sessão de chamada. Agora você verá a tela principal de chamada na qual está a experiência de chamada principal.

:::image type="content" source="./media/calling/main-app.png" alt-text="Captura de tela mostrando a tela principal do aplicativo de exemplo.":::

Componentes da tela principal de chamada:

- **Galeria de Mídia**: a fase principal em que os participantes são mostrados. Se um participante tiver a câmera habilitada, o feed de vídeo dele será mostrado aqui. Cada participante tem um bloco individual que mostra seu nome de exibição e fluxo de vídeo (quando há um)
- **Cabeçalho**: É aí que os controles de chamada primários estão localizados para alternar as configurações e a barra lateral do participante, ativar/desativar vídeo e combinação, compartilhar a tela e sair da chamada.
- **Barra lateral**: é nela que as informações de participantes e configurações são mostradas quando alternadas usando os controles no cabeçalho. O componente pode ser descartado usando o "X"' no canto superior direito. A barra lateral dos participantes mostrará uma lista de participantes e um link para convidar mais usuários para o chat. A barra lateral de configurações permite que você defina as configurações de microfone e câmera.

A seguir, você encontrará mais informações sobre os pré-requisitos, as etapas para configurar o exemplo e os tutoriais passo a passo para familiarizar-se com os diversos componentes.

## <a name="prerequisites"></a>Pré-requisitos

- Criar uma conta do Azure com uma assinatura ativa. Para obter detalhes, confira [Criar uma conta gratuitamente](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
- [Node.js (12.18.4 e superiores)](https://nodejs.org/en/download/)
- [Visual Studio (2019 e superiores)](https://visualstudio.microsoft.com/vs/)
- [.NET Core 2.2](https://dotnet.microsoft.com/download/dotnet-core/2.2) (instale a versão que corresponde à sua instância do Visual Studio, 32 vs. 64 bits)
- Criar um recurso dos Serviços de Comunicação do Azure. Para obter detalhes, confira [Criar um recurso de comunicação do Azure](../quickstarts/create-communication-resource.md). Você precisará registrar a **cadeia de conexão** do recurso para este guia de início rápido.

## <a name="locally-deploy-the-service--client-applications"></a>Implantar localmente o serviço e o aplicativos cliente

O exemplo de chamada em grupo consiste, essencialmente, em dois aplicativos: ClientApp e Service.NET.

Quando queremos implantar localmente, precisamos iniciar ambos os aplicativos. Quando o aplicativo para servidores é acessado pelo navegador, ele usa o ClientApp implantado localmente para a experiência do usuário.

Você pode testar o exemplo localmente abrindo várias sessões do navegador com a URL da chamada para simular uma chamada de vários usuários.

### <a name="before-running-the-sample-for-the-first-time"></a>Antes de executar o exemplo pela primeira vez

1. Abra uma instância do PowerShell, terminal do Windows, prompt de comando ou equivalente e navegue até o diretório para o qual você deseja clonar o exemplo.
2. `git clone`
3. Vá para **Pasta de Chamada/ClientApp** e execute `npm run setup`
   1. Se você encontrar um erro 1, procure acima, na saída, por uma URL que você precisará acessar para autorizar o cliente. (A URL terá a seguinte aparência: `app.vssps.visualstudio.com/oauth2/authorize?clientid=...`) Depois de acessar a URL em um navegador, copie o comando da janela do navegador e execute-o.
   2. Execute o comando `npm run setup-vsts-auth` novamente depois de concluir a etapa anterior.
4. Obtenha a `Connection String` do portal do Azure. Para obter mais informações sobre cadeias de conexão, confira [Criar Recursos de Comunicação do Azure](../quickstarts/create-communication-resource.md)
5. Depois de obter a cadeia de conexão, adicione-a ao arquivo **Chamadas/appsetting.json** encontrado na pasta .NET do Serviço. Insira a cadeia de conexão na variável: `ResourceConnectionString`.

### <a name="local-run"></a>Execução local

1. Ir para a pasta de Chamada
2. Abra a solução `Calling.csproj` no Visual Studio
2. Execute o projeto `Calling`*

*O navegador será aberto em `localhost:5000` (em que o nó está implantando o aplicativo cliente). Não há suporte para o aplicativo no Internet Explorer.

#### <a name="troubleshooting"></a>Solução de problemas

- A solução não é criada; ela gera erros durante a instalação/criação do NPM.

   Tente limpar/recompilar os projetos.

## <a name="publish-the-sample-to-azure"></a>Publicar o exemplo no Azure

1. Clique com o botão direito do mouse no projeto `Calling` e selecione Publicar.
2. Crie um perfil de publicação e selecione sua assinatura do Azure.
3. Antes de publicar, adicione sua cadeia de conexão com `Edit App Service Settings`, preencha `ResourceConnectionString` como a chave e forneça sua cadeia de conexão (copiada de appsettings.json) como o valor.

## <a name="clean-up-resources"></a>Limpar os recursos

Se quiser limpar e remover uma assinatura dos Serviços de Comunicação, exclua o recurso ou o grupo de recursos. Excluir o grupo de recursos também exclui todos os recursos associados a ele. Saiba mais sobre [como limpar recursos](../quickstarts/create-communication-resource.md#clean-up-resources).

## <a name="next-steps"></a>Próximas etapas

Para obter mais informações, consulte os seguintes artigos:

- Familiarize-se com [usar a biblioteca de clientes de chamada](../quickstarts/voice-video-calling/calling-client-samples.md)
- Saiba mais sobre as [funcionalidades da biblioteca de clientes de chamada](../quickstarts/voice-video-calling/calling-client-samples.md)
- Saiba mais sobre [como a chamada funciona](../concepts/voice-video-calling/about-call-types.md)

## <a name="additional-reading"></a>Leituras adicionais

- [Versão Prévia da Comunicação do Azure](https://github.com/Azure/communication-preview) – para saber mais sobre o SDK da Web de chamada
- [Redux](https://redux.js.org/) – gerenciamento de estado do lado do cliente
- [FluentUI](https://developer.microsoft.com/fluentui#/) – biblioteca de interface do usuário da Microsoft
- [React](https://reactjs.org/) – biblioteca para criar interfaces do usuário
- [ASP.NET Core](https://docs.microsoft.com/aspnet/core/introduction-to-aspnet-core?view=aspnetcore-3.1&preserve-view=true) – Estrutura para criar aplicativos Web
