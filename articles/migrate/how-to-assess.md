---
title: Adicionar ferramentas de avaliação em migrações para Azure
description: Saiba como adicionar ferramentas de avaliação nas migrações para Azure.
ms.topic: how-to
ms.date: 04/26/2020
ms.openlocfilehash: 10e8b66575893bc8e63aa94392c872acc04407c4
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: pt-BR
ms.lasthandoff: 07/02/2020
ms.locfileid: "84195627"
---
# <a name="add-assessment-tools"></a>Adicionar ferramentas de avaliação

Este artigo descreve como adicionar ferramentas de avaliação nas [migrações para Azure](migrate-overview.md). 

- Se você quiser adicionar uma ferramenta de avaliação e ainda não tiver um projeto de migrações para Azure, siga este [artigo](how-to-add-tool-first-time.md).-
- Se você tiver adicionado uma ferramenta ISV, ou mover, para avaliação, [siga as etapas](prepare-isv-movere.md)para se preparar para trabalhar com a ferramenta.

## <a name="select-an-assessment-scenario"></a>Selecionar um cenário de avaliação

1. No projeto das Migrações para Azure, clique em **Visão Geral**.
2. Selecione o cenário de avaliação que você deseja usar:

    - Para descobrir e avaliar máquinas e cargas de trabalho para migração para o Azure, selecione **avaliar e migrar servidores**.
    - Para avaliar os bancos de dados SQL Server locais, selecione **avaliar e migrar bancos de dados**.
    - Para avaliar aplicativos Web locais, selecione **avaliar e migrar aplicativos Web**.

    ![Cenário de avaliação](./media/how-to-assess/assess-scenario.png)

## <a name="select-a-server-assessment-tool"></a>Selecione uma ferramenta de avaliação do servidor 

1. Clique em **avaliar e migrar servidores**.
2. Em **migrações para Azure-servidores**, se você ainda não tiver adicionado uma ferramenta de avaliação, em **ferramentas de avaliação**, selecione **clique aqui para adicionar uma ferramenta de avaliação**. Se você já tiver adicionado ferramentas de avaliação, em **adicionar mais ferramentas de avaliação**, selecione **alterar**.

    > [!NOTE]
    > Se você precisar navegar para um projeto diferente, no **Migrations-Servers do Azure**, ao lado de **Ver os detalhes de um projeto de migração diferente**, clique **em clique aqui**.

3. Em **migrações para Azure**, selecione a ferramenta de avaliação que você deseja usar.

    - Se você usar a avaliação de servidor de migrações para Azure, poderá configurar, executar e exibir avaliações diretamente no projeto de migrações para Azure.
    - Se você usar uma ferramenta de avaliação diferente, navegue até o link fornecido para seu site e execute a avaliação de acordo com as instruções que eles fornecem.


## <a name="select-a-database-assessment-tool"></a>Selecionar uma ferramenta de avaliação de banco de dados

1. Clique em **avaliar e migrar bancos de dados**
2. Em **bancos de dados**, clique em **Adicionar ferramentas**.
3. Em adicionar uma ferramenta > **selecione ferramenta de avaliação**, selecione a ferramenta que você deseja usar para avaliar seu banco de dados.

## <a name="select-a-web-app-assessment-tool"></a>Selecione uma ferramenta de avaliação de aplicativo Web

1. Clique em **avaliar e migrar aplicativos Web**.
2. Siga o link para a ferramenta de migração para o serviço de Azure App. Use a ferramenta de migração para:

    - **Avaliar aplicativos online**: você pode avaliar aplicativos com uma URL pública online, usando o assistente de migração de serviço Azure app.
    - **.Net/php**: para aplicativos internos do .net e PHP, você pode baixar e executar o assistente de migração.



## <a name="next-steps"></a>Próximas etapas

Experimente uma avaliação usando o Azure Migrate Server Assessment para VMs [VMware](tutorial-prepare-vmware.md) , [Hyper-V](tutorial-prepare-hyper-v.md)ou [servidores físicos](tutorial-prepare-physical.md)
