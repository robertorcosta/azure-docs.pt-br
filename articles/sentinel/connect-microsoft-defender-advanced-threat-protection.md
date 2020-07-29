---
title: Conectar dados do Microsoft defender ATP ao Azure Sentinel | Microsoft Docs
description: Saiba como conectar dados de proteção avançada contra ameaças do Microsoft defender ao Azure Sentinel.
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
ms.openlocfilehash: 831baabcc26130d9b2c730afd85f2ed4b3842690
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/02/2020
ms.locfileid: "80756346"
---
# <a name="connect-alerts-from-microsoft-defender-advanced-threat-protection"></a>Conectar alertas da proteção avançada contra ameaças do Microsoft defender 


> [!IMPORTANT]
> A ingestão dos alertas de proteção avançada contra ameaças do Microsoft defender está atualmente em visualização pública.
> Esse recurso é fornecido sem um contrato de nível de serviço e não é recomendado para cargas de trabalho de produção.
> Para obter mais informações, consulte [Termos de Uso Complementares de Versões Prévias do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).
 

O conector de [proteção avançada contra ameaças do Microsoft defender](https://docs.microsoft.com/windows/security/threat-protection/microsoft-defender-atp/microsoft-defender-advanced-threat-protection) permite transmitir alertas da proteção avançada contra ameaças do Microsoft defender para o Azure Sentinel. Isso permitirá que você analise de forma mais abrangente os eventos de segurança em sua organização e crie guias estratégicos para uma resposta efetiva e imediata.

## <a name="prerequisites"></a>Pré-requisitos

- Você deve ter uma licença válida para a proteção avançada contra ameaças do Microsoft defender, conforme descrito em [Configurar a implantação do Microsoft defender ATP](https://docs.microsoft.com/windows/security/threat-protection/microsoft-defender-atp/licensing). 
- Você deve ser um administrador ou um administrador de segurança no locatário do Azure Sentinel.


## <a name="connect-to-microsoft-defender-advanced-threat-protection"></a>Conectar-se à proteção avançada contra ameaças do Microsoft defender

Se a proteção avançada contra ameaças do Microsoft defender for implantada e ingerir seus dados, os alertas poderão ser facilmente transmitidos para o Azure Sentinel.


1. No Azure Sentinel, selecione **conectores de dados**, clique no bloco **proteção avançada contra ameaças do Microsoft defender** e selecione a **página abrir conector**.
1. Clique em **Conectar**. 
1. Para usar o esquema relevante no Log Analytics para os alertas do defender ATP, procure **SecurityAlert** e o **nome do provedor** é **MDATP**.




## <a name="next-steps"></a>Próximas etapas
Neste documento, você aprendeu a conectar o Microsoft defender ATP ao Azure Sentinel. Para saber mais sobre o Azure Sentinel, consulte os seguintes artigos:
- Saiba como [obter visibilidade dos seus dados e possíveis ameaças](quickstart-get-visibility.md).
- Comece a [detectar ameaças com o Azure Sentinel](tutorial-detect-threats.md).
