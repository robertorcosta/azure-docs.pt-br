---
title: Excluir um projeto das Migrações para Azure
description: Descreve como criar um projeto do Azure Migrate e adicionar uma ferramenta de avaliação/migração.
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: article
ms.date: 10/22/2019
ms.author: raynew
ms.openlocfilehash: 55842d36cddb2a7851ff5bd7002c20e9873158f5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "73512724"
---
# <a name="delete-an-azure-migrate-project"></a>Excluir um projeto das Migrações para Azure

Este artigo descreve como excluir um [projeto do Azure Migrate.](migrate-overview.md)


## <a name="before-you-start"></a>Antes de começar

Antes de excluir um projeto:

- Quando você exclui um projeto, os metadados da máquina e do projeto são excluídos.
- Se você anexou um espaço de trabalho do Log Analytics à ferramenta Avaliação do servidor para análise de dependência, decida se deseja excluir o espaço de trabalho. 
    - O espaço de trabalho não é automaticamente excluído. Exclua-o manualmente.
    - Verifique para que é usado um espaço de trabalho antes de excluí-lo. O mesmo espaço de trabalho do Log Analytics pode ser usado para vários cenários.
    - Antes de excluir o projeto, você pode encontrar um link para o espaço de trabalho no **Azure Migrate - Servers** > **Azure Migrate - Server Assessment**, no OMS **Workspace**.
    - Para excluir um espaço de trabalho após a exclusão de um projeto, encontre o espaço de trabalho no grupo de recursos relevante e siga [estas instruções](../azure-monitor/platform/delete-workspace.md).


## <a name="delete-a-project"></a>Excluir um projeto


1. No portal Azure, abra o grupo de recursos em que o projeto foi criado.
2. Na página do grupo de recursos, selecione **Mostrar tipos ocultos**.
3. Selecione o projeto e os recursos associados que deseja excluir.
    - O tipo de recurso para projetos do Azure Migrate é **microsoft.migrate/migrateprojects**.
    - Na próxima seção, revise os recursos criados para descoberta, avaliação e migração em um projeto do Azure Migrate.
    - Se o grupo de recursos contiver apenas o projeto Azure Migrate, você poderá excluir todo o grupo de recursos.
    - Se você quiser excluir um projeto da versão anterior do Azure Migrate, as etapas serão as mesmas. O tipo de recurso para esses projetos é **o projeto Migração.**


## <a name="created-resources"></a>Recursos criados

Essas tabelas resumem os recursos criados para descoberta, avaliação e migração em um projeto do Azure Migrate.

> [!NOTE]
> Exclua o cofre da chave com cuidado porque ele pode conter chaves de segurança.

### <a name="vmwarephysical-server"></a>VMware/servidor físico

**Recurso** | **Tipo**
--- | ---
"Appliancename"kv | Cofre de chaves
Site "Appliancename". | Microsoft.OffAzure/VMwareSites
"ProjectName" | Microsoft.Migrar/migrar projetos
Projeto "ProjectName" | Microsoft.Migrar/avaliaçãoProjetos
"ProjectName"rsvault | Cofre dos Serviços de Recuperação
"ProjectName"-MigrateVault-* | Cofre dos Serviços de Recuperação
migrarappligwsa* | Conta de armazenamento
migrarapplilsa* | Conta de armazenamento
migraçãoapplicsa* | Conta de armazenamento
migrarapplikv* | Cofre de chaves
migraçãoapplisbns16041 | Namespace do Barramento de Serviço

### <a name="hyper-v-vm"></a>VM do Hyper-V 

**Recurso** | **Tipo**
--- | ---
"ProjectName" | Microsoft.Migrar/migrar projetos
Projeto "ProjectName" | Microsoft.Migrar/avaliaçãoProjetos
HiperV*kv | Cofre de chaves
HiperV*Site | Microsoft.OffAzure/HyperVSites
"ProjectName"-MigrateVault-* | Cofre dos Serviços de Recuperação


## <a name="next-steps"></a>Próximas etapas

Saiba como adicionar ferramentas adicionais de [avaliação](how-to-assess.md) e [migração.](how-to-migrate.md) 
