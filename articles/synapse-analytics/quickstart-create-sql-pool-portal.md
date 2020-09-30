---
title: 'Início Rápido: Criar um pool de SQL do Synapse (versão prévia) usando o portal do Azure'
description: Crie um pool de SQL do Synapse usando o portal do Azure seguindo as etapas neste guia.
services: synapse-analytics
author: julieMSFT
ms.service: synapse-analytics
ms.topic: quickstart
ms.subservice: sql
ms.date: 04/15/2020
ms.author: jrasnick
ms.reviewer: jrasnick
ms.openlocfilehash: c89bd01cb2e6947df5ed192d66e0dbae08cf66d5
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/25/2020
ms.locfileid: "91260624"
---
# <a name="quickstart-create-a-synapse-sql-pool-preview-using-the-azure-portal"></a>Início Rápido: Criar um pool de SQL do Synapse (versão prévia) usando o portal do Azure

O Azure Synapse Analytics oferece vários mecanismos de análise para ajudar você a ingerir, transformar, modelar e analisar seus dados. Um pool de SQL oferece recursos de computação e armazenamento baseados em T-SQL. Após a criação de um pool de SQL em seu workspace do Synapse, os dados podem ser carregados, modelados, processados e fornecidos para chegar mais rapidamente a insights analíticos.

Neste guia de início rápido, você aprende a criar um pool de SQL em um workspace do Synapse usando o portal do Azure.

Se você não tiver uma assinatura do Azure, [crie uma conta gratuita antes de começar](https://azure.microsoft.com/free/).

## <a name="prerequisites"></a>Pré-requisitos

- Assinatura do Azure - [criar uma gratuitamente](https://azure.microsoft.com/free/)
- [Workspace do Synapse](./quickstart-create-workspace.md)

## <a name="sign-in-to-the-azure-portal"></a>Entre no Portal do Azure

Entre no [Portal do Azure](https://portal.azure.com/)

## <a name="navigate-to-the-synapse-workspace"></a>Navegue até o workspace do Synapse

1. Navegue até o workspace do Synapse em que o pool de SQL será criado digitando o nome do serviço (ou o nome do recurso diretamente) na barra de pesquisa.
![Barra de pesquisa do portal do Azure com workspaces do Synapse digitados](media/quickstart-create-sql-pool/create-sql-pool-00a.png). 
1. Na lista de workspaces, digite o nome (ou parte do nome) do workspace a ser aberto. Para este exemplo, vamos usar um workspace chamado **contosoanalytics**.
![Lista de workspaces do Synapse filtrados para mostrar os que contêm o nome Contoso.](media/quickstart-create-sql-pool/create-sql-pool-00b.png)

## <a name="create-new-sql-pool"></a>Criar pool de SQL

1. No workspace do Synapse em que deseja criar o pool de SQL, selecione o comando **Novo pool de SQL** na barra superior.
![Visão geral do workspace do Synapse com uma caixa vermelha em torno do comando para criar um pool de SQL.](media/quickstart-create-sql-pool/create-sql-pool-portal-01.png)
2. Insira os seguintes detalhes na guia **Informações Básicas**:

    | Configuração | Valor sugerido | Descrição |
    | :------ | :-------------- | :---------- |
    | **Nome do pool de SQL** | Qualquer nome válido | Nome do pool de SQL. |
    | **Nível de desempenho** | DW100c | Defina para o menor tamanho para reduzir os custos deste início rápido |

  
    ![Fluxo de criação do pool de SQL – guia Básico.](media/quickstart-create-sql-pool/create-sql-pool-portal-02.png)
    > [!IMPORTANT]
    > Observe que há limitações específicas para os nomes que os pools de SQL podem usar. Os nomes não podem conter caracteres especiais, devem ter 15 caracteres ou menos, não conter palavras reservadas e ser exclusivos no workspace.

3. Selecione **Avançar: configurações adicionais**.
4. Selecione **Nenhum** para provisionar o pool de SQL sem dados. Deixe a ordenação padrão selecionada.
![Fluxo de criação de pool de SQL – guia Configurações adicionais.](media/quickstart-create-sql-pool/create-sql-pool-portal-03.png)

5. Selecione **Examinar + criar**.
6. Verifique se os detalhes estão corretos com base no que foi inserido anteriormente. Selecione **Criar**.
![Fluxo de criação do pool de SQL – guia Examinar configurações.](media/quickstart-create-sql-pool/create-sql-pool-portal-04.png)

7. Neste ponto, o fluxo de provisionamento de recursos será iniciado.
 ![Fluxo de criação do pool de SQL – provisionamento de recursos.](media/quickstart-create-sql-pool/create-sql-pool-portal-06.png)

8. Após a conclusão do provisionamento, ao navegar de volta para o workspace, aparecerá uma nova entrada para o pool de SQL recém-criado.
 ![Fluxo de criação do pool de SQL – provisionamento de recursos.](media/quickstart-create-sql-pool/create-sql-pool-portal-07.png)


Depois que o pool de SQL for criado, ele estará disponível no workspace para carregar dados, processar fluxos, ler no lake etc.

## <a name="clean-up-resources"></a>Limpar os recursos

Siga as etapas abaixo para excluir o pool de SQL do workspace.
> [!WARNING]
> A exclusão de um pool de SQL removerá o mecanismo de análise e os dados armazenados no banco de dados do pool de SQL excluído do workspace. Não será mais possível se conectar ao pool de SQL e todas as consultas, pipelines e notebooks que leem ou gravam nesse pool de SQL deixarão de funcionar.

Se você quiser excluir o pool de SQL, realize as seguintes etapas:

1. Navegue até a folha de pools de SQL na folha do workspace
1. Selecione o pool de SQL a ser excluído (nesse caso, **contosowdw**)
1. Depois de selecionar, clique em **excluir**
1. Confirme a exclusão e pressione o botão **Excluir** ![Visão geral do pool de SQL – realçando confirmação de exclusão.](media/quickstart-create-sql-pool/create-sql-pool-portal-11.png)
1. Quando o processo for concluído com êxito, o pool de SQL não será mais listado nos recursos do workspace.

## <a name="next-steps"></a>Próximas etapas

- Confira [Início Rápido: Criar um Pool do Apache Spark no Synapse Studio usando ferramentas da Web](quickstart-apache-spark-notebook.md).
- Confira [Início Rápido: Criar um Pool do Apache Spark usando o portal do Azure](quickstart-create-apache-spark-pool-portal.md).
