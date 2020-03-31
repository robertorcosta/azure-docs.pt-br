---
title: Conecte os dados de segurança do aplicativo em nuvem ao Azure Sentinel| Microsoft Docs
description: Saiba como conectar dados de segurança de aplicativos em nuvem ao Azure Sentinel.
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
ms.date: 10/23/2019
ms.author: yelevin
ms.openlocfilehash: 348576fbbdd1037f9e2e792218b96bbbecf36668
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "77588358"
---
# <a name="connect-data-from-microsoft-cloud-app-security"></a>Conecte dados da Microsoft Cloud App Security 



Você pode transmitir logs da [Cloud App Security](https://docs.microsoft.com/cloud-app-security/what-is-cloud-app-security) para o Azure Sentinel com um único clique. Essa conexão permite que você transmita os alertas da Cloud App Security para o Azure Sentinel. 

## <a name="prerequisites"></a>Pré-requisitos

- Usuário com permissões globais de administrador ou administrador de segurança
- Para transmitir os logs do Cloud Discovery no Azure Sentinel, [habilite o Azure Sentinel como seu SIEM no Microsoft Cloud App Security](https://aka.ms/AzureSentinelMCAS).

> [!IMPORTANT]
> A ingestão de registros do Cloud Discovery está atualmente em visualização pública.
> Este recurso é fornecido sem um contrato de nível de serviço, e não é recomendado para cargas de trabalho de produção.
> Para obter mais informações, consulte [Termos de Uso Suplementares para Visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).
 
## <a name="connect-to-cloud-app-security"></a>Conectar-se ao Cloud App Security

Se você já tiver o Cloud App Security, certifique-se de que ele está [ativado em sua rede](https://docs.microsoft.com/cloud-app-security/getting-started-with-cloud-app-security).
Se o Cloud App Security for implantado e ingerindo seus dados, os dados de alerta podem ser facilmente transmitidos para o Azure Sentinel.


1. No Azure Sentinel, selecione **conectores de dados,** clique no bloco **de segurança do aplicativo** em nuvem e selecione abrir a página do **conector**.

1. Selecione quais logs deseja transmitir no Azure Sentinel, você pode escolher **Alertas** e **registros do Discovery na nuvem** (visualização). 

1. Clique em **Conectar**.

1. Para usar o esquema relevante no Log Analytics para os alertas de segurança do aplicativo em nuvem, procure **o SecurityAlert**.




## <a name="next-steps"></a>Próximas etapas
Neste documento, você aprendeu como conectar o Microsoft Cloud App Security ao Azure Sentinel. Para saber mais sobre o Azure Sentinel, consulte os seguintes artigos:
- Saiba como [obter visibilidade dos seus dados e possíveis ameaças](quickstart-get-visibility.md).
- Comece a [detectar ameaças com o Azure Sentinel](tutorial-detect-threats.md).
