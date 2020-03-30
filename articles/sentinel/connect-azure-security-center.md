---
title: Conecte os dados do Azure Security Center ao Azure Sentinel
description: Saiba como conectar os dados do Azure Security Center ao Azure Sentinel.
author: yelevin
manager: rkarlin
ms.assetid: d28c2264-2dce-42e1-b096-b5a234ff858a
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.topic: conceptual
ms.date: 09/23/2019
ms.author: yelevin
ms.openlocfilehash: e6e24a97a8b98bdd5447295880811914100563fe
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "77588528"
---
# <a name="connect-data-from-azure-security-center"></a>Conecte dados do Azure Security Center





O Azure Sentinel permite conectar alertas do [Azure Security Center](../security-center/security-center-intro.md) e transmiti-los para o Azure Sentinel. 

## <a name="prerequisites"></a>Pré-requisitos

- Para exportar alertas do Azure Security Center, você deve ter a função leitor de segurança na assinatura dos logs transmitidos.

- Você deve ter o [nível Azure Security Center Standard](../security-center/security-center-pricing.md) em execução na assinatura. Se não, [atualize sua assinatura para o padrão](https://azure.microsoft.com/pricing/details/security-center/).



## <a name="connect-to-azure-security-center"></a>Conecte-se ao Azure Security Center

1. No Azure Sentinel, selecione **conectores de dados** e clique no azulejo **do Azure Security Center.**

1. À direita, clique em **Conectar** ao lado de cada assinatura cujos alertas você deseja transmitir no Azure Sentinel. Certifique-se de atualizar cada assinatura para o nível Padrão do Azure Security Center para transmitir alertas para o Azure Sentinel.

1. Você pode selecionar se deseja que os alertas do Azure Security Center gerem automaticamente incidentes no Azure Sentinel. Em **Criar incidentes**, selecione **Habilitar** para habilitar a regra analítica padrão que cria incidentes automaticamente com base em alertas gerados no serviço de segurança conectado. É possível editar essa regra em **Análise** e depois em **Regras ativas**.

3. Clique em **Conectar**.

4. Para usar o esquema relevante no Log Analytics para os alertas do Azure Security Center, procure o **SecurityAlert**.

## <a name="next-steps"></a>Próximas etapas
Neste documento, você aprendeu como conectar o Azure Security Center ao Azure Sentinel. Para saber mais sobre o Azure Sentinel, consulte os seguintes artigos:
- Saiba como [obter visibilidade dos seus dados e possíveis ameaças](quickstart-get-visibility.md).
- Comece a [detectar ameaças com o Azure Sentinel](tutorial-detect-threats-built-in.md).
