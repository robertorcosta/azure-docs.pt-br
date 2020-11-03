---
title: 'Início Rápido: Criar um pool de SQL do Synapse usando Synapse Studio'
description: Crie um pool de SQL do Synapse usando o Synapse Studio seguindo as etapas neste guia.
services: synapse-analytics
author: julieMSFT
ms.service: synapse-analytics
ms.topic: quickstart
ms.subservice: sql
ms.date: 10/16/2020
ms.author: jrasnick
ms.reviewer: jrasnick
ms.openlocfilehash: 806831ac5e965afcd076066f4baa498297a43a3e
ms.sourcegitcommit: 8c7f47cc301ca07e7901d95b5fb81f08e6577550
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/27/2020
ms.locfileid: "92740489"
---
# <a name="quickstart-create-a-synapse-sql-pool-using-synapse-studio"></a>Início Rápido: Criar um pool de SQL do Synapse usando Synapse Studio

O Azure Synapse Analytics oferece vários mecanismos de análise para ajudar você a ingerir, transformar, modelar e analisar seus dados. Um pool de SQL oferece recursos de computação e armazenamento baseados em T-SQL. Após a criação de um pool de SQL em seu workspace do Synapse, os dados podem ser carregados, modelados, processados e fornecidos para chegar mais rapidamente a insights analíticos.

Este início rápido descreve as etapas para criar um pool de SQL em um workspace do Synapse usando o Synapse Studio.

Se você não tiver uma assinatura do Azure, [crie uma conta gratuita antes de começar](https://azure.microsoft.com/free/).


## <a name="prerequisites"></a>Pré-requisitos

- Assinatura do Azure - [criar uma gratuitamente](https://azure.microsoft.com/free/)
- [Workspace do Synapse](quickstart-create-workspace.md)

## <a name="sign-in-to-the-azure-portal"></a>Entre no Portal do Azure

Entre no [Portal do Azure](https://portal.azure.com/)

## <a name="navigate-to-the-synapse-workspace"></a>Navegue até o workspace do Synapse

1. Navegue até o workspace do Synapse em que o pool de SQL será criado digitando o nome do serviço (ou o nome do recurso diretamente) na barra de pesquisa.

    ![Barra de pesquisa do portal do Azure com workspaces do Synapse digitados.](media/quickstart-create-sql-pool/create-sql-pool-00a.png)
1. Na lista de workspaces, digite o nome (ou parte do nome) do workspace a ser aberto. Para este exemplo, vamos usar um workspace chamado **contosoanalytics**.

    ![Lista de workspaces do Synapse filtrados para mostrar os que contêm o nome Contoso.](media/quickstart-create-sql-pool/create-sql-pool-00b.png)

## <a name="launch-synapse-studio"></a>Iniciar o Synapse Studio

1. Na visão geral do workspace, selecione **Iniciar o Synapse Studio** para abrir o local onde o pool de SQL será criado. Digite o nome do serviço ou o nome do recurso diretamente na barra de pesquisa.

    ![Visão geral do workspace do Synapse no portal do Azure com Iniciar o Synapse Studio realçado.](media/quickstart-create-apache-spark-pool/create-spark-pool-studio-20.png)

## <a name="create-a-sql-pool-in-synapse-studio"></a>Criar um pool de SQL no Synapse Studio

1. Na home page do Synapse Studio, navegue até o **Hub de Gerenciamento** no painel de navegação esquerdo selecionando o ícone **Gerenciar**.

    ![Home page do Synapse Studio com a seção Hub de Gerenciamento realçada.](media/quickstart-create-apache-spark-pool/create-spark-pool-studio-21.png)

1. Uma vez no Hub de Gerenciamento, navegue até a seção **Pools de SQL** para ver a lista atual de pools de SQL disponíveis no workspace.

    ![Hub de Gerenciamento do Synapse Studio com a navegação dos pools de SQL selecionada](media/quickstart-create-sql-pool/create-sql-pool-studio-22.png)

1. Selecione o comando **+ Novo** e o novo assistente de criação de pool de SQL será exibido. 

    ![Listagem de pools de SQL do Hub de Gerenciamento do Synapse Studio.](media/quickstart-create-sql-pool/create-sql-pool-studio-23.png)

1. Insira os seguintes detalhes na guia **Informações Básicas** :

    | Configuração | Valor sugerido | Descrição |
    | :------ | :-------------- | :---------- |
    | **Nome do pool de SQL** | contosoedw | Esse é o nome que o pool de SQL terá. |
    | **Nível de desempenho** | DW100c | Defina isso com o menor tamanho para reduzir os custos deste início rápido |

    ![Fluxo de criação de pools de SQL – guia Básico.](media/quickstart-create-sql-pool/create-sql-pool-studio-24.png)
    > [!IMPORTANT]
    > Observe que há limitações específicas para os nomes que os pools de SQL podem usar. Os nomes não podem conter caracteres especiais, devem ter 15 caracteres ou menos, não conter palavras reservadas e ser exclusivos no workspace.

4. Na próxima guia, **Configurações adicionais** , selecione **nenhum** para provisionar o pool de SQL sem dados. Deixe a ordenação padrão conforme selecionada.

    ![Fluxo de criação de pool de SQL – guia Configurações adicionais.](media/quickstart-create-sql-pool/create-sql-pool-studio-25.png)

1. Por enquanto não adicionaremos marcas, assim, selecione **Examinar + criar**.

1. Na guia **Revisar + criar** , verifique se os detalhes estão corretos com base no que foi inserido anteriormente e clique em **criar**. 

    ![Fluxo de criação do pool de SQL – guia Examinar configurações.](media/quickstart-create-sql-pool/create-sql-pool-studio-26.png)

1. Neste ponto, o fluxo de provisionamento de recursos será iniciado.

1. Após a conclusão do provisionamento, ao navegar de volta para o workspace, aparecerá uma nova entrada para o pool de SQL recém-criado.

    ![Fluxo de criação do pool de SQL – provisionamento de recursos.](media/quickstart-create-sql-pool/create-sql-pool-studio-27.png)

1. Depois que o pool de SQL for criado, ele estará disponível no workspace para carregar dados, processar fluxos, ler no lake etc.

## <a name="clean-up-sql-pools-using-synapse-studio"></a>Limpar pools de SQL usando o Synapse Studio    

Siga as etapas abaixo para excluir o pool de SQL do workspace usando o Synapse Studio.
> [!WARNING]
> A exclusão de um pool de SQL removerá o mecanismo de análise do workspace. Não será mais possível se conectar ao pool e todas as consultas, pipelines e scripts que usam esse pool de SQL deixarão de funcionar.

Caso deseje excluir o pool de SQL, faça o seguinte:

1. Navegue até os pools de SQL no Hub de Gerenciamento no Synapse Studio.
1. Selecione as reticências no pool de SQL a ser excluído (nesse caso, **contosoedw** ) para mostrar os comandos do pool de SQL:

    ![Listagem de pools de SQL, com o pool recém-criado selecionado.](media/quickstart-create-sql-pool/create-sql-pool-studio-28.png)
1. Selecione **delete**.
1. Confirme a exclusão e selecione o botão **Delete**.
1. Quando o processo for concluído com êxito, o pool de SQL não será mais listado nos recursos do workspace.

## <a name="next-steps"></a>Próximas etapas 
- Confira [Início Rápido: Criar um notebook do Apache Spark](quickstart-apache-spark-notebook.md).
- Confira [Início Rápido: Criar um pool de SQL do Synapse usando o portal do Azure](quickstart-create-sql-pool-portal.md).
