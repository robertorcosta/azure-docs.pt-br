---
title: Conecte os dados do Microsoft Defender ATP ao Azure Sentinel| Microsoft Docs
description: Saiba como conectar os dados do Microsoft Defender Advanced Threat Protection ao Azure Sentinel.
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
ms.openlocfilehash: ee2c917d02a442ebabc0e1b40bc245982af794f8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "77588205"
---
# <a name="connect-alerts-from-microsoft-defender-advanced-threat-protection"></a>Conecte alertas do Microsoft Defender Advanced Threat Protection 


> [!IMPORTANT]
> A ingestão de logs de proteção contra ameaças avançadas do Microsoft Defender está atualmente em visualização pública.
> Este recurso é fornecido sem um contrato de nível de serviço, e não é recomendado para cargas de trabalho de produção.
> Para obter mais informações, consulte [Termos de Uso Suplementares para Visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).
 

Você pode transmitir alertas do [Microsoft Defender Advanced Threat Protection](https://docs.microsoft.com/windows/security/threat-protection/microsoft-defender-atp/microsoft-defender-advanced-threat-protection) para o Azure Sentinel com um único clique. Essa conexão permite que você transmita os alertas do Microsoft Defender Advanced Threat Protection para o Azure Sentinel. 

## <a name="prerequisites"></a>Pré-requisitos

- Licença válida para o Microsoft Defender Advanced Threat Protection que está habilitada conforme descrito no [provisionamento de licenciamento valida e configuração completa para o Microsoft Defender Advanced Threat Protection](https://docs.microsoft.com/windows/security/threat-protection/microsoft-defender-atp/licensing). 
- Você deve ser um administrador ou um administrador de segurança no inquilino do Azure Sentinel.


## <a name="connect-to-microsoft-defender-advanced-threat-protection"></a>Conecte-se ao Microsoft Defender Advanced Threat Protection

Se o Microsoft Defender Advanced Threat Protection for implantado e ingerindo seus dados, os alertas podem ser facilmente transmitidos para o Azure Sentinel.


1. No Azure Sentinel, selecione **conectores de dados,** clique no bloco **de proteção de ameaças avançada do Microsoft Defender** e selecione a página de **conector Aberto**.
1. Clique em **Conectar**. 
1. Para usar o esquema relevante no Log Analytics para os alertas do Defender ATP, procure o **SecurityAlert** e o **nome do Provedor** é **MDATP**.




## <a name="next-steps"></a>Próximas etapas
Neste documento, você aprendeu como conectar o Microsoft Defender ATP ao Azure Sentinel. Para saber mais sobre o Azure Sentinel, consulte os seguintes artigos:
- Saiba como [obter visibilidade dos seus dados e possíveis ameaças](quickstart-get-visibility.md).
- Comece a [detectar ameaças com o Azure Sentinel](tutorial-detect-threats.md).
