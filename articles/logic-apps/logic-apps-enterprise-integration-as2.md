---
title: Envie e receba mensagens AS2 para B2B
description: Troque mensagens AS2 por cenários de integração corporativa B2B usando aplicativos de lógica do Azure com o Enterprise Integration Pack
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, logicappspm
ms.topic: article
ms.date: 02/27/2020
ms.openlocfilehash: 0e7c34e42d0ab68a5dab9718075f02a85322ce6c
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/16/2020
ms.locfileid: "81458821"
---
# <a name="exchange-as2-messages-for-b2b-enterprise-integration-in-azure-logic-apps-with-enterprise-integration-pack"></a>Trocar mensagens AS2 para Enterprise Integration B2B nos Aplicativos Lógicos do Azure com Enterprise Integration Pack

> [!IMPORTANT]
> O conector AS2 original está sendo depreciado, por isso certifique-se de usar o conector **AS2 (v2).** Esta versão fornece os mesmos recursos da versão original, é nativa do tempo de execução do Logic Apps e fornece melhorias significativas de desempenho em termos de throughput e tamanho de mensagem. Além disso, o conector v2 nativo não requer que você crie uma conexão com sua conta de integração. Em vez disso, conforme descrito nos pré-requisitos, certifique-se de vincular sua conta de integração ao aplicativo lógico onde você planeja usar o conector.

Para trabalhar com mensagens AS2 em Aplicativos Lógicos Azure, você pode usar o conector AS2, que fornece gatilhos e ações para gerenciar a comunicação AS2. Por exemplo, para estabelecer segurança e confiabilidade ao transmitir mensagens, você pode usar essas ações:

