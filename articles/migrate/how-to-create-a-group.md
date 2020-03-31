---
title: Agrupar máquinas para avaliação com as Migrações para Azure | Microsoft Docs
description: Descreve como agrupar máquinas antes de executar uma avaliação com o serviço Migrações para Azure.
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: article
ms.date: 07/17/2019
ms.author: hamusa
ms.openlocfilehash: 13c640d25265b2663520ef7ab203b0b0a33829e2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "68301690"
---
# <a name="create-a-group-for-assessment"></a>Crie um grupo para avaliação

Este artigo descreve como criar grupos de máquinas para avaliação com o Azure Migrate: Server Assessment.

[O Azure Migrate](migrate-services-overview.md) ajuda você a migrar para o Azure. As Migrações para Azure oferecem um hub centralizado para acompanhar a descoberta, a avaliação e a migração de infraestrutura, aplicativos e dados locais para o Azure. O hub fornece ferramentas do Azure para avaliação e migração, além de ofertas de ISV (fornecedor independente de software) de terceiros. 

## <a name="grouping-machines"></a>Máquinas de agrupamento

Você reúne máquinas em grupos para avaliar se elas são adequadas para migração para o Azure, e para obter estimativas de dimensionamento e custos do Azure para eles. Existem algumas maneiras de criar grupos:

- Se você conhece as máquinas que precisam ser migradas juntas, você pode criar manualmente o grupo no Azure Migrate.
- Se você não tem certeza sobre as máquinas que precisam ser agrupadas, você pode usar a funcionalidade de visualização de dependência no Azure Migrate para criar grupos. 

> [!NOTE]
> A funcionalidade de visualização de dependências não está disponível no Azure Governamental.

## <a name="create-a-group-manually"></a>Crie um grupo manualmente

Você pode criar um grupo ao mesmo tempo em que [você cria uma avaliação](how-to-create-assessment.md).

Se você quiser criar um grupo manualmente fora da criação de uma avaliação, faça o seguinte:

1. No projeto Azure Migrate > **Visão Geral,** clique em **Avaliar e migrar servidores**. No **Azure Migrate: Avaliação do servidor,** clique em **Grupos**
    - Se você ainda não adicionou a ferramenta Azure Migrate: Server Assessment, clique para adicioná-la. [Saiba mais](how-to-assess.md).
    - Se você ainda não criou um projeto do Azure Migrate, [saiba mais](how-to-add-tool-first-time.md).

    ![Selecionar grupos](./media/how-to-create-a-group/select-groups.png)

2. Clique no ícone **Grupo.**
3. No **grupo Criar,** especifique um nome de grupo e, em **nome do Appliance,** selecione o aparelho Azure Migrate que você está usando para a descoberta da máquina.
1. Na lista de máquinas, selecione as máquinas que deseja adicionar ao grupo > **Criar**.

    ![Criar grupo](./media/how-to-create-a-group/create-group.png)

Agora você pode usar este grupo quando [criar uma avaliação](how-to-create-assessment.md).

## <a name="refine-a-group-with-dependency-mapping"></a>Refinar um grupo com mapeamento de dependência

O mapeamento de dependência ajuda você a visualizar dependências entre máquinas. Você normalmente usa mapeamento de dependência quando deseja avaliar grupos de máquinas com níveis mais altos de confiança.
- Ele ajuda você a cruzar as dependências da máquina, antes de executar uma avaliação. 
- Ele também ajuda a planejar efetivamente sua migração para o Azure, garantindo que nada seja deixado para trás e, assim, evitando paralisações surpresadurante a migração.
- Você pode descobrir sistemas interdependentes que precisam migrar juntos e identificar se um sistema em execução ainda está atendendo os usuários ou se é um candidato para descomissionamento em vez de migração.

Se você já [configurou o mapeamento de dependência](how-to-create-group-machine-dependencies.md)e deseja refinar um grupo existente, faça o seguinte:

1. Na guia **Servidores,** no **Azure Migrate: Server Assessment** tile, clique em **Grupos**.
2. Clique no grupo que deseja refinar.
    - Se você ainda não configurou o mapeamento de dependência, a coluna **Dependências** mostrará um **status de instalação Requer.** Para cada VM para a qual você deseja visualizar dependências, clique **em Requer instalação**. Instale alguns agentes em cada VM, antes de mapear as dependências da máquina. [Saiba mais](how-to-create-group-machine-dependencies.md).

        ![Adicionar mapeamento de dependência](./media/how-to-create-a-group/add-dependency-mapping.png)

    - Se você já configurou o mapeamento de dependência, na página do grupo, clique em **Exibir dependências** para abrir o mapa de dependência de grupo.

3. Após clicar **em Exibir dependências,** o mapa de dependência de grupo mostra o seguinte:

    - Conexões TCP de entrada (clientes) e saída (servidores) para e de todas as máquinas do grupo que têm os agentes de dependência instalados.
    - As máquinas dependentes que não possuem os agentes de dependência instalados são agrupadas por números de portas.
    - Máquinas dependentes com agentes de dependência instalados são mostradas como caixas separadas.
    - Processos funcionando dentro da máquina. Expanda cada caixa de máquina para visualizar os processos.
    - Propriedades da máquina (incluindo FQDN, sistema operacional, endereço MAC). Clique em cada caixa da máquina para ver os detalhes.

4. Para exibir dependências em um intervalo de tempo de sua escolha, modifique o intervalo de tempo (uma hora por padrão) especificando datas de início e término ou a duração.

    > [!NOTE]
    > O intervalo de tempo pode ser de até uma hora. Se você precisar de um intervalo maior, use [o Azure Monitor para consultar dados dependentes](how-to-create-group-machine-dependencies.md) por um período mais longo.

5. Depois de identificar as dependências que deseja adicionar ou remover do grupo, você pode modificar o grupo. Use Ctrl+Clique para adicionar ou remover máquinas do grupo.

    - Você só pode adicionar máquinas que foram descobertas.
    - Adicionar e remover máquinas invalida avaliações passadas para um grupo.
    - Opcionalmente, você pode criar uma nova avaliação ao modificar o grupo.


## <a name="next-steps"></a>Próximas etapas

Aprenda a configurar e usar [o mapeamento de dependência](how-to-create-group-machine-dependencies.md) para criar grupos de alta confiança.

