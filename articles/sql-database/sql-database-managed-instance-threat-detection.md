---
title: Configurar proteção avançada contra ameaças - instância gerenciada
description: O Advanced Threat Protection detecta atividades anômalas de banco de dados indicando potenciais ameaças à segurança ao banco de dados em uma instância gerenciada.
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: rmatchoro
ms.author: ronmat
ms.reviewer: vanto
ms.date: 08/05/2019
ms.openlocfilehash: 69292a934af8b8777f11ab58ed3fe306abf8b408
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "73822563"
---
# <a name="configure-advanced-threat-protection-in-azure-sql-database-managed-instance"></a>Configure a proteção avançada contra ameaças na instância gerenciada do Banco de Dados SQL do Azure

[O Advanced Threat Protection](sql-database-threat-detection-overview.md) para uma [instância gerenciada](sql-database-managed-instance-index.yml) detecta atividades anômalas indicando tentativas incomuns e potencialmente prejudiciais de acessar ou explorar bancos de dados. O Advanced Threat Protection pode identificar **potenciais injeção sql,** **acesso a partir de localização ou data center incomuns,** acesso a aplicativos **principais ou potencialmente prejudiciais desconhecidos**e **credenciais SQL de força bruta** - veja mais detalhes em [alertas avançados de proteção contra ameaças](sql-database-threat-detection-overview.md#advanced-threat-protection-alerts).

Você pode receber notificações sobre as ameaças detectadas por meio de [notificações por email](sql-database-threat-detection-overview.md#explore-anomalous-database-activities-upon-detection-of-a-suspicious-event) ou do [Portal do Azure](sql-database-threat-detection-overview.md#explore-advanced-threat-protection-alerts-for-your-database-in-the-azure-portal)

[O Advanced Threat Protection](sql-database-threat-detection-overview.md) faz parte da oferta avançada de segurança de [dados](sql-database-advanced-data-security.md) (ADS), que é um pacote unificado para recursos avançados de segurança SQL. A Proteção Avançada contra Ameaças pode ser acessada e gerenciada por meio do portal central da ADS do SQL.

## <a name="set-up-advanced-threat-protection-in-the-azure-portal"></a>Configure proteção avançada contra ameaças no portal Azure

1. Inicie o portal Azure em [https://portal.azure.com](https://portal.azure.com).
2. Navegue até a página de configuração da instância gerenciada que você quer proteger. Na página **Configurações,** selecione **Segurança avançada de dados**.
3. Na página de configuração de segurança avançada de dados
   - **Ativar segurança** avançada de dados.
   - Configure a **lista de e-mails** para receber alertas de segurança após a detecção de atividades anômalas de banco de dados.
   - Selecione a **Conta de Armazenamento do Azure** onde os registros de auditoria de ameaças anormais são salvos.
   - Selecione os **tipos avançados de proteção contra ameaças** que você gostaria de configurar. Saiba mais sobre [alertas avançados de proteção contra ameaças.](sql-database-threat-detection-overview.md)
4. Clique **em Salvar** para salvar a nova ou atualizada política de segurança de dados avançada.

   ![Proteção Avançada contra Ameaças](./media/sql-database-managed-instance-threat-detection/threat-detection.png)

   > [!NOTE]
   > Os preços nas capturas de tela nem sempre refletem o preço atual, e são um exemplo.

## <a name="next-steps"></a>Próximas etapas

- Saiba mais sobre [o Advanced Threat Protection](sql-database-threat-detection-overview.md).
- Para saber mais sobre instâncias gerenciadas, confira [O que é uma instância gerenciada](sql-database-managed-instance.md).
- Saiba mais sobre [o Advanced Threat Protection para banco de dados único.](sql-database-threat-detection.md)
- Saiba mais sobre [auditoria de instânciagerenciada gerenciada.](https://go.microsoft.com/fwlink/?linkid=869430)
- Saiba mais sobre [o centro de segurança do Azure.](https://docs.microsoft.com/azure/security-center/security-center-intro)
