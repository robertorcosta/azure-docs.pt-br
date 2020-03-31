---
title: Trabalhe com vários inquilinos no Azure Sentinel para provedores de serviços MSSP| Microsoft Docs
description: Como trabalhar com vários inquilinos no Azure Sentinel para provedores de serviços MSSP.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/23/2019
ms.author: yelevin
ms.openlocfilehash: fdb58686fcdd18a8e2861aab533717dbc91e8893
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79476008"
---
# <a name="work-with-multiple-tenants-in-azure-sentinel"></a>Trabalhe com vários inquilinos no Azure Sentinel 

Se você é um provedor de serviços de segurança gerenciado (MSSP) e está usando o [Azure Lighthouse](../lighthouse/overview.md) para gerenciar os centros de operações de segurança (SOC) de seus clientes, você poderá gerenciar os recursos do Azure Sentinel de seus clientes sem se conectar diretamente ao inquilino do cliente, do seu próprio inquilino do Azure. 

## <a name="prerequisites"></a>Pré-requisitos
- [Farol A bordo do Azure](../lighthouse/how-to/onboard-customer.md)
- Para que isso funcione corretamente, seu inquilino deve ser registrado no Azure Sentinel Resource Provider em pelo menos uma assinatura. Se você tem um Azure Sentinel registrado em seu inquilino, você está pronto para começar. Caso assim, **selecione Assinaturas** do portal Azure, seguido por **provedores de recursos**.  Em seguida, a partir da tela **SOC - Provedores de recursos,** pesquise e selecione `Microsoft.OperationalInsights` e `Microsoft.SecurityInsights`selecione **Register**.
   ![Verifique os provedores de recursos](media/multiple-tenants-service-providers/check-resource-provider.png)
## <a name="how-to-access-azure-sentinel-from-other-tenants"></a>Como acessar o Azure Sentinel de outros inquilinos
1. Em **Diretório + assinatura,** selecione os diretórios delegados e as assinaturas onde os espaços de trabalho do Azure Sentinel do seu cliente estão localizados.

   ![Gerar incidentes de segurança](media/multiple-tenants-service-providers/directory-subscription.png)

1. Abra o Azure Sentinel. Você verá todos os espaços de trabalho nas assinaturas selecionadas, e poderá trabalhar com eles perfeitamente, como qualquer espaço de trabalho em seu próprio inquilino.

> [!NOTE]
> Você não será capaz de implantar conectores no Azure Sentinel a partir de um espaço de trabalho gerenciado. Para implantar um conector, você deve entrar diretamente no inquilino no qual deseja implantar um conector e autenticar lá com as permissões necessárias.





## <a name="next-steps"></a>Próximas etapas
Neste documento, você aprendeu a gerenciar vários inquilinos do Azure Sentinel perfeitamente. Para saber mais sobre o Azure Sentinel, consulte os seguintes artigos:
- Saiba como [obter visibilidade dos seus dados e possíveis ameaças](quickstart-get-visibility.md).
- Comece a [detectar ameaças com o Azure Sentinel](tutorial-detect-threats-built-in.md).

