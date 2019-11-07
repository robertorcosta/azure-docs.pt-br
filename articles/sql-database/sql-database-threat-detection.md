---
title: Configurar a proteção avançada contra ameaças – banco de dados SQL do Azure
description: A proteção avançada contra ameaças detecta atividades anormais de banco de dados que indicam possíveis ameaças de segurança ao banco de dados em um único banco de dados ou pool elástico.
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: seo-dt-2019
ms.topic: conceptual
author: rmatchoro
ms.author: ronmat
ms.reviewer: vanto, carlrab
ms.date: 08/05/2019
ms.openlocfilehash: 8a6d40f37dec3488009a702d52d6437cd345717b
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/06/2019
ms.locfileid: "73686946"
---
# <a name="azure-sql-database-advanced-threat-protection-for-single-or-pooled-databases"></a>Proteção avançada contra ameaças do banco de dados SQL do Azure para bancos de dados individuais ou em pool

A [proteção avançada contra ameaças](sql-database-threat-detection-overview.md) para bancos de dados individuais e em pool detecta atividades anormais que indicam tentativas incomuns e potencialmente prejudiciais de acessar ou explorar bancos de dados. A proteção avançada contra ameaças pode identificar **possíveis injeção de SQL**, **acesso de localização incomum ou Data Center**, **acesso a partir de um aplicativo não familiar ou potencialmente prejudicial**e **credenciais SQL de força bruta** -consulte mais detalhes em [alertas de proteção avançada contra ameaças](sql-database-threat-detection-overview.md#advanced-threat-protection-alerts).

Você pode receber notificações sobre as ameaças detectadas por meio de [notificações por email](sql-database-threat-detection-overview.md#explore-anomalous-database-activities-upon-detection-of-a-suspicious-event) ou do [Portal do Azure](sql-database-threat-detection-overview.md#explore-advanced-threat-protection-alerts-for-your-database-in-the-azure-portal)

A [proteção avançada contra ameaças](sql-database-threat-detection-overview.md) faz parte da oferta do ADS ( [segurança de dados avançada](sql-database-advanced-data-security.md) ), que é um pacote unificado para recursos avançados de segurança do SQL. A proteção avançada contra ameaças pode ser acessada e gerenciada por meio do portal central de anúncios do SQL.

## <a name="set-up-advanced-threat-protection-in-the-azure-portal"></a>Configurar a proteção avançada contra ameaças no portal do Azure

1. Inicie o portal do Azure em [https://portal.azure.com](https://portal.azure.com).
2. Navegue para a página de configuração do servidor do Banco de Dados SQL do Azure que você deseja proteger. Nas configurações de segurança, selecione **Segurança de Dados Avançada**.
3. Na página de configuração **Segurança de Dados Avançada**:

   - Habilite a Segurança de Dados Avançada no servidor.
   - Em **Configurações de Proteção Avançada contra Ameaças**, na caixa de texto **Enviar alertas para**, forneça a lista de endereços de email para receber alertas de segurança em caso de detecção de atividades anômalas em banco de dados.
  
   ![Configurar a proteção avançada contra ameaças](./media/sql-database-threat-detection/set_up_threat_detection.png)

   > [!NOTE]
   > Os preços nas capturas de tela nem sempre refletem o preço atual e são um exemplo.

## <a name="set-up-advanced-threat-protection-using-powershell"></a>Configurar a proteção avançada contra ameaças usando o PowerShell

Para obter um exemplo de script, consulte [Configurar a auditoria e a proteção avançada contra ameaças usando o PowerShell](scripts/sql-database-auditing-and-threat-detection-powershell.md).

## <a name="next-steps"></a>Próximas etapas

- Saiba mais sobre a [proteção avançada contra ameaças](sql-database-threat-detection-overview.md).
- Saiba mais sobre a [proteção avançada contra ameaças na instância gerenciada](sql-database-managed-instance-threat-detection.md).  
- Saiba mais sobre a [segurança de dados avançada](sql-database-advanced-data-security.md).
- Saiba mais sobre [auditoria](sql-database-auditing.md)
- Saiba mais sobre a [Central de Segurança do Azure](https://docs.microsoft.com/azure/security-center/security-center-intro)
- Para saber mais sobre preços, visite a [página de preços do Banco de Dados SQL](https://azure.microsoft.com/pricing/details/sql-database/)  
