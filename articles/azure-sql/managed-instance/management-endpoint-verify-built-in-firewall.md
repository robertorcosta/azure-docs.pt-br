---
title: Verificar a segurança da porta no firewall interno
description: Saiba como verificar a proteção de firewall interna no Azure SQL Instância Gerenciada.
services: sql-database
ms.service: sql-managed-instance
ms.subservice: security
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: srdan-bozovic-msft
ms.author: srbozovi
ms.reviewer: sstein, carlrab
ms.date: 12/04/2018
ms.openlocfilehash: 1d2fffabba3615394bdf96ed487177bf21f3ecec
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: pt-BR
ms.lasthandoff: 07/02/2020
ms.locfileid: "84708698"
---
# <a name="verify-the-azure-sql-managed-instance-built-in-firewall"></a>Verifique o firewall interno do Azure SQL Instância Gerenciada
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

As [regras de segurança de entrada obrigatórias](connectivity-architecture-overview.md#mandatory-inbound-security-rules) do SQL do Azure instância gerenciada exigem que as portas de gerenciamento 9000, 9003, 1438, 1440 e 1452 sejam abertas de **qualquer fonte** no NSG (grupo de segurança de rede) que protege o SQL instância gerenciada. Embora essas portas estejam abertas no nível do NSG, elas são protegidas no nível da rede pelo firewall interno.

## <a name="verify-firewall"></a>Verificar o firewall

Para verificar essas portas, use qualquer ferramenta de verificador de segurança para testá-las. A captura de tela a seguir mostra como usar uma dessas ferramentas.

![Verificando o firewall interno](./media/management-endpoint-verify-built-in-firewall/03_verify_firewall.png)

## <a name="next-steps"></a>Próximas etapas

Para obter mais informações sobre o SQL Instância Gerenciada e conectividade, consulte [arquitetura de conectividade do sql instância gerenciada do Azure](connectivity-architecture-overview.md).
