---
title: Conecte-se ao armazenamento Azure Blob
description: Crie e gerencie blobs em contas de armazenamento do Azure usando aplicativos azure logic
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: conceptual
ms.date: 02/21/2020
tags: connectors
ms.openlocfilehash: eb943bfe36be10d1e95d569a5c1bf48563e909c1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79247352"
---
# <a name="create-and-manage-blobs-in-azure-blob-storage-by-using-azure-logic-apps"></a>Crie e gerencie blobs no Azure Blob Storage usando aplicativos azure logic

Este artigo mostra como você pode acessar e gerenciar arquivos armazenados como blobs em sua conta de armazenamento do Azure de dentro de um aplicativo lógico com o conector do Armazenamento de Blobs do Azure. Dessa forma, você pode criar aplicativos lógicos que automatizam tarefas e fluxos de trabalho para gerenciar seus arquivos. Por exemplo, você pode criar aplicativos lógicos que criam, obtêm, atualizam e excluem arquivos na sua conta de armazenamento.

Suponha que você tem uma ferramenta que é atualizada em um site do Azure. que atua como o gatilho do aplicativo lógico. Quando esse evento ocorre, você pode fazer com que o aplicativo lógico atualize um arquivo em seu contêiner de armazenamento de blobs, o que é uma ação no aplicativo lógico.

