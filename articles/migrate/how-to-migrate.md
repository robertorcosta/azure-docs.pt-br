---
title: Adicionar ferramentas de migração no Azure Migrate
description: Saiba como adicionar ferramentas de migração no Azure Migrate.
author: rayne-wiselman
ms.service: azure-migrate
ms.manager: carmonm
ms.topic: article
ms.date: 11/18/2019
ms.author: raynew
ms.openlocfilehash: 6bcb65912627356215769ccc8380ee1a7404f6f7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "74185955"
---
# <a name="add-migration-tools"></a>Adicionar ferramentas de migração

Este artigo descreve como adicionar ferramentas de migração no [Azure Migrate](migrate-overview.md).

O Azure Migrate fornece um hub de ferramentas para avaliação e migração para o Azure. Ele inclui ferramentas nativas, ferramentas fornecidas por outros serviços do Azure e ofertas de fornecedores independentes de software (ISV) de terceiros.

Se você quiser adicionar uma ferramenta de migração e ainda não configurar um projeto do Azure Migrate, siga este [artigo](how-to-add-tool-first-time.md).



## <a name="selecting-an-isv-tool"></a>Como selecionar uma ferramenta ISV

Se você escolher uma [ferramenta ISV](migrate-services-overview.md#isv-integration) para migração, você pode começar obtendo uma licença ou se inscrevendo para uma avaliação gratuita, de acordo com a política ISV. Em cada ferramenta, há uma opção para se conectar às Migrações para Azure. Implante a ferramenta e siga as instruções e documentação da ferramenta para conectar o espaço de trabalho da ferramenta com o Azure Migrate. 

## <a name="select-a-migration-scenario"></a>Selecione um cenário de migração

1. No projeto das Migrações para Azure, clique em **Visão Geral**.
2. Selecione o cenário de migração que deseja usar:

    - Para migrar máquinas e cargas de trabalho para o Azure, **selecione Avaliar e migrar servidores**.
    - Para migrar máquinas SQL no local, selecione **Avaliar e migrar bancos de dados**.
    - Para migrar aplicativos web no local, selecione **Avaliar e migrar aplicativos web**.
    - Para migrar grandes quantidades de dados on-premises para o Azure no modo offline, **selecione Order a Data Box**.

    ![Cenário de avaliação](./media/how-to-migrate/assess-scenario.png)

## <a name="select-a-server-migration-tool"></a>Selecione uma ferramenta de migração de servidor

1. Clique **em Avaliar e Migrar Servidores**.
2. No **Azure Migrate - Servers**, se você ainda não adicionou ferramentas de migração, em **ferramentas de migração,** selecione Clique aqui para adicionar uma ferramenta de **migração**. Se você já adicionou ferramentas de migração, em **Adicionar mais ferramentas de migração,** selecione **Alterar**.

    > [!NOTE]
    > Se você precisar navegar para um projeto diferente, no **Azure Migrate - Servers**, ao lado **de Ver detalhes para um projeto de migração diferente,** **clique aqui**.

3. No **Azure Migrate,** selecione a ferramenta de migração que deseja usar.
    - Se você usar a migração do servidor Azure Migrate, poderá configurar e executar migrações diretamente no projeto Azure Migrate.
    - Se você usar uma ferramenta de avaliação de terceiros, navegue até o link fornecido para o ISV e execute a migração de acordo com as instruções que eles fornecem.

## <a name="select-a-database-migration-tool"></a>Selecione uma ferramenta de migração de banco de dados

1. Clique **em Avaliar e migrar bancos de dados**
2. Em **Bancos de Dados,** clique **em Adicionar ferramentas**.
3. Em Adicionar uma ferramenta > **Selecionar ferramenta de migração,** selecione a ferramenta que deseja usar para migrar seu banco de dados.

## <a name="select-a-web-app-migration-tool"></a>Selecione uma ferramenta de migração de aplicativos web

1. Clique **em Avaliar e migrar aplicativos web**.
2. Siga o link para a ferramenta Migração para o Serviço de Aplicativos Do Azure. Use a ferramenta de migração para:

    - **Avalie aplicativos on-line**: Você pode avaliar e migrar aplicativos com uma URL pública on-line, usando o Assistente de Migração de Serviços de Aplicativos do Azure.
    - **.NET/PHP**: Para aplicativos internos .NET e PHP, você pode baixar e executar o Assistente de Migração.

## <a name="order-an-azure-data-box"></a>Encomendar uma caixa de dados do Azure

Para migrar grandes quantidades de dados para o Azure, você pode solicitar uma Caixa Azure DAta para transferência de dados off-line.

1. Clique **em Encomendar uma caixa de dados**.
2. Em **Selecionar sua Caixa de Dados do Azure,** especifique sua assinatura. 
3. A transferência será uma importação para o Azure. Especifique a fonte de dados e o destino da região do Azure para os dados.

## <a name="next-steps"></a>Próximas etapas

Experimente uma migração usando a migração do servidor Azure Migrate para [VMs Hyper-V](tutorial-migrate-hyper-v.md) ou [VMware.](tutorial-migrate-vmware.md)
