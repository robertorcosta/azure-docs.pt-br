---
title: 'Início Rápido: carregamento em massa de dados com pool de SQL dedicado'
description: Use o Synapse Studio para o carregamento em massa de dados no pool de SQL dedicado do Azure Synapse Analytics.
services: synapse-analytics
author: kevinvngo
ms.service: synapse-analytics
ms.subservice: sql
ms.topic: quickstart
ms.date: 12/11/2020
ms.author: kevin
ms.reviewer: jrasnick
ms.openlocfilehash: 86ef610af605c657868824eefe2e6e706f6963ac
ms.sourcegitcommit: dfc4e6b57b2cb87dbcce5562945678e76d3ac7b6
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/12/2020
ms.locfileid: "97360162"
---
# <a name="quickstart-bulk-loading-with-synapse-sql"></a>Início Rápido: Carregar em massa com o SQL do Synapse

Carregar dados é fácil com o assistente de Carregamento em Massa no Synapse Studio. O assistente de Carregamento em Massa orientará você quanto à criação de um script T-SQL com a [instrução COPY](/sql/t-sql/statements/copy-into-transact-sql?view=azure-sqldw-latest&preserve-view=true) para carregar dados em massa. 

## <a name="entry-points-to-the-bulk-load-wizard"></a>Pontos de entrada para o assistente de Carregamento em Massa

É fácil carregar dados em massa usando pools de SQL dedicados apenas clicando com o botão direito do mouse nas seguintes áreas dentro do Synapse Studio:

- Um arquivo ou pasta de uma conta de armazenamento do Azure anexada ao seu workspace ![Clicar com o botão direito do mouse em um arquivo ou uma pasta de uma conta de armazenamento](./sql/media/bulk-load/bulk-load-entry-point-0.png)

## <a name="prerequisites"></a>Pré-requisitos

- O assistente gera uma instrução COPY, que usa a passagem do Azure AD para autenticação. Seu [usuário do Azure AD deve ter acesso](
./sql-data-warehouse/quickstart-bulk-load-copy-tsql-examples.md#d-azure-active-directory-authentication) ao workspace com pelo menos a função do Azure de Colaborador de Dados do Blob de Armazenamento para a conta do ADLS Gen2. 

- Será preciso ter as [permissões necessárias para usar a instrução COPY](/sql/t-sql/statements/copy-into-transact-sql?view=azure-sqldw-latest&preserve-view=true#permissions) e as permissões Create table se estiver criando uma tabela na qual carregar.

- O serviço vinculado associado à conta do ADLS Gen2 **deve ter acesso ao arquivo**/**à pasta** a ser carregada. Por exemplo, se o mecanismo de autenticação do serviço vinculado for uma Identidade Gerenciada, a identidade gerenciada do workspace deverá ter, pelo menos, a permissão Leitor de blob de armazenamento na conta de armazenamento.

- Se a VNet estiver habilitada em seu workspace, verifique se o runtime integrado associado aos serviços vinculados da Conta do ADLS Gen2 para a localização do arquivo de erro e dos dados de origem tem a criação interativa habilitada. A criação interativa é necessária para a detecção de esquema automático, visualização do conteúdo do arquivo de origem e busca de contas de armazenamento do ADLS Gen2 no assistente.

### <a name="steps"></a>Etapas

1. No painel Local de armazenamento de origem, selecione a conta de armazenamento e o arquivo ou a pasta da qual você está carregando. O assistente tentará automaticamente detectar arquivos Parquet e de texto delimitado (CSV), incluindo o mapeamento de campos de origem do arquivo para os tipos de dados SQL de destino apropriados. 

   ![Selecionar localização de origem](./sql/media/bulk-load/bulk-load-source-location.png)

2. Selecione as configurações do formato de arquivo incluindo suas configurações de erro para quando houver linhas rejeitadas durante o processo de carregamento em massa. Você também pode selecionar "Visualizar dados" para ver como a instrução COPY analisará o arquivo para ajudar você a definir as configurações de formato de arquivo. Clique em "Visualizar dados" toda vez que você alterar uma configuração de formato de arquivo para ver como a instrução COPY analisará o arquivo com a configuração atualizada:

   ![Visualizar dados](./sql/media/bulk-load/bulk-load-file-format-settings-preview-data.png) 

> [!NOTE]  
>
> - Não há suporte para a visualização dos dados com terminadores de campo de vários caracteres no assistente de carregamento em massa. O assistente de carregamento em massa visualizará os dados em uma só coluna quando um terminador de campo de vários caracteres for especificado. 
> - Quando você seleciona "Inferir nomes de coluna", o assistente de carregamento em massa analisará os nomes das colunas da primeira linha especificada pelo campo "Primeira linha". O assistente de carregamento em massa incrementará automaticamente o valor FIRSTROW na instrução COPY em 1 para ignorar essa linha de cabeçalho. 
> - Há suporte para a especificação de terminadores de linha de vários caracteres na instrução COPY; no entanto, não há suporte para ela no assistente de carregamento em massa, em que um erro será gerado.

3. Selecione o pool de SQL dedicado que você está usando para carregar, incluindo se a carga será para uma tabela nova ou existente: ![Selecionar localização de destino](./sql/media/bulk-load/bulk-load-target-location.png)
4. Clique em "Configurar mapeamento de coluna" para verificar se você tem o mapeamento de coluna apropriado. Observação: os nomes de colunas serão detectados automaticamente se a opção "Inferir nomes de colunas" estiver habilitada. Para novas tabelas, configurar o mapeamento de coluna é crítico para atualizar os tipos de dados da coluna de destino:

   ![Configurar o mapeamento de coluna](./sql/media/bulk-load/bulk-load-target-location-column-mapping.png)
5. Clique em "Abrir script" e um script T-SQL será gerado com a instrução COPY a ser carregada do data lake: ![Abrir o script SQL](./sql/media/bulk-load/bulk-load-target-final-script.png)

## <a name="next-steps"></a>Próximas etapas

- Confira o artigo sobre a [instrução COPY](/sql/t-sql/statements/copy-into-transact-sql?view=azure-sqldw-latest&preserve-view=true#syntax) para obter mais informações sobre as funcionalidades dela
- Confira o artigo [visão geral do carregamento de dados](./sql-data-warehouse/design-elt-data-loading.md#what-is-elt)
