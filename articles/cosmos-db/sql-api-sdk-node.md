---
title: 'Azure Cosmos DB: SQL Node.js API, SDK & recursos'
description: Saiba tudo sobre o SDK e a API do SQL Node.js, incluindo as datas de lançamento, as datas de desativação e as alterações feitas entre cada versão do SDK do Node.js para o Azure Cosmos DB.
author: deborahc
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: nodejs
ms.topic: reference
ms.date: 09/24/2018
ms.author: dech
ms.openlocfilehash: 03f79535b3a62fbb4d0309ae86a142bd842cc308
ms.sourcegitcommit: 7d8158fcdcc25107dfda98a355bf4ee6343c0f5c
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/09/2020
ms.locfileid: "80982881"
---
# <a name="azure-cosmos-db-nodejs-sdk-for-sql-api-release-notes-and-resources"></a>SDK do Node.js do Azure Cosmos DB para a API do SQL: notas de versão e recursos
> [!div class="op_single_selector"]
> * [.NET](sql-api-sdk-dotnet.md)
> * [.NET Change Feed](sql-api-sdk-dotnet-changefeed.md)
> * [.NET Core](sql-api-sdk-dotnet-core.md)
> * [Node.js](sql-api-sdk-node.md)
> * [Async Java](sql-api-sdk-async-java.md)
> * [Java](sql-api-sdk-java.md)
> * [Python](sql-api-sdk-python.md)
> * [REST](https://docs.microsoft.com/rest/api/cosmos-db/)
> * [Provedor de Recursos REST](https://docs.microsoft.com/rest/api/cosmos-db-resource-provider/)
> * [Sql](sql-api-query-reference.md)
> * [Executor a granel - .NET](sql-api-sdk-bulk-executor-dot-net.md)
> * [Executor a granel - Java](sql-api-sdk-bulk-executor-java.md)

|Recurso  |Link  |
|---------|---------|
|Baixar o SDK  |   [NPM](https://www.npmjs.com/package/@azure/cosmos) 
|Documentação da API  |  [Documentação de referência de SDK do JavaScript](https://docs.microsoft.com/javascript/api/%40azure/cosmos/?view=azure-node-latest)
|Instruções de instalação do SDK  |  [Instruções de instalação](https://github.com/Azure/azure-cosmos-js#installation)
|Contribuir para o SDK | [GitHub](https://github.com/Azure/azure-cosmos-js/tree/master)
| Exemplos | [Amostras de código node.js](sql-api-nodejs-samples.md)
| Tutorial de introdução | [Introdução ao SDK do JavaScript](sql-api-nodejs-get-started.md)
| Tutorial do aplicativo Web | [Criar um aplicativo web Node.js usando o Azure Cosmos DB](sql-api-nodejs-application.md)
| Plataforma atual com suporte | [Node.js v12.x](https://nodejs.org/en/blog/release/v12.7.0/) - Versão SDK 3.x.x<br/>[Node.js v10.x](https://nodejs.org/en/blog/release/v10.6.0/) - Versão SDK 3.x.x<br/>[Node.js v8.x](https://nodejs.org/en/blog/release/v8.16.0/) - Versão SDK 3.x.x<br/>[Node.js v6.x](https://nodejs.org/en/blog/release/v6.10.3/) - Versão SDK 2.x.x<br/>[Node.js v4.2.0](https://nodejs.org/en/blog/release/v4.2.0/)- Versão SDK 1.x.x<br/> [Node.js v0.12](https://nodejs.org/en/blog/release/v0.12.0/)- Versão SDK 1.x.x<br/> [Node.js v0.10](https://nodejs.org/en/blog/release/v0.10.0/)- Versão SDK 1.x.x

## <a name="release-notes"></a>Notas de versão

### <a name=""></a><a name="3.1.0"/>3.1.0</a>
* Defina a resposta padrãoContinuatokenLimitInKB como 1kb. Por padrão, estamos fechando isso para 1kb para evitar cabeçalhos longos (node.js tem um limite global de tamanho de cabeçalho). Um usuário pode definir este campo para permitir cabeçalhos mais longos, o que pode ajudar o backend a otimizar a execução da consulta.
* Remover desativarSSLVerification. Essa opção tem novas alternativas descritas em [#388](https://github.com/Azure/azure-cosmos-js/pull/388)

### <a name=""></a><a name="3.0.4"/>3.0.4</a>
* Permitir que os InitialHeaders definam explicitamente o cabeçalho da chave de partição
* Use package.json#files para evitar que arquivos estranhos sejam publicados
* Corrigir erro de classificação do mapa de roteamento na versão mais antiga do nó+v8
* Corrige bug quando o usuário fornece opções parciais de retritry

### <a name=""></a><a name="3.0.3"/>3.0.3</a>
* Impedir que o Webpack resolva módulos chamados com a exigência

### <a name=""></a><a name="3.0.2"/>3.0.2</a>
* Corrige um longo e pendente bug onde as RUs estavam sempre sendo relatadas como 0 para consultas agregadas

### <a name=""></a><a name="3.0.0"/>3.0.0</a>

🎉 lançamento v3! 🎉 Muitos novos recursos, correções de bugs e algumas mudanças de quebra. Objetivos principais desta versão:

* Implementar grandes novos recursos
  * Consultas distintas
  * Consultas de LIMITE/DESLOCAMENTO
  * Solicitações canceláveis pelo usuário
* Atualize para a versão mais recente da Cosmos REST API onde todos os contêineres têm escala ilimitada
* Torne mais fácil usar o Cosmos a partir do navegador
* Melhor alinhar com as novas diretrizes do Azure JS SDK

#### <a name="migration-guide-for-breaking-changes"></a>Guia de migração para quebrar mudanças
##### <a name="improved-client-constructor-options"></a>Opções aprimoradas de construtores de clientes

As opções de construção foram simplificadas:

* masterKey foi renomeado chave e mudou-se para o nível superior
* Propriedades anteriormente em options.auth mudaram-se para o nível superior

``` js
// v2
const client = new CosmosClient({
    endpoint: "https://your-database.cosmos.azure.com",
    auth: {
        masterKey: "your-primary-key"
    }
})

// v3
const client = new CosmosClient({
    endpoint: "https://your-database.cosmos.azure.com",
    key: "your-primary-key"
})
```

##### <a name="simplified-queryiterator-api"></a>API de Iterator de Consulta Simplificada
Em v2 havia muitas maneiras diferentes de iterar ou recuperar resultados de uma consulta. Tentamos simplificar a API v3 e remover APIs similares ou duplicadas:

* Remover iterator.next() e iterator.current(). Use fetchNext() para obter páginas de resultados.
* Remover iterator.forEach(). Use os itteradores de async.
* iterator.executeNext() renomeado para iterator.fetchNext()
* iterator.toArray() renomeado para iterator.fetchAll()
* As páginas agora são objetos de resposta adequados em vez de objetos JS simples
* contêiner const = client.database(dbId).container(containerId)

``` js
// v2
container.items.query('SELECT * from c').toArray()
container.items.query('SELECT * from c').executeNext()
container.items.query('SELECT * from c').forEach(({ body: item }) => { console.log(item.id) })

// v3
container.items.query('SELECT * from c').fetchAll()
container.items.query('SELECT * from c').fetchNext()
for await(const { result: item } in client.databases.readAll().getAsyncIterator()) {
    console.log(item.id)
}
```

##### <a name="fixed-containers-are-now-partitioned"></a>Os contêineres fixos são agora particionados
O serviço Cosmos agora suporta chaves de partição em todos os contêineres, incluindo aqueles que foram criados anteriormente como contêineres fixos. O V3 SDK atualiza para a versão mais recente da API que implementa essa mudança, mas não está quebrando. Se você não fornecer uma chave de partição para operações, nós vamos padrão para uma chave de sistema que funciona com todos os seus contêineres e documentos existentes.

##### <a name="upsert-removed-for-stored-procedures"></a>Upsert removido para procedimentos armazenados
Anteriormente upsert era permitido para coleções não particionadas, mas com a atualização da versão aPI, todas as coleções são particionadas, então removemos completamente.

##### <a name="item-reads-will-not-throw-on-404"></a>Leituras de itens não serão jogadas no 404
contêiner const = client.database(dbId).container(containerId)

``` js
// v2
try {
    container.items.read(id, undefined)
} catch (e) {
    if (e.code === 404) { console.log('item not found') }
}

// v3
const { result: item }  = container.items.read(id, undefined)
if (item === undefined) { console.log('item not found') }
```

##### <a name="default-multi-region-write"></a>Gravação padrão de várias regiões
O SDK agora será escrito para várias regiões por padrão se a configuração do Cosmos o suportar. Este foi anteriormente opt-in comportamento.

##### <a name="proper-error-objects"></a>Objetos de erro adequados
As solicitações com falha agora jogam erro ou subclasses adequadas de erro. Anteriormente, eles atiravam objetos JS simples.

#### <a name="new-features"></a>Novos recursos
##### <a name="user-cancelable-requests"></a>Solicitações canceladas pelo usuário
A mudança para buscar internamente nos permite usar a API abortcontroller do navegador para suportar operações canceláveis pelo usuário. No caso de operações em que várias solicitações estão potencialmente em andamento (como consultas de partição cruzada), todas as solicitações para a operação serão canceladas. Os usuários modernos do navegador já terão o AbortController. Os usuários do Node.js precisarão usar uma biblioteca de polipreenchimento

``` js
 const controller = new AbortController()
 const {result: item} = await items.query('SELECT * from c', { abortSignal: controller.signal});
 controller.abort()
```

##### <a name="set-throughput-as-part-of-dbcontainer-create-operation"></a>Definir throughput como parte da operação db/container create
``` js
const { database }  = client.databases.create({ id: 'my-database', throughput: 10000 })
database.containers.create({ id: 'my-container', throughput: 10000 })
```

##### <a name="azurecosmos-sign"></a>@azure/cosmos-sign
A geração de tokens de @azure/cosmos-signcabeçalho foi dividida em uma nova biblioteca, . Qualquer pessoa que ligue diretamente para a API Cosmos REST pode @azure/cosmosusar isso para assinar cabeçalhos usando o mesmo código que chamamos dentro .

##### <a name="uuid-for-generated-ids"></a>UUID para IDs gerados
v2 tinha código personalizado para gerar IDs de itens. Nós mudamos para o conhecido e mantido uuid biblioteca comunitária.

##### <a name="connection-strings"></a>Cadeias de conexão
Agora é possível passar uma seqüência de conexão copiada do portal Azure:

``` js
const client = new CosmosClient("AccountEndpoint=https://test-account.documents.azure.com:443/;AccountKey=c213asdasdefgdfgrtweaYPpgoeCsHbpRTHhxuMsTaw==;")
Add DISTINCT and LIMIT/OFFSET queries (#306)
 const { results } = await items.query('SELECT DISTINCT VALUE r.name FROM ROOT').fetchAll()
 const { results } = await items.query('SELECT * FROM root r OFFSET 1 LIMIT 2').fetchAll()
```

#### <a name="improved-browser-experience"></a>Melhor experiência do navegador
Embora fosse possível usar o V2 SDK no navegador não foi uma experiência ideal. Você precisava polir várias bibliotecas incorporadas do node.js e usar um empacotador como Webpack ou Parcel. O V3 SDK torna a experiência out of the box muito melhor para os usuários do navegador.

* Substitua os internos da solicitação por fetch (#245)
* Remover o uso de Buffer (#330)
* Remova o uso de nó embutido em favor de pacotes/APIs universais (#328)
* Mude para controlador de nó-abortar (#294)

#### <a name="bug-fixes"></a>Correções de bug
* Corrigir oferta de leitura e trazer de volta testes de oferta (#224)
* Corrigir EnableEndpointDiscovery (#207)
* Corrigir rus ausentes em resultados paginados (#360)
* Expandir o tipo de parâmetro de consulta SQL (#346)
* Adicionar ttl ao ItemDefinition (#341)
* Corrigir métricas de consulta CP (#311)
* Adicionar activityId ao FeedResponse (#293)
* Mudar _ts tipo de string para número (#252)(#295)
* Agregação de carga de solicitação de correção (#289)
* Permitir teclas de partição de seqüência em branco (#277)
* Adicionar string ao tipo de consulta de conflito (#237)
* Adicionar uniqueKeyPolicy ao contêiner (#234)

#### <a name="engineering-systems"></a>Sistemas de engenharia
Nem sempre as mudanças mais visíveis, mas ajudam nossa equipe a enviar melhor código, mais rápido.

* Use rollup para builds de produção (#104)
* Atualização para Typescript 3.5 (#327)
* Converta-se em referências de projeto TS. Extrair pasta de teste (#270)
* Habilitar nãoutilizadosLocais e parâmetros não utilizados (#275)
* Azure Pipelines YAML para construções ci (#298)

### <a name=""></a><a name="2.1.5"/>2.1.5</a>
* Sem mudanças de código. Corrige um problema onde alguns arquivos extras foram incluídos no pacote 2.1.4.

### <a name=""></a><a name="2.1.4"/>2.1.4</a>
* Corrigir failover regional dentro da política de retritry
* Corrigir ChangeFeed tem propriedadeMoreResults
* Atualizações de dependência de vv
* Adicionar PolicheckExclusions.txt

### <a name=""></a><a name="2.1.3"/>2.1.3</a>
* Mudar _ts tipo de string para número
* Corrigir testes de indexação padrão
* Política de chave de segurança exclusiva do Backport para v2
* Correções de depuração de demonstração e demonstração

### <a name=""></a><a name="2.1.2"/>2.1.2</a>
* Correções de oferta de backport da filial v3
* Corrigir bug no executeAssinatura do tipoNext()
* Correções de erro de digitação

### <a name=""></a><a name="2.1.1"/>2.1.1</a>
* Construir reestruturação. Permite puxar a versão SDK no momento da compilação.

### <a name=""></a><a name="2.1.0"/>2.1.0</a>
#### <a name="new-features"></a>Novos recursos
* Adicionado suporte ao ChangeFeed (#196)
* Adicionado tipo de dados MultiPolygon para indexação (#191)
* Adicione a propriedade "chave" ao construtor como alias para masterKey (#202)

#### <a name="fixes"></a>Correções
* Corrigir bug onde next() estava retornando valor incorreto no iterador

#### <a name="engineering-improvements"></a>Melhorias de engenharia
* Adicionar teste de integração para consumo de escrita (#199)
* Habilite a instalação diretamente do GitHub (#194)

### <a name=""></a><a name="2.0.5"/>2.0.5</a>
* Adiciona a interface para o tipo de agente do nó. Os usuários de typescript não precisam mais instalar @types/node como uma dependência
* Os locais preferenciais agora são considerados corretamente
* Melhorias para contribuir com a documentação do desenvolvedor
* Diversas correções de erro de digitação

### <a name=""></a><a name="2.0.4"/>2.0.4</a>
* Correções de tipo de problema de definição, introduzido no 2.0.3

### <a name=""></a><a name="2.0.3"/>2.0.3</a>
* Remover a dependência `big-integer`
* Alterne para diretivas de referência para o tipo AsyncIterable. Os usuários de typescript não precisam mais personalizar a configuração "lib".
* Correções de erro de digitação

### <a name=""></a><a name="2.0.2"/>2.0.2</a>
* Corrigir links do leiame

### <a name=""></a><a name="2.0.1"/>2.0.1</a>
* Corrigir a implementação de interface de repetição

### <a name=""></a><a name="2.0.0"/>2.0.0</a>
* Disponibilidade geral da versão 2.0.0 do SDK do JavaScript
* Suporte adicionado para gravações de várias regiões.

### <a name=""></a><a name="2.0.0-3"/>2.0.0-3</a>
* RC1 da Versão 2.0.0 do SDK do JavaScript para a visualização pública.
* Novo modelo de objeto, com CosmosClient de nível superior e os métodos são divididos em classes relevantes de banco de dados, contêiner e classes de item. 
* Suporte para [promessas](https://developer.mozilla.org/docs/Web/JavaScript/Guide/Using_promises). 
* SDK convertido para o TypeScript.

### <a name=""></a><a name="1.14.4"/>1.14.4</a>
* documentação do npm corrigida.

### <a name=""></a><a name="1.14.3"/>1.14.3</a>
* Adicionado suporte para repetições padrão em problemas de conexão.
* Adicionado suporte para o feed de alteração de coleção de leitura.
* Bug de consistência de sessão fixa que intermitentemente causou “sessão de leitura não disponível”
* Adicionado suporte para métricas de consulta.
* Modificado o número máximo de conexões do Agente de http.

### <a name=""></a><a name="1.14.2"/>1.14.2</a>
* Documentação atualizada para fazer referência ao Azure Cosmos DB em vez do Azure DocumentDB.
* Adicionado suporte para configuração de proxyUrl em ConnectionPolicy.

### <a name=""></a><a name="1.14.1"/>1.14.1</a>
* Correção secundária para sistemas de arquivos que diferenciam maiúsculas de minúsculas.

### <a name=""></a><a name="1.14.0"/>1.14.0</a>
* Adiciona suporte à Consistência de Sessão.
* Esta versão do SDK requer a versão mais recente do [Azure Cosmos DB Emulator](https://aka.ms/cosmosdb-emulator).

### <a name=""></a><a name="1.13.0"/>1.13.0</a>
* Divide consultas entre partições aprovadas.
* Adiciona suporte para o link de recurso com barras à esquerda e à direita (e os testes correspondentes).

### <a name=""></a><a name="1.12.2"/>1.12.2</a>
*    documentação do npm corrigida.

### <a name=""></a><a name="1.12.1"/>1.12.1</a>
* Correção de um bug no executeStoredProcedure, em que documentos envolvidos tinham caracteres especiais Unicode (LS, PS).
* Correção de um bug no tratamento de documentos com caracteres Unicode na chave de partição.
* Suporte corrigido para criar coleções com a mídia de nome. Problema nº 114 do GitHub.
* Suporte fixo para o token de autorização de permissão. Problema nº 178 do GitHub.

### <a name=""></a><a name="1.12.0"/>1.12.0</a>
* Foi adicionado suporte a um novo [nível de consistência](consistency-levels.md) chamado ConsistentPrefix.
* Foi adicionado suporte para UriFactory.
* Correção de um bug de suporte ao Unicode. Problema nº 171 do Github.

### <a name=""></a><a name="1.11.0"/>1.11.0</a>
* Adição do suporte para consultas de agregação (COUNT, MIN, MAX, SUM e AVG).
* Adição da opção para controlar o grau de paralelismo em consultas de partição cruzada.
* Adicionada a opção para desativar a verificação TLS ao executar contra o Azure Cosmos DB Emulador.
* Taxa de transferência mínima reduzida em coleções particionadas de 10.100 RU/s a 2500 RU/s.
* Correção do bug de token de continuação para a coleta de partição única. Problema nº 107 do GitHub.
* Correção do bug executeStoredProcedure no tratamento de 0 como parâmetro único. Problema nº 155 do GitHub.

### <a name=""></a><a name="1.10.2"/>1.10.2</a>
* Cabeçalho de agente do usuário fixo para incluir a versão do SDK.
* Limpeza de código secundária.

### <a name=""></a><a name="1.10.1"/>1.10.1</a>
* Desativar a verificação tls ao usar o SDK para direcionar o emulador (hostname=localhost).
* Adicionado suporte para habilitar o registro em log de script durante a execução do procedimento armazenado.

### <a name=""></a><a name="1.10.0"/>1.10.0</a>
* Adicionado suporte para várias consultas paralelas de partição.
* Adição de suporte a consultas TOP/ORDER BY de coleções particionadas.

### <a name=""></a><a name="1.9.0"/>1.9.0</a>
* Suporte à política de repetições para solicitações limitadas adicionado. (As solicitações estranguladas recebem uma taxa de solicitação muito grande exceção, código de erro 429.) Por padrão, o Azure Cosmos DB tenta novamente nove vezes para cada solicitação quando o código de erro 429 é encontrado, honrando a repetiçãoApós o tempo no cabeçalho de resposta. Um intervalo de repetição fixo agora poderá ser definido como parte da propriedade RetryOptions no objeto ConnectionPolicy, se você quiser ignorar o tempo retryAfter retornado pelo servidor entre as repetições. O Azure Cosmos DB agora aguarda um período máximo de 30 segundos para cada solicitação que está sendo limitada (independentemente da contagem de repetições) e retorna a resposta com o código de erro 429. Esse tempo também pode ser substituído na propriedade RetryOptions, no objeto ConnectionPolicy.
* O Cosmos DB agora retorna x-ms-throttle-retry-count e x-ms-throttle-retry-wait-time-ms como os cabeçalhos de resposta em cada solicitação para indicar a contagem de repetições restritas e o tempo cumulativo que a solicitação aguardou entre as tentativas.
* A classe RetryOptions foi adicionada, expondo a propriedade RetryOptions na classe ConnectionPolicy, que pode ser usada para substituir algumas das opções de repetição padrão.

### <a name=""></a><a name="1.8.0"/>1.8.0</a>
* Suporte adicionado para contas de banco de dados de várias regiões.

### <a name=""></a><a name="1.7.0"/>1.7.0</a>
* Adicionado o suporte para o recurso TTL (tempo de vida) para documentos.

### <a name=""></a><a name="1.6.0"/>1.6.0</a>
* Implementação de [coleções particionadas](partition-data.md) e [níveis de desempenho definidos pelo usuário](performance-levels.md).

### <a name=""></a><a name="1.5.6"/>1.5.6</a>
* O bug RangePartitionResolver.resolveForRead foi corrigido, pois ele não estava retornando links devido a uma concatenação incorreta dos resultados.

### <a name=""></a><a name="1.5.5"/>1.5.5</a>
* Corrigido hashPartitionResolver resolveForRead (): quando nenhuma chave de partição fornecida gerava uma exceção, em vez de retornar uma lista de todos os links registrados.

### <a name=""></a><a name="1.5.4"/>1.5.4</a>
* Corrige o problema [nº 100](https://github.com/Azure/azure-documentdb-node/issues/100) — Agente HTTPS Dedicado: evite modificar o agente global para os fins do Azure Cosmos DB. Use um agente dedicado para todos os pedidos da lib.

### <a name=""></a><a name="1.5.3"/>1.5.3</a>
* Corrige o problema [nº 81](https://github.com/Azure/azure-documentdb-node/issues/81) — trate corretamente os traços em IDs de mídia.

### <a name=""></a><a name="1.5.2"/>1.5.2</a>
* Corrige o problema [nº 95](https://github.com/Azure/azure-documentdb-node/issues/95) — aviso de perda do ouvinte EventEmitter.

### <a name=""></a><a name="1.5.1"/>1.5.1</a>
* Corrige o problema [nº 92](https://github.com/Azure/azure-documentdb-node/issues/90) — renomeie a pasta Hash para hash nos sistemas que diferenciam maiúsculas de minúsculas.

### <a name=""></a><a name="1.5.0"/>1.5.0</a>
* Implemente o suporte a fragmentação ao adicionar os resolvedores de partição de hash e de intervalo.

### <a name=""></a><a name="1.4.0"/>1.4.0</a>
* Implementar o Upsert. Novos métodos upsertXXX em documentClient.

### <a name=""></a><a name="1.3.0"/>1.3.0</a>
* Ignorado para alinhar os números de versão com outros SDKs.

### <a name=""></a><a name="1.2.2"/>1.2.2</a>
* Slipt Q promete wrapper para o novo repositório.
* Atualização para o arquivo de pacote do registro npm.

### <a name=""></a><a name="1.2.1"/>1.2.1</a>
* Implementa o roteamento com base em ID.
* Corrige o problema [nº 49](https://github.com/Azure/azure-documentdb-node/issues/49) - a propriedade atual está em conflito com o método atual().

### <a name=""></a><a name="1.2.0"/>1.2.0</a>
* Suporte adicionado para índice geoespaciais.
* Valida a propriedade de ID de todos os recursos. IDs para recursos não podem conter ?, #, &#47;&#47;, caracteres ou terminar com um espaço.
* Adiciona o novo cabeçalho "andamento de transformação do índice" ao ResourceResponse.

### <a name=""></a><a name="1.1.0"/>1.1.0</a>
* Implementa a política de indexação V2.

### <a name=""></a><a name="1.0.3"/>1.0.3</a>
* Problema [nº 40](https://github.com/Azure/azure-documentdb-node/issues/40) - Configurações eslint e grunt implementadas no núcleo e SDK de promessa.

### <a name=""></a><a name="1.0.2"/>1.0.2</a>
* Problema [nº 45](https://github.com/Azure/azure-documentdb-node/issues/45) - Promessa de wrapper não inclui o cabeçalho com erro.

### <a name=""></a><a name="1.0.1"/>1.0.1</a>
* Habilidade implementada de consultar conflitos por meio da adição de readConflicts, readConflictAsync e queryConflicts.
* Documentação da API atualizada.
* Problema [nº 41](https://github.com/Azure/azure-documentdb-node/issues/41) - Erro client.createDocumentAsync.

### <a name=""></a><a name="1.0.0"/>1.0.0</a>
* SDK DO GA.

## <a name="release--retirement-dates"></a>Datas de lançamento e desativação
A Microsoft notifica pelo menos **12 meses** antes de desativar um SDK, a fim de realizar uma transição tranquila para uma versão mais recente/com suporte.

Os novos recursos, funcionalidades e otimizações são adicionados apenas ao SDK atual. Portanto, recomendamos que você atualize sempre que possível para a versão do SDK mais recente.

Qualquer solicitação feita ao Cosmos DB com o uso de um SDK desativado será rejeitada pelo serviço.

> [!WARNING]
> Todas as versões **1.x** do Cliente Node SDK para API SQL serão retiradas em 30 de agosto de **2020**. Isso afeta apenas o SDK do nó do lado do cliente e não afeta scripts do lado do servidor (procedimentos armazenados, gatilhos e UDFs).
> 
>
<br/>

| Versão | Data de lançamento | Data de desativação |
| --- | --- | --- |
| [3.1.0](#3.1.0) |26 de julho de 2019 |--- |
| [3.0.4](#3.0.4) |22 de julho de 2019 |--- |
| [3.0.3](#3.0.3) |17 de julho de 2019 |--- |
| [3.0.2](#3.0.2) |9 de julho de 2019 |--- |
| [3.0.0](#3.0.0) |28 de junho de 2019 |--- |
| [2.1.5](#2.1.5) |20 de março de 2019 |--- |
| [2.1.4](#2.1.4) |15 de março de 2019 |--- |
| [2.1.3](#2.1.3) |8 de março de 2019 |--- |
| [2.1.2](#2.1.2) |28 de janeiro de 2019 |--- |
| [2.1.1](#2.1.1) |5 de dezembro de 2018 |--- |
| [2.1.0](#2.1.0) |4 de dezembro de 2018 |--- |
| [2.0.5](#2.0.5) |7 de novembro de 2018 |--- |
| [2.0.4](#2.0.4) |30 de outubro de 2018 |--- |
| [2.0.3](#2.0.3) |30 de outubro de 2018 |--- |
| [2.0.2](#2.0.2) |10 de outubro de 2018 |--- |
| [2.0.1](#2.0.1) |25 de setembro de 2018 |--- |
| [2.0.0](#2.0.0) |24 de setembro de 2018 |--- |
| [2.0.0-3 (RC)](#2.0.0-3) |2 de agosto de 2018 |--- |
| [1.14.4](#1.14.4) |03 de maio de 2018 |30 de agosto de 2020 |
| [1.14.3](#1.14.3) |03 de maio de 2018 |30 de agosto de 2020 |
| [1.14.2](#1.14.2) |21 de dezembro de 2017 |30 de agosto de 2020 |
| [1.14.1](#1.14.1) |10 de novembro, 2017 |30 de agosto de 2020 |
| [1.14.0](#1.14.0) |9 de novembro de 2017 |30 de agosto de 2020 |
| [1.13.0](#1.13.0) |11 de outubro de 2017 |30 de agosto de 2020 |
| [1.12.2](#1.12.2) |10 de agosto de 2017 |30 de agosto de 2020 |
| [1.12.1](#1.12.1) |10 de agosto de 2017 |30 de agosto de 2020 |
| [1.12.0](#1.12.0) |10 de maio de 2017 |30 de agosto de 2020 |
| [1.11.0](#1.11.0) |16 de março de 2017 |30 de agosto de 2020 |
| [1.10.2](#1.10.2) |27 de janeiro de 2017 |30 de agosto de 2020 |
| [1.10.1](#1.10.1) |22 de dezembro de 2016 |30 de agosto de 2020 |
| [1.10.0](#1.10.0) |03 de outubro de 2016 |30 de agosto de 2020 |
| [1.9.0](#1.9.0) |07 de julho de 2016 |30 de agosto de 2020 |
| [1.8.0](#1.8.0) |14 de junho de 2016 |30 de agosto de 2020 |
| [1.7.0](#1.7.0) |26 de abril de 2016 |30 de agosto de 2020 |
| [1.6.0](#1.6.0) |29 de março de 2016 |30 de agosto de 2020 |
| [1.5.6](#1.5.6) |08 de março de 2016 |30 de agosto de 2020 |
| [1.5.5](#1.5.5) |02 de fevereiro de 2016 |30 de agosto de 2020 |
| [1.5.4](#1.5.4) |1 de fevereiro de 2016 |30 de agosto de 2020 |
| [1.5.2](#1.5.2) |26 de janeiro de 2016 |30 de agosto de 2020 |
| [1.5.2](#1.5.2) |22 de janeiro de 2016 |30 de agosto de 2020 |
| [1.5.1](#1.5.1) |4 de janeiro de 2016 |30 de agosto de 2020 |
| [1.5.0](#1.5.0) |31 de dezembro de 2015 |30 de agosto de 2020 |
| [1.4.0](#1.4.0) |06 de outubro de 2015 |30 de agosto de 2020 |
| [1.3.0](#1.3.0) |06 de outubro de 2015 |30 de agosto de 2020 |
| [1.2.2](#1.2.2) |10 de setembro de 2015 |30 de agosto de 2020 |
| [1.2.1](#1.2.1) |15 de agosto de 2015 |30 de agosto de 2020 |
| [1.2.0](#1.2.0) |5 de agosto de 2015 |30 de agosto de 2020 |
| [1.1.0](#1.1.0) |9 de julho de 2015 |30 de agosto de 2020 |
| [1.0.3](#1.0.3) |04 de junho de 2015 |30 de agosto de 2020 |
| [1.0.2](#1.0.2) |23 de maio de 2015 |30 de agosto de 2020 |
| [1.0.1](#1.0.1) |15 de maio de 2015 |30 de agosto de 2020 |
| [1.0.0](#1.0.0) |8 de abril de 2015 |30 de agosto de 2020 |

## <a name="faq"></a>Perguntas frequentes
[!INCLUDE [cosmos-db-sdk-faq](../../includes/cosmos-db-sdk-faq.md)]

## <a name="see-also"></a>Confira também
Para saber mais sobre o Cosmos DB, consulte a página de serviço do [Microsoft Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/).

