---
title: Alterar transformação de linha no fluxo de dados de mapeamento
description: Como atualizar o destino do banco de dados usando a transformação alterar linha no mapeamento de data Flow
author: kromerm
ms.author: makromer
ms.reviewer: daperlov
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 04/20/2020
ms.openlocfilehash: 6b353967c9b9c7517f1a42581717c6394c0e6374
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/28/2020
ms.locfileid: "81729132"
---
# <a name="alter-row-transformation-in-mapping-data-flow"></a>Alterar transformação de linha no fluxo de dados de mapeamento

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Use a transformação alterar linha para definir políticas de inserção, exclusão, atualização e Upsert em linhas. Você pode adicionar condições um-para-muitos como expressões. Essas condições devem ser especificadas em ordem de prioridade, pois cada linha será marcada com a política correspondente à primeira expressão correspondente. Cada uma dessas condições pode resultar em uma linha (ou linhas) sendo inserida, atualizada, excluída ou introduzida. ALTER Row pode produzir as ações de DDL & DML em seu banco de dados.

![Alterar configurações de linha](media/data-flow/alter-row1.png "Alterar configurações de linha")

As transformações de alteração de linha só funcionarão em coletores de banco de dados ou CosmosDB em seu fluxo de dados. As ações que você atribui a linhas (inserir, atualizar, excluir, Upsert) não ocorrerão durante as sessões de depuração. Execute uma atividade executar fluxo de dados em um pipeline para aplicar as políticas de alteração de linha nas tabelas do banco de dados.

## <a name="specify-a-default-row-policy"></a>Especificar uma política de linha padrão

Crie uma transformação ALTER Row e especifique uma política de linha com uma condição `true()`de. Cada linha que não corresponde a nenhuma das expressões definidas anteriormente será marcada para a política de linha especificada. Por padrão, cada linha que não corresponde a nenhuma expressão condicional será marcada para `Insert`.

![Alterar política de linha](media/data-flow/alter-row4.png "Alterar política de linha")

> [!NOTE]
> Para marcar todas as linhas com uma política, você pode criar uma condição para essa política e especificar a condição `true()`como.

## <a name="view-policies-in-data-preview"></a>Exibir políticas na visualização de dados

Use o [modo de depuração](concepts-data-flow-debug-mode.md) para exibir os resultados de suas políticas de alteração de linha no painel de visualização de dados. Uma visualização de dados de uma transformação ALTER Row não produzirá ações DDL ou DML em relação ao seu destino.

![Alterar políticas de linha](media/data-flow/alter-row3.png "Alterar políticas de linha")

Cada política de alteração de linha é representada por um ícone que indica se uma ação de inserção, atualização, Upsert ou excluída ocorrerá. O cabeçalho superior mostra quantas linhas são afetadas por cada política na visualização.

## <a name="allow-alter-row-policies-in-sink"></a>Permitir políticas de alteração de linha no coletor

Para que as políticas de alteração de linha funcionem, o fluxo de dados deve gravar em um Database ou coletor de Cosmos. Na guia **configurações** no coletor, habilite quais políticas de alteração de linha são permitidas para esse coletor.

![Alterar coletor de linha](media/data-flow/alter-row2.png "Alterar coletor de linha")

O comportamento padrão é permitir apenas inserções. Para permitir atualizações, upserts ou exclusões, marque a caixa no coletor correspondente a essa condição. Se atualizações, upserts ou exclusões estiverem habilitadas, você deverá especificar quais colunas de chave no coletor devem corresponder.

> [!NOTE]
> Se suas inserções, atualizações ou upserts modificarem o esquema da tabela de destino no coletor, o fluxo de dados falhará. Para modificar o esquema de destino em seu banco de dados, escolha **recriar tabela** como a ação da tabela. Isso removerá e recriará sua tabela com a nova definição de esquema.

A transformação do coletor requer uma única chave ou uma série de chaves para identificação de linha exclusiva no banco de dados de destino. Para coletores SQL, defina as chaves na guia Configurações do coletor. Para CosmosDB, defina a chave de partição nas configurações e também defina o campo do sistema CosmosDB "ID" em seu mapeamento de coletor. Para CosmosDB, é obrigatório incluir a coluna "ID" do sistema para atualizações, upserts e exclusões.

## <a name="data-flow-script"></a>Script de fluxo de dados

### <a name="syntax"></a>Sintaxe

```
<incomingStream>
    alterRow(
           insertIf(<condition>?),
           updateIf(<condition>?),
           deleteIf(<condition>?),
           upsertIf(<condition>?),
        ) ~> <alterRowTransformationName>
```

### <a name="example"></a>Exemplo

O exemplo abaixo é uma transformação ALTER Row chamada `CleanData` que usa um fluxo `SpecifyUpsertConditions` de entrada e cria três condições de alteração de linha. Na transformação anterior, uma coluna chamada `alterRowCondition` é calculada que determina se uma linha é inserida, atualizada ou excluída no banco de dados. Se o valor da coluna tiver um valor de cadeia de caracteres que corresponda à regra de alteração de linha, essa política será atribuída.

No Data Factory UX, essa transformação é semelhante à imagem abaixo:

![Exemplo de ALTER Row](media/data-flow/alter-row4.png "Exemplo de ALTER Row")

O script de fluxo de dados para essa transformação está no trecho de código abaixo:

```
SpecifyUpsertConditions alterRow(insertIf(alterRowCondition == 'insert'),
    updateIf(alterRowCondition == 'update'),
    deleteIf(alterRowCondition == 'delete')) ~> AlterRow
```

## <a name="next-steps"></a>Próximas etapas

Após a transformação alterar linha, talvez você queira [coletar seus dados em um armazenamento de dados de destino](data-flow-sink.md).
