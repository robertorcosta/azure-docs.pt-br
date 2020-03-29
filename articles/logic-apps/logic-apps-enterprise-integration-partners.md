---
title: Adicionar parceiros comerciais para integrações B2B
description: Crie parceiros comerciais em sua conta de integração para usar com o Azure Logic Apps
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, logicappspm
ms.topic: article
ms.date: 06/22/2019
ms.openlocfilehash: e58cbe85f30ea09adde45d55bb7b80c710c45495
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "74792429"
---
# <a name="add-trading-partners-to-integration-accounts-for-azure-logic-apps"></a>Adicionar parceiros comerciais às contas de integração dos Aplicativos Azure Logic

No [Azure Logic Apps,](../logic-apps/logic-apps-overview.md)você pode criar fluxos de trabalho automatizados de integração business-to-business (B2B) usando uma [conta de integração](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md) com seus aplicativos lógicos. Para representar sua organização e outros, você cria e adiciona parceiros comerciais como artefatos à sua conta de integração. Parceiros são entidades que participam de transações B2B e trocam mensagens entre si.

Antes de criar esses parceiros, certifique-se de discutir e compartilhar informações com seus parceiros sobre como identificar e validar as mensagens que o outro envia. Depois de concordar com esses detalhes, você está pronto para criar parceiros em sua conta de integração.

## <a name="partner-roles-in-integration-accounts"></a>Funções de parceiro em contas de integração

Para definir os detalhes sobre as mensagens trocadas com seus parceiros, você cria e adiciona [acordos](../logic-apps/logic-apps-enterprise-integration-agreements.md) como artefatos à sua conta de integração. Os acordos exigem pelo menos dois parceiros em sua conta de integração. Sua organização é sempre o *parceiro anfitrião* no acordo. A organização que troca mensagens com sua organização é a *parceira convidada.* Um parceiro convidado pode ser outra empresa, ou até mesmo um departamento em sua própria organização. Depois de adicionar esses parceiros, você pode criar um contrato.

Em um acordo, você especifica os detalhes para lidar com mensagens recebidas e saídas da perspectiva do parceiro host. Para as mensagens recebidas, as **Configurações de recebimento** especificam como o parceiro host recebe mensagens do parceiro convidado no acordo. Para mensagens de saída, as **Configurações de envio** especificam como o parceiro host envia mensagens para o parceiro convidado.

## <a name="prerequisites"></a>Pré-requisitos

* Uma assinatura do Azure. Caso você ainda não tenha uma assinatura do Azure, [inscreva-se em uma conta gratuita do Azure](https://azure.microsoft.com/free/).

* Uma [conta de integração](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md) para armazenar seus parceiros, acordos e outros artefatos B2B. Essa conta de integração deve estar associada à sua assinatura do Azure.

## <a name="create-partner"></a>Criar parceiro

1. Faça login no [portal Azure](https://portal.azure.com).

1. No menu principal do Azure, selecione **Todos os serviços**. Na caixa de pesquisa, digite "integração" e selecione **Contas de Integração**.

   ![Selecione "Contas de integração"](./media/logic-apps-enterprise-integration-partners/find-integration-accounts.png)

1. Em **Contas de Integração**, selecione a conta de integração onde você deseja adicionar seus parceiros.

   ![Selecionar conta de integração](./media/logic-apps-enterprise-integration-partners/select-integration-account.png)

1. Escolha o bloco **Parceiros**.

   ![Escolher o bloco "Parceiros"](./media/logic-apps-enterprise-integration-partners/choose-partners.png)

1. Em **parceiros**, escolha **Adicionar**. Em **Add Partner,** forneça os detalhes do parceiro conforme descrito na tabela abaixo.

   ![Escolha "Adicionar" e forneça detalhes do parceiro](./media/logic-apps-enterprise-integration-partners/add-partners.png)

   | Propriedade | Obrigatório | Descrição |
   |----------|----------|-------------|
   | **Nome** | Sim | O nome do parceiro |
   | **Qualificador** | Sim | O órgão de autenticação que fornece identidades comerciais únicas para organizações, por exemplo, **D-U-N-S (Dun & Bradstreet)**. <p>Os parceiros podem optar por uma identidade de negócios mutuamente definida. Para esses cenários, selecione **Mutuamente Definido** para EDIFACT ou **Mutuamente Definido (X12)** para X12. <p>Para RosettaNet, selecione apenas **DUNS**, que é o padrão. |
   | **Valor** | Sim | Um valor que identifica os documentos que seus aplicativos lógicos recebem. <p>Para RosettaNet, este valor deve ser um número de nove dígitos que corresponde ao número DUNS. |
   ||||

   > [!NOTE]
   > Para parceiros que usam o RosettaNet, você pode especificar informações adicionais criando primeiro esses parceiros e, em seguida, [editando-os depois](#edit-partner).

1. Quando terminar, escolha **OK**.

   Seu novo parceiro agora aparece na lista **de Parceiros.** Além disso, o bloco **parceiros** atualiza o número atual de parceiros.

   ![Novo parceiro](./media/logic-apps-enterprise-integration-partners/new-partner.png)

<a name="edit-partner"></a>

## <a name="edit-partner"></a>Editar parceiro

1. No [portal do Azure](https://portal.azure.com), encontre e selecione sua conta de integração.
Escolha o bloco **Parceiros**.

   ![Escolher o bloco "Parceiros"](./media/logic-apps-enterprise-integration-partners/edit.png)

1. Em **Parceiros,** selecione o parceiro que deseja editar e escolha **Editar**. Em **Editar,** faça suas alterações.

   ![Fazer e salvar suas alterações](./media/logic-apps-enterprise-integration-partners/edit-partner.png)

   Para RosettaNet, em **Propriedades de ParceiroS RosettaNet,** você pode especificar essas informações adicionais:

   | Propriedade | Obrigatório | Descrição |
   |----------|----------|-------------|
   | **Classificação do Parceiro** | Não | O tipo de organização do parceiro |
   | **Código da cadeia de suprimentos** | Não | O código da cadeia de suprimentos do parceiro, por exemplo, "Tecnologia da Informação" ou "Componentes Eletrônicos" |
   | **Nome de contato** | Não | O nome de contato do parceiro |
   | **Email** | Não | O endereço de e-mail do parceiro |
   | **Fax** | Não | O número de fax do parceiro |
   | **Telefone** | Não | O número de telefone do parceiro |
   ||||

1. Quando terminar, escolha **OK** para salvar suas mudanças.

## <a name="delete-partner"></a>Excluir parceiro

1. No [portal do Azure](https://portal.azure.com), encontre e selecione sua conta de integração. Escolha o bloco **Parceiros**.

   ![Escolher o bloco "Parceiros"](./media/logic-apps-enterprise-integration-partners/choose-partners-to-delete.png)

1. Em **Parceiros**, selecione o parceiro que deseja excluir. Escolha **Excluir**.

   ![Excluir parceiro](./media/logic-apps-enterprise-integration-partners/delete-partner.png)

## <a name="next-steps"></a>Próximas etapas

* Saiba mais sobre [acordos](../logic-apps/logic-apps-enterprise-integration-agreements.md)