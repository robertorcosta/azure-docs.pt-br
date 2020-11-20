---
title: Como habilitar o recurso de solução do Azure VMware
description: Saiba como enviar uma solicitação de suporte para habilitar o recurso de solução do Azure VMware. Você também pode solicitar mais hosts em sua nuvem privada da solução Azure VMware existente.
ms.topic: how-to
ms.date: 11/12/2020
ms.openlocfilehash: cd58611f77c2f2fb1694597fc9ef049420093ccc
ms.sourcegitcommit: cd9754373576d6767c06baccfd500ae88ea733e4
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/20/2020
ms.locfileid: "94967356"
---
# <a name="how-to-enable-azure-vmware-solution-resource"></a>Como habilitar o recurso da solução Azure VMware
Saiba como enviar uma solicitação de suporte para habilitar o recurso de [solução do Azure VMware](introduction.md) . Você também pode solicitar mais hosts em sua nuvem privada da solução Azure VMware existente.

## <a name="eligibility-criteria"></a>Critérios de qualificação

Você precisará de uma conta do Azure em uma assinatura do Azure. A assinatura do Azure deve estar em conformidade com um dos seguintes critérios:

* Uma assinatura em um [ea (Enterprise Agreement do Azure)](../cost-management-billing/manage/ea-portal-agreements.md) com a Microsoft.
* Uma assinatura gerenciada do CSP (provedor de soluções na nuvem) em um plano do Azure.


## <a name="enable-azure-vmware-solution-for-ea-customers"></a>Habilitar solução Azure VMware para clientes EA
Antes de criar o recurso de solução do Azure VMware, você precisará enviar um tíquete de suporte para que seus hosts sejam alocados. Depois que a equipe de suporte receber sua solicitação, levará até cinco dias úteis para confirmar a solicitação e alocar os hosts. Se você tiver uma nuvem privada da Solução VMware no Azure existente e quiser mais hosts alocados, deverá passar pelo mesmo processo.


