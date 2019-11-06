---
title: Arquivo de inclusão
description: Arquivo de inclusão
services: azure-policy
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 09/18/2018
ms.author: dacoulte
ms.custom: include file
ms.openlocfilehash: dabadf3a4175947f09ba20b0e644f1d2b485ee38
ms.sourcegitcommit: f4d8f4e48c49bd3bc15ee7e5a77bee3164a5ae1b
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/04/2019
ms.locfileid: "73590328"
---
### <a name="sql-servers"></a>SQL Servers

|  |  |
|---------|---------|
| [Auditar nenhum administrador do Azure Active Directory](../articles/governance/policy/samples/audit-no-aad-admin.md) | Auditar quando não há nenhum administrador do Azure Active Directory atribuído ao SQL server. |
| [Auditar a configuração de detecção de ameaças no nível do servidor](../articles/governance/policy/samples/audit-sql-server-threat-detection-setting.md) | Faz a auditoria de políticas de alerta de segurança do banco de dados SQL se essas políticas não estiverem definidas para o estado especificado. Especifique um valor que indica se a detecção de ameaças está habilitada ou desabilitada.  |
| [Auditar as configurações de auditoria do SQL Server](../articles/governance/policy/samples/sql-server-audit.md) | Audita o SQL Server para verificar se as configurações de auditoria estão habilitadas. |
| [Auditar a Configuração da Auditoria no Nível do SQL Server](../articles/governance/policy/samples/audit-sql-server-audit-setting.md) | Auditará as configurações de auditoria do SQL Server se elas não corresponderem a uma configuração especificada. Especifique um valor que indica se as configurações de auditoria devem ser habilitadas ou desabilitadas. |