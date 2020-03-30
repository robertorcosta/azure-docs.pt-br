---
title: Adicionar ferramentas de avaliação no Azure Migrate
description: Saiba como adicionar ferramentas de avaliação no Azure Migrate.
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: article
ms.manager: carmonm
ms.date: 11/19/2019
ms.author: raynew
ms.openlocfilehash: 64af78abd8f82b41d4a03fbb56c96e3038cef5a5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "74185906"
---
# <a name="add-assessment-tools"></a>Adicionar ferramentas de avaliação

Este artigo descreve como adicionar ferramentas de avaliação no [Azure Migrate](migrate-overview.md).

O Azure Migrate fornece um hub de ferramentas para avaliação e migração para o Azure. Ele inclui ferramentas do Azure Migrate, bem como outras ferramentas e ofertas independentes de fornecedores de software (ISV).

Se você quiser adicionar uma ferramenta de avaliação e ainda não tiver um projeto do Azure Migrate, siga este [artigo](how-to-add-tool-first-time.md).

## <a name="select-a-tool"></a>Selecionar uma ferramenta

Se você escolher uma ferramenta não-Azure Migrate para avaliação, comece obtendo uma licença ou se inscrevendo para uma avaliação gratuita, de acordo com a política da ferramenta. As ferramentas têm a opção de se conectar ao Azure Migrate. Siga as instruções e documentação, para conectar a ferramenta ao Azure Migrate. [Saiba mais](migrate-services-overview.md) sobre ferramentas.


## <a name="select-an-assessment-scenario"></a>Selecione um cenário de avaliação

1. No projeto das Migrações para Azure, clique em **Visão Geral**.
2. Selecione o cenário de avaliação que deseja usar:

    - Para descobrir e avaliar máquinas e cargas de trabalho para migração para o Azure, **selecione Avaliar e migrar servidores**.
    - Para avaliar as máquinas SQL no local, selecione **Avaliar e migrar bancos de dados**.
    - Para avaliar aplicativos web no local, selecione **Avaliar e migrar aplicativos web**.

    ![Cenário de avaliação](./media/how-to-assess/assess-scenario.png)

## <a name="select-a-server-assessment-tool"></a>Selecione uma ferramenta de avaliação de servidor 

1. Clique **em Avaliar e Migrar Servidores**.
2. No **Azure Migrate - Servers**, se você não adicionou uma ferramenta de avaliação, em **ferramentas de avaliação,** selecione Clique aqui para adicionar uma ferramenta de **avaliação**. Se você já adicionou ferramentas de avaliação, em **Adicionar mais ferramentas de avaliação,** selecione **Alterar**.

    > [!NOTE]
    > Se você precisar navegar para um projeto diferente, no **Azure Migrate - Servers**, ao lado **de Ver detalhes para um projeto de migração diferente,** **clique aqui**.

3. No **Azure Migrate,** selecione a ferramenta de avaliação que deseja usar.

    - Se você usar a Avaliação do Servidor Migração do Azure, poderá configurar, executar e visualizar avaliações diretamente no projeto Azure Migrate.
    - Se você usar uma ferramenta de avaliação diferente, navegue até o link fornecido para o site e execute a avaliação de acordo com as instruções que eles fornecem.


## <a name="select-a-database-assessment-tool"></a>Selecione uma ferramenta de avaliação de banco de dados

1. Clique **em Avaliar e migrar bancos de dados**
2. Em **Bancos de Dados,** clique **em Adicionar ferramentas**.
3. Em Adicionar uma ferramenta > **Selecionar ferramenta de avaliação**, selecione a ferramenta que deseja usar para avaliar seu banco de dados.

## <a name="select-a-web-app-assessment-tool"></a>Selecione uma ferramenta de avaliação de aplicativos web

1. Clique **em Avaliar e migrar aplicativos web**.
2. Siga o link para a ferramenta Migração para o Serviço de Aplicativos Do Azure. Use a ferramenta de migração para:

    - **Avalie aplicativos on-line**: Você pode avaliar aplicativos com uma URL pública on-line, usando o Assistente de Migração de Serviços de Aplicativos do Azure.
    - **.NET/PHP**: Para aplicativos internos .NET e PHP, você pode baixar e executar o Assistente de Migração.



## <a name="next-steps"></a>Próximas etapas

Experimente uma avaliação usando a avaliação do servidor Azure Migrate para [VMs,](tutorial-prepare-vmware.md) [Hyper-V](tutorial-prepare-hyper-v.md)ou [servidores físicos](tutorial-prepare-physical.md)
