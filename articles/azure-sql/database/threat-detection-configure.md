---
title: Configurar a Proteção Avançada contra Ameaças
description: A proteção avançada contra ameaças detecta atividades anormais de banco de dados indicando possíveis ameaças de segurança ao banco de dados no banco de dados SQL do Azure
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: seo-dt-2019, sqldbrb=1
ms.topic: conceptual
author: rmatchoro
ms.author: ronmat
ms.reviewer: vanto, carlrab
ms.date: 08/05/2019
ms.openlocfilehash: 2f4e9841a44252829fae283a12ba804219204022
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/02/2020
ms.locfileid: "84321531"
---
# <a name="configure-advanced-threat-protection-for-azure-sql-database"></a>Configurar a proteção avançada contra ameaças para o banco de dados SQL do Azure
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

A [proteção avançada contra ameaças](threat-detection-overview.md) para o banco de dados SQL do Azure detecta atividades anormais que indicam tentativas incomuns e potencialmente prejudiciais de acessar ou explorar bancos de dados. A proteção avançada contra ameaças pode identificar **possíveis injeção de SQL**, **acesso de localização incomum ou Data Center**, **acesso a partir de um aplicativo não familiar ou potencialmente prejudicial**e **credenciais SQL de força bruta** -consulte mais detalhes em [alertas de proteção avançada contra ameaças](threat-detection-overview.md#alerts).

Você pode receber notificações sobre as ameaças detectadas por meio de [notificações por email](threat-detection-overview.md#explore-detection-of-a-suspicious-event) ou do [Portal do Azure](threat-detection-overview.md#explore-alerts-in-the-azure-portal)

A [proteção avançada contra ameaças](threat-detection-overview.md) faz parte da oferta de [segurança de dados avançada](advanced-data-security.md) , que é um pacote unificado para recursos avançados de segurança do SQL. A proteção avançada contra ameaças pode ser acessada e gerenciada por meio do portal de segurança de dados avançado do SQL central.

## <a name="set-up-advanced-threat-protection-in-the-azure-portal"></a>Configurar a proteção avançada contra ameaças no portal do Azure

1. Entre no [Portal do Azure](https://portal.azure.com).
2. Navegue até a página de configuração do servidor que você deseja proteger. Nas configurações de segurança, selecione **Segurança de Dados Avançada**.
3. Na página de configuração **Segurança de Dados Avançada**:

   - Habilite a Segurança de Dados Avançada no servidor.
   - Em **Configurações de Proteção Avançada contra Ameaças**, na caixa de texto **Enviar alertas para**, forneça a lista de endereços de email para receber alertas de segurança em caso de detecção de atividades anômalas em banco de dados.
  
   ![Configurar a proteção avançada contra ameaças](./media/threat-detection/set_up_threat_detection.png)

## <a name="set-up-advanced-threat-protection-using-powershell"></a>Configurar a Proteção Avançada contra Ameaças usando o PowerShell

Para obter um exemplo de script, consulte [Configurar a auditoria e a proteção avançada contra ameaças usando o PowerShell](scripts/auditing-threat-detection-powershell-configure.md).

## <a name="next-steps"></a>Próximas etapas

- Saiba mais sobre a [proteção avançada contra ameaças](threat-detection-overview.md).
- Saiba mais sobre a [proteção avançada contra ameaças no SQL instância gerenciada](../managed-instance/threat-detection-configure.md).  
- Saiba mais sobre a [segurança de dados avançada](advanced-data-security.md).
- Saiba mais sobre [auditoria](../../azure-sql/database/auditing-overview.md)
- Saiba mais sobre a [central de segurança do Azure](https://docs.microsoft.com/azure/security-center/security-center-intro)
- Para obter mais informações sobre preços, consulte a [página de preços do banco de dados SQL](https://azure.microsoft.com/pricing/details/sql-database/)  
