---
title: Configurar a Proteção Avançada contra Ameaças
description: O Advanced Threat Protection detecta atividades anômalas de banco de dados indicando potenciais ameaças à segurança ao banco de dados em um único banco de dados ou pool elástico.
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: seo-dt-2019
ms.topic: conceptual
author: rmatchoro
ms.author: ronmat
ms.reviewer: vanto, carlrab
ms.date: 08/05/2019
ms.openlocfilehash: 8eb8e4fccc17fe31def671cf6e8edb19d867b244
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "73822499"
---
# <a name="azure-sql-database-advanced-threat-protection-for-single-or-pooled-databases"></a>Azure SQL Database Advanced Threat Protection para bancos de dados únicos ou agrupados

[O Advanced Threat Protection](sql-database-threat-detection-overview.md) para bancos de dados únicos e agrupados detecta atividades anômalas indicando tentativas incomuns e potencialmente prejudiciais de acessar ou explorar bancos de dados. O Advanced Threat Protection pode identificar **potenciais injeção sql,** **acesso a partir de localização ou data center incomuns,** acesso a aplicativos **principais ou potencialmente prejudiciais desconhecidos**e **credenciais SQL de força bruta** - veja mais detalhes em [alertas avançados de proteção contra ameaças](sql-database-threat-detection-overview.md#advanced-threat-protection-alerts).

Você pode receber notificações sobre as ameaças detectadas por meio de [notificações por email](sql-database-threat-detection-overview.md#explore-anomalous-database-activities-upon-detection-of-a-suspicious-event) ou do [Portal do Azure](sql-database-threat-detection-overview.md#explore-advanced-threat-protection-alerts-for-your-database-in-the-azure-portal)

[O Advanced Threat Protection](sql-database-threat-detection-overview.md) faz parte da oferta avançada de segurança de [dados](sql-database-advanced-data-security.md) (ADS), que é um pacote unificado para recursos avançados de segurança SQL. A Proteção Avançada contra Ameaças pode ser acessada e gerenciada por meio do portal central da ADS do SQL.

## <a name="set-up-advanced-threat-protection-in-the-azure-portal"></a>Configure proteção avançada contra ameaças no portal Azure

1. Inicie o portal Azure em [https://portal.azure.com](https://portal.azure.com).
2. Navegue para a página de configuração do servidor do Banco de Dados SQL do Azure que você deseja proteger. Nas configurações de segurança, selecione **Segurança de Dados Avançada**.
3. Na página de configuração **Segurança de Dados Avançada**:

   - Habilite a Segurança de Dados Avançada no servidor.
   - Em **Configurações de Proteção Avançada contra Ameaças**, na caixa de texto **Enviar alertas para**, forneça a lista de endereços de email para receber alertas de segurança em caso de detecção de atividades anômalas em banco de dados.
  
   ![Configure proteção avançada contra ameaças](./media/sql-database-threat-detection/set_up_threat_detection.png)

   > [!NOTE]
   > Os preços nas capturas de tela nem sempre refletem o preço atual, e são um exemplo.

## <a name="set-up-advanced-threat-protection-using-powershell"></a>Configurar a Proteção Avançada contra Ameaças usando o PowerShell

Para um exemplo de script, consulte [Configurar auditoria e proteção avançada contra ameaças usando o PowerShell](scripts/sql-database-auditing-and-threat-detection-powershell.md).

## <a name="next-steps"></a>Próximas etapas

- Saiba mais sobre [o Advanced Threat Protection](sql-database-threat-detection-overview.md).
- Saiba mais sobre [o Advanced Threat Protection na instância gerenciada](sql-database-managed-instance-threat-detection.md).  
- Saiba mais sobre a [segurança de dados avançada](sql-database-advanced-data-security.md).
- Saiba mais sobre [auditoria](sql-database-auditing.md)
- Saiba mais sobre [o centro de segurança do Azure](https://docs.microsoft.com/azure/security-center/security-center-intro)
- Para obter mais informações sobre preços, consulte a página de preços do [Banco de Dados SQL](https://azure.microsoft.com/pricing/details/sql-database/)  
