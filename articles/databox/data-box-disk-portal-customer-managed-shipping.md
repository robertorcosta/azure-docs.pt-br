---
title: Remessa autogerenciada do Microsoft Azure Data Box Disk | Microsoft Docs em dados
description: Descreve o fluxo de trabalho de remessa autogerenciada para dispositivos Azure Data Box Disk
services: databox
author: twooley
ms.service: databox
ms.subservice: disk
ms.topic: how-to
ms.date: 05/20/2020
ms.author: twooley
ms.openlocfilehash: f3292e2088397e332323bec71f9a5965eddd0fe0
ms.sourcegitcommit: 9c3cfbe2bee467d0e6966c2bfdeddbe039cad029
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/24/2020
ms.locfileid: "88783222"
---
# <a name="use-self-managed-shipping-for-azure-data-box-disk-in-the-azure-portal"></a>Use a remessa autogerenciada para Azure Data Box Disk no portal do Azure

Este artigo descreve as tarefas de remessa autogerenciada para solicitar, pegar e retirar o Azure Data Box Disk. Você pode gerenciar o Data Box Disk usando o portal do Azure.

## <a name="prerequisites"></a>Pré-requisitos

A remessa autogerenciada está disponível como uma opção quando você [Compra o Azure Data Box Disk](data-box-disk-deploy-ordered.md). A remessa autogerenciada está disponível nas seguintes regiões:

* Governo dos EUA
* Europa Ocidental
* Japão
* Singapura
* Coreia do Sul
* África do Sul
* Índia (versão prévia)

## <a name="use-self-managed-shipping"></a>Usar remessa autogerenciada

Ao fazer o pedido do Data Box Disk, você pode escolher a opção de remessa autogerenciada.

1. Em seu pedido do Azure Data Box Disk, em **Detalhes de contato**, selecione **+ Adicionar Endereço para Entrega**.

   ![Remessa autogerenciada](media\data-box-portal-customer-managed-shipping\choose-self-managed-shipping-1.png)

2. Ao escolher tipo de remessa, selecione a opção **Remessa autogerenciada**. Essa opção só estará disponível se você estiver em uma região com suporte, conforme descrito em pré-requisitos.

3. Depois de fornecer seu endereço para remessa, você precisará validá-lo e concluir seu pedido.

   ![Remessa autogerenciada](media\data-box-portal-customer-managed-shipping\choose-self-managed-shipping-2.png)

4. Depois que o dispositivo tiver sido preparado e você receber uma notificação por email, você poderá agendar uma retirada. Em seu pedido do Azure Data Box Disk, acesse **Visão geral** e, em seguida, selecione **Agendar retirada**.

   ![Pedir um dispositivo Data Box para retirada](media\data-box-disk-portal-customer-managed-shipping\data-box-disk-user-pickup-01b.png)

5. Siga as instruções em **Agendar a retirada do Azure**. Para obter seu código de autorização, envie um email para [adbops@microsoft.com](mailto:adbops@microsoft.com) a fim de agendar a retirada do dispositivo do datacenter da sua região.

   ![Agendar retirada](media\data-box-disk-portal-customer-managed-shipping\data-box-disk-user-pickup-02c.png)

6. Depois de agendar a retirada do dispositivo, você poderá exibir o código de autorização em **Agendar a retirada do Azure**.

   ![Exibir seu código de autorização](media\data-box-disk-portal-customer-managed-shipping\data-box-disk-authcode-01b.png)

   Anote este **Código de autorização**.

   De acordo com os requisitos de segurança, no momento da retirada do agendamento, é necessário apresentar o nome da pessoa que vai chegar à escolha.

   Você também precisa fornecer detalhes de quem vai para o datacenter para retirada. Você ou o ponto de contato deve estar com uma ID de foto oficial que será validada no datacenter.

   Além disso, a pessoa que está pegando o dispositivo também precisa ter o **Código de autorização**. O código de autorização é exclusivo para uma retirada ou uma redistribuição e é validado no datacenter.

7. Seu pedido será movido automaticamente para o estado **Retirado** depois que o dispositivo tiver sido obtido do datacenter.

   ![Recolhido](media\data-box-disk-portal-customer-managed-shipping\data-box-disk-ready-disk-01b.png)

8. Depois que o dispositivo for retirado, você poderá copiar dados para o Data Box Disk em seu site. Após a conclusão da cópia dos dados, você poderá preparar o Data Box Disk para o envio.

   Depois de concluir a cópia de dados, você precisará entrar em contato com a equipe de operações para agendar um compromisso para a retirada. Você precisará compartilhar os detalhes da pessoa que chegará ao datacenter para retirar os discos. O datacenter também precisará verificar o código de autorização no momento da retirada. O código de autorização para retirada estará disponível no portal do Azure em **Agendar retirada**.

   > [!NOTE]
   > Não compartilhe o código de autorização por email. Isso só deve ser verificado no datacenter durante a coleta.

9. Se você tiver recebido um compromisso para a retirada, agora o pedido deverá estar no estado **Pronto para receber no datacenter do Azure** no portal do Azure.

   ![Exibir seu código de autorização](media\data-box-disk-portal-customer-managed-shipping\data-box-disk-authcode-dropoff-02b.png)

10. Depois que a ID e o código de autorização são verificados e você retirou o dispositivo no datacenter, o status do pedido deve ser **Recebido**.

    ![Recebido concluído](media\data-box-disk-portal-customer-managed-shipping\data-box-disk-received-01a.png)

11. Depois que o dispositivo for recebido, a cópia de dados continuará. Quando a cópia for concluída, o pedido será concluído.

## <a name="next-steps"></a>Próximas etapas

* [Início Rápido: Implantar o Azure Data Box Disk usando o portal do Azure](data-box-disk-quickstart-portal.md)
