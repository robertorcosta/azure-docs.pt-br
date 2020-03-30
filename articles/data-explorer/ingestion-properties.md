---
title: Propriedades de ingestão de dados para O Azure Data Explorer
description: Conheça as várias propriedades de ingestão de dados suportadas pelo Azure Data Explorer.
author: orspod
ms.author: orspodek
ms.reviewer: tzgitlin
ms.service: data-explorer
ms.topic: conceptual
ms.date: 03/19/2020
ms.openlocfilehash: 48bce1bf03a0a4c8d81fff7ae54e0b22261b70f9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80109570"
---
# <a name="azure-data-explorer-data-ingestion-properties"></a>Propriedades de ingestão de dados do Azure Data Explorer 

A ingestão de dados é o processo pelo qual os dados são adicionados a uma tabela e são disponibilizados para consulta no Azure Data Explorer. Você adiciona propriedades ao comando de `with` ingestão após a palavra-chave.

## <a name="ingestion-properties"></a>Propriedades da ingestão

A tabela a seguir lista as propriedades suportadas pelo Azure Data Explorer, descreve-as e fornece exemplos: 

|Propriedade              |Descrição                                              |Exemplo                                             |
|----------------------|---------------------------------------------------------|----------------------------------------------------|
|`ingestionMapping`    |Um valor de cadeia de caracteres que indica como mapear dados do arquivo de origem para as colunas reais na tabela. Defina `format` o valor com o tipo de mapeamento relevante. Confira os [mapeamentos de dados](/azure/kusto/management/mappings).|`with (format="json", ingestionMapping = "[{\"column\":\"rownumber\", \"Properties\":{\"Path\":\"$.RowNumber\"}}, {\"column\":\"rowguid\", \"Properties\":{\"Path\":\"$.RowGuid\"}}]")`<br>(preterido: `avroMapping`, `csvMapping`, `jsonMapping`) |
|`ingestionMappingReference`|um valor de cadeia de caracteres que indica como mapear dados do arquivo de origem para as colunas reais na tabela usando um objeto de política de mapeamento nomeado. Defina `format` o valor com o tipo de mapeamento relevante. Confira os [mapeamentos de dados](/azure/kusto/management/mappings).|`with (format="csv", ingestionMappingReference = "Mapping1")`<br>(preterido: `avroMappingReference`, `csvMappingReference`, `jsonMappingReference`)|
|`creationTime` |O valor de data (formatado como uma seqüência ISO8601) para usar no momento de criação das extensões de dados ingeridas. Se não for especificado, o valor atual (`now()`) será usado. Sobrepor o padrão é útil ao ingerir dados mais antigos, para que a política de retenção seja aplicada corretamente.|`with (creationTime="2017-02-13T11:09:36.7992775Z")`|
|`extend_schema`|Um valor booliano que, se especificado, instruirá o comando a estender o esquema da tabela (o padrão é `false`). Essa opção se aplica somente aos comandos `.append` e `.set-or-append`. As únicas extensões de esquema permitidas têm colunas adicionais inseridas no final da tabela.|Se o esquema de `(a:string, b:int)`tabela original for , uma `(a:string, b:int, c:datetime, d:string)`extensão de esquema válida seria , mas `(a:string, c:datetime)` não seria válido|
|`folder` |Para [comandos ingest-from-consulta,](/azure/kusto/management/data-ingestion/ingest-from-query) a pasta será atribuída à tabela. Se a tabela já existir, essa propriedade substituirá a pasta da tabela.|`with (folder="Tables/Temporary")`|
|`format` |O formato de dados (veja [formatos de dados suportados](ingestion-supported-formats.md)).|`with (format="csv")`|
|`ingestIfNotExists`|um valor de cadeia de caracteres que, se especificado, impede que a ingestão tenha sucesso se a tabela já tiver dados marcados com uma marcação `ingest-by:` com o mesmo valor. Isso garante uma ingestão de dados idempotente. Para obter mais informações, consulte [ingest-by: tags](/azure/kusto/management/extents-overview#ingest-by-extent-tags).|As `with (ingestIfNotExists='["Part0001"]', tags='["ingest-by:Part0001"]')` propriedades indicam que se `ingest-by:Part0001` os dados com a tag já existirem, então não complete a ingestão atual. Se ele ainda não existir, essa nova ingestão deve ter esse conjunto de etiquetas (caso uma futura ingestão tente ingerir os mesmos dados novamente.)|
|`ignoreFirstRecord` |Um valor booliano que, se definido como `true`, indicará que a ingestão deve ignorar o primeiro registro de cada arquivo. Esta propriedade é útil `CSV`para arquivos em formatos semelhantes, se o primeiro registro no arquivo for em nome da coluna. Por padrão, `false` é assumido.|`with (ignoreFirstRecord=false)`|
|`persistDetails` |Um valor booliano que, se especificado, indicará que o comando deve persistir os resultados detalhados (mesmo se for bem-sucedido) para que o comando [.show operation details](/azure/kusto/management/operations#show-operation-details) possa recuperá-los. Usa `false` como padrão.|`with (persistDetails=true)`|
|`policy_ingestiontime`|Um valor booliano que, se especificado, descreve se a [Política de tempo de ingestão](/azure/kusto/management/ingestiontimepolicy) deve ser habilitada em uma tabela criada por esse comando. O padrão é `true`.|`with (policy_ingestiontime=false)`|
|`recreate_schema` |Um valor booliano que, se especificado, descreverá se o comando pode recriar o esquema da tabela. Esta propriedade se aplica `.set-or-replace` apenas ao comando. Esta propriedade tem precedência sobre a `extend_schema` propriedade se ambos forem definidos.|`with (recreate_schema=true)`|
|`tags`|Uma lista de [tags](/azure/kusto/management/extents-overview#extent-tagging) para associar com os dados ingeridos, formatado sinuoso como uma seqüência JSON |`with (tags="['Tag1', 'Tag2']")`|
|`validationPolicy`|Uma cadeia de caracteres JSON que indica quais validações executar durante a ingestão. Consulte [a ingestão](/azure/kusto/management/data-ingestion/) de dados para obter uma explicação das diferentes opções.| `with (validationPolicy='{"ValidationOptions":1, "ValidationImplications":1}')`(esta é, na verdade, a política padrão)|
|`zipPattern`|Use esta propriedade ao ingerar dados de armazenamento que tenha um arquivo ZIP. Este é um valor de cadeia que indica a expressão regular a ser usada ao selecionar quais arquivos no arquivo ZIP ingerem.  Todos os outros arquivos no arquivo serão ignorados.|`with (zipPattern="*.csv")`|

## <a name="next-steps"></a>Próximas etapas

* Saiba mais sobre [a ingestão de dados](/azure/data-explorer/ingest-data-overview)
* Saiba mais sobre [formatos de dados suportados](ingestion-supported-formats.md)
