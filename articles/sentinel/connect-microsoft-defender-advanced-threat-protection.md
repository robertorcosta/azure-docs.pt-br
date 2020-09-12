---
title: Conectar os dados do Microsoft defender para ponto de extremidade ao Azure Sentinel | Microsoft Docs
description: Saiba como conectar dados do Microsoft defender for Endpoint (anteriormente Microsoft defender ATP) ao Azure Sentinel.
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
ms.date: 10/13/2019
ms.author: yelevin
ms.openlocfilehash: 69ce6cb42fd18364a7b93faa9cc01d1f793d5cd6
ms.sourcegitcommit: f8d2ae6f91be1ab0bc91ee45c379811905185d07
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/10/2020
ms.locfileid: "89657543"
---
# <a name="connect-alerts-from-microsoft-defender-for-endpoint-formerly-microsoft-defender-atp"></a>Conectar alertas do Microsoft defender para ponto de extremidade (anteriormente Microsoft defender ATP) 


> [!IMPORTANT]
> A ingestão do Microsoft defender para alertas de ponto de extremidade está atualmente em visualização pública.
> Esse recurso é fornecido sem um contrato de nível de serviço e não é recomendado para cargas de trabalho de produção.
> Para obter mais informações, consulte [Termos de Uso Complementares de Versões Prévias do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).
 

O [Microsoft defender for Endpoint](https://docs.microsoft.com/windows/security/threat-protection/microsoft-defender-atp/microsoft-defender-advanced-threat-protection) Connector permite transmitir alertas do Microsoft defender for Endpoint para o Azure Sentinel. Isso permitirá que você analise de forma mais abrangente os eventos de segurança em sua organização e crie guias estratégicos para uma resposta efetiva e imediata.

## <a name="prerequisites"></a>Pré-requisitos

- Você deve ter uma licença válida para o Microsoft defender para ponto de extremidade, conforme descrito em [Configurar o Microsoft defender para implantação de ponto de extremidade](https://docs.microsoft.com/windows/security/threat-protection/microsoft-defender-atp/licensing). 
- Você deve ser um administrador ou um administrador de segurança no locatário do Azure Sentinel.


## <a name="connect-to-microsoft-defender-for-endpoint"></a>Conectar-se ao Microsoft defender para ponto de extremidade

Se o Microsoft defender for Endpoint for implantado e ingerindo seus dados, os alertas poderão ser facilmente transmitidos para o Azure Sentinel.


1. No Azure Sentinel, selecione **conectores de dados**, selecione **Microsoft defender para ponto de extremidade** (ainda pode ser chamado de proteção avançada contra ameaças do Microsoft defender) na galeria e selecione a **página abrir conector**.
1. Clique em **Conectar**. 
1. Para usar o esquema relevante no Log Analytics para os alertas do defender ATP, procure **SecurityAlert** e o **nome do provedor** é **MDATP**.

## <a name="next-steps"></a>Próximas etapas
Neste documento, você aprendeu a conectar o Microsoft defender para ponto de extremidade ao Azure Sentinel. Para saber mais sobre o Azure Sentinel, consulte os seguintes artigos:
- Saiba como [obter visibilidade dos seus dados e possíveis ameaças](quickstart-get-visibility.md).
- Comece a [detectar ameaças com o Azure Sentinel](tutorial-detect-threats.md).
