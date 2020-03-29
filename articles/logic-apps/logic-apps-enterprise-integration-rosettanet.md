---
title: Mensagens RosettaNet para integração B2B
description: Troque mensagens RosettaNet em aplicativos azure logic com pacote de integração corporativa
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, logicappspm
ms.topic: article
ms.date: 06/22/2019
ms.openlocfilehash: 2cc2ac08b9624c1d1d9bee9ce91a7c91189d7f2c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "74792428"
---
# <a name="exchange-rosettanet-messages-for-b2b-enterprise-integration-in-azure-logic-apps"></a>Troque mensagens RosettaNet para integração corporativa B2B em Aplicativos de Lógica Do Azure

[A RosettaNet](https://resources.gs1us.org) é um consórcio sem fins lucrativos que estabeleceu processos padrão para o compartilhamento de informações comerciais. Esses padrões são comumente usados para processos de cadeia de suprimentos e são difundidos nos setores de semicondutores, eletrônicos e logística. O consórcio RosettaNet cria e mantém os PIPs (Partner Interface Processes, processos de interface de parceiros), que fornecem definições comuns de processos de negócios para todas as trocas de mensagens do RosettaNet. O RosettaNet é baseado no XML e define diretrizes de mensagens, interfaces para processos de negócios e estruturas de implementação para comunicação entre empresas.

No [Azure Logic Apps,](../logic-apps/logic-apps-overview.md)o conector RosettaNet ajuda você a criar soluções de integração que suportam os padrões RosettaNet. O conector é baseado na versão 2.0.01 do RosettaNet Implementation Framework (RNIF). O RNIF é uma estrutura de aplicativos de rede aberta que permite que os parceiros de negócios executem de forma colaborativa os PIPs Da RosettaNet. Essa estrutura define a estrutura de mensagens, a necessidade de reconhecimentos, a codificação mime (Multipurpose Internet Mail Extensions) e a assinatura digital.

Especificamente, o conector fornece esses recursos:

* Codificar ou receber mensagens RosettaNet.
* Decodique ou envie mensagens RosettaNet.
* Aguarde a resposta e a geração de Notificação de Falha.

Para esses recursos, o conector suporta todos os PIPs definidos pelo RNIF 2.0.01. A comunicação com o parceiro pode ser síncrona ou assíncrona.

## <a name="rosettanet-concepts"></a>Conceitos de RosettaNet

Aqui estão alguns conceitos e termos que são exclusivos da especificação RosettaNet e são importantes na construção de integrações baseadas no RosettaNet:

* **Pip**

  A organização RosettaNet cria e mantém os PIPs (Partner Interface Processes, processos de interface de parceiros), que fornecem definições comuns de processos de negócios para todas as trocas de mensagens do RosettaNet. Cada especificação PIP fornece um arquivo de definição de tipo de documento (DTD) e um documento de orientação de mensagem. O arquivo DTD define a estrutura de mensagem de conteúdo de serviço. O documento de orientação de mensagem, que é um arquivo HTML legível por humanos, especifica restrições no nível do elemento. Juntos, esses arquivos fornecem uma definição completa do processo de negócios.

   Os PIPs são categorizados por uma função de negócios de alto nível, ou cluster, e uma subfunção, ou segmento. Por exemplo, "3A4" é o PIP para pedido de compra, enquanto "3" é a função Gerenciamento de Pedidos, e "3A" é a subfunção De entrada de pedidos de cotação &. Para obter mais informações, consulte o [site da RosettaNet](https://resources.gs1us.org).

* **Ação**

  Parte de um PIP, mensagens de ação são mensagens de negócios que são trocadas entre parceiros.

* **Sinal**

   Parte de um PIP, as mensagens de sinal são reconhecimentos que são enviados em resposta a mensagens de ação.

* **Ação única e dupla ação**

  Para um PIP de ação única, a única resposta é uma mensagem de sinal de reconhecimento. Para um PIP de ação dupla, o iniciador recebe uma mensagem de resposta e responde com um reconhecimento, além do fluxo de mensagem de ação única.

## <a name="prerequisites"></a>Pré-requisitos

* Uma assinatura do Azure. Caso você ainda não tenha uma assinatura do Azure, [inscreva-se em uma conta gratuita do Azure](https://azure.microsoft.com/free/).

* Uma [conta de integração](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md) para armazenar seu acordo e outros artefatos B2B. Essa conta de integração deve estar associada à sua assinatura do Azure.

* Pelo menos dois [parceiros](../logic-apps/logic-apps-enterprise-integration-partners.md) que são definidos em sua conta de integração e configurados com o qualificador "DUNS" em **Identidades de Negócios**

* Uma configuração de processo PIP, que é necessária para enviar ou receber mensagens RosettaNet, em sua conta de integração. A configuração do processo armazena todas as características de configuração do PIP. Em seguida, você pode referenciar essa configuração quando criar um acordo com o parceiro. Para criar uma configuração de processo PIP em sua conta de integração, consulte [Adicionar configuração de processo PIP](#add-pip).

* [Certificados opcionais](../logic-apps/logic-apps-enterprise-integration-certificates.md) para criptografar, descriptografar ou assinar as mensagens que você carrega na conta de integração. Os certificados só são necessários se você usar assinatura ou criptografia.

<a name="add-pip"></a>

## <a name="add-pip-process-configuration"></a>Adicionar configuração de processo PIP

Para adicionar uma configuração de processo PIP à sua conta de integração, siga estas etapas:

1. No [portal Azure,](https://portal.azure.com)encontre e abra sua conta de integração.

1. No **painel Visão geral,** selecione o azulejo **RosettaNet PIP.**

   ![Escolha o azulejo RosettaNet](media/logic-apps-enterprise-integration-rosettanet/select-rosettanet-tile.png)

1. Em **RosettaNet PIP,** escolha **Adicionar**. Forneça seus detalhes pip.

   ![Adicionar detalhes do RosettaNet PIP](media/logic-apps-enterprise-integration-rosettanet/add-rosettanet-pip.png)

   | Propriedade | Obrigatório | Descrição |
   |----------|----------|-------------|
   | **Nome** | Sim | Seu nome PIP |
   | **Código PIP** | Sim | O código PIP de três dígitos. Para obter mais informações, consulte [RosettaNet PIPs](https://docs.microsoft.com/biztalk/adapters-and-accelerators/accelerator-rosettanet/rosettanet-pips). |
   | **Versão PIP** | Sim | O número da versão PIP, que está disponível com base no código PIP selecionado |
   ||||

   Para obter mais informações sobre essas propriedades PIP, visite o site da [RosettaNet](https://resources.gs1us.org/RosettaNet-Standards/Standards-Library/PIP-Directory#1043208-pipsreg).

1. Quando terminar, escolha **OK**, que cria a configuração PIP.

1. Para visualizar ou editar a configuração do processo, selecione o PIP e escolha **Editar como JSON**.

   Todas as configurações de configuração do processo vêm das especificações do PIP. Logic Apps preenche a maioria das configurações com os valores padrão que são os valores mais usados para essas propriedades.

   ![Editar configuração pip do RosettaNet](media/logic-apps-enterprise-integration-rosettanet/edit-rosettanet-pip.png)

1. Confirme se as configurações correspondem aos valores na especificação PIP apropriada e atenda às necessidades do seu negócio. Se necessário, atualize os valores no JSON e salve essas alterações.

## <a name="create-rosettanet-agreement"></a>Crie o acordo RosettaNet

1. No [portal do Azure](https://portal.azure.com), localize e abra a conta de integração, caso ainda não esteja aberta.

1. No **painel Visão Geral,** selecione o azulejo **'Acordos'.**

   ![Escolher ladrilho de contratos](media/logic-apps-enterprise-integration-rosettanet/select-agreement-tile.png)

1. Em **Contratos**, escolha **Adicionar**. Forneça os detalhes do seu acordo.

   ![Adicionar detalhes do acordo](media/logic-apps-enterprise-integration-rosettanet/add-agreement-details.png)

   | Propriedade | Obrigatório | Descrição |
   |----------|----------|-------------|
   | **Nome** | Sim | O nome do acordo |
   | **Tipo de acordo** | Sim | Selecione **RosettaNet**. |
   | **Parceiro de Host** | Sim | Um contrato precisa dos parceiros host e convidado. O parceiro host representa a organização que está configurando o contrato. |
   | **Identidade do Host** | Sim | Um identificador para o parceiro host |
   | **Parceiro Convidado** | Sim | Um contrato precisa dos parceiros host e convidado. O parceiro convidado representa a organização que está fazendo negócios com o parceiro host. |
   | **Identidade do Convidado** | Sim | Um identificador para o parceiro convidado |
   | **Configurações de Recebimento** | Varia | Essas propriedades se aplicam a todas as mensagens recebidas pelo parceiro host |
   | **Configurações de Envio** | Varia | Essas propriedades se aplicam a todas as mensagens enviadas pelo parceiro host |  
   | **Referências pip de RosettaNet** | Sim | As referências pip para o acordo. Todas as mensagens RosettaNet requerem configurações PIP. |
   ||||

1. Para configurar seu acordo para receber mensagens recebidas do parceiro convidado, **selecione 'Configurações de recebimento '''**

   ![Receber configurações](media/logic-apps-enterprise-integration-rosettanet/add-agreement-receive-details.png)

   1. Para habilitar a assinatura ou criptografia para mensagens recebidas, em **Mensagens,** selecione **Mensagem deve ser assinada** ou **Mensagem deve ser criptografada,** respectivamente.

      | Propriedade | Obrigatório | Descrição |
      |----------|----------|-------------|
      | **Mensagem deve ser assinada** | Não | Assine as mensagens recebidas com o certificado selecionado. |
      | **Certificado** | Sim, se a assinatura estiver ativada | O certificado para usar para assinar |
      | **Habilitar a criptografia de mensagem** | Não | Criptografe as mensagens recebidas com o certificado selecionado. |
      | **Certificado** | Sim, se a criptografia estiver ativada | O certificado para usar para criptografia |
      ||||

   1. Em cada seleção, selecione o respectivo [certificado](./logic-apps-enterprise-integration-certificates.md), que você adicionou anteriormente à sua conta de integração, para usar para assinatura ou criptografia.

1. Para configurar seu contrato para enviar mensagens ao parceiro convidado, **selecione Enviar configurações**.

   ![Enviar configurações](media/logic-apps-enterprise-integration-rosettanet/add-agreement-send-details.png)

   1. Para habilitar a assinatura ou criptografia para mensagens de saída, em **Mensagens,** selecione **Ativar assinatura de mensagens** ou **Ativar criptografia de mensagens,** respectivamente. Em cada seleção, selecione o respectivo algoritmo e [certificado](./logic-apps-enterprise-integration-certificates.md), que você adicionou anteriormente à sua conta de integração, para usar para assinatura ou criptografia.

      | Propriedade | Obrigatório | Descrição |
      |----------|----------|-------------|
      | **Habilitar a assinatura de mensagens** | Não | Assine mensagens de saída com o algoritmo de assinatura e certificado selecionados. |
      | **Algoritmo de assinatura** | Sim, se a assinatura estiver ativada | O algoritmo de assinatura para usar, com base no certificado selecionado |
      | **Certificado** | Sim, se a assinatura estiver ativada | O certificado para usar para assinar |
      | **Habilitar a criptografia de mensagem** | Não | Criptografe a saída com o algoritmo e certificado de criptografia selecionados. |
      | **Algoritmo de Criptografia** | Sim, se a criptografia estiver ativada | O algoritmo de criptografia para usar, com base no certificado selecionado |
      | **Certificado** | Sim, se a criptografia estiver ativada | O certificado para usar para criptografia |
      ||||

   1. Em **Endpoints,** especifique as URLs necessárias para o envio de mensagens de ação e reconhecimentos.

      | Propriedade | Obrigatório | Descrição |
      |----------|----------|-------------|
      | **URL de ação** |  Sim | A URL para usar para enviar mensagens de ação. A URL é um campo obrigatório para mensagens síncronas e assíncronas. |
      | **URL de reconhecimento** | Sim | A URL a ser usada para enviar mensagens de reconhecimento. A URL é um campo obrigatório para mensagens assíncronas. |
      ||||

1. Para configurar seu acordo com as referências rosettaNet PIP para parceiros, selecione **referências RosettaNet PIP**. Em **Nome PIP,** selecione o nome para o PIP criado anteriormente.

   ![Referências PIP](media/logic-apps-enterprise-integration-rosettanet/add-agreement-pip-details.png)

   Sua seleção preenche as propriedades restantes, que são baseadas no PIP que você configurou em sua conta de integração. Se necessário, você pode alterar a **função PIP**.

   ![PIP selecionado](media/logic-apps-enterprise-integration-rosettanet/add-agreement-selected-pip.png)

Depois de concluir essas etapas, você está pronto para enviar ou receber mensagens do RosettaNet.

## <a name="rosettanet-templates"></a>Modelos RosettaNet

Para acelerar o desenvolvimento e recomendar padrões de integração, você pode usar modelos de aplicativos lógicos para decodificar e codificar mensagens RosettaNet. Quando você cria um aplicativo lógico, você pode selecionar na galeria de modelos no Logic App Designer. Você também pode encontrar esses modelos no [repositório GitHub para Aplicativos lógicos do Azure](https://github.com/Azure/logicapps).

![Modelos RosettaNet](media/logic-apps-enterprise-integration-rosettanet/decode-encode-rosettanet-templates.png)

## <a name="receive-or-decode-rosettanet-messages"></a>Receba ou decodifique mensagens Do RosettaNet

1. [Crie um aplicativo de lógica em branco](quickstart-create-first-logic-app-workflow.md).

1. [Vincule sua conta de integração](logic-apps-enterprise-integration-create-integration-account.md#link-account) ao seu aplicativo lógico.

1. Antes de adicionar uma ação para decodificar a mensagem RosettaNet, você deve adicionar um gatilho para iniciar seu aplicativo lógico, como um gatilho de solicitação.

1. Depois de adicionar o gatilho, escolha **Novo passo**.

   ![Adicionar gatilho de solicitação](media/logic-apps-enterprise-integration-rosettanet/request-trigger.png)

1. Na caixa de pesquisa, digite "rosettanet", e selecione esta ação: **RosettaNet Decode**

   ![Encontre e selecione a ação "RosettaNet Decode"](media/logic-apps-enterprise-integration-rosettanet/select-decode-rosettanet-action.png)

1. Fornecer as informações para as propriedades da ação:

   ![Fornecer detalhes da ação](media/logic-apps-enterprise-integration-rosettanet/decode-action-details.png)

   | Propriedade | Obrigatório | Descrição |
   |----------|----------|-------------|
   | **Mensagem** | Sim | A mensagem RosettaNet para decodificar  |
   | **Cabeçalhos** | Sim | Os cabeçalhos HTTP que fornecem os valores para a versão, que é a versão RNIF, e o tipo de resposta, que indica o tipo de comunicação entre os parceiros e pode ser síncrono ou assíncrono |
   | **Role** | Sim | O papel do parceiro anfitrião no PIP |
   ||||

   A partir da ação Decode do RosettaNet, a saída, juntamente com outras propriedades, inclui **o sinal outbound**, que você pode optar por codificar e retornar ao parceiro, ou tomar qualquer outra ação sobre essa saída.

## <a name="send-or-encode-rosettanet-messages"></a>Enviar ou codificar mensagens RosettaNet

1. [Crie um aplicativo de lógica em branco](quickstart-create-first-logic-app-workflow.md).

1. [Vincule sua conta de integração](logic-apps-enterprise-integration-create-integration-account.md#link-account) ao seu aplicativo lógico.

1. Antes de adicionar uma ação para codificar a mensagem RosettaNet, você deve adicionar um gatilho para iniciar seu aplicativo lógico, como um gatilho de solicitação.

1. Depois de adicionar o gatilho, escolha **Novo passo**.

   ![Adicionar gatilho de solicitação](media/logic-apps-enterprise-integration-rosettanet/request-trigger.png)

1. Na caixa de pesquisa, digite "rosettanet", e selecione esta ação: **RosettaNet Encode**

   ![Encontre e selecione a ação "RosettaNet Encode"](media/logic-apps-enterprise-integration-rosettanet/select-encode-rosettanet-action.png)

1. Fornecer as informações para as propriedades da ação:

   ![Fornecer detalhes da ação](media/logic-apps-enterprise-integration-rosettanet/encode-action-details.png)

   | Propriedade | Obrigatório | Descrição |
   |----------|----------|-------------|
   | **Mensagem** | Sim | A mensagem RosettaNet para codificar  |
   | **Parceiro de host** | Sim | O nome do parceiro anfitrião |
   | **Parceiro convidado** | Sim | O nome do parceiro convidado |
   | **Código PIP** | Sim | O código PIP |
   | **Versão PIP** | Sim | A versão PIP |  
   | **Identidade de instância PIP** | Sim | O identificador exclusivo para esta mensagem PIP |  
   | **Tipo de mensagem** | Sim | O tipo da mensagem para codificar |  
   | **Role** | Sim | O papel do parceiro anfitrião |
   ||||

   A mensagem codificada está agora pronta para enviar ao parceiro.

1. Para enviar a mensagem codificada, este exemplo usa a ação **HTTP,** que é renomeada como "HTTP - Enviar mensagem codificada para parceiro".

   ![Ação HTTP para enviar mensagem RosettaNet](media/logic-apps-enterprise-integration-rosettanet/send-rosettanet-message-to-partner.png)

   De acordo com os padrões da RosettaNet, as transações comerciais são consideradas completas somente quando todas as etapas definidas pelo PIP estiverem concluídas.

1. Depois que o host envia a mensagem codificada para o parceiro, o host aguarda o sinal e o reconhecimento. Para realizar essa tarefa, adicione o **RosettaNet aguardar a** ação de resposta.

   ![Adicionar ação "RosettaNet wait for response"](media/logic-apps-enterprise-integration-rosettanet/rosettanet-wait-for-response-action.png)

   A duração a ser usada para espera e o número de repetições são baseados na configuração PIP em sua conta de integração. Se a resposta não for recebida, essa ação gerará uma Notificação de Falha. Para lidar com as repetições, coloque sempre o **Encode** e **aguarde as** ações de resposta em um loop **Até.**

   ![Até loop com ações do RosettaNet](media/logic-apps-enterprise-integration-rosettanet/rosettanet-loop.png)

## <a name="next-steps"></a>Próximas etapas

* Saiba como validar, transformar e outras operação de mensagem com o [Enterprise Integration Pack](../logic-apps/logic-apps-enterprise-integration-overview.md)
* Saiba mais sobre outros [conectores de Aplicativos Lógicos](../connectors/apis-list.md)