Se você não estiver familiarizado com os Aplicativos Lógicos, examine [O que são Aplicativos Lógicos do Azure](../logic-apps/logic-apps-overview.md) e [Início rápido: crie seu primeiro aplicativo lógico](../logic-apps/quickstart-create-first-logic-app-workflow.md). Para obter informações técnicas específicas do conector, confira a [Referência do conector do Armazenamento de Blobs do Azure](https://docs.microsoft.com/connectors/azureblobconnector/).

> [!IMPORTANT]
> Os aplicativos de lógica não podem acessar diretamente contas de armazenamento que estão por trás de firewalls se ambos estiverem na mesma região. Como solução de solução, você pode ter seus aplicativos lógicos e conta de armazenamento em diferentes regiões. Para obter mais informações sobre como habilitar o acesso do Azure Logic Apps para armazenar contas por trás de firewalls, consulte as contas de armazenamento access por trás da seção [firewalls](#storage-firewalls) mais tarde neste tópico.

<a name="blob-storage-limits"></a>

## <a name="limits"></a>limites

* Por padrão, as ações do Azure Blob Storage podem ler ou gravar arquivos com *50 MB ou menores*. Para lidar com arquivos maiores que 50 MB, mas até 1024 MB, as ações do Azure Blob Storage suportam [o acúmulo de mensagens](../logic-apps/logic-apps-handle-large-messages.md). A ação **de conteúdo Get blob** usa implicitamente o chunking.

* Os gatilhos do Azure Blob Storage não suportam o emabaramento. Ao solicitar conteúdo de arquivo, os gatilhos selecionam apenas arquivos de 50 MB ou menores. Para obter arquivos maiores que 50 MB, siga este padrão:

  * Use um gatilho de armazenamento azure Blob que retorna propriedades do arquivo, como **quando uma bolha é adicionada ou modificada (somente propriedades)**.

  * Siga o gatilho com a ação de conteúdo Azure Blob **Get blob,** que lê o arquivo completo e usa implicitamente o chunking.

## <a name="prerequisites"></a>Pré-requisitos

* Uma assinatura do Azure. Se você não tiver uma assinatura do Azure, [inscreva-se em uma conta gratuita do Azure](https://azure.microsoft.com/free/).

* Um [contêiner de armazenamento padrão e uma conta de armazenamento](../storage/blobs/storage-quickstart-blobs-portal.md)

* O aplicativo lógico onde você precisa do acesso à conta de armazenamento de blobs do Azure. Para iniciar seu aplicativo lógico com um gatilho do Armazenamento de Blobs do Azure, você precisará de um [aplicativo lógico em branco](../logic-apps/quickstart-create-first-logic-app-workflow.md).

<a name="add-trigger"></a>

## <a name="add-blob-storage-trigger"></a>Adicionar gatilho de armazenamento de blobs

Nos Aplicativos Lógicos do Azure, cada aplicativo lógico deve começar com um [gatilho](../logic-apps/logic-apps-overview.md#logic-app-concepts), que é disparado quando um evento específico ocorre ou quando uma condição específica é atendida. Cada vez que o gatilho é acionado, o mecanismo de Aplicativos Lógicos cria uma instância de aplicativo lógico e inicia a execução do fluxo de trabalho do aplicativo.

Este exemplo mostra como você pode iniciar um fluxo de trabalho de aplicativo lógico com o **gatilho Quando uma bolha é adicionada ou modificada (somente propriedades)** quando as propriedades de uma bolha são adicionadas ou atualizadas no recipiente de armazenamento.

1. No [portal Azure](https://portal.azure.com) ou Visual Studio, crie um aplicativo de lógica em branco, que abre o Logic App Designer. Este exemplo usa o portal do Azure.

2. Na caixa de pesquisa, digite "blob do azure" como filtro. Na lista de gatilhos, selecione o gatilho desejado.

   Este exemplo usa este gatilho: **Quando uma bolha é adicionada ou modificada (apenas propriedades)**

   ![Selecione o gatilho de armazenamento do Azure Blob](./media/connectors-create-api-azureblobstorage/add-azure-blob-storage-trigger.png)

3. Se forem solicitados os detalhes da conexão, [crie sua conexão de armazenamento de blobs agora](#create-connection). Ou, se a conexão já existir, forneça as informações necessárias para o gatilho.

   Neste exemplo, selecione o contêiner e a pasta que você deseja monitorar.

   1. Na caixa **Contêiner**, selecione o ícone de pasta.

   2. Na lista de pastas, escolha **>** o suporte de ângulo reto (), e navegue até encontrar e selecionar a pasta desejada.

      ![Selecione a pasta de armazenamento para usar com o gatilho](./media/connectors-create-api-azureblobstorage/trigger-select-folder.png)

   3. Selecione o intervalo e a frequência com a qual o gatilho deve verificar alterações na pasta.

4. Quando terminar, selecione **Salvar** na barra de ferramentas do designer.

5. Agora, continue a adicionar uma ou mais ações ao aplicativo lógico para as tarefas que você deseja executar com os resultados do gatilho.

<a name="add-action"></a>

## <a name="add-blob-storage-action"></a>Adicionar ação de armazenamento de blobs

Em Aplicativos Lógicos do Azure, uma [ação](../logic-apps/logic-apps-overview.md#logic-app-concepts) é uma etapa do fluxo de trabalho que segue um gatilho ou outra ação. Neste exemplo, o aplicativo lógico começa com o [gatilho de recorrência](../connectors/connectors-native-recurrence.md).

1. No [Portal do Azure](https://portal.azure.com) ou no Visual Studio, abra seu aplicativo lógico no Designer do Aplicativo Lógico. Este exemplo usa o portal do Azure.

2. No Logic App Designer, o gatilho ou ação, escolha **Novo passo**.

   ![Adicionar novo passo ao fluxo de trabalho do aplicativo lógico](./media/connectors-create-api-azureblobstorage/add-new-step-logic-app-workflow.png) 

   Para adicionar uma ação entre etapas existentes, mova o mouse sobre a seta de conexão. Escolha o sinal**+** de adição () que aparece e **selecione Adicionar uma ação**.

3. Na caixa de pesquisa, digite "blob do azure" como filtro. Na lista de ações, selecione a ação desejada.

   Este exemplo usa esta ação: **Obtenha conteúdo blob**

   ![Selecione a ação de armazenamento do Azure Blob](./media/connectors-create-api-azureblobstorage/add-azure-blob-storage-action.png)

4. Se forem solicitados os detalhes da conexão, [crie sua conexão do Armazenamento de Blobs do Azure agora](#create-connection).
Ou, se a conexão já existir, forneça as informações necessárias para a ação.

   Neste exemplo, selecione o arquivo desejado.

   1. Na caixa **Blob**, selecione o ícone de pasta.
  
      ![Selecione a pasta de armazenamento para usar com ação](./media/connectors-create-api-azureblobstorage/action-select-folder.png)

   2. Encontre e selecione o arquivo que deseja com base no número de **id** da bolha. Você pode encontrar este número **de ID** nos metadados da bolha que é devolvido pelo gatilho de armazenamento blob descrito anteriormente.

5. Quando terminar, selecione **Salvar** na barra de ferramentas do designer.
Para testar seu aplicativo lógico, verifique se a pasta selecionada contém um blob.

Este exemplo obtém apenas o conteúdo de um blob. Para exibir o conteúdo, adicione outra ação que cria um arquivo com o blob usando outro conector. Por exemplo, adicione uma ação do OneDrive que cria um arquivo com base no conteúdo do blob.

<a name="create-connection"></a>

## <a name="connect-to-storage-account"></a>Conectar à conta de armazenamento

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

1. Quando você for solicitado a criar a conexão, forneça essas informações:

   | Propriedade | Obrigatório | Valor | Descrição |
   |----------|----------|-------|-------------|
   | **Nome da conexão** | Sim | <*nome de conexão*> | O nome a criar para a conexão |
   | **Conta de armazenamento** | Sim | <*conta de armazenamento*> | Selecione sua conta de armazenamento na lista. |
   ||||

   Por exemplo: 

   ![Criar conexão de conta de armazenamento Azure Blob](./media/connectors-create-api-azureblobstorage/create-storage-account-connection.png) 

1. Quando estiver pronto, selecione **Criar**

1. Depois de criar sua conexão, continue com [adicionar gatilho de armazenamento blob](#add-trigger) ou adicionar ação de armazenamento [blob](#add-action).

## <a name="connector-reference"></a>Referência de conector

Para obter mais detalhes técnicos sobre este conector, como gatilhos, ações e limites descritos pelo arquivo Swagger do conector, consulte a [página de referência do conector](https://docs.microsoft.com/connectors/azureblobconnector/).

> [!NOTE]
> Para aplicativos lógicos em um [ambiente de serviço de integração (ISE),](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md)a versão rotulada pelo conector ISE usa os limites de [mensagem ISE.](../logic-apps/logic-apps-limits-and-config.md#message-size-limits)

<a name="storage-firewalls"></a>

## <a name="access-storage-accounts-behind-firewalls"></a>Acesse contas de armazenamento por trás de firewalls

Você pode adicionar segurança de rede a uma conta de armazenamento do Azure restringindo o acesso com regras de [firewall e firewall.](../storage/common/storage-network-security.md) No entanto, essa configuração cria um desafio para o Azure e outros serviços da Microsoft que precisam de acesso à conta de armazenamento. A comunicação local no datacenter abstrai os endereços IP internos, para que você não possa configurar regras de firewall com restrições de IP. Para saber mais, consulte [Configurar Redes Virtuais e Firewalls de Armazenamento do Azure](../storage/common/storage-network-security.md).

Aqui estão várias opções para acessar contas de armazenamento por trás de firewalls dos Aplicativos Azure Logic usando o conector De armazenamento Azure Blob ou outras soluções:

* Conector do blob de armazenamento do Azure

  * [Acessar contas de armazenamento em outras regiões](#access-other-regions)
  * [Acesse contas de armazenamento através de uma rede virtual confiável](#access-trusted-virtual-network)

* Outras soluções

  * [Acesse contas de armazenamento como um serviço confiável com identidades gerenciadas](#access-trusted-service)
  * [Acessar contas de armazenamento através do Gerenciamento de API do Azure](#access-api-management)

<a name="access-other-regions"></a>

### <a name="problems-accessing-storage-accounts-in-the-same-region"></a>Problemas para acessar contas de armazenamento na mesma região

Os aplicativos lógicos não podem acessar diretamente contas de armazenamento atrás de firewalls quando ambos estão na mesma região. Como solução de solução, coloque seus aplicativos lógicos em uma região diferente da sua conta de armazenamento e dê acesso aos [endereços IP de saída para os conectores gerenciados em sua região.](../logic-apps/logic-apps-limits-and-config.md#outbound)

> [!NOTE]
> Essa solução não se aplica ao conector de armazenamento de mesa Azure e ao conector de armazenamento azure queue. Em vez disso, para acessar o armazenamento de mesa ou o armazenamento na fila, use o gatilho e as ações http incorporados.

<a name="access-trusted-virtual-network"></a>

### <a name="access-storage-accounts-through-a-trusted-virtual-network"></a>Acesse contas de armazenamento através de uma rede virtual confiável

Você pode colocar a conta de armazenamento em uma rede virtual Do Zure que você gerencia e, em seguida, adicionar essa rede virtual à lista de redes virtuais confiáveis. Para que seu aplicativo lógico acesse a conta de armazenamento por meio de uma [rede virtual confiável,](../virtual-network/virtual-networks-overview.md)você precisa implantar esse aplicativo lógico em um [ambiente de serviço de integração (ISE),](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md)que pode se conectar a recursos em uma rede virtual. Em seguida, você pode adicionar as sub-redes nesse ISE à lista de confiança. Os conectores de armazenamento Azure, como o conector Blob Storage, podem acessar diretamente o contêiner de armazenamento. Essa configuração é a mesma experiência de usar os pontos finais de serviço de um ISE.

<a name="access-trusted-service"></a>

### <a name="access-storage-accounts-as-a-trusted-service-with-managed-identities"></a>Acesse contas de armazenamento como um serviço confiável com identidades gerenciadas

Para dar aos serviços confiáveis da Microsoft acesso a uma conta de armazenamento por meio de um firewall, você pode configurar uma exceção nessa conta de armazenamento para esses serviços. Essa solução permite que os serviços do Azure que ofereçam suporte [a identidades gerenciadas para autenticação](../active-directory/managed-identities-azure-resources/overview.md) acessem contas de armazenamento por trás de firewalls como serviços confiáveis. Especificamente, para um aplicativo lógico no Global Multi-Tenant Azure acessar essas contas de armazenamento, você primeiro habilita o [suporte de identidade gerenciado](../logic-apps/create-managed-service-identity.md) no aplicativo lógico. Em seguida, você usa a ação HTTP ou acionar em seu aplicativo lógico e [definir seu tipo de autenticação para usar a identidade gerenciada do seu aplicativo lógico](../logic-apps/create-managed-service-identity.md#authenticate-access-with-managed-identity). Para este cenário, você pode usar *apenas* a ação HTTP ou o gatilho.

Para configurar a exceção e o suporte de identidade gerenciado, siga estas etapas gerais:

1. Em sua conta de armazenamento, em **Configurações,** selecione **Firewalls e redes virtuais.** Em **Permitir acesso a partir de**, selecione a opção Redes **selecionadas** para que as configurações relacionadas apareçam.

1. Em **Exceções,** **selecione Permitir que serviços confiáveis da Microsoft acessem essa conta de armazenamento**e, em seguida, **selecione Salvar**.

   ![Selecione exceção que permite serviços confiáveis da Microsoft](./media/connectors-create-api-azureblobstorage/allow-trusted-services-firewall.png)

1. Nas configurações do seu aplicativo lógico, [habilite o suporte à identidade gerenciada](../logic-apps/create-managed-service-identity.md).

1. No fluxo de trabalho do seu aplicativo lógico, adicione e configure a ação HTTP ou seja acionado para acessar a conta ou entidade de armazenamento.

   > [!IMPORTANT]
   > Para a saída da ação HTTP ou para disparar chamadas para contas `x-ms-version` do Azure Storage, certifique-se de que o cabeçalho de solicitação inclua a propriedade e a versão da API para a operação que você deseja executar na conta de armazenamento. Para obter mais informações, consulte [Authenticate access with managed identity](../logic-apps/create-managed-service-identity.md#authenticate-access-with-managed-identity) and [Versioning for Azure Storage services](https://docs.microsoft.com/rest/api/storageservices/versioning-for-the-azure-storage-services#specifying-service-versions-in-requests).

1. Nessa ação, [selecione a identidade gerenciada](../logic-apps/create-managed-service-identity.md#authenticate-access-with-managed-identity) a ser usada para autenticação.

<a name="access-api-management"></a>

### <a name="access-storage-accounts-through-azure-api-management"></a>Acessar contas de armazenamento através do Gerenciamento de API do Azure

Se você usar um nível dedicado para [gerenciamento de API,](../api-management/api-management-key-concepts.md)você pode enfrentar a API de armazenamento usando o Gerenciamento de API e permitindo os endereços IP deste último através do firewall. Basicamente, adicione a rede virtual Azure usada pelo Gerenciamento de API à configuração de firewall da conta de armazenamento. Em seguida, você pode usar a ação De gerenciamento de API ou a ação HTTP para chamar as APIs de armazenamento do Azure. No entanto, se você escolher essa opção, você tem que lidar com o processo de autenticação você mesmo. Para obter mais informações, confira [Arquitetura Enterprise Integration simples](https://aka.ms/aisarch).

## <a name="next-steps"></a>Próximas etapas

* Saiba mais sobre outros [conectores de Aplicativos Lógicos](../connectors/apis-list.md)
