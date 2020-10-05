---
title: 'Tutorial: Introdução à criação de um workspace do Synapse'
description: Neste tutorial, você aprenderá a criar um workspace do Synapse, um pool de SQL e um Pool do Apache Spark.
services: synapse-analytics
author: saveenr
ms.author: saveenr
manager: julieMSFT
ms.reviewer: jrasnick
ms.service: synapse-analytics
ms.topic: tutorial
ms.date: 08/27/2020
ms.openlocfilehash: f5851174ba223bd0ab48512b9e75449dcc927a04
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/25/2020
ms.locfileid: "91300098"
---
# <a name="prerequisites"></a>Pré-requisitos

Para completar todas as etapas deste tutorial, você precisa ter acesso a um grupo de recursos no qual tenha a função de **Proprietário**. Crie o workspace do Synapse nesse grupo de recursos.

## <a name="create-a-synapse-workspace"></a>Criar um workspace do Synapse

Neste tutorial, você aprenderá a criar um workspace do Synapse, um pool de SQL e um Pool do Apache Spark. 

## <a name="create-a-synapse-workspace"></a>Criar um workspace do Synapse

1. Abra o [portal do Azure](https://portal.azure.com) e, na parte superior, procure por **Synapse**.
1. Nos resultados da pesquisa em **Serviços**, selecione **Azure Synapse Analytics (versão prévia de workspaces)** .
1. Selecione **Adicionar** para criar um workspace.
1. Em **Noções básicas**, escolha o nome do workspace. Neste tutorial, usaremos **myworkspace**.
1. Você precisa de uma conta do ADLSGEN2 para criar um workspace. A opção mais simples é criar uma conta. Se você quiser reutilizar uma existente, será necessário executar algumas configurações adicionais. 
1. OPÇÃO 1 Criação de uma conta do ADLSGEN2 
    1. Navegue até **Selecionar Data Lake Storage Gen 2**. 
    1. Clique em **Criar Novo** e nomeie a conta como **contosolake**.
    1. Clique em **Sistema de Arquivos** e nomeie a conta como **usuários**.
1. OPÇÃO 2 Como usar uma conta existente do ADLSGEN2. Confira as instruções sobre **Como preparar uma Conta de Armazenamento do ADLSGEN2** na parte inferior deste documento.
1. Seu workspace do Azure Synapse usará essa conta de armazenamento como a conta de armazenamento "primária" e o contêiner para armazenar dados do workspace. O workspace armazena dados em tabelas do Apache Spark. Ele armazena logs de aplicativo do Spark em uma pasta chamada **/synapse/nomedoworkspace**.
1. Selecione **Examinar + criar** > **Criar**. Seu workspace fica pronto em alguns minutos.


## <a name="open-synapse-studio"></a>Abrir o Synapse Studio

Após criar o workspace do Azure Synapse, você tem duas maneiras de abrir o Synapse Studio:

* Abra o workspace do Synapse no [portal do Azure](https://portal.azure.com). Na parte superior da seção **Visão Geral**, selecione **Iniciar o Synapse Studio**.
* Acesse `https://web.azuresynapse.net` e entre no seu workspace.

## <a name="create-a-sql-pool"></a>Criar um pool de SQL

1. No Synapse Studio, no painel do lado esquerdo, selecione **Gerenciar** > **Pools de SQL**.
1. Selecione **Novo** e insira estas configurações:

    |Configuração | Valor sugerido | 
    |---|---|---|
    |**Nome do pool de SQL**| **SQLDB1**|
    |**Nível de desempenho**|**DW100C**|
    |||

1. Selecione **Examinar + criar** > **Criar**. Seu pool de SQL estará pronto em alguns minutos. O pool de SQL é associado a um banco de dados do pool de SQL também chamado **SQLDB1**.

Um pool de SQL consome recursos faturáveis desde que ele esteja ativo. Você pode pausar o pool posteriormente para reduzir custos.

## <a name="create-an-apache-spark-pool"></a>Criar um Pool do Apache Spark

1. No Synapse Studio, no painel do lado esquerdo, selecione **Gerenciar** > **Pools do Apache Spark**.
1. Selecione **Novo** e insira estas configurações:

    |Configuração | Valor sugerido | 
    |---|---|---|
    |**Nome do Pool do Apache Spark**|**Spark1**
    |**Tamanho do nó**| **Pequeno**|
    |**Número de nós**| Defina o mínimo como 3 e o máximo como 3|

1. Selecione **Examinar + criar** > **Criar**. Seu Pool do Apache Spark estará pronto em alguns segundos.

> [!NOTE]
> Apesar do nome, um Pool do Apache Spark não é como um pool de SQL. São apenas alguns metadados básicos que você usa para informar ao workspace do Azure Synapse como interagir com o Spark.

Como são metadados, os Pools do Spark não podem ser iniciados nem interrompidos.

Ao realizar qualquer atividade do Spark no Azure Synapse, você especifica um Pool do Spark a ser usado. O pool informa ao Azure Synapse quantos recursos do Spark usar. Você paga somente pelos recursos que usa. Quando você parar ativamente de usar o pool, os recursos atingirão o tempo limite automaticamente e serão reciclados.

> [!NOTE]
> Os bancos de dados do Spark são criados independentemente dos Pools do Spark. Um workspace sempre tem um banco de dados Spark chamado **padrão**. Você pode criar bancos de dados do Spark adicionais.

## <a name="the-sql-on-demand-pool"></a>O pool de SQL sob demanda

Todo workspace é fornecido com um pool pré-criado chamado **SQL sob demanda**. Esse pool não pode ser excluído. O pool do SQL sob demanda permite que você trabalhe com o SQL sem precisar criar nem pensar sobre gerenciar um pool de SQL no Azure Synapse.

Ao contrário dos outros tipos de pools, a cobrança pelo SQL sob demanda baseia-se na quantidade de dados examinados para executar a consulta e não no número de recursos usados para executar a consulta.

* O SQL sob demanda tem seus próprios bancos de dados sob demanda do SQL que existem independentemente de qualquer pool do SQL sob demanda.
* Um workspace sempre tem exatamente um pool do SQL sob demanda chamado **SQL sob demanda**.

## <a name="preparing-a-adlsgen2-storage-account"></a>Como preparar uma conta de armazenamento do ADLSGEN2

### <a name="perform-the-following-steps-before-you-create-your-workspace"></a>Execute as etapas a seguir ANTES de criar seu workspace

1. Abra o [Portal do Azure](https://portal.azure.com).
1. Navegue até a sua conta de armazenamento existente
1. Selecione **Controle de acesso (IAM)** no painel esquerdo. 
1. Atribua as funções a seguir ou certifique-se de que elas já estejam atribuídas:
    * Atribua-se à função de **Proprietário**.
    * Atribua-se à função de **Proprietário de Dados do Storage Blob**.
1. No painel esquerdo, selecione **Contêineres** e crie um contêiner.
1. Será possível nomear o contêiner. Neste documento, usaremos o nome **usuários**.
1. Aceite a configuração padrão **Nível de acesso público** e, em seguida, selecione **Criar**.

### <a name="perform-the-following-steps-after-you-create-your-workspace"></a>Execute as etapas a seguir DEPOIS de criar seu workspace

Configure o acesso à conta de armazenamento em seu workspace. Identidades gerenciadas para seu workspace do Azure Synapse talvez já tenham acesso à conta de armazenamento. Siga estas etapas para se certificar:

1. Abra o [portal do Azure](https://portal.azure.com) e a conta de armazenamento primária escolhida para seu workspace.
1. Selecione **Controle de acesso (IAM)** no painel esquerdo.
1. Atribua as funções a seguir ou verifique se elas já estão atribuídas. Usamos o mesmo nome para a identidade do workspace e o nome do workspace.
    * Para a função de **Colaborador de Dados do Storage Blob** na conta de armazenamento, atribua **myworkspace** como a identidade do workspace.
    * Atribua **myworkspace** como o nome do workspace.

1. Clique em **Salvar**.

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Análise por meio de um pool de SQL](get-started-analyze-sql-pool.md)
