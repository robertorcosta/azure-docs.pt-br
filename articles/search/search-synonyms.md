---
title: Sinônimos para expansão de consulta em um índice de pesquisa - Azure Search
description: Crie um mapa de sinônimos para expandir o escopo de uma consulta de pesquisa em um índice do Azure Search. O escopo é ampliado para incluir termos equivalentes fornecidos por você em uma lista.
author: brjohnstmsft
services: search
ms.service: search
ms.devlang: rest-api
ms.topic: conceptual
ms.date: 05/02/2019
manager: nitinme
ms.author: brjohnst
ms.custom: seodec2018
ms.openlocfilehash: a17e2ae5313f9d0b662d343230a04dd3e726c16d
ms.sourcegitcommit: 1d0b37e2e32aad35cc012ba36200389e65b75c21
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/15/2019
ms.locfileid: "72331172"
---
# <a name="synonyms-in-azure-search"></a>Sinônimos no Azure Search

Sinônimos nos termos equivalentes associados do mecanismo de pesquisa que expandem implicitamente o escopo de uma consulta, sem que o usuário tenha de fornecer realmente o termo. Por exemplo, considerando o termo "cão" e as associações de sinônimo de "canino" e "filhote de cão", qualquer documento contendo "cão", "canino" ou "filhote de cão" cairá dentro do escopo da consulta.

No Azure Search, a expansão do sinônimo é feita no momento da consulta. Você pode adicionar mapas de sinônimo a um serviço sem interromper as operações existentes. Você pode adicionar uma propriedade **synonymMaps** a uma definição de campo sem necessidade de recriar o índice.

## <a name="create-synonyms"></a>Criar sinônimos

