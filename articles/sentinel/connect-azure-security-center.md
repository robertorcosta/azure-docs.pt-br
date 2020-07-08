---
title: Conectar dados da central de segurança do Azure ao Azure Sentinel
description: Saiba como conectar alertas da camada Standard da central de segurança do Azure (ASC) e transmiti-los para o Azure Sentinel.
author: yelevin
manager: rkarlin
ms.assetid: d28c2264-2dce-42e1-b096-b5a234ff858a
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.topic: conceptual
ms.date: 09/23/2019
ms.author: yelevin
ms.openlocfilehash: 2fc7744600a9652ad43fd0aae8d886dc94acd58f
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/02/2020
ms.locfileid: "85559149"
---
# <a name="connect-data-from-azure-security-center-asc"></a>Conectar dados da central de segurança do Azure (ASC)

O Azure Sentinel permite que você conecte alertas da [central de segurança do Azure](../security-center/security-center-intro.md) e transmita-os para o Azure Sentinel. 

## <a name="prerequisites"></a>Pré-requisitos

- Para exportar alertas da central de segurança do Azure, você deve ter a função de leitor de segurança na assinatura dos logs que você transmite.

- Você deve ter a [camada Standard da central de segurança do Azure](../security-center/security-center-pricing.md) em execução na assinatura. Caso contrário, [Atualize sua assinatura para Standard](https://azure.microsoft.com/pricing/details/security-center/).

## <a name="connect-to-azure-security-center"></a>Conectar-se à central de segurança do Azure

1. No Azure Sentinel, selecione **conectores de dados** no menu de navegação.

1. Na Galeria de conectores de dados, selecione **central de segurança do Azure**e clique no botão **abrir página do conector** .

1. Em **configuração**, clique em **conectar** ao lado de cada assinatura cujos alertas você deseja transmitir para o Azure Sentinel. O botão conectar estará disponível somente se você tiver as permissões necessárias e a assinatura da camada padrão ASC.

1. Você pode selecionar se deseja que os alertas da central de segurança do Azure gerem incidentes automaticamente no Azure Sentinel. Em **criar incidentes**, selecione **habilitado** para ativar a regra de análise padrão que cria automaticamente incidentes de alertas. Em seguida, você pode editar essa regra em **análise**, na guia **regras ativas** .

1. Para usar o esquema relevante no Log Analytics para os alertas da central de segurança do Azure, procure **SecurityAlert**.

## <a name="next-steps"></a>Próximas etapas
Neste documento, você aprendeu a conectar a central de segurança do Azure ao Azure Sentinel. Para saber mais sobre o Azure Sentinel, consulte os seguintes artigos:
- Saiba como [obter visibilidade dos seus dados e possíveis ameaças](quickstart-get-visibility.md).
- Comece a [detectar ameaças com o Azure Sentinel](tutorial-detect-threats-built-in.md).