* [ **Ação do AS2 Encode** ](#encode) para fornecer criptografia, assinatura digital e reconhecimentos através de MDN (Message Disposition Notifications, notificações de disposição de mensagem), que ajudam a apoiar o não-repúdio. Por exemplo, essa ação aplica cabeçalhos AS2/HTTP e executa essas tarefas quando configurada:

  * Assina mensagens de saída.
  * Criptografa mensagens de saída.
  * Comprime a mensagem.
  * Transmite o nome do arquivo no cabeçalho MIME.

* [ **Ação AS2 Decode** ](#decode) para fornecer descriptografia, assinatura digital e reconhecimentos através de Notificações de Disposição de Mensagem (MDN). Por exemplo, essa ação executa essas tarefas:

  * Processa cabeçalhos AS2/HTTP.
  * Concilia MDNs recebidos com as mensagens de saída originais.
  * Atualiza e correlaciona registros no banco de dados de não-repúdio.
  * Grava registros para relatórios de status AS2.
  * Produz o conteúdo da carga como base64 codificado.
  * Determina se os MDNs são necessários. Com base no acordo AS2, determina se os MDNs devem ser síncronos ou assíncronos.
  * Gera MDNs síncronos ou assíncronos com base no contrato AS2.
  * Define os tokens de correlação e propriedades em MDNs.

  Essa ação também executa essas tarefas quando configuradas:

  * Verifica a assinatura.
  * Descriptografa as mensagens.
  * Descomprime a mensagem.
  * Verifique e descha duplicações de iD de mensagem.

Este artigo mostra como adicionar as ações de codificação e decodificação do AS2 a um aplicativo lógico existente.

## <a name="prerequisites"></a>Pré-requisitos

* Uma assinatura do Azure. Caso você ainda não tenha uma assinatura do Azure, [inscreva-se em uma conta gratuita do Azure](https://azure.microsoft.com/free/).

* O aplicativo lógico de onde você deseja usar o conector AS2 e um gatilho que inicia o fluxo de trabalho do seu aplicativo lógico. O conector AS2 fornece apenas ações, não gatilhos. Se você não estiver familiarizado com os Aplicativos Lógicos, examine [O que são Aplicativos Lógicos do Azure](../logic-apps/logic-apps-overview.md) e [Início rápido: crie seu primeiro aplicativo lógico](../logic-apps/quickstart-create-first-logic-app-workflow.md).

* Uma conta de [integração](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md) que está associada à sua assinatura do Azure e vinculada ao aplicativo lógico onde você planeja usar o conector AS2. Tanto seu aplicativo lógico quanto sua conta de integração devem existir no mesmo local ou região do Azure.

* Pelo menos dois [parceiros comerciais](../logic-apps/logic-apps-enterprise-integration-partners.md) que você já definiu em sua conta de integração usando o qualificador de identidade AS2.

* Antes de usar o conector AS2, você deve criar um [contrato](../logic-apps/logic-apps-enterprise-integration-agreements.md) AS2 entre seus parceiros comerciais e armazenar esse contrato em sua conta de integração.

* Se você usar [o Azure Key Vault](../key-vault/general/overview.md) para gerenciamento de certificados, verifique se as chaves do cofre permitem as operações **Encrypt** e **Decrypt.** Caso contrário, as ações de codificação e decodificação falham.

  No portal Azure, vá até a chave do cofre principal, revise as **operações permitidas**da sua chave e confirme se as operações **Encrypt** e **Decrypt** estão selecionadas, por exemplo:

  ![Verifique as principais operações do cofre](media/logic-apps-enterprise-integration-as2/key-vault-permitted-operations.png)

<a name="encode"></a>

## <a name="encode-as2-messages"></a>Codificar mensagens AS2

1. Se você ainda não tem, no [portal Azure,](https://portal.azure.com)abra seu aplicativo lógico no Logic App Designer.

1. No designer, adicione uma nova ação ao seu aplicativo lógico.

1. Em **Escolher uma ação** e embaixo da caixa de pesquisa, selecione **Todos**. Na caixa de pesquisa, digite "as2 encode", e certifique-se de selecionar a ação AS2 (v2): **AS2 Encode**

   ![Selecione "Encode AS2"](./media/logic-apps-enterprise-integration-as2/select-as2-encode.png)

1. Agora forneça informações para essas propriedades:

   | Propriedade | Descrição |
   |----------|-------------|
   | **Mensagem para codificar** | A carga de mensagem |
   | **AS2 de** | O identificador para o remetente de mensagem conforme especificado pelo seu contrato AS2 |
   | **AS2 para** | O identificador para o receptor de mensagem conforme especificado pelo seu contrato AS2 |
   |||

   Por exemplo:

   ![Propriedades de codificação de mensagens](./media/logic-apps-enterprise-integration-as2/as2-message-encoding-details.png)

> [!TIP]
> Se você tiver problemas ao enviar mensagens assinadas ou criptografadas, considere experimentar diferentes formatos de algoritmo SHA256. A especificação AS2 não fornece nenhuma informação sobre formatos SHA256, então cada provedor usa sua própria implementação ou formato.

<a name="decode"></a>

## <a name="decode-as2-messages"></a>Decodificar mensagens AS2

1. Se você ainda não tem, no [portal Azure,](https://portal.azure.com)abra seu aplicativo lógico no Logic App Designer.

1. No designer, adicione uma nova ação ao seu aplicativo lógico.

1. Em **Escolher uma ação** e embaixo da caixa de pesquisa, selecione **Todos**. Na caixa de pesquisa, digite "as2 decode", e certifique-se de selecionar a ação AS2 (v2): **AS2 Decode**

   ![Selecione "Decodificação AS2"](media/logic-apps-enterprise-integration-as2/select-as2-decode.png)

1. Para que a **mensagem codifique** e os **cabeçalhos de mensagem,** selecione esses valores em saídas de gatilho ou ação anteriores.

   Por exemplo, suponha que seu aplicativo lógico receba mensagens através de um gatilho de solicitação. Você pode selecionar as saídas desse gatilho.

   ![Selecione Corpo e Cabeçalhos de saídas de Solicitação](media/logic-apps-enterprise-integration-as2/as2-message-decoding-details.png)

## <a name="sample"></a>Amostra

Para tentar implantar um aplicativo lógico totalmente operacional e o cenário de AS2 de exemplo, confira o[modelo e cenário de aplicativo lógico AS2](https://azure.microsoft.com/documentation/templates/201-logic-app-as2-send-receive/).

## <a name="connector-reference"></a>Referência de conector

Para obter mais detalhes técnicos sobre este conector, como ações e limites descritos pelo arquivo Swagger do conector, consulte a [página de referência do conector](https://docs.microsoft.com/connectors/as2/). 

> [!NOTE]
> Para aplicativos lógicos em um [ambiente de serviço de integração (ISE),](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md)a versão original rotulada pelo ISE deste conector usa os limites de [mensagem ISE.](../logic-apps/logic-apps-limits-and-config.md#message-size-limits)

## <a name="next-steps"></a>Próximas etapas

* Saiba mais sobre outros [conectores de Aplicativos Lógicos](../connectors/apis-list.md)
