---
title: Segmentos do UNH 2.5 em mensagens EDIFACT
description: Resolva mensagens EDIFACT com segmentos UNH2.5 em Aplicativos de Lógica Do Azure com pacote de integração corporativa
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, logicappspm
ms.topic: article
ms.date: 04/27/2017
ms.openlocfilehash: ad50cbb423f8c60f1caad159bc1a20cf96ed98aa
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "74792546"
---
# <a name="handle-edifact-documents-with-unh25-segments-in-azure-logic-apps"></a>Manipular documentos EDIFACT com segmentos UNH 2.5 nos Aplicativos Lógicos do Azure

Se existir um segmento UNH2.5 em um documento EDIFACT, o segmento será usado para pesquisa de esquemas. Por exemplo, nesta mensagem EDIFACT amostra, `EAN008`o campo UNH é:

`UNH+SSDD1+ORDERS:D:03B:UN:EAN008`

Para lidar com esta mensagem, siga estas etapas descritas abaixo:

1. Atualize o esquema.

1. Verifique as configurações do acordo.

## <a name="update-the-schema"></a>Atualizar o esquema

Para processar a mensagem, você precisa implantar um esquema que tenha o nome do nó raiz do UNH2.5. Por exemplo, o nome raiz do esquema para `EFACT_D03B_ORDERS_EAN008`o campo de unh amostra é . Para `D03B_ORDERS` cada um que tenha um segmento DIFERENTE de UNH2.5, você tem que implantar um esquema individual.

## <a name="add-schema-to-edifact-agreement"></a>Adicionar esquema ao acordo EDIFACT

### <a name="edifact-decode"></a>Decodificação de EDIFACT

Para decodificar a mensagem recebida, configure o esquema nas configurações de recebimento do contrato EDIFACT:

1. No [portal Azure,](https://portal.azure.com)abra sua conta de integração.

1. Adicione o esquema à sua conta de integração.

1. Configure o esquema nas configurações de recebimento do contrato EDIFACT.

1. Selecione o contrato EDIFACT e selecione **Editar como JSON**. Adicionar o valor do UNH2.5 `schemaReferences` à seção Do Acordo de Recebimento:

   ![Adicionar UNH2.5 para receber acordo](./media/logic-apps-enterprise-integration-edifact_inputfile_unh2.5/image1.png)

### <a name="edifact-encode"></a>Codificação de EDIFACT

Para codificar a mensagem recebida, configure o esquema nas configurações de envio de contrato EDIFACT

1. No [portal Azure,](https://portal.azure.com)abra sua conta de integração.

1. Adicione o esquema à sua conta de integração.

1. Configure o esquema nas configurações de envio do contrato EDIFACT.

1. Selecione o contrato de EDIFACT e clique em **Editar como JSON**.  Adicione o valor UNH2.5 no contrato de envio **schemaReferences**

1. Selecione o contrato EDIFACT e selecione **Editar como JSON**. Adicionar o valor do UNH2.5 `schemaReferences` à seção Enviar Acordo:

   ![Adicionar UNH2.5 para enviar acordo](./media/logic-apps-enterprise-integration-edifact_inputfile_unh2.5/image2.png)

## <a name="next-steps"></a>Próximas etapas

* Saiba mais sobre [os contratos de conta de integração](../logic-apps/logic-apps-enterprise-integration-agreements.md)