1. Em seu portal do Azure, em **ajuda + suporte**, crie uma **[nova solicitação de suporte](https://rc.portal.azure.com/#create/Microsoft.Support)** e forneça as seguintes informações para o tíquete:
   - **Tipo de problema:** técnico
   - **Assinatura:** Selecione sua assinatura
   - **Serviço:** Todos os serviços > solução VMware do Azure
   - **Recurso:** Pergunta geral 
   - **Resumo:** Capacidade necessária
   - **Tipo de problema:** Problemas de Gerenciamento de Capacidade
   - **Subtipo do problema:** solicitação do cliente para cota/capacidade de host adicional

1. Na **Descrição** do tíquete de suporte, na guia **detalhes** , forneça:

   - POC ou produção 
   - Nome da Região
   - Número de hosts
   - Quaisquer outros detalhes

   >[!NOTE]
   >A solução Azure VMware recomenda um mínimo de três hosts para criar sua nuvem privada e para redundância N + 1 hosts. 

1. Selecione **revisão + criar** para enviar a solicitação.

   Levará até cinco dias úteis para que um representante de suporte confirme sua solicitação.

   >[!IMPORTANT] 
   >Se você já tiver uma solução existente do Azure VMware e estiver solicitando hosts adicionais, observe que precisamos de cinco dias úteis para alocar os hosts. 

1. Antes de poder provisionar seus hosts, certifique-se de registrar o provedor de recursos **Microsoft. AVS** no portal do Azure.  

   ```azurecli-interactive
   az provider register -n Microsoft.AVS --subscription <your subscription ID>
   ```

   Para conhecer outras maneiras de registrar o provedor de recursos, confira [Provedores e tipos de recursos do Azure](../azure-resource-manager/management/resource-providers-and-types.md).

## <a name="enable-azure-vmware-solution-for-csp-customers"></a>Habilitar a solução Azure VMware para clientes CSP 

Os CSPs devem usar [o Microsoft Partner Center](https://partner.microsoft.com) para habilitar a solução Azure VMware para seus clientes. 

   >[!IMPORTANT] 
   >O serviço de solução do Azure VMware não fornece um ambiente multilocatário e, portanto, os parceiros de hospedagem ainda não têm suporte. 

1. No **Partner Center**, selecione **CSP** para acessar a área **clientes** .

   :::image type="content" source="media/enable-azure-vmware-solution/csp-customers-screen.png" alt-text="Área de clientes do Microsoft Partner Center" lightbox="media/enable-azure-vmware-solution/csp-customers-screen.png":::

1. Selecione o cliente e, em seguida, selecione **Adicionar produtos**.

   :::image type="content" source="media/enable-azure-vmware-solution/csp-partner-center.png" alt-text="Microsoft Partner Center" lightbox="media/enable-azure-vmware-solution/csp-partner-center.png":::

1. Selecione **plano do Azure** e, em seguida, selecione **Adicionar ao carrinho**. 

1. Examine e conclua a configuração geral da assinatura do plano do Azure para seu cliente. Para obter mais informações, consulte a [documentação do Microsoft Partner Center](https://docs.microsoft.com/partner-center/azure-plan-manage).

Depois de configurar o plano do Azure e as permissões de RBAC vSphere necessárias estiverem em vigor como um CSP, você entrará em contato com a Microsoft para habilitar a cota de uma assinatura do plano do Azure. Acesse portal do Azure do Partner Center usando o procedimento **admin em nome do** (Aobo).

1. Entre no [Partner Center](https://partner.microsoft.com).

1. Selecione **CSP** para acessar a área **clientes** .

1. Expanda detalhes do cliente e selecione **portal de gerenciamento do Microsoft Azure**.

1. No portal do Azure, em **ajuda + suporte**, crie uma **[nova solicitação de suporte](https://rc.portal.azure.com/#create/Microsoft.Support)** e forneça as seguintes informações para o tíquete:
   - **Tipo de problema:** técnico
   - **Assinatura:** Selecione sua assinatura
   - **Serviço:** Todos os serviços > solução VMware do Azure
   - **Recurso:** Pergunta geral 
   - **Resumo:** Capacidade necessária
   - **Tipo de problema:** Problemas de Gerenciamento de Capacidade
   - **Subtipo do problema:** solicitação do cliente para cota/capacidade de host adicional

1. Na **Descrição** do tíquete de suporte, na guia **detalhes** , forneça:

   - POC ou produção 
   - Nome da Região
   - Número de hosts
   - Quaisquer outros detalhes
   - O destina-se a hospedar vários clientes?

   >[!NOTE]
   >A solução Azure VMware recomenda um mínimo de três hosts para criar sua nuvem privada e para redundância N + 1 hosts. 

1. Selecione **revisão + criar** para enviar a solicitação.

   Levará até cinco dias úteis para que um representante de suporte confirme sua solicitação.

   >[!IMPORTANT] 
   >Se você já tiver uma solução existente do Azure VMware e estiver solicitando hosts adicionais, observe que precisamos de cinco dias úteis para alocar os hosts. 

1. Depois de adicionado ao plano do Azure e com a cota habilitada, o cliente ou o administrador do parceiro pode implantar uma nuvem privada da solução Azure VMware por meio do portal do Azure. Antes de poder provisionar seus hosts, certifique-se de registrar o provedor de recursos **Microsoft. AVS** no portal do Azure.  

   ```azurecli-interactive
   az provider register -n Microsoft.AVS --subscription <your subscription ID>
   ```

   Para conhecer outras maneiras de registrar o provedor de recursos, confira [Provedores e tipos de recursos do Azure](../azure-resource-manager/management/resource-providers-and-types.md).

## <a name="next-steps"></a>Próximas etapas

Depois de habilitar o recurso de solução do Azure VMware e ter a rede adequada em vigor, você pode [criar uma nuvem privada](tutorial-create-private-cloud.md).