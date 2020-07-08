---
title: Adicionar ferramentas de migração nas migrações para Azure
description: Saiba como adicionar ferramentas de migração nas migrações para Azure.
ms.topic: article
ms.date: 04/26/2020
ms.openlocfilehash: 670341adca7ab2958d43132aab164d7bba0f87d0
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: pt-BR
ms.lasthandoff: 07/02/2020
ms.locfileid: "84195817"
---
# <a name="add-migration-tools"></a>Adicionar ferramentas de migração

Este artigo descreve como adicionar ferramentas de migração nas [migrações para Azure](migrate-overview.md).

- Se você quiser adicionar uma ferramenta de migração e ainda não tiver configurado um projeto de migrações para Azure, siga este [artigo](how-to-add-tool-first-time.md).
- Se você tiver adicionado uma ferramenta ISV para migração, [siga as etapas](prepare-isv-movere.md)para se preparar para trabalhar com a ferramenta.

## <a name="select-a-migration-scenario"></a>Selecionar um cenário de migração

1. No projeto das Migrações para Azure, clique em **Visão Geral**.
2. Selecione o cenário de migração que você deseja usar:

    - Para migrar máquinas e cargas de trabalho para o Azure, selecione **avaliar e migrar servidores**.
    - Para migrar bancos de dados SQL Server locais, selecione **avaliar e migrar bancos de dados**.
    - Para migrar aplicativos Web locais, selecione **avaliar e migrar aplicativos Web**.
    - Para migrar grandes quantidades de dados locais para o Azure no modo offline, selecione **ordenar um data Box**.

    ![Cenário de avaliação](./media/how-to-migrate/assess-scenario.png)

## <a name="select-a-server-migration-tool"></a>Selecione uma ferramenta de migração de servidor

1. Clique em **avaliar e migrar servidores**.
2. Em **migrações para Azure-servidores**, se ainda não tiver adicionado ferramentas de migração, em **ferramentas de migração**, selecione **clique aqui para adicionar uma ferramenta de migração**. Se você já tiver adicionado ferramentas de migração, em **adicionar mais ferramentas de migração**, selecione **alterar**.

    > [!NOTE]
    > Se você precisar navegar para um projeto diferente, no **Migrations-Servers do Azure**, ao lado de **Ver os detalhes de um projeto de migração diferente**, clique **em clique aqui**.

3. Em **migrações para Azure**, selecione a ferramenta de migração que você deseja usar.
    - Se você usar a migração de servidor de migrações para Azure, poderá configurar e executar as migrações diretamente no projeto de migrações para Azure.
    - Se você usar uma ferramenta de avaliação de terceiros, navegue até o link fornecido para o ISV e execute a migração de acordo com as instruções que eles fornecem.

## <a name="select-a-database-migration-tool"></a>Selecione uma ferramenta de migração de banco de dados

1. Clique em **avaliar e migrar bancos de dados**
2. Em **bancos de dados**, clique em **Adicionar ferramentas**.
3. Em adicionar uma ferramenta > **selecione ferramenta de migração**, selecione a ferramenta que você deseja usar para migrar seu banco de dados.

## <a name="select-a-web-app-migration-tool"></a>Selecione uma ferramenta de migração de aplicativo Web

1. Clique em **avaliar e migrar aplicativos Web**.
2. Siga o link para a ferramenta de migração para o serviço de Azure App. Use a ferramenta de migração para:

    - **Avaliar aplicativos online**: você pode avaliar e migrar aplicativos com uma URL pública online, usando o assistente de migração de serviço Azure app.
    - **.Net/php**: para aplicativos internos do .net e PHP, você pode baixar e executar o assistente de migração.

## <a name="order-an-azure-data-box"></a>Ordenar um Azure Data Box

Para migrar grandes quantidades de dados para o Azure, você pode solicitar um Azure DAta box para transferência de dados offline.

1. Clique em **solicitar um data Box**.
2. Em **selecionar seu Azure data Box**, especifique sua assinatura. 
3. A transferência será uma importação para o Azure. Especifique a fonte de dados e o destino da região do Azure para os dados.

## <a name="next-steps"></a>Próximas etapas

Experimente uma migração usando a migração de servidor de migração do Azure para VMs [Hyper-V](tutorial-migrate-hyper-v.md) ou [VMware](tutorial-migrate-vmware.md) .
