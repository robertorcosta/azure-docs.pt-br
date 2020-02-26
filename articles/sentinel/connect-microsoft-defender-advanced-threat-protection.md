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
ms.openlocfilehash: ee2c917d02a442ebabc0e1b40bc245982af794f8
ms.sourcegitcommit: 7f929a025ba0b26bf64a367eb6b1ada4042e72ed
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/25/2020
ms.locfileid: "77588205"
---
# <a name="connect-alerts-from-microsoft-defender-advanced-threat-protection"></a>Conectar alertas da proteção avançada contra ameaças do Microsoft defender 


> [!IMPORTANT]
> A ingestão dos logs da proteção avançada contra ameaças do Microsoft defender está atualmente em visualização pública.
> Esse recurso é fornecido sem um contrato de nível de serviço e não é recomendado para cargas de trabalho de produção.
> Para obter mais informações, consulte [Termos de Uso Complementares de Versões Prévias do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).
 

Você pode transmitir alertas da [proteção avançada contra ameaças do Microsoft defender](https://docs.microsoft.com/windows/security/threat-protection/microsoft-defender-atp/microsoft-defender-advanced-threat-protection) para o Azure Sentinel com um único clique. Essa conexão permite que você transmita os alertas da proteção avançada contra ameaças do Microsoft defender para o Azure Sentinel. 

## <a name="prerequisites"></a>Prerequisites

- Licença válida para a proteção avançada contra ameaças do Microsoft defender habilitada conforme descrito em [validar provisionamento de licenciamento e concluir configuração para proteção avançada contra ameaças do Microsoft defender](https://docs.microsoft.com/windows/security/threat-protection/microsoft-defender-atp/licensing). 
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
