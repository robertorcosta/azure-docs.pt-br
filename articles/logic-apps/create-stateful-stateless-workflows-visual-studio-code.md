---
title: Criar fluxos de trabalho de automação (visualização) no Visual Studio Code
description: Criar fluxos de trabalho de automação com ou sem estado com a extensão de aplicativos lógicos do Azure (versão prévia) no Visual Studio Code para integrar aplicativos, dados, serviços de nuvem e sistemas locais
services: logic-apps
ms.suite: integration
ms.reviewer: deli, rohitha, vikanand, hongzili, sopai, absaafan, logicappspm
ms.topic: conceptual
ms.date: 09/26/2020
ms.openlocfilehash: cc52358af203bafc87c5f9ac3ae1f237c0c7ae6c
ms.sourcegitcommit: ffa7a269177ea3c9dcefd1dea18ccb6a87c03b70
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/30/2020
ms.locfileid: "91597787"
---
# <a name="create-stateful-or-stateless-workflows-in-visual-studio-code-with-the-azure-logic-apps-preview-extension"></a>Criar fluxos de trabalho com ou sem estado no Visual Studio Code com a extensão de aplicativos lógicos do Azure (versão prévia)

> [!IMPORTANT]
> Esse recurso está em visualização pública, é fornecido sem um contrato de nível de serviço e não é recomendado para cargas de trabalho de produção. Alguns recursos podem não ter suporte ou podem ter restrição de recursos. Para obter mais informações, consulte [Termos de Uso Complementares de Versões Prévias do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Para criar fluxos de trabalho de aplicativo lógico que se integram entre aplicativos, dados, serviços de nuvem e sistemas, você pode usar Visual Studio Code e a extensão de aplicativos lógicos do Azure (versão prévia) para compilar e executar localmente [fluxos de trabalho de aplicativo lógico com *estado* e *sem estado* ](#stateful-stateless) em seu ambiente de desenvolvimento.

![Captura de tela que mostra Visual Studio Code e o fluxo de trabalho do aplicativo lógico.](./media/create-stateful-stateless-workflows-visual-studio-code/visual-studio-code-logic-apps-overview.png)

Os aplicativos lógicos que você cria com a extensão de visualização pública usam o novo tipo de recurso de **aplicativo lógico (versão prévia)** e são alimentados pelo tempo de execução de [Azure Functions](../azure-functions/functions-overview.md) no seu ambiente local. Esse novo tipo de recurso pode incluir vários fluxos de trabalho e é semelhante de algumas maneiras ao tipo de recurso **aplicativo de funções** , que pode incluir várias funções.

Enquanto isso, o tipo de recurso **aplicativos lógicos** originais ainda existe para você criar e usar no Visual Studio Code e no portal do Azure. No entanto, as experiências para o tipo de recurso original são separadas do novo tipo de recurso. Neste momento, os tipos de recurso **aplicativos lógicos** e **aplicativo lógico (visualização)** podem existir ao mesmo tempo em Visual Studio Code e no portal do Azure. Você pode exibir e acessar todos os aplicativos lógicos implantados em sua assinatura do Azure, mas eles aparecem e são mantidos separadamente em suas próprias categorias e seções.

Este artigo fornece uma visão geral de alto nível [sobre essa visualização pública](#whats-new), descreve vários aspectos sobre o tipo de recurso de **aplicativo lógico (versão prévia)** e como criar esse recurso usando Visual Studio Code:

* Como os aplicativos lógicos com [estado e sem estado](#stateful-stateless) diferem uns dos outros.

* Como atender aos [requisitos de instalação](#prerequisites) e [configurar Visual Studio Code](#set-up) para a extensão de visualização pública.

* Como criar novos fluxos de trabalho de **aplicativo lógico (versão prévia)** [criando um projeto e selecionando um modelo de fluxo de trabalho](#create-project).

* Como executar e depurar localmente seus novos aplicativos lógicos no Visual Studio Code.

* Como publicar esses novos aplicativos lógicos diretamente do Visual Studio Code [no Azure](#publish-azure) ou [em um contêiner do Docker](#deploy-docker) que você pode executar em qualquer lugar. Para obter mais informações sobre o Docker, consulte [o que é o Docker](/dotnet/architecture/microservices/container-docker-introduction/docker-defined)?

<a name="whats-new"></a>

## <a name="whats-in-this-public-preview"></a>O que há nesta visualização pública?

A extensão de aplicativos lógicos do Azure (versão prévia) traz vários recursos de aplicativos lógicos atuais e adicionais para sua experiência de desenvolvimento local em Visual Studio Code, por exemplo:

* Crie aplicativos lógicos para fluxos de trabalho de integração e automação de [390 + conectores](/connectors/connector-reference/connector-reference-logicapps-connectors) para aplicativos de software como serviço (SaaS) e plataforma como serviço (PaaS) e serviços mais conectores para sistemas locais.

  * Alguns conectores gerenciados, como o barramento de serviço do Azure, hubs de eventos do Azure e SQL Server executados de forma semelhante a gatilhos nativos internos e ações como a ação HTTP.

  * Crie e implante aplicativos lógicos que podem ser executados em qualquer lugar porque o serviço de aplicativos lógicos do Azure gera cadeias de conexão de SAS (assinatura de acesso compartilhado) que esses aplicativos lógicos podem usar para enviar solicitações ao ponto de extremidade do tempo de execução de conexão de nuvem O serviço de aplicativos lógicos salva essas cadeias de conexão com outras configurações de aplicativo para que você possa armazenar facilmente esses valores em Azure Key Vault ao implantar no Azure.

    > [!NOTE]
    > Por padrão, um recurso de **aplicativo lógico (versão prévia)** tem sua [identidade gerenciada atribuída pelo sistema](../logic-apps/create-managed-service-identity.md) habilitada automaticamente para autenticar conexões em tempo de execução. Essa identidade difere das credenciais de autenticação ou da cadeia de conexão que você usa ao criar uma conexão. Se você desabilitar essa identidade, as conexões não funcionarão no tempo de execução.

* Crie aplicativos lógicos sem estado que são executados somente na memória para que eles sejam concluídos mais rapidamente, respondam com mais rapidez, tenham maior taxa de transferência e custam menos para serem executados, pois os históricos e dados de execução entre as ações não persistem no armazenamento externo. Opcionalmente, você pode habilitar o histórico de execuções para uma depuração mais fácil. Para obter mais informações, consulte [aplicativos lógicos com estado versus sem estado](#stateful-stateless).

* Execute e depure seus aplicativos lógicos localmente no ambiente de desenvolvimento de Visual Studio Code.

* Publique e implante seus aplicativos lógicos de Visual Studio Code diretamente em vários ambientes de hospedagem, como [Azure app Service](../app-service/environment/intro.md) e [contêineres do Docker](/dotnet/core/docker/introduction).

> [!NOTE]
> Para obter informações sobre os problemas atuais conhecidos, examine a [página do GitHub problemas conhecidos](https://github.com/Azure/logicapps/blob/master/articles/logic-apps-public-preview-known-issues.md)da extensão de visualização.

<a name="stateful-stateless"></a>

## <a name="stateful-versus-stateless-logic-apps"></a>Aplicativos lógicos com estado versus sem estado

* *Com estado*

  Crie aplicativos lógicos com estado quando precisar manter, examinar ou referenciar dados de eventos anteriores. Esses aplicativos lógicos mantêm a entrada e a saída de cada ação e seus Estados de fluxo de trabalho no armazenamento externo, o que torna possível examinar os detalhes de execução e o histórico possível após a conclusão de cada execução. Os aplicativos lógicos com estado fornecem alta resiliência se ou quando ocorrerem interrupções. Depois que os serviços e sistemas forem restaurados, você poderá reconstruir o aplicativo lógico interrompido é executado a partir do estado salvo e executar novamente os aplicativos lógicos para conclusão. Fluxos de trabalho com estado podem continuar em execução por até um ano.

* *Sem estado*

  Crie aplicativos lógicos sem estado quando você não precisar salvar, examinar ou referenciar dados de eventos anteriores no armazenamento externo para revisão posterior. Esses aplicativos lógicos mantêm a entrada e a saída de cada ação e seus Estados de fluxo de trabalho somente na memória, em vez de transferir essas informações para o armazenamento externo. Como resultado, os aplicativos lógicos sem estado têm execuções mais curtas que geralmente não são mais do que 5 minutos, um desempenho mais rápido com tempos de resposta mais rápidos, maior taxa de transferência e custos de execução reduzidos, pois os detalhes e o histórico de execução não são mantidos no armazenamento externo. No entanto, se ou quando ocorrerem interrupções, as execuções interrompidas não serão automaticamente restauradas, portanto, o chamador precisará reenviar manualmente as execuções interrompidas. Esses aplicativos lógicos só podem ser executados de forma síncrona e para facilitar a depuração, você pode [habilitar o histórico de execução](#run-history), o que tem algum impacto sobre o desempenho.

  Atualmente, os fluxos de trabalho sem estado dão suporte apenas a ações para [conectores gerenciados](../connectors/apis-list.md#managed-api-connectors), não gatilhos. Para iniciar o fluxo de trabalho, selecione a [solicitação interna, os hubs de eventos ou o gatilho do barramento de serviço](../connectors/apis-list.md#built-ins). Para obter mais informações sobre gatilhos, ações e conectores sem suporte, consulte [recursos sem suporte](#unsupported).

Para obter informações sobre como os aplicativos lógicos aninhados se comportam de forma diferente entre os aplicativos lógicos com e sem estado, consulte [diferenças de comportamento aninhado entre aplicativos lógicos com estado](#nested-behavior)

<a name="pricing-model"></a>

## <a name="pricing-model"></a>Modelo de preços

Ao implantar o novo tipo de recurso de **aplicativo lógico (versão prévia)** , você será solicitado a selecionar um plano de hospedagem, especificamente o plano do [serviço de aplicativo ou o plano Premium](../azure-functions/functions-scale.md) para usar como o modelo de preços. Se você selecionar o plano do serviço de aplicativo, você também será solicitado a selecionar um [tipo de preço](../app-service/overview-hosting-plans.md). Durante a visualização pública, a execução de aplicativos lógicos no serviço de aplicativo não incorre em encargos *adicionais* na parte superior do plano selecionado.

Os aplicativos lógicos com estado usam [armazenamento externo](../azure-functions/functions-scale.md#storage-account-requirements), portanto, o modelo de preços do armazenamento do Azure se aplica às transações de armazenamento que o tempo de execução do aplicativo lógico do Azure executa Por exemplo, as filas são usadas para agendamento, enquanto tabelas e BLOBs são usados para armazenar Estados de fluxo de trabalho.

Para obter mais informações sobre os modelos de preços que se aplicam a esse novo tipo de recurso, consulte estes tópicos:

* [Escala e hospedagem do Azure Functions](../azure-functions/functions-scale.md)
* [Escalar verticalmente um serviço de Azure App](../app-service/manage-scale-up.md)
* [Detalhes de preços do Azure Functions](https://azure.microsoft.com/pricing/details/functions/)
* [Detalhes de preços do serviço de aplicativo](https://azure.microsoft.com/pricing/details/app-service/windows/)
* [Detalhes de preços do armazenamento do Azure](https://azure.microsoft.com/pricing/details/storage/)

<a name="unsupported"></a>

## <a name="unavailable-or-unsupported-capabilities"></a>Recursos indisponíveis ou sem suporte

Para esta visualização pública, esses recursos não estão disponíveis ou não têm suporte:

* A criação do novo recurso de **aplicativo lógico (versão prévia)** não está disponível no MacOS no momento.

* Nem todas as regiões do Azure ainda têm suporte. Para as regiões disponíveis no momento, verifique a [lista de regiões](https://github.com/Azure/logicapps/blob/master/articles/logic-apps-public-preview-known-issues.md#available-regions).

* Para iniciar o fluxo de trabalho, use a [solicitação, o http, os hubs de eventos ou o gatilho do barramento de serviço](../connectors/apis-list.md). Atualmente, [conectores Enterprise](../connectors/apis-list.md#enterprise-connectors), gatilhos [de gateway de dados locais](../connectors/apis-list.md#on-premises-connectors), gatilhos baseados em webhook, gatilho de janela deslizante, [conectores personalizados](../connectors/apis-list.md#custom-apis-and-connectors), contas de integração, seus artefatos e [seus conectores](../connectors/apis-list.md#integration-account-connectors) não têm suporte nesta visualização. A funcionalidade "chamar uma função do Azure" não está disponível, portanto, por enquanto, use a *ação* http para chamar a URL de solicitação para a função do Azure.

  Fluxos de trabalho de aplicativo lógico sem estado só podem usar ações para [conectores gerenciados](../connectors/apis-list.md#managed-api-connectors), não para gatilhos. Exceto para os gatilhos especificados anteriormente, os fluxos de trabalho com estado podem usar gatilhos e ações para conectores gerenciados.

* Você pode implantar o novo tipo de recurso de **aplicativo lógico (versão prévia)** somente em um [plano de hospedagem do serviço de aplicativo ou Premium no Azure](#publish-azure) ou em um [contêiner do Docker](#deploy-docker), e não em [ambientes de serviço de integração (ISEs)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md). Os planos de Hospedagem de **consumo** não têm suporte nem estão disponíveis para a implantação desse tipo de recurso.

* No portal do Azure, não é possível criar novos aplicativos lógicos com o novo tipo de recurso de **aplicativo lógico (versão prévia)** . Você só pode criar esses aplicativos lógicos no Visual Studio Code. No entanto, depois de implantar aplicativos lógicos com esse tipo de recurso de Visual Studio Code para o Azure, você pode [Adicionar novos fluxos de trabalho a esses aplicativos lógicos](#add-workflows).

## <a name="prerequisites"></a>Pré-requisitos

### <a name="access-and-connectivity"></a>Acesso e conectividade

* Acesso à Internet para que você possa baixar os requisitos, conectar-se de Visual Studio Code à sua conta do Azure e publicar de Visual Studio Code para o Azure, um contêiner do Docker ou outro ambiente.

* Uma conta e uma assinatura do Azure. Se você não tem uma assinatura, [inscreva-se em uma conta gratuita do Azure](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

* Para criar o mesmo exemplo de aplicativo lógico neste artigo, você precisa de uma conta de email do Office 365 Outlook que usa uma conta corporativa ou de estudante da Microsoft para entrar.

  Se você optar por usar um [conector de email diferente com suporte dos aplicativos lógicos do Azure](/connectors/), como Outlook.com ou [gmail](../connectors/connectors-google-data-security-privacy-policy.md), ainda poderá seguir o exemplo, e as etapas gerais geral são as mesmas, mas a interface do usuário e as opções podem ser diferentes de algumas maneiras. Por exemplo, se você usar o conector Outlook.com, use seu conta Microsoft pessoal em vez de entrar.

### <a name="storage-requirements"></a>Requisitos de armazenamento

1. Baixe e instale o [emulador de armazenamento do Azure 5,10](https://go.microsoft.com/fwlink/p/?linkid=717179).

1. Para executar o emulador, você precisa ter uma instalação local do SQL DB, como a [versão gratuita SQL Server 2019 Express Edition](https://go.microsoft.com/fwlink/p/?linkid=866658). Para obter mais informações, consulte [usar o emulador de armazenamento do Azure para desenvolvimento e teste](../storage/common/storage-use-emulator.md).

   > [!IMPORTANT]
   > Antes de abrir o designer do aplicativo lógico para criar o fluxo de trabalho do aplicativo lógico, certifique-se de iniciar o emulador. Caso contrário, você receberá uma mensagem informando que o `Workflow design time could not be started` .
   >
   > ![Captura de tela que mostra o emulador de armazenamento do Azure em execução.](./media/create-stateful-stateless-workflows-visual-studio-code/start-storage-emulator.png)

### <a name="tools"></a>Ferramentas

* [Visual Studio Code 1.30.1 (janeiro de 2019) ou superior](https://code.visualstudio.com/), que é gratuito. Além disso, baixe e instale essas ferramentas adicionais para Visual Studio Code, se você ainda não as tiver:

  * [Extensão de conta do Azure](https://marketplace.visualstudio.com/items?itemName=ms-vscode.azure-account), que fornece uma única experiência comum de entrada e filtragem de assinatura do Azure para todas as outras extensões do azure no Visual Studio Code.

  * [C# para Visual Studio Code extensão](https://marketplace.visualstudio.com/items?itemName=ms-vscode.csharp), que habilita a funcionalidade F5 para executar seu aplicativo lógico.

  * [Azure Functions Core Tools](../azure-functions/functions-run-local.md), a versão [3.0.2931](https://github.com/Azure/azure-functions-core-tools/releases/tag/3.0.2931) ou [2.7.2936](https://github.com/Azure/azure-functions-core-tools/releases/tag/2.7.2936), por meio do MSI (Microsoft Installer). Essas ferramentas incluem uma versão do mesmo tempo de execução que alimenta o Azure Functions Runtime executado no Visual Studio Code.

    > [!IMPORTANT]
    > Se você tiver uma instalação anterior a essas versões, desinstale essa versão primeiro ou certifique-se de que a variável de ambiente PATH aponta para a versão que você baixa e instala.
    >
    > Se você quiser usar a ação de [ **código embutido** ](../logic-apps/logic-apps-add-run-inline-code.md) para executar o código JavaScript, será necessário usar o Azure Functions Runtime versão 3x porque a ação não dá suporte à versão 2x. Além disso, essa ação atualmente não tem suporte em sistemas operacionais Linux.

  * [Extensão de aplicativos lógicos do Azure (versão prévia) para Visual Studio Code](https://go.microsoft.com/fwlink/p/?linkid=2143167). Essa extensão de visualização pública fornece a capacidade de criar aplicativos lógicos com estado e sem estado e executá-los localmente no Visual Studio Code.

    No momento, você pode ter a extensão original de **aplicativos lógicos do Azure** e a nova extensão de **aplicativos lógicos do Azure (versão prévia)** instalada ao mesmo tempo no Visual Studio Code. Selecionando o ícone do Azure na barra de ferramentas Visual Studio Code, você pode exibir todos os aplicativos lógicos implantados no Azure, mas cada tipo de recurso aparece em suas próprias seções de extensão, **aplicativos lógicos** e **aplicativos lógicos do Azure (versão prévia)**.

    > [!IMPORTANT]
    > Se você criou aplicativos lógicos usando a extensão **aplicativos lógicos do Azure (versão prévia privada)** , esses aplicativos lógicos não funcionarão com a extensão de visualização pública. No entanto, você pode migrar esses aplicativos lógicos desinstalando a extensão de visualização privada, executando a limpeza necessária e instalando a extensão de visualização pública. Em seguida, você pode criar seu novo projeto no Visual Studio Code e copiar o arquivo **Workflow. Definition** do aplicativo lógico criado anteriormente para o novo projeto.
    >
    > Portanto, antes de instalar a extensão de visualização pública, certifique-se de desinstalar todas as versões anteriores e excluir esses artefatos:
    >
    > * A pasta **Microsoft. Azure. Functions. ExtensionBundle. workflows** , que contém pacotes de extensão anteriores e está localizada ao longo de um dos caminhos aqui:
    >
    >   * `C:\Users\{userName}\AppData\Local\Temp\Functions\ExtensionBundles`
    >
    >   * `C:\Users\{userName}.azure-functions-core-tools\Functions\ExtensionBundles`
    >
    > * A pasta **Microsoft. Azure. workflows. webjobs. Extension** , que é o cache do [NuGet](/nuget/what-is-nuget) para a extensão de visualização particular e está localizada ao longo deste caminho:
    >
    >   `C:\Users\{userName}\.nuget\packages`

    Para instalar a extensão de **aplicativos lógicos do Azure (versão prévia)** , siga estas etapas:

    1. No Visual Studio Code, na barra de ferramentas à esquerda, selecione **extensões**.

    1. Na caixa de pesquisa extensões, digite `azure logic apps preview` . Na lista de resultados, selecione **instalação de aplicativos lógicos do Azure (versão prévia)** **>** **Install**.

       Após a conclusão da instalação, a extensão de visualização pública aparecerá na lista **extensões: instalada** .

       ![Captura de tela que mostra Visual Studio Code lista de extensões instaladas com a extensão "aplicativos lógicos do Azure (visualização)" sublinhada.](./media/create-stateful-stateless-workflows-visual-studio-code/azure-logic-apps-extension-installed.png)

* Para testar o aplicativo lógico de exemplo que você cria neste artigo, você precisa de uma ferramenta que possa enviar chamadas para o gatilho de solicitação, que é a primeira etapa no aplicativo lógico de exemplo. Se você não tiver essa ferramenta, poderá baixar, instalar e usar o [postmaster](https://www.postman.com/downloads/).

* Para facilitar o registro em log de diagnóstico e o recurso de rastreamento, você pode adicionar e usar um recurso de [Application insights](../azure-monitor/app/app-insights-overview.md) . Você pode criar esse recurso durante a implantação do aplicativo lógico ou no portal do Azure depois de implantar seu aplicativo lógico.

<a name="set-up"></a>

## <a name="set-up-visual-studio-code"></a>Configurar o Visual Studio Code

1. Para certificar-se de que todas as extensões estejam instaladas corretamente, recarregue ou reinicie Visual Studio Code.

1. Habilite ou verifique se Visual Studio Code localiza e instala automaticamente atualizações de extensão para que sua extensão de visualização pública obtenha as atualizações mais recentes.

   Para verificar essa configuração, siga estas etapas:

   1. No menu **arquivo** , acesse **preferências** **>** **configurações**.

   1. Na guia **usuário** , vá para extensões de **recursos** **>** **Extensions**.

   1. Confirme se a **seleção automática de atualizações** e **atualização automática** está selecionada.

1. Habilite ou verifique se essas configurações de extensão de **aplicativos lógicos do Azure (versão prévia)** estão configuradas no Visual Studio Code:

   * **Aplicativos lógicos do Azure v2: modo de painel**
   * **Aplicativos lógicos do Azure v2: tempo de execução do projeto**

   1. No menu **arquivo** , acesse **preferências** **>** **configurações**.

   1. Na guia **usuário** , vá para **>** **extensões** **>** **aplicativos lógicos do Azure (versão prévia)**.

   1. Em **aplicativo lógico do Azure v2: modo de painel**, confirme se o **modo habilitar painel** está selecionado. Em **aplicativos lógicos do Azure v2: tempo de execução do projeto**, defina a versão como **~ 3** ou **~ 2**, com base na [versão do Azure Functions Core Tools](#prerequisites) que você instalou anteriormente.

      > [!IMPORTANT]
      > Se você quiser usar a ação de [ **código embutido** ](../logic-apps/logic-apps-add-run-inline-code.md) para executar o código JavaScript, certifique-se de usar o tempo de execução do projeto versão 3 porque a ação não dá suporte à versão 2. Além disso, essa ação atualmente não tem suporte em sistemas operacionais Linux.

      ![Captura de tela que mostra as configurações de Visual Studio Code para a extensão "aplicativos lógicos do Azure (visualização)".](./media/create-stateful-stateless-workflows-visual-studio-code/azure-logic-apps-preview-settings.png)

<a name="connect-azure-account"></a>

## <a name="connect-to-your-azure-account"></a>Conectar-se à sua conta do Azure

1. Na barra de ferramentas Código do Visual Studio, selecione o ícone do Azure.

   ![Captura de tela que mostra Visual Studio Code barra de ferramentas e o ícone do Azure selecionado.](./media/create-stateful-stateless-workflows-visual-studio-code/visual-studio-code-azure-icon.png)

1. No painel do Azure, em **Azure: aplicativos lógicos (versão prévia)**, selecione **entrar no Azure**. Quando a página de autenticação do Visual Studio Code for exibida, entre com sua conta do Azure.

   ![Captura de tela que mostra o painel do Azure e o link selecionado para entrar no Azure.](./media/create-stateful-stateless-workflows-visual-studio-code/sign-in-azure-subscription.png)

   Depois de entrar, o painel do Azure mostra as assinaturas em sua conta do Azure. Se você tiver a extensão de aplicativos lógicos lançada publicamente, poderá encontrar todos os recursos de aplicativos lógicos originais que você criou usando a extensão original na seção **aplicativos lógicos** da extensão liberada, não os **aplicativos lógicos** da extensão de visualização (seção de visualização.
   
   Se as assinaturas esperadas não aparecerem ou se você quiser que o painel mostre apenas assinaturas específicas, siga estas etapas:

   1. Na lista assinaturas, mova o ponteiro ao lado da primeira assinatura até que o botão **selecionar assinaturas** (ícone de filtro) seja exibido. Selecione o ícone de filtro.

      ![Captura de tela que mostra o painel do Azure e o ícone de filtro selecionado.](./media/create-stateful-stateless-workflows-visual-studio-code/filter-subscription-list.png)

      Ou, na barra de status Visual Studio Code, selecione sua conta do Azure. 

   1. Quando outra lista de assinaturas for exibida, selecione as assinaturas desejadas e, em seguida, certifique-se de selecionar **OK**.

<a name="create-project"></a>

## <a name="create-a-local-project"></a>Criar um projeto local

Antes de criar seu aplicativo lógico, crie um projeto local para que você possa gerenciar e implantar seu aplicativo lógico do Visual Studio Code. O projeto subjacente é semelhante a um projeto Azure Functions, também conhecido como um projeto de aplicativo de funções. No entanto, esses tipos de projeto são separados uns dos outros, portanto, os fluxos de trabalho e as funções do aplicativo lógico não podem existir no mesmo projeto.

1. Em seu computador, crie uma pasta local *vazia* a ser usada para o projeto que você criará posteriormente no Visual Studio Code.

1. Em Visual Studio Code, feche qualquer e todas as pastas abertas.

1. No painel do Azure, ao lado de **Azure: aplicativos lógicos (versão prévia)**, selecione **criar novo projeto** (ícone que mostra uma pasta e um raio).

   ![Captura de tela que mostra a barra de ferramentas do painel do Azure com "criar novo projeto" selecionado.](./media/create-stateful-stateless-workflows-visual-studio-code/create-new-project-folder.png)

1. Se o Windows Defender firewall solicitar que você conceda acesso à rede para `Code.exe` , que é Visual Studio Code e, para `func.exe` , que é o Azure Functions Core Tools, selecione **redes privadas, como minha rede doméstica ou de trabalho** **>** **permitir acesso**.

1. Navegue até o local onde você criou a pasta do projeto, selecione essa pasta e continue.

   ![Captura de tela que mostra a caixa de diálogo "Selecionar pasta" com uma pasta de projeto recém-criada e o botão "selecionar" selecionado.](./media/create-stateful-stateless-workflows-visual-studio-code/select-project-folder.png)

1. Na lista de modelos exibida, selecione fluxo de **trabalho com estado** ou **fluxo de trabalho sem estado**. Este exemplo seleciona **fluxo de trabalho com estado**.

   ![Captura de tela que mostra a lista de modelos de fluxo de trabalho com "fluxo de trabalho com estado" selecionado.](./media/create-stateful-stateless-workflows-visual-studio-code/select-stateful-stateless-workflow.png)

1. Forneça um nome para o fluxo de trabalho do aplicativo lógico e pressione Enter. Este exemplo usa `example-workflow` como o nome.

   ![Captura de tela que mostra a caixa "criar um novo fluxo de trabalho com estado (3/4)" e "exemplo-fluxo de trabalho" como o nome do fluxo de trabalho.](./media/create-stateful-stateless-workflows-visual-studio-code/name-your-workflow.png)

1. Na próxima lista exibida, selecione **abrir na janela atual**.

   ![Captura de tela que mostra a lista com a seleção "abrir na janela atual" selecionada.](./media/create-stateful-stateless-workflows-visual-studio-code/select-project-location.png)

   Visual Studio Code recarrega, abre o painel Gerenciador e mostra seu projeto, que agora inclui arquivos de projeto gerados automaticamente. Por exemplo, o projeto tem uma pasta que mostra o nome do fluxo de trabalho do aplicativo lógico. Dentro dessa pasta, o **workflow.jsno** arquivo contém a definição de JSON subjacente do fluxo de trabalho do aplicativo lógico.

   ![Captura de tela que mostra a janela do Explorer com a pasta do projeto, a pasta do fluxo de trabalho e o arquivo "workflow.jsem".](./media/create-stateful-stateless-workflows-visual-studio-code/local-project-created.png)

<a name="open-workflow-definition-designer"></a>

## <a name="open-the-workflow-definition-file-in-logic-app-designer"></a>Abrir o arquivo de definição de fluxo de trabalho no designer de aplicativo lógico

1. Verifique as versões que estão instaladas no seu computador executando o seguinte comando:

   `..\Users\{yourUserName}\dotnet --list-sdks`

   Se você tiver SDK do .NET Core 5. x, essa versão poderá impedi-lo de abrir a definição de fluxo de trabalho subjacente do aplicativo lógico no designer. Em vez de desinstalar esta versão, no local raiz do projeto, crie uma **global.jsno** arquivo que faz referência à versão 3. x do tempo de execução do .NET Core que você tem, por exemplo:

   ```json
   {
      "sdk": {
         "version": "3.1.8",
         "rollForward": "disable"
      }
   }
   ```

   Certifique-se de expliclitly adicionar essa **global.jsno** arquivo ao seu projeto no local raiz de dentro Visual Studio Code. Caso contrário, o designer não será aberto.

1. Se Visual Studio Code estiver em execução no Windows ou Linux, verifique se o emulador de armazenamento do Azure está em execução. Para obter mais informações, examine os [pré-requisitos](#prerequisites).

1. Expanda a pasta do projeto do seu fluxo de trabalho. Abra o **workflow.jsno** menu de atalho do arquivo e selecione **abrir no designer**.

   ![Captura de tela que mostra o painel do Explorer e a janela de atalho para a workflow.jsno arquivo com "abrir no designer" selecionado.](./media/create-stateful-stateless-workflows-visual-studio-code/open-definition-file-in-designer.png)

   Se você receber a mensagem de erro que o, verifique se `Workflow design time could not be started` o emulador de armazenamento do Azure está em execução. Caso contrário, experimente estas outras sugestões de solução de problemas:

   Em Visual Studio Code, verifique a saída da extensão de visualização.

   1. No menu **Exibir** , selecione **saída**.

   1. Na lista da barra de título de **saída** , selecione **aplicativos lógicos do Azure** para que você possa exibir a saída para a extensão de visualização, por exemplo:

      ![Captura de tela que mostra a janela de saída do Visual Studio Code com "aplicativos lógicos do Azure" selecionado.](./media/create-stateful-stateless-workflows-visual-studio-code/check-outout-window-azure-logic-apps.png)

   1. Examine a saída e verifique se esta mensagem de erro é exibida:

      ```text
      A host error has occurred during startup operation '{operationID}'.
      System.Private.CoreLib: The file 'C:\Users\{userName}\AppData\Local\Temp\Functions\
      ExtensionBundles\Microsoft.Azure.Functions.ExtensionBundle.Workflows\1.1.1\bin\
      DurableTask.AzureStorage.dll' already exists.
      Value cannot be null. (Parameter 'provider')
      Application is shutting down...
      Initialization cancellation requested by runtime.
      Stopping host...
      Host shutdown completed.
      ```

      Esse erro pode ocorrer se você tentou anteriormente abrir o designer e, em seguida, descontinuau ou excluiu seu projeto. Para resolver esse erro, exclua a pasta **ExtensionBundles** neste local **. ..\Users \\ {Your-username} \AppData\Local\Temp\Functions\ExtensionBundles**e tente abrir novamente o **workflow.jsno** arquivo no designer.

1. Na lista **habilitar conectores no Azure** , selecione **usar conectores do Azure**, que se aplica a todos os conectores gerenciados que estão disponíveis no portal do Azure, não apenas aos conectores dos serviços do Azure.

   ![Captura de tela que mostra o painel do Explorer com a lista "Habilitar conectores no Azure" aberta e "usar conectores do Azure" selecionado.](./media/create-stateful-stateless-workflows-visual-studio-code/use-connectors-from-azure.png)

1. Na lista grupos de recursos, selecione **criar novo grupo de recursos**.

   ![Captura de tela que mostra o painel do Explorer com a lista de grupos de recursos e "criar novo grupo de recursos" selecionado](./media/create-stateful-stateless-workflows-visual-studio-code/create-select-resource-group.png)

1. Forneça um nome para o grupo de recursos e pressione Enter. Este exemplo usa o `example-logic-app-preview-rg`.

   ![Captura de tela que mostra o painel do Explorer e a caixa nome do grupo de recursos.](./media/create-stateful-stateless-workflows-visual-studio-code/enter-name-for-resource-group.png)

1. Na lista locais, localize e selecione uma [região do Azure com suporte](https://github.com/Azure/logicapps/blob/master/articles/logic-apps-public-preview-known-issues.md#available-regions) a ser usada para criar o grupo de recursos e os recursos. Este exemplo usa **EUA Central ocidental**.

   > [!IMPORTANT]
   > Nem todas as regiões têm suporte no momento, mas as atualizações para adicionar mais regiões estão em andamento. A seleção de uma região sem suporte pode resultar em problemas, como a criação de conexões. Para as regiões com suporte no momento, examine a [página do GitHub problemas conhecidos](https://github.com/Azure/logicapps/blob/master/articles/logic-apps-public-preview-known-issues.md#available-regions)da extensão de visualização.

   ![Captura de tela que mostra o painel do Explorer com a lista de locais e "Oeste EUA Central" selecionados.](./media/create-stateful-stateless-workflows-visual-studio-code/select-azure-region.png)

   Depois de executar essa etapa, Visual Studio Code abrirá o designer do aplicativo lógico.

   > [!NOTE]
   > Quando Visual Studio Code inicia a API de tempo de design do fluxo de trabalho, é exibida uma mensagem informando que a inicialização pode levar alguns segundos. Você pode ignorar esta mensagem ou selecionar **OK**.

   Depois que o designer do aplicativo lógico for exibido, o prompt **escolher um operação** aparecerá no designer e será selecionado por padrão, que mostra o painel **Adicionar uma ação** .

   ![Captura de tela que mostra o designer do aplicativo lógico.](./media/create-stateful-stateless-workflows-visual-studio-code/workflow-app-designer.png)

1. Em seguida, [adicione um gatilho e ações](#add-trigger-actions) ao seu fluxo de trabalho.

<a name="add-trigger-actions"></a>

## <a name="add-a-trigger-and-actions"></a>Adicionar um gatilho e ações

Depois de abrir o designer do aplicativo lógico em seu **workflow.jsno** menu de atalho do arquivo, o prompt **escolher um operação** aparece no designer e é selecionado por padrão. Agora você pode começar a criar seu fluxo de trabalho adicionando um gatilho e ações.

O fluxo de trabalho do aplicativo lógico neste exemplo usa esse gatilho e estas ações:

* O [gatilho de solicitação](../connectors/connectors-native-reqres.md)interno, **quando uma solicitação HTTP é recebida**, que recebe chamadas de entrada ou solicitações e cria um ponto de extremidade que outros serviços ou aplicativos lógicos podem chamar.

* A [ação do Outlook do Office 365](../connectors/connectors-create-api-office365-outlook.md), **enviar um email**.

* A [ação de resposta](../connectors/connectors-native-reqres.md)interna, que você usa para enviar uma resposta e retornar dados de volta para o chamador.

### <a name="add-the-request-trigger"></a>Adicionar o gatilho de Solicitação

1. Ao lado do designer, no painel **Adicionar um gatilho** , na caixa de pesquisa **escolher uma operação** , certifique-se de que o **interno** está selecionado para que você possa selecionar um gatilho que seja executado nativamente.

1. Na caixa de pesquisa **escolher uma operação** , insira `when a http request` e selecione o gatilho de solicitação interno que é nomeado **quando uma solicitação HTTP é recebida**.

   ![Captura de tela que mostra o designer de aplicativo lógico e * * adicionar um painel de gatilho * * com o gatilho "quando um pedido HTTP é recebido" selecionado.](./media/create-stateful-stateless-workflows-visual-studio-code/add-request-trigger.png)

   Quando o gatilho aparece no designer, o painel de detalhes do gatilho é aberto para mostrar as propriedades, as configurações e outras ações do gatilho.

   ![Captura de tela que mostra o designer de aplicativo lógico com o gatilho "quando uma solicitação HTTP é recebida" está selecionado e o painel detalhes do gatilho é aberto.](./media/create-stateful-stateless-workflows-visual-studio-code/request-trigger-added-to-designer.png)

   > [!TIP]
   > Se o painel de detalhes não for exibido, verifique se o gatilho está selecionado no designer.

1. Se você precisar excluir um item no designer, siga estas etapas:

   1. No designer, selecione o item, que abre o painel detalhes do item no lado direito.

   1. Expanda a janela de Visual Studio Code muito suficiente para que ao lado do gatilho ou do nome da ação, o botão de reticências (**...**) seja exibido no canto superior direito. 

   1. Abra o menu de reticências (**...**) e selecione **excluir**. Para confirmar a exclusão, selecione **OK**.

      ![Captura de tela que mostra o item selecionado no designer com o painel de detalhes aberto e com o botão de reticências selecionadas e a opção "excluir".](./media/create-stateful-stateless-workflows-visual-studio-code/delete-item-from-designer.png)

### <a name="add-the-office-365-outlook-action"></a>Adicionar a ação do Outlook do Office 365

1. No designer, no gatilho que você adicionou, selecione **nova etapa**.

   O prompt **escolher uma operação** aparece no designer e o **painel Adicionar uma ação** é reaberto para que você possa selecionar a próxima ação.

1. No painel **Adicionar uma ação** , na caixa de pesquisa **escolher uma operação** , selecione **Azure** para que você possa encontrar e selecionar uma ação para um conector gerenciado que é implantado no Azure.

   Este exemplo seleciona e usa a ação do Outlook do Office 365, **enviar um email (v2)**.

   ![Captura de tela que mostra o designer de aplicativo lógico e * * adicionar um painel ação * * com o Office 365 Outlook "enviar um email" ação selecionada.](./media/create-stateful-stateless-workflows-visual-studio-code/add-send-email-action.png)

1. No painel de detalhes da ação, selecione **entrar** para que você possa criar uma conexão com sua conta de email.

   ![Captura de tela que mostra o designer de aplicativo lógico e * * enviar um email (v2) * * painel com "entrar" selecionado.](./media/create-stateful-stateless-workflows-visual-studio-code/send-email-action-sign-in.png)

   > [!NOTE]
   > Se você receber o erro, `Failed to create connection...` talvez tenha escolhido uma região atualmente sem suporte para seu aplicativo lógico. As atualizações para adicionar mais regiões estão em andamento. Enquanto isso, para as regiões com suporte no momento, examine a [página do GitHub de problemas conhecidos](https://github.com/Azure/logicapps/blob/master/articles/logic-apps-public-preview-known-issues.md#available-regions)da extensão de visualização.

1. Quando Visual Studio Code solicitar o consentimento para acessar sua conta de email, selecione **abrir**.

   ![Captura de tela que mostra o Visual Studio Code prompt para permitir o acesso.](./media/create-stateful-stateless-workflows-visual-studio-code/visual-studio-code-open-external-website.png)

   > [!TIP]
   > Para evitar prompts futuros, selecione **configurar domínios confiáveis** para que você possa adicionar a página de autenticação como um domínio confiável.

1. Siga os prompts subsequentes para entrar, permitir o acesso e permitir o retorno a Visual Studio Code.

   > [!NOTE]
   > Se houver muito tempo antes de concluir os prompts, o processo de autenticação atingirá o tempo limite e falhará. Nesse caso, retorne ao designer e tente entrar novamente para criar a conexão.

1. Quando a extensão de visualização de aplicativos lógicos do Azure solicitar o consentimento para acessar sua conta de email, selecione **abrir**. Siga o prompt subsequente para permitir o acesso.

   ![Captura de tela que mostra o prompt de extensão de visualização para permitir o acesso.](./media/create-stateful-stateless-workflows-visual-studio-code/allow-preview-extension-open-uri.png)

   > [!TIP]
   > Para evitar solicitações futuras, selecione **não perguntar novamente para esta extensão**.

   Depois que Visual Studio Code cria sua conexão, alguns conectores mostram a mensagem que `The connection will be valid for {n} days only.` esse limite de tempo aplica somente à duração enquanto você cria seu aplicativo lógico no Visual Studio Code. Após a implantação, esse limite não se aplica mais porque seu aplicativo lógico pode autenticar em tempo de execução usando sua [identidade gerenciada atribuída pelo sistema](../logic-apps/create-managed-service-identity.md)habilitada automaticamente. Essa identidade gerenciada difere das credenciais de autenticação ou da cadeia de conexão que você usa ao criar uma conexão. Se você desabilitar essa identidade gerenciada atribuída pelo sistema, as conexões não funcionarão no tempo de execução.

1. No designer, se a ação **enviar um email** não aparecer selecionada, selecione essa ação.

1. No painel detalhes da ação, na guia **parâmetros** , forneça as informações necessárias para a ação, por exemplo:

   ![Captura de tela que mostra o designer de aplicativo lógico com detalhes para a ação "enviar um email" do Outlook do Office 365.](./media/create-stateful-stateless-workflows-visual-studio-code/send-email-action-details.png)

   | Propriedade | Obrigatório | Valor | Descrição |
   |----------|----------|-------|-------------|
   | **Para** | Sim | <*your-email-address*> | O destinatário do email, que pode ser seu endereço de email para fins de teste. Este exemplo usa o email fictício, `sophiaowen@fabrikam.com` . |
   | **Assunto** | Sim | `An email from your example workflow` | O assunto do email |
   | **Corpo** | Sim | `Hello from your example workflow!` | O conteúdo do corpo do email |
   ||||

   > [!NOTE]
   > Se você quiser fazer alterações no painel de detalhes na guia **configurações**, **executar após**, ou **resultado estático** , certifique-se de selecionar **concluído** para confirmar essas alterações antes de alternar as guias ou alterar o foco para o designer. Caso contrário, Visual Studio Code não manterá suas alterações. Para obter mais informações, consulte a página do [GitHub de problemas conhecidos](https://github.com/Azure/logicapps/blob/master/articles/logic-apps-public-preview-known-issues.md)da extensão de visualização.

1. No designer, selecione **salvar**.

Em seguida, execute e depure seu fluxo de trabalho localmente no Visual Studio Code.

<a name="debug-test-locally"></a>

## <a name="run-and-debug-locally"></a>Executar e depurar localmente

Para testar seu aplicativo lógico, siga estas etapas para iniciar uma sessão de depuração e localizar a URL para o ponto de extremidade que é criado pelo gatilho de solicitação. Você precisa dessa URL para que possa enviar posteriormente uma solicitação para esse ponto de extremidade.

1. Para ajudá-lo a depurar com mais facilidade um fluxo de trabalho de aplicativo lógico sem estado, você pode [habilitar o histórico de execução para esse fluxo de trabalho](#run-history).

1. Na barra de ferramentas Visual Studio Code, abra o menu **executar** e selecione **Iniciar Depuração** (F5).

   A janela do **terminal** é aberta para que você possa examinar a sessão de depuração.

1. Agora, localize a URL de retorno de chamada para o ponto de extremidade no gatilho de solicitação.

   1. Reabra o painel do Explorer para que você possa exibir seu projeto.

   1. Na **workflow.js** menu de atalho do arquivo, selecione **visão geral**.

      ![Captura de tela que mostra o painel do Explorer e a janela de atalho para a workflow.jsno arquivo com a "visão geral" selecionada.](./media/create-stateful-stateless-workflows-visual-studio-code/open-workflow-overview.png)

   1. Localize o valor da **URL de retorno de chamada** , que é semelhante a esta URL para o gatilho de solicitação de exemplo:

      `http://localhost:7071/api/<workflow-name>/triggers/manual/invoke?api-version=2020-05-01-preview&sp=%2Ftriggers%2Fmanual%2Frun&sv=1.0&sig=<shared-access-signature>`

      ![Captura de tela que mostra a página Visão geral do fluxo de trabalho com URL de retorno de chamada](./media/create-stateful-stateless-workflows-visual-studio-code/find-callback-url.png)

1. Para testar a URL de retorno de chamada disparando o fluxo de trabalho do aplicativo lógico, abra o [postmaster](https://www.postman.com/downloads/) ou sua ferramenta preferida para criar e enviar solicitações.

   Este exemplo continua usando o postmaster. Para obter mais informações, consulte [introdução do postmaster](https://learning.postman.com/docs/getting-started/introduction/).

   1. Na barra de ferramentas do postmaster, selecione **novo**.

      ![Captura de tela que mostra o postmaster com o botão novo selecionado](./media/create-stateful-stateless-workflows-visual-studio-code/postman-create-request.png)

   1. No painel **criar novo** , em **blocos de construção**, selecione **solicitação**.

   1. Na janela **salvar solicitação** , em **nome da solicitação**, forneça um nome para a solicitação, por exemplo, `Test workflow trigger` .

   1. Em **selecionar uma coleção ou pasta para salvar**, selecione **criar coleção**.

   1. Em **todas as coleções**, forneça um nome para a coleção a ser criada para organizar suas solicitações, pressione Enter e selecione **salvar para <*nome* > da coleção**. Este exemplo usa `Logic Apps requests` como o nome da coleção.

      O painel de solicitação do postmaster é aberto para que você possa enviar uma solicitação para a URL de retorno de chamada para o gatilho de solicitação.

      ![Captura de tela que mostra o postmaster com o painel de solicitação aberto](./media/create-stateful-stateless-workflows-visual-studio-code/postman-request-pane.png)

   1. Retornar para Visual Studio Code. na página Visão geral do fluxo de trabalho, copie o valor da propriedade **URL de retorno de chamada** .

   1. Retornar ao postmaster. No painel de solicitação, próximo à lista de métodos, que atualmente mostra **Get** como o método de solicitação padrão, Cole a URL de retorno de chamada que você copiou anteriormente na caixa endereço e selecione **Enviar**.

      ![Captura de tela que mostra o postmaster e a URL de retorno de chamada na caixa de endereço com o botão enviar selecionado](./media/create-stateful-stateless-workflows-visual-studio-code/postman-test-call-back-url.png)

      O fluxo de trabalho do aplicativo lógico de exemplo envia um email que é semelhante a este exemplo:

      ![Captura de tela que mostra o email do Outlook, conforme descrito no exemplo](./media/create-stateful-stateless-workflows-visual-studio-code/workflow-app-result-email.png)

1. Em Visual Studio Code, retorne à página de visão geral do fluxo de trabalho.

   Se você criou um fluxo de trabalho com estado, após a solicitação que você enviou dispara o fluxo de trabalho, a página Visão geral mostra o status e o histórico de execução do fluxo de trabalho. Para obter mais informações sobre status de execução, consulte [revisar histórico de execuções](../logic-apps/monitor-logic-apps.md#review-runs-history).

   ![Captura de tela que mostra a página de visão geral do fluxo de trabalho com status e histórico de execução](./media/create-stateful-stateless-workflows-visual-studio-code/post-trigger-call.png)

   > [!TIP]
   > Se o status de execução não for exibido, tente atualizar a página Visão geral selecionando **Atualizar**.

1. Para examinar os status de cada etapa em uma execução específica e as entradas e saídas da etapa, selecione o botão de reticências (**...**) para essa execução e selecione **Mostrar execução**.

   ![Captura de tela que mostra a linha do histórico de execução do fluxo de trabalho com o botão de reticências e "mostrar execução" selecionado](./media/create-stateful-stateless-workflows-visual-studio-code/show-run-history.png)

   Visual Studio Code mostra os status de execução para cada ação.

1. Para examinar as entradas e saídas de cada etapa, expanda a etapa que você deseja inspecionar. Para examinar melhor as entradas e saídas brutas para essa etapa, selecione **Mostrar entradas brutas** ou **Mostrar saídas brutas**.

   ![Captura de tela que mostra o status de cada etapa no fluxo de trabalho mais as entradas e saídas na ação "enviar um email" expandida](./media/create-stateful-stateless-workflows-visual-studio-code/run-history-details.png)

1. Para interromper a sessão de depuração, no menu **executar** , selecione **parar depuração** (Shift + F5).

<a name="return-response"></a>

## <a name="return-a-response-to-the-caller"></a>Retornar uma resposta ao chamador

Para retornar uma resposta para o chamador que enviou uma solicitação ao seu aplicativo lógico, você pode usar a [ação de resposta](../connectors/connectors-native-reqres.md) interna para um fluxo de trabalho que começa com o gatilho de solicitação.

1. No designer do aplicativo lógico, na ação **enviar um email** , selecione **nova etapa**.

   O prompt **escolher uma operação** aparece no designer e o **painel Adicionar uma ação** é reaberto para que você possa selecionar a próxima ação.

1. No painel **Adicionar uma ação** , na caixa de pesquisa **escolher uma ação** , certifique-se de que o **interno** está selecionado. Na caixa de pesquisa, insira `response` e selecione a ação de **resposta** .

   ![Captura de tela que mostra o designer de aplicativo lógico com a ação de resposta selecionada.](./media/create-stateful-stateless-workflows-visual-studio-code/add-response-action.png)

   Quando a ação de **resposta** aparece no designer, o painel detalhes da ação é aberto automaticamente.

   ![Captura de tela que mostra o designer de aplicativo lógico com o painel de detalhes da ação "resposta" aberto e a propriedade "corpo" definida como o valor da propriedade "corpo" da ação "enviar um email".](./media/create-stateful-stateless-workflows-visual-studio-code/response-action-details.png)

1. Na guia **parâmetros** , forneça as informações necessárias para a função que você deseja chamar.

   Este exemplo retorna o valor da propriedade **Body** que é a saída da ação **enviar um email** .

   1. Clique dentro da caixa de propriedade **corpo** para que a lista de conteúdo dinâmico apareça e mostre os valores de saída disponíveis do gatilho anterior e as ações no fluxo de trabalho.

      ![Captura de tela que mostra o painel de detalhes da ação "resposta" com o ponteiro do mouse dentro da propriedade "corpo" para que a lista de conteúdo dinâmico seja exibida.](./media/create-stateful-stateless-workflows-visual-studio-code/open-dynamic-content-list.png)

   1. Na lista conteúdo dinâmico, em **enviar um email**, selecione **corpo**.

      ![Captura de tela que mostra a lista de conteúdo dinâmico aberto. Na lista, no cabeçalho "enviar um email", o valor de saída "corpo" é selecionado.](./media/create-stateful-stateless-workflows-visual-studio-code/select-send-email-action-body-output-value.png)

      Quando terminar, a propriedade **Body** da ação de resposta será agora definida como o valor de saída do **corpo** da ação **enviar um email** .

      ![Captura de tela que mostra o status de cada etapa no fluxo de trabalho mais as entradas e saídas na ação expandida de "resposta".](./media/create-stateful-stateless-workflows-visual-studio-code/response-action-details-body-property.png)

1. No designer, selecione **salvar**.

<a name="retest-workflow"></a>

## <a name="retest-your-logic-app"></a>Testar novamente seu aplicativo lógico

Depois de fazer atualizações em seu aplicativo lógico, você pode executar outro teste executando novamente o depurador no Visual Studio e enviando outra solicitação para disparar seu aplicativo lógico atualizado, semelhante às etapas em [depurar e testar seu aplicativo lógico](#debug-test-locally).

1. Na barra de ferramentas Visual Studio Code, abra o menu **executar** e selecione **Iniciar Depuração** (F5).

1. No postmaster ou em sua ferramenta para criar e enviar solicitações, envie outra solicitação para disparar seu fluxo de trabalho.

1. Se você criou um fluxo de trabalho com estado, na página Visão geral do fluxo de trabalho, verifique o status da execução mais recente. Para exibir o status, as entradas e as saídas de cada etapa na execução, selecione o botão de reticências (**...**) para essa execução e selecione **Mostrar execução**.

   Por exemplo, aqui está o status passo a passo para uma execução após a atualização do fluxo de trabalho de exemplo com a ação de resposta.

   ![Captura de tela que mostra o status de cada etapa no fluxo de trabalho atualizado mais as entradas e saídas na ação expandida de "resposta".](./media/create-stateful-stateless-workflows-visual-studio-code/run-history-details-rerun.png)

1. Para interromper a sessão de depuração, no menu **executar** , selecione **parar depuração** (Shift + F5).

<a name="publish-azure"></a>

## <a name="publish-to-azure"></a>Publicar no Azure

Em Visual Studio Code, você pode implantar seu projeto diretamente no Azure, que publica seu aplicativo lógico usando o novo tipo de recurso de **aplicativo lógico (versão prévia)** . Semelhante ao recurso de aplicativo de funções no Azure Functions, a implantação para esse novo tipo de recurso requer que você selecione um [plano de hospedagem e tipo de preço](../app-service/overview-hosting-plans.md), que pode ser configurado durante a implantação. Para obter mais informações sobre planos de hospedagem e preços, consulte estes tópicos:

* [Escalar verticalmente um serviço de Azure App](../app-service/manage-scale-up.md)
* [Escala e hospedagem do Azure Functions](../azure-functions/functions-scale.md)

Você pode publicar seu aplicativo lógico como um novo recurso, que cria automaticamente quaisquer recursos adicionais necessários, como uma [conta de armazenamento do Azure, semelhante aos requisitos do aplicativo de funções](../azure-functions/storage-considerations.md). Ou então, você pode publicar seu aplicativo lógico em um recurso de aplicativo lógico implantado anteriormente **(versão prévia)** , que substitui esse aplicativo lógico.

### <a name="publish-as-a-new-logic-app-preview-resource"></a>Publicar como um novo recurso de aplicativo lógico (versão prévia)

1. Na barra de ferramentas Código do Visual Studio, selecione o ícone do Azure.

1. Na barra de ferramentas **do painel Azure: aplicativos lógicos (visualização)** , selecione **implantar no aplicativo lógico**.

   ![Captura de tela que mostra o painel "Azure: aplicativos lógicos (visualização)" e a barra de ferramentas do painel com "implantar no aplicativo lógico" selecionado.](./media/create-stateful-stateless-workflows-visual-studio-code/deploy-to-logic-app.png)

1. Na lista que Visual Studio Code é aberta, selecione uma destas opções:

   * **Criar novo aplicativo lógico (versão prévia) no Azure** (rápido)
   * **Criar novo aplicativo lógico (versão prévia) no Azure avançado**
   * Um recurso de **aplicativo lógico** implantado anteriormente (versão prévia), se existir algum

   Este exemplo continua com **criar novo aplicativo lógico (versão prévia) no Azure avançado**.

   ![Captura de tela que mostra o painel "Azure: aplicativos lógicos (visualização)" com uma lista com "criar novo aplicativo lógico (visualização) no Azure" selecionado.](./media/create-stateful-stateless-workflows-visual-studio-code/select-create-logic-app-options.png)

1. Para criar seu novo recurso de **aplicativo lógico (versão prévia)** , siga estas etapas:

   1. Forneça um nome global exclusivo para seu novo aplicativo lógico, que é o nome a ser usado para o recurso de **aplicativo lógico (versão prévia)** . Este exemplo usa o `example-logic-app-preview`.

      ![Captura de tela que mostra o painel "Azure: aplicativos lógicos (visualização)" e um prompt para fornecer um nome para o novo aplicativo lógico a ser criado.](./media/create-stateful-stateless-workflows-visual-studio-code/enter-logic-app-name.png)

   1. Selecione um plano de hospedagem para seu novo aplicativo lógico, ou para o [**plano do serviço de aplicativo**](../azure-functions/functions-scale.md#app-service-plan) ou [**Premium**](../azure-functions/functions-scale.md#premium-plan). Este exemplo seleciona o **plano do serviço de aplicativo**.

      ![Captura de tela que mostra o painel "Azure: aplicativos lógicos (visualização)" e um prompt para selecionar "plano do serviço de aplicativo" ou "Premium".](./media/create-stateful-stateless-workflows-visual-studio-code/select-hosting-plan.png)

   1. Crie um novo plano do serviço de aplicativo ou selecione um plano existente. Este exemplo seleciona **criar novo plano do serviço de aplicativo**.

      ![Captura de tela que mostra o painel "Azure: aplicativos lógicos (visualização)" e um prompt para "criar novo plano do serviço de aplicativo" ou selecionar um plano do serviço de aplicativo existente.](./media/create-stateful-stateless-workflows-visual-studio-code/create-app-service-plan.png)

   1. Forneça um nome para o plano do serviço de aplicativo e, em seguida, selecione um [tipo de preço](../app-service/overview-hosting-plans.md) para o plano. Este exemplo seleciona o plano **gratuito F1** .

      ![Captura de tela que mostra o painel "Azure: aplicativos lógicos (visualização)" e um prompt para selecionar um tipo de preço.](./media/create-stateful-stateless-workflows-visual-studio-code/select-pricing-tier.png)

   1. Para obter o desempenho ideal, localize e selecione o mesmo grupo de recursos que o seu projeto para a implantação.

      > [!NOTE]
      > Embora você possa criar ou usar um grupo de recursos diferente, isso pode afetar o desempenho. Se você criar ou escolher um grupo de recursos diferente, mas cancelar após o prompt de confirmação aparecer, sua implantação também será cancelada.

   1. Para fluxos de trabalho com estado, selecione **criar nova conta de armazenamento** ou uma conta de armazenamento existente.

      ![Captura de tela que mostra o painel "Azure: aplicativos lógicos (visualização)" e um prompt para criar ou selecionar uma conta de armazenamento.](./media/create-stateful-stateless-workflows-visual-studio-code/create-storage-account.png)

   1. Para facilitar o registro em log de diagnóstico e o recurso de rastreamento, você pode selecionar um recurso de Application Insights existente. Caso contrário, você pode selecionar **criar novo recurso de Application insights**ou configurar Application Insights no portal do Azure depois de implantar seu aplicativo.

      Antes de implantar o, certifique-se de adicionar o `logLevel` objeto ao `logging` objeto no **host.jsno** arquivo que existe no nível raiz do projeto e defina `Host.Triggers.Workflow` como `Information` , por exemplo:

      ```json
      "logLevel": {
         "Host.Triggers.Workflow": "Information"
      },
      ```

      Veja como a **host.jsno** arquivo pode parecer:

      ```json
      {
         "version": "2.0",
         "logging": {
            "applicationInsights": {
               "samplingExcludedTypes": "Request",
               "samplingSettings": {
                  "isEnabled": true
               }
            },
            "logLevel": {
               "Host.Triggers.Workflow": "Information"
            }
         }
      }
      ``` 

   Quando terminar, Visual Studio Code começará a criar e implantar os recursos necessários para publicar seu aplicativo lógico.

1. Para revisar e monitorar o processo de implantação, no menu **Exibir** , selecione **saída**. Na lista da barra de ferramentas da janela saída, selecione **aplicativos lógicos do Azure**.

   ![Captura de tela que mostra a janela de saída com o "aplicativo lógico do Azure" selecionado na lista de barras de ferramentas, juntamente com o progresso e os status da implantação.](./media/create-stateful-stateless-workflows-visual-studio-code/logic-app-deployment-output-window.png)

   Quando Visual Studio Code concluir a implantação do fluxo de trabalho do aplicativo lógico no Azure, essa mensagem será exibida:

   ![Captura de tela que mostra uma mensagem informando que a implantação para o Azure foi concluída com êxito.](./media/create-stateful-stateless-workflows-visual-studio-code/deployment-to-azure-completed.png)

   Parabéns, seu aplicativo lógico agora está ativo no Azure e habilitado por padrão.

Em seguida, você pode aprender a executar estas tarefas:

* [Localize seu aplicativo lógico implantado na portal do Azure](#find-manage-deployed-workflows-portal) ou [no Visual Studio Code](#find-manage-deployed-workflows-vs-code).

* [Habilite o histórico de execução em fluxos de trabalho de aplicativo lógico sem estado](#run-history).

* [Habilite o monitoramento em um recurso de aplicativo lógico implantado **(versão prévia)** ](#enable-monitoring).

<a name="find-manage-deployed-workflows-vs-code"></a>

## <a name="find-and-manage-deployed-logic-apps-in-visual-studio-code"></a>Localizar e gerenciar aplicativos lógicos implantados no Visual Studio Code

No Visual Studio Code, você pode exibir todos os aplicativos lógicos implantados em sua assinatura do Azure, sejam eles os **aplicativos lógicos** originais ou o tipo de recurso **aplicativo lógico (versão prévia)** e selecionar tarefas que ajudam a gerenciar esses aplicativos lógicos. No entanto, para acessar ambos os tipos de recurso, você precisa dos **aplicativos lógicos do Azure** e das extensões de **aplicativos lógicos do Azure (versão prévia)** para Visual Studio Code.

1. Na barra de ferramentas à esquerda, selecione o ícone do Azure. No painel **Azure: aplicativos lógicos (visualização)** , expanda sua assinatura, que mostra todos os aplicativos lógicos implantados para essa assinatura.

1. Localize e selecione o aplicativo lógico que você deseja gerenciar. Abra o menu de atalho do aplicativo lógico e selecione a tarefa que você deseja executar.

   Por exemplo, você pode selecionar tarefas como parar, iniciar, reiniciar ou excluir seu aplicativo lógico implantado.

   ![Captura de tela que mostra Visual Studio Code com o painel de extensão "aplicativos lógicos do Azure (visualização)" aberto e o fluxo de trabalho implantado.](./media/create-stateful-stateless-workflows-visual-studio-code/find-deployed-workflow-visual-studio-code.png)

1. Para exibir todos os fluxos de trabalho no aplicativo lógico, expanda seu aplicativo lógico e, em seguida, expanda o nó **fluxos de trabalho** .

1. Para exibir um fluxo de trabalho específico, abra o menu de atalho do fluxo de trabalho e selecione **abrir no designer**, que abre o fluxo de trabalho no modo somente leitura.

   Para editar o fluxo de trabalho, você tem estas opções:

   * No Visual Studio Code, abra o **workflow.js** do seu projeto no arquivo no designer do aplicativo lógico, faça suas edições e reimplante seu aplicativo lógico no Azure.

   * Na portal do Azure, [Localize e abra seu aplicativo lógico](#find-manage-deployed-workflows-portal). Localize, edite e salve o fluxo de trabalho.

1. Para abrir o aplicativo lógico implantado no portal do Azure, abra o menu de atalho do aplicativo lógico e selecione **abrir no portal**.

   O portal do Azure é aberto no navegador, você entra no portal automaticamente se estiver conectado a Visual Studio Code e mostra seu aplicativo lógico.

   ![Captura de tela que mostra a página de portal do Azure para seu aplicativo lógico no Visual Studio Code.](./media/create-stateful-stateless-workflows-visual-studio-code/deployed-workflow-azure-portal.png)

   Você também pode entrar separadamente para o portal do Azure, use a caixa de pesquisa do portal para localizar seu aplicativo lógico e, em seguida, selecione seu aplicativo lógico na lista de resultados.

   ![Captura de tela que mostra a portal do Azure e a barra de pesquisa com os resultados da pesquisa para o aplicativo lógico implantado, que aparece selecionado.](./media/create-stateful-stateless-workflows-visual-studio-code/find-deployed-workflow-azure-portal.png)

<a name="find-manage-deployed-workflows-portal"></a>

## <a name="find-and-manage-deployed-logic-apps-in-the-portal"></a>Localizar e gerenciar aplicativos lógicos implantados no portal

No portal do Azure, você pode exibir todos os aplicativos lógicos implantados que estão em sua assinatura do Azure, sejam eles o tipo de recurso **aplicativos lógicos** originais ou o tipo de recurso **aplicativo lógico (versão prévia)** . Atualmente, cada tipo de recurso é organizado e gerenciado como categorias separadas no Azure.

> [!NOTE]
> Para visualização pública, você só pode exibir recursos de aplicativo lógico implantado **(versão prévia)** no portal do Azure, não criar novos recursos de **aplicativo lógico (versão prévia)** . Você pode criar esses aplicativos lógicos somente no Visual Studio Code. No entanto, você pode [Adicionar fluxos de trabalho](#add-workflows) a aplicativos lógicos implantados com esse tipo de recurso.

Para localizar aplicativos lógicos que têm o tipo de recurso **aplicativo lógico (versão prévia)** , siga estas etapas:

1. Na caixa de pesquisa portal do Azure, digite `logic app preview` . Quando a lista de resultados aparecer, em **Serviços**, selecione **aplicativo lógico (versão prévia)**.

   ![Captura de tela que mostra a caixa de pesquisa portal do Azure com o texto de pesquisa "visualização do aplicativo lógico".](./media/create-stateful-stateless-workflows-visual-studio-code/portal-find-logic-app-preview-resource.png)

1. No painel **aplicativo lógico (visualização)** , localize e selecione o aplicativo lógico que você implantou de Visual Studio Code.

   ![Captura de tela que mostra os recursos de portal do Azure e aplicativo lógico (versão prévia) implantados no Azure.](./media/create-stateful-stateless-workflows-visual-studio-code/logic-app-preview-resources-pane.png)

   O portal do Azure abre a página de recursos individuais para o aplicativo lógico selecionado.

   ![Captura de tela que mostra a página de recursos do fluxo de trabalho do aplicativo lógico no portal do Azure.](./media/create-stateful-stateless-workflows-visual-studio-code/deployed-workflow-azure-portal.png)

1. Para exibir os fluxos de trabalho neste aplicativo lógico, no menu do aplicativo lógico, selecione **fluxos de trabalho**.

   O painel **fluxos de trabalho** mostra todos os fluxos de trabalho no aplicativo lógico atual. Este exemplo mostra o fluxo de trabalho que você criou no Visual Studio Code.

   ![Captura de tela que mostra uma página de recursos "aplicativo lógico (visualização)" com o painel "fluxos de trabalho" aberto e o fluxo de trabalho implantado](./media/create-stateful-stateless-workflows-visual-studio-code/deployed-logic-app-workflows-pane.png)

1. Para exibir um fluxo de trabalho, no painel **fluxos de trabalho** , selecione esse fluxo de trabalho.

   O painel fluxo de trabalho é aberto e mostra mais informações e tarefas que podem ser executadas nesse fluxo de trabalho.

   Por exemplo, para exibir as etapas no fluxo de trabalho, selecione **Designer**.

   ![Captura de tela que mostra o painel "visão geral" do fluxo de trabalho selecionado, enquanto o menu fluxo de trabalho mostra o comando "designer" selecionado.](./media/create-stateful-stateless-workflows-visual-studio-code/workflow-overview-pane-select-designer.png)

   O designer do aplicativo lógico é aberto e mostra o fluxo de trabalho que você criou Visual Studio Code. Agora você pode fazer alterações nesse fluxo de trabalho na portal do Azure.

   ![Captura de tela que mostra o designer de aplicativo lógico e o fluxo de trabalho implantado de Visual Studio Code.](./media/create-stateful-stateless-workflows-visual-studio-code/opened-workflow-designer.png)

<a name="add-workflows"></a>

## <a name="add-a-workflow-to-deployed-logic-apps"></a>Adicionar um fluxo de trabalho a aplicativos lógicos implantados

Por meio do portal do Azure, você pode adicionar fluxos de trabalho em branco a um recurso de **aplicativo lógico (versão prévia)** implantado de Visual Studio Code e criar esses fluxos de trabalho no portal do Azure.

1. Na [portal do Azure](https://portal.azure.com), localize e selecione o recurso aplicativo lógico implantado **(versão prévia)** .

1. No menu do aplicativo lógico, selecione **fluxos de trabalho**. No painel **fluxos de trabalho** , selecione **Adicionar**.

   ![Captura de tela que mostra o painel "fluxos de trabalho" do aplicativo lógico selecionado e a barra de ferramentas com o comando "Adicionar" selecionado.](./media/create-stateful-stateless-workflows-visual-studio-code/add-new-workflow.png)

1. No **novo painel fluxo de trabalho** , forneça um nome para o fluxo de trabalho. Selecione a opção com **estado** ou **sem monitoração de estado** **>** **Create**.

   Depois que o Azure implantar o novo fluxo de trabalho, que aparece no painel **fluxos de trabalho** , selecione esse fluxo de trabalho para executar o gerenciamento e outras tarefas, como abrir o designer de aplicativo lógico ou o modo de exibição de código.

   ![Captura de tela que mostra o fluxo de trabalho selecionado com opções de gerenciamento e revisão.](./media/create-stateful-stateless-workflows-visual-studio-code/view-new-workflow.png)

   Por exemplo, abrir o designer para um novo fluxo de trabalho mostra uma tela em branco. Agora você pode criar esse fluxo de trabalho no portal do Azure.

   ![Captura de tela que mostra o designer do aplicativo lógico e um fluxo de trabalho em branco.](./media/create-stateful-stateless-workflows-visual-studio-code/opened-blank-workflow-designer.png)

<a name="run-history"></a>

## <a name="run-history-for-stateless-logic-app-workflows"></a>Histórico de execuções de fluxos de trabalho de aplicativo lógico sem estado

Para depurar mais facilmente um fluxo de trabalho de aplicativo lógico sem estado, você pode habilitar o histórico de execução para esse fluxo de trabalho em Visual Studio Code ou no portal do Azure e, em seguida, desabilitar o histórico de execução quando terminar.

### <a name="for-a-stateless-logic-app-workflow-in-visual-studio-code"></a>Para um fluxo de trabalho de aplicativo lógico sem estado no Visual Studio Code

Se você estiver trabalhando no e executando o fluxo de trabalho do aplicativo lógico sem estado localmente no Visual Studio Code, siga estas etapas:

1. Em seu projeto, localize e expanda a pasta **fluxo de trabalho-designtime** . Localize e abra o **local.settings.jsno** arquivo.

1. Adicione a `Workflows.{yourWorkflowName}.operationOptions` propriedade e defina o valor como `WithStatelessRunHistory` , por exemplo:

   ```json
   {
      "IsEncrypted": false,
      "Values": {
         "AzureWebJobsStorage": "UseDevelopmentStorage=true",
         "FUNCTIONS_WORKER_RUNTIME": "dotnet",
         "Workflows.{yourWorkflowName}.OperationOptions": "WithStatelessRunHistory"
      }
   }
   ```

1. Para desabilitar o histórico de execuções quando terminar, exclua a `Workflows.{yourWorkflowName}.OperationOptions` propriedade e seu valor ou defina a propriedade como `None` .

### <a name="for-a-stateless-logic-app-workflow-in-the-azure-portal"></a>Para um fluxo de trabalho de aplicativo lógico sem estado no portal do Azure

Se você já implantou seu projeto no portal do Azure, siga estas etapas:

1. Na [portal do Azure](https://portal.azure.com), localize e abra seu recurso de **aplicativo lógico (versão prévia)** .

1. No menu do aplicativo lógico, em **configurações**, selecione **configuração**.

1. Na guia **configurações do aplicativo** , selecione **nova configuração de aplicativo**.

1. No painel **Adicionar/Editar configuração de aplicativo** , na caixa **nome** , digite este nome de opção de operação: 

   `Workflows.{yourWorkflowName}.OperationOptions`

1. Na caixa **valor** , insira o seguinte valor: `WithStatelessRunHistory`

   Por exemplo:

   ![Captura de tela que mostra o portal do Azure e o recurso de aplicativo lógico (versão prévia) com a "configuração" > "nova configuração de aplicativo" < painel "Adicionar/Editar configuração de aplicativo" aberto e os "fluxos de trabalho. {yourWorkflowName}. Operationoptions ", opção definida como" WithStatelessRunHistory ".](./media/create-stateful-stateless-workflows-visual-studio-code/stateless-operation-options-run-history.png)

1. Quando terminar, selecione **OK**. No painel **configuração** , selecione **salvar**.

Para habilitar o monitoramento em seu recurso de aplicativo lógico implantado (versão prévia), continue na próxima seção.

<a name="enable-monitoring"></a>

## <a name="enable-monitoring-for-deployed-logic-app-preview-resources"></a>Habilitar o monitoramento de recursos do aplicativo lógico implantado (versão prévia)

Para habilitar o monitoramento em um recurso de aplicativo lógico implantado **(versão prévia)** , siga estas etapas:

1. Na [portal do Azure](https://portal.azure.com), localize e selecione o recurso aplicativo lógico implantado **(versão prévia)** .

1. No menu desse recurso, em **API**, selecione **CORS**.

1. No painel **CORS** , em **origens permitidas**, adicione o caractere curinga (*).

1. Quando terminar, na barra de ferramentas **CORS** , selecione **salvar**.

   ![Captura de tela que mostra a portal do Azure com um recurso de aplicativos lógicos implantados (versão prévia). No menu de recursos, "CORS" é selecionado com uma nova entrada para "origens permitidas" definida como o caractere curinga "*".](./media/create-stateful-stateless-workflows-visual-studio-code/enable-run-history-deployed-logic-app.png)

<a name="deploy-docker"></a>

## <a name="deploy-to-docker-container"></a>Implantar no contêiner do Docker

Usando a [ferramenta CLI (interface de linha de comando) do .NET Core](/dotnet/core/tools/), você pode criar seu projeto e, em seguida, publicar sua compilação. Em seguida, você pode criar e usar um [contêiner do Docker](/visualstudio/docker/tutorials/docker-tutorial#what-is-a-container) como o destino para implantar o fluxo de trabalho do aplicativo lógico. Para obter mais informações, consulte estes tópicos:

* [Introdução aos contêineres e ao Docker](/dotnet/architecture/microservices/container-docker-introduction/)
* [Introdução ao .NET e ao Docker](/dotnet/core/docker/introduction)
* [Terminologia do Docker](/dotnet/architecture/microservices/container-docker-introduction/docker-terminology)
* [Tutorial: introdução ao Docker](/visualstudio/docker/tutorials/docker-tutorial)

1. Para criar seu projeto, abra um prompt de linha de comando e execute este comando:

   `dotnet build -c release`

   Para obter mais informações, consulte a página de referência de [Build do dotnet](/dotnet/core/tools/dotnet-build/) .

1. Publique sua compilação executando este comando:

   `dotnet publish -c release`

   Para obter mais informações, consulte a página de referência do [dotnet Publish](/dotnet/core/tools/dotnet-publish/) .

1. Crie um contêiner do Docker usando um arquivo do Docker para um fluxo de trabalho do .NET e execute este comando:

   `docker build --tag local/workflowcontainer .`

   Por exemplo, aqui está um exemplo de arquivo Docker que implanta um aplicativo lógico com estado e especifica a cadeia de conexão para a conta de armazenamento do Azure que foi usada para publicar o aplicativo lógico no portal do Azure. Para localizar e copiar a cadeia de conexão da conta de armazenamento no portal do Azure, examine [gerenciar chaves da conta de armazenamento](../storage/common/storage-account-keys-manage.md?tabs=azure-portal#view-account-access-keys).

   ![Captura de tela que mostra a portal do Azure com chaves de acesso da conta de armazenamento e cadeia de conexão copiadas.](./media/create-stateful-stateless-workflows-visual-studio-code/find-storage-account-connection-string.png)

   A cadeia de conexão é semelhante a este exemplo:

   `DefaultEndpointsProtocol=https;AccountName=fabrikamstorageaccount;AccountKey={access-key};EndpointSuffix=core.windows.net`

   Este é o formato do arquivo do Docker:


   ```text
   FROM mcr.microsoft.com/azure-functions/dotnet:3.0.14492-appservice

   ENV AzureWebJobsStorage <storage-account-connection-string>
   ENV AZURE_FUNCTIONS_ENVIRONMENT Development
   ENV AzureWebJobsScriptRoot=/home/site/wwwroot
   ENV AzureFunctionsJobHost__Logging__Console__IsEnabled=true
   ENV FUNCTIONS_V2_COMPATIBILITY_MODE=true

   COPY ./bin/Release/netcoreapp3.1/publish/ /home/site/wwwroot
   ```

   Para obter mais informações, consulte [Docker Build](https://docs.docker.com/engine/reference/commandline/build/).

1. Salve a cadeia de caracteres em um local seguro para que você possa adicionar posteriormente a cadeia de caracteres à **local.settings.jsem** arquivos no projeto que você usa para criar seu aplicativo lógico no Visual Studio Code.

1. Execute o contêiner localmente usando este comando:

   `docker run -e WEBSITE_HOSTNAME=localhost -p 8080:80 local/workflowcontainer`

   Para obter mais informações, consulte [Docker Run](https://docs.docker.com/engine/reference/commandline/run/).

1. Para obter a URL de retorno de chamada para o gatilho de solicitação, envie esta solicitação:

   `POST /runtime/webhooks/workflow/api/management/workflows/{workflow-name}/triggers/{trigger-name}/listCallbackUrl?api-version=2019-10-01-edge-preview&code={master-key}`

   O valor <*master-key*> é definido na conta de armazenamento do Azure que você definiu para `AzureWebJobsStorage` no arquivo, **Azure-webjobs-segredos/{Deployment-Name}/host.jsem**, onde você pode encontrar o valor nesta seção:

   ```json
   {
     <...>
     "masterKey": {
        "name": "master",
        "value": "<master-key>",
        "encrypted": false
     },
     <...>
   }
   ```

<a name="nested-behavior"></a>

## <a name="nested-behavior-differences-between-stateful-and-stateless-logic-apps"></a>Diferenças de comportamento aninhado entre aplicativos lógicos com estado e sem estado

Você pode [fazer com que um fluxo de trabalho de aplicativo lógico](../logic-apps/logic-apps-http-endpoint.md) possa ser chamado por outros fluxos de trabalho de aplicativo lógico que existam no mesmo recurso de **aplicativo lógico (versão prévia)** usando o gatilho de [solicitação](../connectors/connectors-native-reqres.md) , o gatilho de [webhook http](../connectors/connectors-native-webhook.md) ou gatilhos de conector gerenciado que têm o [tipo ApiConnectionWehook](../logic-apps/logic-apps-workflow-actions-triggers.md#apiconnectionwebhook-trigger) e podem receber solicitações HTTPS.

Aqui estão os padrões de comportamento que os fluxos de trabalho de aplicativo lógico aninhados podem seguir depois que um fluxo de trabalho pai chama um fluxo de trabalho filho:

* Padrão de sondagem assíncrona

  O pai não aguarda uma resposta para sua chamada inicial, mas verifica continuamente o histórico de execução do filho até que o filho termine a execução. Por padrão, os fluxos de trabalho com estado seguem esse padrão, que é ideal para fluxos de trabalho filho de longa execução que podem exceder [os limites de tempo limite de solicitação](../logic-apps/logic-apps-limits-and-config.md).

* Padrão síncrono ("disparar e esquecer")

  O filho reconhece a chamada retornando imediatamente uma `202 ACCEPTED` resposta, e o pai continua para a próxima ação sem esperar os resultados do filho. Em vez disso, o pai recebe os resultados quando o filho termina de ser executado. Fluxos de trabalho com estado filho que não incluem uma ação de resposta sempre seguem o padrão síncrono. Para fluxos de trabalho com estado filho, o histórico de execução está disponível para análise.

  Para habilitar esse comportamento, na definição JSON do fluxo de trabalho, defina a `operationOptions` propriedade como `DisableAsyncPattern` . Para obter mais informações, consulte [gatilho e tipos de ação – opções de operação](../logic-apps/logic-apps-workflow-actions-triggers.md#operation-options).

* Gatilho e espera

  Para um fluxo de trabalho com estado filho, o pai espera por uma resposta que retorna os resultados do filho. Esse padrão funciona de forma semelhante ao uso do [gatilho http interno ou da ação](../connectors/connectors-native-http.md) para chamar um fluxo de trabalho filho. Fluxos de trabalho sem estado filho que não incluem uma ação de resposta retornam imediatamente uma `202 ACCEPTED` resposta, mas o pai espera que o filho termine antes de continuar para a próxima ação. Esses comportamentos se aplicam somente a fluxos de trabalho sem estado filho.

Esta tabela especifica o comportamento do fluxo de trabalho filho com base em se o pai e o filho estão com estado, sem monitoração de estado ou são tipos de fluxo de trabalho mistos:

| Fluxo de trabalho pai | Fluxo de trabalho filho | Comportamento filho |
|-----------------|----------------|----------------|
| Com estado | Com estado | Assíncrono ou síncrono com `"operationOptions": "DisableAsyncPattern"` configuração |
| Com estado | Sem estado | Gatilho e espera |
| Sem estado | Com estado | Síncrono |
| Sem estado | Sem estado | Gatilho e espera |
||||

## <a name="limits"></a>Limites

Embora muitos [limites existentes para aplicativos lógicos do Azure](../logic-apps/logic-apps-limits-and-config.md) sejam os mesmos para esse tipo de recurso, aqui estão as diferenças nesta extensão de visualização pública:

* Conectores gerenciados: 50 solicitações por minuto por conexão

* Para a [ação de código embutido para a ação de JavaScript](../logic-apps/logic-apps-add-run-inline-code.md) , esses limites foram alterados:

  * O limite de caracteres de código aumenta de 1.024 caracteres para 100.000 caracteres.

  * O limite de tempo para executar o código aumenta de cinco segundos para 15 segundos.

## <a name="next-steps"></a>Próximas etapas

Gostaríamos de conhecer suas experiências com essa extensão de visualização pública!

* Para bugs ou problemas, [crie seus problemas no GitHub](https://github.com/Azure/logicapps/issues).
* Para perguntas, solicitações, comentários e outros comentários, [Use este formulário de comentários](https://aka.ms/lafeedback).
