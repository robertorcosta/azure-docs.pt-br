---
title: Remessa autogerenciada do Microsoft Azure Data Box | Microsoft Docs em dados
description: Descreve o fluxo de trabalho de remessa autogerenciada para dispositivos Azure Data Box
services: databox
author: priestlg
ms.service: databox
ms.subservice: disk
ms.topic: conceptual
ms.date: 05/20/2020
ms.author: v-grpr
ms.openlocfilehash: 2933eb216b7faed7b28fb53d79fec2f50bc4458d
ms.sourcegitcommit: 493b27fbfd7917c3823a1e4c313d07331d1b732f
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/21/2020
ms.locfileid: "83748925"
---
# <a name="use-self-managed-shipping-for-azure-data-box-in-the-azure-portal"></a>Use a remessa autogerenciada para Azure Data Box no portal do Azure

Este artigo descreve as tarefas de remessa autogerenciada para solicitar, pegar e remover um dispositivo Azure Data Box. Você pode gerenciar o dispositivo Data Box pelo portal do Azure.

## <a name="prerequisites"></a>Pré-requisitos

A remessa autogerenciada está disponível como uma opção quando você [solicita o Azure Data Box](data-box-deploy-ordered.md). A remessa autogerenciada está disponível nas seguintes regiões:

* Governo dos EUA
* Europa Ocidental
* Japão
* Singapura
* Coreia do Sul

## <a name="use-self-managed-shipping"></a>Usar remessa autogerenciada

Ao fazer o pedido do Data Box, você pode escolher a opção de remessa autogerenciada.

1. Em seu pedido do Azure Data Box,em **Detalhes de contato do** , selecione **+ Adicionar Endereço para Entrega**.
   ![Remessa autogerenciada](media\data-box-portal-customer-managed-shipping\choose-self-managed-shipping-1.png)

2. Ao escolher tipo de remessa, selecione a opção **Remessa autogerenciada**. Essa opção só estará disponível se você estiver em uma região com suporte, conforme descrito em pré-requisitos.

3. Depois de fornecer seu endereço para remessa, você precisará validá-lo e concluir seu pedido.
   ![Remessa autogerenciada](media\data-box-portal-customer-managed-shipping\choose-self-managed-shipping-2.png)

4. Depois que o dispositivo tiver sido preparado, você poderá agendar um recebimento.

   Em seu pedido do Azure Data Box, acesse **Visão geral** e, em seguida, selecione **Agendar retirada**.

   ![Pedir um dispositivo Data Box para retirada](media\data-box-portal-customer-managed-shipping\data-box-portal-schedule-pickup-01.png)

5. Siga as instruções em **Agendar a retirada do Azure**.

   Antes de obter seu código de autorização, você deve enviar por email para [adbops@microsoft.com](mailto:adbops@microsoft.com), com o objetivo de agendar a retirada do dispositivo do datacenter da sua região.

   ![Agendar retirada](media\data-box-portal-customer-managed-shipping\data-box-portal-schedule-pickup-email-01.png)

6. Depois de agendar o recebimento do dispositivo, você poderá exibir o código de autorização do dispositivo no painel **Agendar a retirada do Azure**.

   ![Exibir seu código de autorização](media\data-box-portal-customer-managed-shipping\data-box-portal-auth-01b.png)

   Anote este **Código de autorização**. De acordo com os requisitos de segurança, no momento do agendamento de retirada e devolução, é necessário apresentar o nome da pessoa fará a retirada.

   Você também precisa fornecer detalhes de quem vai para o datacenter para retirada. Você ou o ponto de contato deve estar com uma ID de foto oficial que será validada no datacenter.

   Além disso, a pessoa que retirará o dispositivo também precisa ter o **Código de autorização**. O código de autorização é validado no datacenter na hora da retirada.

7. Seu pedido será movido automaticamente para o estado **Retirado** depois que o dispositivo tiver sido obtido do datacenter.

    ![Recolhido](media\data-box-portal-customer-managed-shipping\data-box-portal-picked-up-boxed-01.png)

8. Depois que o dispositivo for retirado, copie os dados para o Data Box em seu site. Após a conclusão da cópia dos dados, você pode preparar a remessa do Data Box. Para obter mais informações, consulte [Preparar para o envio](data-box-deploy-picked-up.md#prepare-to-ship).

   A etapa **Preparar para o envio** precisa ser concluída sem nenhum erro crítico, caso contrário, você precisará executar essa etapa novamente depois de fazer as correções necessárias. Depois que a preparação para o envio for concluída com êxito, você poderá exibir o código de autorização para a devolução na interface do usuário local do dispositivo.

   > [!NOTE]
   > Não compartilhe o código de autorização por email. Isso só deve ser verificado no datacenter durante a coleta.

9. Se você tiver recebido um agendamento para a retirada, o pedido deverá estar no estado **Pronto para receber no data center do Azure** no portal do Azure. Siga as instruções em **Agendar a devolução** para devolver o dispositivo.

   ![Exibir seu código de autorização](media\data-box-portal-customer-managed-shipping\data-box-portal-received-complete-02b.png)

10. Depois que a ID e o código de autorização são verificados e você removeu o dispositivo no datacenter, o status do pedido deve ser **Recebido**.

    ![Recebido concluído](media\data-box-portal-customer-managed-shipping\data-box-portal-received-complete-01.png)

11. Depois que o dispositivo for recebido, a cópia de dados continuará. Quando a cópia for concluída, o pedido será concluído.

## <a name="next-steps"></a>Próximas etapas

* [Introdução ao Azure Data Box](data-box-quickstart-portal.md)
