---
title: Conectar dados da central de segurança do Azure ao Azure Sentinel
description: Saiba como conectar dados da central de segurança do Azure ao Azure Sentinel.
author: yelevin
manager: rkarlin
ms.assetid: d28c2264-2dce-42e1-b096-b5a234ff858a
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.topic: conceptual
ms.date: 09/23/2019
ms.author: yelevin
ms.openlocfilehash: e6e24a97a8b98bdd5447295880811914100563fe
ms.sourcegitcommit: 7f929a025ba0b26bf64a367eb6b1ada4042e72ed
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/25/2020
ms.locfileid: "77588528"
---
# <a name="connect-data-from-azure-security-center"></a>Conectar dados da central de segurança do Azure





O Azure Sentinel permite que você conecte alertas da [central de segurança do Azure](../security-center/security-center-intro.md) e transmita-os para o Azure Sentinel. 

## <a name="prerequisites"></a>Prerequisites

- Para exportar alertas da central de segurança do Azure, você deve ter a função de leitor de segurança na assinatura dos logs que você transmite.

- Você deve ter a [camada Standard da central de segurança do Azure](../security-center/security-center-pricing.md) em execução na assinatura. Caso contrário, [Atualize sua assinatura para Standard](https://azure.microsoft.com/pricing/details/security-center/).



## <a name="connect-to-azure-security-center"></a>Conectar-se à central de segurança do Azure

1. No Azure Sentinel, selecione **conectores de dados** e clique no bloco da **central de segurança do Azure** .

1. À direita, clique em **conectar** ao lado de cada assinatura cujos alertas você deseja transmitir para o Azure Sentinel. Certifique-se de atualizar cada assinatura para a camada Standard da central de segurança do Azure para transmitir alertas para o Azure Sentinel.

1. Você pode selecionar se deseja que os alertas da central de segurança do Azure gerem automaticamente incidentes no Azure Sentinel automaticamente. Em **Criar incidentes**, selecione **Habilitar** para habilitar a regra analítica padrão que cria incidentes automaticamente com base em alertas gerados no serviço de segurança conectado. É possível editar essa regra em **Análise** e depois em **Regras ativas**.

3. Clique em **Conectar**.

4. Para usar o esquema relevante no Log Analytics para os alertas da central de segurança do Azure, procure **SecurityAlert**.

## <a name="next-steps"></a>Próximas etapas
Neste documento, você aprendeu a conectar a central de segurança do Azure ao Azure Sentinel. Para saber mais sobre o Azure Sentinel, consulte os seguintes artigos:
- Saiba como [obter visibilidade dos seus dados e possíveis ameaças](quickstart-get-visibility.md).
- Comece a [detectar ameaças com o Azure Sentinel](tutorial-detect-threats-built-in.md).
