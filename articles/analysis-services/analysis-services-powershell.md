---
title: Gerenciar o Azure Analysis Services com o PowerShell | Microsoft Docs
description: Descreve os cmdlets do Azure Analysis PowerShell para tarefas administrativas comuns, como a criação de servidores, suspensão de operações ou alteração do nível de serviço.
author: minewiskan
ms.service: azure-analysis-services
ms.topic: reference
ms.date: 10/28/2019
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: 2c8f4c0541d97a189087af692658cfe794eaaf7e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "73572701"
---
# <a name="manage-azure-analysis-services-with-powershell"></a>Gerenciar o Azure Analysis Services com PowerShell

Este artigo descreve os cmdlets do PowerShell usados para executar tarefas de gerenciamento de banco de dados e servidor do Azure Analysis Services. 

Tarefas de gerenciamento de recursos do servidor, como criar ou excluir um servidor, suspender ou retomar as operações do servidor ou alterar o nível de serviço (nível) usam cmdlets do Azure Analysis Services. Outras tarefas de gerenciamento de bancos de dados, como adição ou remoção de membros da função, processamento ou particionamento, usam os cmdlets incluídos no mesmo módulo SqlServer que o SQL Server Analysis Services.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="permissions"></a>Permissões

A maioria das tarefas do PowerShell exige privilégios de administrador no servidor do Analysis Services que está sendo gerenciado. As tarefas agendadas do PowerShell são operações autônomas. A conta ou a entidade de serviço que executa o agendador precisa ter privilégios de administrador no servidor do Analysis Services. 

Para operações de servidor usando cmdlets Do Azure PowerShell, sua conta ou o agendador de execução da conta também devem pertencer à função Proprietário para o recurso no [RBAC (Azure Role-Based Access Control).](../role-based-access-control/overview.md) 

## <a name="resource-and-server-operations"></a>Operações de recursos e servidores 

Módulo de instalação [- Az.AnalysisServices](https://www.powershellgallery.com/packages/Az.AnalysisServices)   
Documentação - [Az.AnalysisServices referência](/powershell/module/az.analysisservices)

## <a name="database-operations"></a>Operações de banco de dados

As operações de banco de dados do Azure Analysis Services usam o mesmo módulo SqlServer que o SQL Server Analysis Services. No entanto, nem todos os cmdlets têm suporte para o Azure Analysis Services. 

O módulo SqlServer fornece cmdlets de gerenciamento de banco de dados de tarefas específicas e o cmdlet Invoke-ASCmd de uso geral que aceita um script ou consulta de Linguagem de Script de Modelo Tabular (TMSL). Os cmdlets a seguir no módulo SqlServer têm suporte para o Azure Analysis Services.

Módulo de instalação - [SqlServer](https://www.powershellgallery.com/packages/SqlServer)   
Documentação - [Referência sqlServer](/powershell/module/sqlserver)

### <a name="supported-cmdlets"></a>Cmdlets suportados

|Cmdlet|Descrição|
|------------|-----------------| 
|[Membro de complemento](https://docs.microsoft.com/powershell/module/sqlserver/Add-RoleMember)|Adicionar um membro a uma função de banco de dados.| 
|[Backup-ASDatabase](https://docs.microsoft.com/powershell/module/sqlserver/backup-asdatabase)|Fazer backup de um banco de dados do Analysis Services.|  
|[Remove-RoleMember](https://docs.microsoft.com/powershell/module/sqlserver/remove-rolemember)|Remover um membro de uma função de banco de dados.|   
|[Invoke-ASCmd](https://docs.microsoft.com/powershell/module/sqlserver/invoke-ascmd)|Executar um script TMSL.|
|[Invoke-ProcessASDatabase](https://docs.microsoft.com/powershell/module/sqlserver/invoke-processasdatabase)|Processar um banco de dados.|  
|[Invoke-ProcessPartition](https://docs.microsoft.com/powershell/module/sqlserver/invoke-processpartition)|Processar uma partição.| 
|[Invoke-ProcessTable](https://docs.microsoft.com/powershell/module/sqlserver/invoke-processtable)|Processar uma tabela.|  
|[Merge-Partition](https://docs.microsoft.com/powershell/module/sqlserver/merge-partition)|Mesclar uma partição.|  
|[Restore-ASDatabase](https://docs.microsoft.com/powershell/module/sqlserver/restore-asdatabase)|Restaurar um banco de dados do Analysis Services.| 
  

## <a name="related-information"></a>Informações relacionadas

* [SQL Server PowerShell](https://docs.microsoft.com/sql/powershell/sql-server-powershell)      
* [Baixar o módulo do PowerShell do SQL Server](https://docs.microsoft.com/sql/ssms/download-sql-server-ps-module)   
* [Baixar o SSMS](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms)   
* [Módulo SqlServer na Galeria do PowerShell](https://www.powershellgallery.com/packages/SqlServer)    
* [Nível de compatibilidade 1200 e superior de programação do modelo tabular](https://docs.microsoft.com/analysis-services/tabular-model-programming-compatibility-level-1200/tabular-model-programming-for-compatibility-level-1200)