Não há suporte do portal para criar sinônimos, mas você pode usar a API REST ou o SDK do .NET. Para começar com o REST, é recomendável [usar o postmaster](search-get-started-postman.md) e a formulação de solicitações usando esta API: [criar mapas de sinônimos](https://docs.microsoft.com/rest/api/searchservice/create-synonym-map). Para C# os desenvolvedores, você pode começar a [Adicionar sinônimos na pesquisa do Azure C#usando ](search-synonyms-tutorial-sdk.md)o.

Opcionalmente, se você estiver usando [chaves gerenciadas pelo cliente](search-security-manage-encryption-keys.md) para criptografia do lado do serviço em repouso, poderá aplicar essa proteção ao conteúdo do seu mapa de sinônimos.

## <a name="use-synonyms"></a>Usar sinônimos

No Azure Search, suporte a sinônimos baseia-se em mapas de sinônimo que você define e carrega em seu serviço. Esses mapas constituem um recurso independente (como índices ou fontes de dados) e podem ser usados por qualquer campo pesquisável em um índice do serviço de pesquisa.

Mapas de sinônimo e índices são mantidos de maneira independente. Depois de definir um mapa de sinônimo e carregá-lo em seu serviço, habilite o recurso de sinônimo em um campo, adicionando uma nova propriedade chamada **synonymMaps** à definição de campo. Criar, atualizar e excluir um mapa de sinônimo é sempre uma operação de documento inteiro, o que significa que você não pode criar, atualizar nem excluir partes do mapa sinônimo incrementalmente. Até mesmo uma única entrada a atualização requer um recarregamento.

A incorporação de sinônimos ao seu aplicativo de pesquisa é um processo de duas etapas:

1.  Adicione um mapa de sinônimos ao serviço de pesquisa por meio das APIs a seguir.  

2.  Configure um campo de pesquisa para usar o mapa de sinônimos na definição do índice.

Você pode criar vários mapas de sinônimos para o aplicativo de pesquisa (por exemplo, por idioma, se seu aplicativo dá suporte a uma base de clientes com vários idiomas). Atualmente, um campo só pode usar um deles. Atualize a propriedade synonymMaps de um campo a qualquer momento.

### <a name="synonymmaps-resource-apis"></a>APIs do recurso SynonymMaps

#### <a name="add-or-update-a-synonym-map-under-your-service-using-post-or-put"></a>Adicione ou atualize um mapa de sinônimos em seu serviço, usando POST ou PUT.

Mapas de sinônimos são carregados no serviço via POST ou PUT. Cada regra deve ser delimitada por um caractere de nova linha ('\n'). É possível definir até 5.000 regras por mapa de sinônimos em um serviço gratuito e 10.000 regras em todas as outras SKUs. Cada regra pode ter até 20 expansões.

Os mapas de sinônimos devem estar no formato Apache Solr explicado abaixo. Se você tiver um dicionário de sinônimos em um formato diferente e quiser usá-lo diretamente, informe-nos no [UserVoice](https://feedback.azure.com/forums/263029-azure-search).

Crie um novo mapa de sinônimos usando HTTP POST, como no exemplo a seguir:

    POST https://[servicename].search.windows.net/synonymmaps?api-version=2019-05-06
    api-key: [admin key]

    {
       "name":"mysynonymmap",
       "format":"solr",
       "synonyms": "
          USA, United States, United States of America\n
          Washington, Wash., WA => WA\n"
    }

Como alternativa, use PUT e especifique o nome do mapa de sinônimos no URI. Se o mapa de sinônimos não existir, ele será criado.

    PUT https://[servicename].search.windows.net/synonymmaps/mysynonymmap?api-version=2019-05-06
    api-key: [admin key]

    {
       "format":"solr",
       "synonyms": "
          USA, United States, United States of America\n
          Washington, Wash., WA => WA\n"
    }

##### <a name="apache-solr-synonym-format"></a>Formato de sinônimo Apache Solr

O formato Solr dá suporte a mapeamentos de sinônimo equivalentes e explícitos. As regras de mapeamento aderem à especificação de filtro de sinônimos de software livre do Apache Solr, descritas neste documento: [SynonymFilter](https://cwiki.apache.org/confluence/display/solr/Filter+Descriptions#FilterDescriptions-SynonymFilter). Abaixo está um exemplo de regra de sinônimos equivalentes.
```
USA, United States, United States of America
```

Com a regra acima, uma consulta de pesquisa "EUA" será expandida para "EUA" OR "Estados Unidos" OR "Estados Unidos da América".

Mapeamento explícito é indicado por uma seta "=>". Quando especificado, uma sequência de termo de uma consulta de pesquisa que corresponde ao lado esquerdo de "= >" será substituída pelas alternativas no lado direito. Dada a regra abaixo, consultas de pesquisa "Washington", "Wash." ou "WA" serão regravadas como "WA". Mapeamento explícito só é aplicável na direção especificada e não regrava a consulta "WA" para "Washington" nesse caso.
```
Washington, Wash., WA => WA
```

#### <a name="list-synonym-maps-under-your-service"></a>Liste os mapas de sinônimos em seu serviço.

    GET https://[servicename].search.windows.net/synonymmaps?api-version=2019-05-06
    api-key: [admin key]

#### <a name="get-a-synonym-map-under-your-service"></a>Obtenha um mapa de sinônimos em seu serviço.

    GET https://[servicename].search.windows.net/synonymmaps/mysynonymmap?api-version=2019-05-06
    api-key: [admin key]

#### <a name="delete-a-synonyms-map-under-your-service"></a>Exclua um mapa de sinônimos em seu serviço.

    DELETE https://[servicename].search.windows.net/synonymmaps/mysynonymmap?api-version=2019-05-06
    api-key: [admin key]

### <a name="configure-a-searchable-field-to-use-the-synonym-map-in-the-index-definition"></a>Configure um campo de pesquisa para usar o mapa de sinônimos na definição do índice.

Uma nova propriedade de campo **synonymMaps** pode ser usada a fim de especificar um mapa de sinônimos para usar em um campo pesquisável. Mapas de sinônimos são recursos de nível de serviço e podem ser consultados por qualquer campo de um índice no serviço.

    POST https://[servicename].search.windows.net/indexes?api-version=2019-05-06
    api-key: [admin key]

    {
       "name":"myindex",
       "fields":[
          {
             "name":"id",
             "type":"Edm.String",
             "key":true
          },
          {
             "name":"name",
             "type":"Edm.String",
             "searchable":true,
             "analyzer":"en.lucene",
             "synonymMaps":[
                "mysynonymmap"
             ]
          },
          {
             "name":"name_jp",
             "type":"Edm.String",
             "searchable":true,
             "analyzer":"ja.microsoft",
             "synonymMaps":[
                "japanesesynonymmap"
             ]
          }
       ]
    }

**synonymMaps** pode ser especificado para campos de pesquisa do tipo “Edm.String” ou “Collection(Edm.String)”.

> [!NOTE]
> Só pode haver um mapa de sinônimos por campo. Se você quiser usar vários mapas de sinônimo, informe-nos no [UserVoice](https://feedback.azure.com/forums/263029-azure-search).

## <a name="impact-of-synonyms-on-other-search-features"></a>Impacto de sinônimos em outros recursos de pesquisa

O recurso de sinônimos regrava a consulta original com sinônimos com o operador OR. Por esse motivo, os perfis de destaque e pontuação acessados tratam o termo original e os sinônimos como equivalentes.

O recurso de sinônimo aplica-se a consultas de pesquisa e não se aplicam a filtros ou facetas. Da mesma forma, sugestões baseiam-se apenas no termo original; correspondências de sinônimo não aparecem na resposta.

Expansões de sinônimo não se aplicam a termos de pesquisa de curinga; prefixo, lógica difusa e termos de regex não são expandidos.

Se for necessário fazer uma única consulta que aplique expansão de sinônimos e curingas, regex ou pesquisas difusas, você poderá combinar as consultas usando a sintaxe OR. Por exemplo, para combinar sinônimos com curingas para uma sintaxe de consulta simples, o termo seria `<query> | <query>*`.

Se houver um índice em um ambiente de desenvolvimento (não produção), experimente um dicionário pequeno para ver como a adição de sinônimos altera a experiência de pesquisa, incluindo o impacto nos perfis de pontuação, o realce de acesso e as sugestões.

## <a name="next-steps"></a>Próximos passos

> [!div class="nextstepaction"]
> [Criar um mapa de sinônimos](https://docs.microsoft.com/rest/api/searchservice/create-synonym-map)