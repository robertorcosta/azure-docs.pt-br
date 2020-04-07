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
ms.openlocfilehash: 831baabcc26130d9b2c730afd85f2ed4b3842690
ms.sourcegitcommit: 441db70765ff9042db87c60f4aa3c51df2afae2d
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/06/2020
ms.locfileid: "80756346"
---
# <a name="connect-alerts-from-microsoft-defender-advanced-threat-protection"></a>Conecte alertas do Microsoft Defender Advanced Threat Protection 


> [!IMPORTANT]
> A ingestão de alertas de proteção avançada contra ameaças do Microsoft Defender está atualmente em visualização pública.
> Este recurso é fornecido sem um contrato de nível de serviço, e não é recomendado para cargas de trabalho de produção.
> Para obter mais informações, consulte [Termos de Uso Suplementares para Visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).
 

O conector [Microsoft Defender Advanced Threat Protection](https://docs.microsoft.com/windows/security/threat-protection/microsoft-defender-atp/microsoft-defender-advanced-threat-protection) permite transmitir alertas do Microsoft Defender Advanced Threat Protection para o Azure Sentinel. Isso permitirá que você analise de forma mais abrangente os eventos de segurança em toda a sua organização e construa cartilhas para uma resposta efetiva e imediata.

## <a name="prerequisites"></a>Pré-requisitos

- Você deve ter uma licença válida para o Microsoft Defender Advanced Threat Protection, conforme descrito na [implantação do Microsoft Defender ATP](https://docs.microsoft.com/windows/security/threat-protection/microsoft-defender-atp/licensing). 
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
