---
title: Configurar a Proteção Avançada contra Ameaças
titleSuffix: Azure SQL Managed Instance
description: A proteção avançada contra ameaças detecta atividades anormais de banco de dados que indicam possíveis ameaças de segurança ao banco de dados no Azure SQL Instância Gerenciada.
services: sql-database
ms.service: sql-managed-instance
ms.subservice: security
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: conceptual
author: rmatchoro
ms.author: ronmat
ms.reviewer: vanto
ms.date: 08/05/2019
ms.openlocfilehash: ceb6285448df2a5d87dfa87ab249c99bf22c9928
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: pt-BR
ms.lasthandoff: 07/02/2020
ms.locfileid: "84686317"
---
# <a name="configure-advanced-threat-protection-in-azure-sql-managed-instance"></a>Configurar a proteção avançada contra ameaças no Azure SQL Instância Gerenciada
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

A [proteção avançada contra ameaças](../database/threat-detection-overview.md) para um [Azure SQL instância gerenciada](sql-managed-instance-paas-overview.md) detecta atividades anormais que indicam tentativas incomuns e potencialmente prejudiciais de acessar ou explorar bancos de dados. A proteção avançada contra ameaças pode identificar **possíveis injeção de SQL**, **acesso de localização incomum ou Data Center**, **acesso a partir de um aplicativo não familiar ou potencialmente prejudicial**e **credenciais SQL de força bruta** -consulte mais detalhes em [alertas de proteção avançada contra ameaças](../database/threat-detection-overview.md#alerts).

Você pode receber notificações sobre as ameaças detectadas por meio de [notificações por email](../database/threat-detection-overview.md#explore-detection-of-a-suspicious-event) ou do [Portal do Azure](../database/threat-detection-overview.md#explore-alerts-in-the-azure-portal)

A [proteção avançada contra ameaças](../database/threat-detection-overview.md) faz parte da oferta de [segurança de dados avançada](../database/advanced-data-security.md) , que é um pacote unificado para recursos avançados de segurança do SQL. A Proteção Avançada contra Ameaças pode ser acessada e gerenciada por meio do portal central da ADS do SQL.

##  <a name="azure-portal"></a>Portal do Azure

1. Entre no [portal do Azure](https://portal.azure.com). 
2. Navegue até a página de configuração da instância do SQL Instância Gerenciada que você deseja proteger. Na página **configurações** , selecione **segurança de dados avançada**.
3. Na página configuração de segurança de dados avançada
   - Ative a segurança **de** dados avançada.
   - Configure a **lista de emails** para receber alertas de segurança após a detecção de atividades anormais do banco de dados.
   - Selecione a **Conta de Armazenamento do Azure** onde os registros de auditoria de ameaças anormais são salvos.
   - Selecione os **tipos de proteção avançada contra ameaças** que você gostaria de configurar. Saiba mais sobre [alertas de proteção avançada contra ameaças](../database/threat-detection-overview.md).
4. Clique em **salvar** para salvar a política de segurança de dados avançada nova ou atualizada.

   ![Proteção Avançada contra Ameaças](./media/threat-detection-configure/threat-detection.png)


## <a name="next-steps"></a>Próximas etapas

- Saiba mais sobre a [proteção avançada contra ameaças](../database/threat-detection-overview.md).
- Saiba mais sobre instâncias gerenciadas, consulte [o que é um Azure SQL instância gerenciada](sql-managed-instance-paas-overview.md).
- Saiba mais sobre [a proteção avançada contra ameaças para o banco de dados SQL do Azure](../database/threat-detection-configure.md).
- Saiba mais sobre a [auditoria do SQL instância gerenciada](https://go.microsoft.com/fwlink/?linkid=869430).
- Saiba mais sobre a [central de segurança do Azure](https://docs.microsoft.com/azure/security-center/security-center-intro).
