---
title: Aumento do limite de rede | Microsoft Docs
description: Aumento de limite de rede
author: anavinahar
ms.author: anavin
ms.date: 01/23/2020
ms.topic: article
ms.service: azure-supportability
ms.assetid: ce37c848-ddd9-46ab-978e-6a1445728a3b
ms.openlocfilehash: 9b5c7043b06172c2d4931ca1c3fd3ac5d0e80883
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "76547688"
---
# <a name="networking-limit-increase"></a>Aumento de limite de rede

Use o [portal Azure](https://portal.azure.com) para aumentar sua cota de rede.

Para ver seu uso e cota de rede atual no portal Azure, abra sua assinatura e selecione **Usos + cotas**. Você também pode usar as seguintes opções para visualizar o uso e os limites da rede.

* [CLI de uso](/cli/azure/network#az-network-list-usages)
* [Powershell](/powershell/module/azurerm.network/get-azurermnetworkusage)
* [A API de uso da rede](/rest/api/virtualnetwork/virtualnetworks/listusage)

Você pode solicitar um aumento usando **ajuda + suporte** ou em **Usos + cotas** no portal.

> [!Note]
> Para alterar o tamanho padrão dos **prefixos IP públicos,** selecione **Min Public IP InterNetwork Prefix Length** da lista de estiletes.

## <a name="request-networking-quota-increase-at-subscription-level-using-help--support"></a>Solicitar aumento da cota de rede no nível de assinatura usando ajuda + suporte

Siga as instruções abaixo para criar uma solicitação de suporte usando o **suporte Help +** no portal Azure.

1. Faça login no [portal Azure](https://portal.azure.com)e selecione **Ajuda + suporte** no menu do portal Azure ou procure e selecione Ajuda + **suporte**.

    ![Ajuda + Suporte](./media/networking-quota-request/help-plus-support.png)

1. Selecione **Nova solicitação de suporte**.

    ![Nova solicitação de suporte](./media/networking-quota-request/new-support-request.png)

1. Para **o tipo Emissão,** escolha **Limites de serviço e assinatura (cotas)**.

    ![Selecione os limites de assinatura da gota de tipo de problema](./media/networking-quota-request/select-quota-issue-type.png)

1. Selecione a assinatura que precisa de uma cota maior.

    ![Selecione nova assinaturaSR](./media/networking-quota-request/select-subscription-support-request.png)

1. Em **Tipo de Cota,** **selecione Rede**. Selecione **A seguir: Soluções**.

    ![Selecione o tipo de cota](./media/networking-quota-request/select-quota-type-network.png)

1. Em **DETALHES DO PROBLEMA,** selecione **Forneça detalhes** e preencha informações adicionais para ajudar a processar sua solicitação.

    ![Fornecer detalhes](./media/networking-quota-request/provide-details-link.png)

1. No painel **Dedetalhes de Cotas,** selecione um modelo de implantação, um local e os recursos a serem inseridos em sua solicitação.

    ![Detalhes da cota DM](./media/networking-quota-request/quota-details-network.png)

1. Insira os novos limites desejados na assinatura. Para remover uma linha, desmarque o recurso no menu **Recursos** ou selecione o ícone descarte "x". Depois de inserir a cota para cada recurso, **selecione Salvar e continuar** com a criação da solicitação de suporte.

    ![Novos Limites](./media/networking-quota-request/network-new-limits.png)

## <a name="request-networking-quota-increase-at-subscription-level-using-usages--quotas"></a>Solicitação Aumento da cota de rede no nível de subscrição usando Usos + cotas

Siga estas instruções para criar uma solicitação de suporte usando **use + quota** no portal Azure.

1. De https://portal.azure.com, procure e selecione **Assinaturas**.

    ![Assinaturas](./media/networking-quota-request/search-for-suscriptions.png)

1. Selecione a assinatura que precisa de uma cota maior.

    ![Selecionar uma assinatura](./media/networking-quota-request/select-subscription-change-quota.png)

1. Selecione **Uso + cotas**

    ![Selecione uso e cotas](./media/networking-quota-request/select-usage-plus-quotas.png)

1. No canto superior direito, selecione **Solicitar Aumento**.

    ![Solicitar Aumento](./media/networking-quota-request/request-increase-from-subscription.png)

1. Siga as etapas a partir do passo 3 no [aumento da cota de solicitação de rede no nível de inscrição](#request-networking-quota-increase-at-subscription-level-using-help--support).

## <a name="about-networking-limits"></a>Sobre os limites de rede

Para saber mais sobre os limites de rede, consulte a [seção Rede](../../azure-resource-manager/management/azure-subscription-service-limits.md#networking-limits) da página limites ou nossa FAQ de limites de rede.
