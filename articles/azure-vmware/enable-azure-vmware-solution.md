---
title: Como habilitar o recurso de solução do Azure VMware
description: Saiba como enviar uma solicitação de suporte para habilitar o recurso de solução do Azure VMware. Você também pode solicitar mais nós em sua nuvem privada da solução Azure VMware existente.
ms.topic: how-to
ms.date: 09/22/2020
ms.openlocfilehash: 72a7aca97067ce4e9ed0e901e4016c82b3549eb1
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "90817888"
---
# <a name="how-to-enable-azure-vmware-solution-resource"></a>Como habilitar o recurso da solução Azure VMware
Saiba como enviar uma solicitação de suporte para habilitar o recurso de solução do Azure VMware. Você também pode solicitar mais nós em sua nuvem privada da solução Azure VMware existente.

## <a name="eligibility-criteria"></a>Critérios de qualificação

* Você precisará de um [ea (Enterprise Agreement do Azure)](https://docs.microsoft.com/azure/cost-management-billing/manage/ea-portal-agreements) com a Microsoft.
* Você precisará de uma conta do Azure em uma assinatura do Azure.


## <a name="enable-azure-vmware-solution-resource"></a>Habilitar recurso da solução Azure VMware
Antes de criar o recurso da Solução VMware no Azure, você precisará enviar um tíquete de suporte para que os nós sejam alocados. Depois que a equipe de suporte receber sua solicitação, levará até cinco dias úteis para confirmar a solicitação e alocar os nós. Se você tiver uma nuvem privada da Solução VMware no Azure existente e quiser mais nós alocados, deverá passar pelo mesmo processo.


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
   - Número de nós
   - Quaisquer outros detalhes

   >[!NOTE]
   >A solução Azure VMware recomenda um mínimo de três nós para criar sua nuvem privada e para redundância de N + 1 nós. 

1. Selecione **revisão + criar** para enviar a solicitação.

   Levará até cinco dias úteis para que um representante de suporte confirme sua solicitação.

   >[!IMPORTANT] 
   >Se você já tiver uma solução existente do Azure VMware e estiver solicitando nós adicionais, observe que precisamos de cinco dias úteis para alocar os nós. 

1. Antes de provisionar seus nós, certifique-se de registrar o provedor de recursos **Microsoft. AVS** no portal do Azure.  

   ```azurecli-interactive
   az provider register -n Microsoft.AVS --subscription <your subscription ID>
   ```

   Para conhecer outras maneiras de registrar o provedor de recursos, confira [Provedores e tipos de recursos do Azure](../azure-resource-manager/management/resource-providers-and-types.md).