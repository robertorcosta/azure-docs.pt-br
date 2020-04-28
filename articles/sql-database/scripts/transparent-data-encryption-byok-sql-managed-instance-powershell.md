---
title: 'PowerShell: habilitar BYOK TDE-Instância Gerenciada do Banco de Dados SQL do Azure '
description: Saiba como configurar um Instância Gerenciada do SQL do Azure para começar a usar a TDE (Transparent Data Encryption) com BYOK para criptografia em repouso usando o PowerShell.
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: MladjoA
ms.author: mlandzic
ms.reviewer: vanto, carlrab
ms.date: 11/05/2019
ms.openlocfilehash: ddffda5229c9c0d33c563e3ae7b4a884f0f92dff
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/27/2020
ms.locfileid: "73691398"
---
# <a name="manage-transparent-data-encryption-in-a-managed-instance-using-your-own-key-from-azure-key-vault"></a>Gerenciar Transparent Data Encryption em uma Instância Gerenciada usando sua própria chave do Azure Key Vault

Este exemplo de script do PowerShell configura Transparent Data Encryption (TDE) com a chave gerenciada pelo cliente para o SQL Instância Gerenciada do Azure, usando uma chave de Azure Key Vault. Isso é muitas vezes chamado de cenário de Bring Your Own Key para TDE. Para saber mais sobre o TDE com a chave gerenciada pelo cliente, confira [TDE Bring your own Key ao Azure SQL](../transparent-data-encryption-byok-azure-sql.md).

## <a name="prerequisites"></a>Pré-requisitos

- Um Instância Gerenciada existente. Consulte [usar o PowerShell para criar uma instância gerenciada do banco de dados SQL do Azure](sql-database-create-configure-managed-instance-powershell.md).

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]
[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]
[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Usar o PowerShell localmente ou usando Azure Cloud Shell requer AZ PowerShell 2.3.2 ou uma versão posterior. Se você precisar atualizar, consulte [instalar Azure PowerShell módulo](/powershell/azure/install-az-ps)ou execute o script de exemplo abaixo para instalar o módulo para o usuário atual:

`Install-Module -Name Az -AllowClobber -Scope CurrentUser`

Se você estiver executando o PowerShell localmente, também precisará executar o `Connect-AzAccount` para criar uma conexão com o Azure.

## <a name="sample-scripts"></a>Scripts de exemplo

[!code-powershell-interactive[main](../../../powershell_scripts/sql-database/transparent-data-encryption/setup-tde-byok-sqlmi.ps1 "Set up BYOK TDE for SQL Managed Instance")]

## <a name="next-steps"></a>Próximas etapas

Para obter mais informações sobre o Azure PowerShell, confira a [Documentação do Azure PowerShell](/powershell/azure/overview).

Outros exemplos de script do PowerShell para Banco de Dados SQL podem ser encontrados nos [scripts do PowerShell para Banco de Dados SQL do Azure](../sql-database-powershell-samples.md).
