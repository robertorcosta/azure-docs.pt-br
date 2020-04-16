---
title: Alterar a transformação da linha no mapeamento do fluxo de dados
description: Como atualizar o destino do banco de dados usando a transformação da linha de alteração no mapeamento do fluxo de dados
author: kromerm
ms.author: makromer
ms.reviewer: daperlov
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 01/08/2020
ms.openlocfilehash: 2923e087426ee04c74da629f4e2d2d49a06eb1ef
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/16/2020
ms.locfileid: "81416543"
---
# <a name="alter-row-transformation-in-mapping-data-flow"></a>Alterar a transformação da linha no mapeamento do fluxo de dados

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Use a transformação Alter Row para definir políticas de inserção, exclusão, atualização e upsert em linhas. Você pode adicionar de um a muitas condições como expressões. Essas condições devem ser especificadas por ordem de prioridade, pois cada linha será marcada com a política correspondente à primeira expressão correspondente. Cada uma dessas condições pode resultar em uma linha (ou linhas) sendo inseridas, atualizadas, excluídas ou upserted. Alter Row pode produzir ações DDL & DML contra seu banco de dados.

![Alterar configurações de linha](media/data-flow/alter-row1.png "Alterar configurações da linha")

As transformações do Alter Row só funcionarão no banco de dados ou no CosmosDB afunda no seu fluxo de dados. As ações que você atribui às linhas (inserir, atualizar, excluir, upsert) não ocorrerão durante as sessões de depuração. Execute uma atividade Execute Data Flow em um pipeline para promulgar as políticas de alter row em suas tabelas de banco de dados.

## <a name="specify-a-default-row-policy"></a>Especificar uma diretiva de linha padrão

Crie uma transformação alter row e especifique uma política de linha com uma condição de `true()`. Cada linha que não corresponder a nenhuma das expressões definidas anteriormente será marcada para a diretiva de linha especificada. Por padrão, cada linha que não corresponder a nenhuma `Insert`expressão condicional será marcada para .

![Alterar política de linha](media/data-flow/alter-row4.png "Alterar política de linha")

> [!NOTE]
> Para marcar todas as linhas com uma política, você pode criar `true()`uma condição para essa política e especificar a condição como .

## <a name="view-policies-in-data-preview"></a>Exibir políticas na visualização de dados

Use [o modo de depuração](concepts-data-flow-debug-mode.md) para exibir os resultados das políticas de alter row no painel de visualização de dados. Uma visualização de dados de uma transformação de alter row não produzirá ações DeDL ou DML contra seu alvo.

![Alterar políticas de linha](media/data-flow/alter-row3.png "Alter e políticas de linha")

Cada diretiva de alter row é representada por um ícone que indica se uma ação inserida, atualizada, upsert ou excluída ocorrerá. O cabeçalho superior mostra quantas linhas são afetadas por cada diretiva na pré-visualização.

## <a name="allow-alter-row-policies-in-sink"></a>Permitir alterar políticas de linha na pia

Para que as políticas de alter row funcionem, o fluxo de dados deve ser escrito em um banco de dados ou no sumidouro do Cosmos. Na guia **Configurações** em sua pia, habilite quais políticas de alter de linha são permitidas para esse dissipador.

![Alterar pia de linha](media/data-flow/alter-row2.png "Pia de alter row")

 O comportamento padrão é apenas permitir inserções. Para permitir atualizações, upserts ou exclusões, verifique a caixa na pia correspondente a essa condição. Se as atualizações, upserts ou, exclusões estiverem ativadas, você deve especificar quais colunas-chave na pia devem ser compatíveis.

> [!NOTE]
> Se suas inserções, atualizações ou upserts modificarem o esquema da tabela de destino na pia, o fluxo de dados falhará. Para modificar o esquema de destino em seu banco de dados, escolha **Recriar tabela** como a ação da tabela. Isso vai cair e recriar sua tabela com a nova definição de esquema.

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

O exemplo abaixo é uma `CleanData` transformação de `SpecifyUpsertConditions` alter row nomeada que pega um fluxo de entrada e cria três condições de alter row. Na transformação anterior, `alterRowCondition` uma coluna nomeada é calculada que determina se uma linha é inserida, atualizada ou excluída no banco de dados. Se o valor da coluna tiver um valor de string que corresponda à regra alterar linha, ele será atribuído a essa diretiva.

No UX da Fábrica de Dados, essa transformação se parece com a imagem abaixo:

![Alterar exemplo de linha](media/data-flow/alter-row4.png "Alterar exemplo de linha")

O script de fluxo de dados para essa transformação está no trecho abaixo:

```
SpecifyUpsertConditions alterRow(insertIf(alterRowCondition == 'insert'),
    updateIf(alterRowCondition == 'update'),
    deleteIf(alterRowCondition == 'delete')) ~> AlterRow
```

## <a name="next-steps"></a>Próximas etapas

Após a transformação do Alter Row, você pode querer [afundar seus dados em um armazenamento de dados de destino](data-flow-sink.md).
