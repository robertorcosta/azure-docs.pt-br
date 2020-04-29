---
title: 'Azure Cosmos DB: API do SQL node. js, recursos de & do SDK'
description: Saiba tudo sobre o SDK e a API do SQL Node.js, incluindo as datas de lançamento, as datas de desativação e as alterações feitas entre cada versão do SDK do Node.js para o Azure Cosmos DB.
author: deborahc
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: nodejs
ms.topic: reference
ms.date: 09/24/2018
ms.author: dech
ms.openlocfilehash: 03f79535b3a62fbb4d0309ae86a142bd842cc308
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/28/2020
ms.locfileid: "80982881"
---
# <a name="azure-cosmos-db-nodejs-sdk-for-sql-api-release-notes-and-resources"></a>SDK do Node.js do Azure Cosmos DB para a API do SQL: notas de versão e recursos
> [!div class="op_single_selector"]
> * [.NET](sql-api-sdk-dotnet.md)
> * [Feed de alterações do .NET](sql-api-sdk-dotnet-changefeed.md)
> * [.NET Core](sql-api-sdk-dotnet-core.md)
> * [Node.js](sql-api-sdk-node.md)
> * [Async Java](sql-api-sdk-async-java.md)
> * [Java](sql-api-sdk-java.md)
> * [Python](sql-api-sdk-python.md)
> * [REST](https://docs.microsoft.com/rest/api/cosmos-db/)
> * [Provedor de Recursos REST](https://docs.microsoft.com/rest/api/cosmos-db-resource-provider/)
> * [SQL](sql-api-query-reference.md)
> * [Executor em massa-.NET](sql-api-sdk-bulk-executor-dot-net.md)
> * [Executor em massa – Java](sql-api-sdk-bulk-executor-java.md)

|Recurso  |Link  |
|---------|---------|
|Baixar o SDK  |   [NPM](https://www.npmjs.com/package/@azure/cosmos) 
|Documentação da API  |  [Documentação de referência de SDK do JavaScript](https://docs.microsoft.com/javascript/api/%40azure/cosmos/?view=azure-node-latest)
|Instruções de instalação do SDK  |  [Instruções de instalação](https://github.com/Azure/azure-cosmos-js#installation)
|Contribuir para o SDK | [GitHub](https://github.com/Azure/azure-cosmos-js/tree/master)
| Exemplos | [Exemplos de código do node. js](sql-api-nodejs-samples.md)
| Tutorial de introdução | [Introdução ao SDK do JavaScript](sql-api-nodejs-get-started.md)
| Tutorial do aplicativo Web | [Criar um aplicativo web Node.js usando o Azure Cosmos DB](sql-api-nodejs-application.md)
| Plataforma atual com suporte | [Node. js V12. x](https://nodejs.org/en/blog/release/v12.7.0/) -SDK versão 3. x. x<br/>[Node. js v10. x](https://nodejs.org/en/blog/release/v10.6.0/) -SDK versão 3. x. x<br/>[Node. js V8. x](https://nodejs.org/en/blog/release/v8.16.0/) -SDK versão 3. x. x<br/>[Node. js v6. x](https://nodejs.org/en/blog/release/v6.10.3/) -SDK versão 2. x. x<br/>[Node. js v 4.2.0](https://nodejs.org/en/blog/release/v4.2.0/)-SDK versão 1. x. x<br/> [Node. js v 0.12](https://nodejs.org/en/blog/release/v0.12.0/)-SDK versão 1. x. x<br/> [Node. js v 0.10](https://nodejs.org/en/blog/release/v0.10.0/)-SDK versão 1. x. x

## <a name="release-notes"></a>Notas de versão

### <a name=""></a><a name="3.1.0"/>3.1.0</a>
* Defina ResponseContinuationTokenLimitInKB padrão como 1 KB. Por padrão, estamos limitando isso a 1 KB para evitar cabeçalhos longos (o Node. js tem um limite de tamanho de cabeçalho global). Um usuário pode definir esse campo para permitir cabeçalhos mais longos, o que pode ajudar o back-end a otimizar a execução da consulta.
* Remova disableSSLVerification. Essa opção tem novas alternativas descritas em [#388](https://github.com/Azure/azure-cosmos-js/pull/388)

### <a name=""></a><a name="3.0.4"/>3.0.4</a>
* Permitir que initialHeaders defina explicitamente o cabeçalho de chave de partição
* Usar arquivos Package. JSON # para impedir que arquivos estranhos sejam publicados
* Corrigir o erro de classificação do mapa de roteamento na versão mais antiga do nó + V8
* Corrige o bug quando o usuário fornece opções de repetição parciais

### <a name=""></a><a name="3.0.3"/>3.0.3</a>
* Impedir que o webpack resolva os módulos chamados com require

### <a name=""></a><a name="3.0.2"/>3.0.2</a>
* Corrige um grande bug pendente em que RUs sempre estavam sendo relatados como 0 para consultas de agregação

### <a name=""></a><a name="3.0.0"/>3.0.0</a>

versão do 🎉 v3! 🎉 muitos recursos novos, correções de bugs e algumas alterações significativas. Metas principais desta versão:

* Implemente novos recursos importantes
  * Consultas distintas
  * Consultas de limite/deslocamento
  * Solicitações canceláveis do usuário
* Atualizar para a versão mais recente da API REST do cosmos, na qual todos os contêineres têm escala ilimitada
* Facilitar o uso do cosmos do navegador
* Melhor alinhamento com as novas diretrizes do SDK do Azure JS

#### <a name="migration-guide-for-breaking-changes"></a>Guia de migração para alterações significativas
##### <a name="improved-client-constructor-options"></a>Opções de construtor de cliente aprimoradas

As opções do Construtor foram simplificadas:

* masterKey foi renomeado como chave e movido para o nível superior
* As propriedades anteriormente em Options. auth foram movidas para o nível superior

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

##### <a name="simplified-queryiterator-api"></a>API QueryIterator simplificada
Na v2, havia várias maneiras diferentes de iterar ou recuperar resultados de uma consulta. Tentamos simplificar a API v3 e remover APIs semelhantes ou duplicadas:

* Remova iterador. Next () e Iterator. Current (). Use fetchNext () para obter páginas de resultados.
* Remova iterador. forEach (). Em vez disso, use iteradores assíncronos.
* Iterator. executeNext () renomeado como iterador. fetchNext ()
* Iterator. toArray () renomeado como iterador. fetchAll ()
* As páginas agora são objetos de resposta adequados em vez de objetos JS simples
* contêiner const = cliente. banco de dados (dbId). contêiner (ContainerId)

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

##### <a name="fixed-containers-are-now-partitioned"></a>Os contêineres fixos agora estão particionados
O serviço Cosmos agora dá suporte a chaves de partição em todos os contêineres, incluindo aqueles que foram criados anteriormente como contêineres fixos. O SDK do v3 é atualizado para a versão mais recente da API que implementa essa alteração, mas não está interrompendo. Se você não fornecer uma chave de partição para operações, iremos usar como padrão uma chave do sistema que funcione com todos os seus contêineres e documentos existentes.

##### <a name="upsert-removed-for-stored-procedures"></a>Upsert removido para procedimentos armazenados
Anteriormente, Upsert era permitido para coleções não particionadas, mas com a atualização de versão da API, todas as coleções são particionadas para que elas sejam removidas por completo.

##### <a name="item-reads-will-not-throw-on-404"></a>As leituras de item não serão lançadas em 404
contêiner const = cliente. banco de dados (dbId). contêiner (ContainerId)

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

##### <a name="default-multi-region-write"></a>Gravação de várias regiões padrão
O SDK agora irá gravar em várias regiões por padrão se a configuração do cosmos der suporte a ela. Esse era um comportamento de consentimento anteriormente.

##### <a name="proper-error-objects"></a>Objetos de erro apropriados
As solicitações com falha agora geram erro ou subclasses de erro apropriadas. Anteriormente, eles lançavam objetos JS simples.

#### <a name="new-features"></a>Novos recursos
##### <a name="user-cancelable-requests"></a>Solicitações canceladas pelo usuário
A mudança para buscar internamente nos permite usar a API AbortController do navegador para dar suporte a operações canceláveis do usuário. No caso de operações em que várias solicitações estão potencialmente em andamento (como consultas entre partições), todas as solicitações para a operação serão canceladas. Os usuários do navegador moderno já terão AbortController. Os usuários do node. js precisarão usar uma biblioteca de metapreenchimento

``` js
 const controller = new AbortController()
 const {result: item} = await items.query('SELECT * from c', { abortSignal: controller.signal});
 controller.abort()
```

##### <a name="set-throughput-as-part-of-dbcontainer-create-operation"></a>Definir a taxa de transferência como parte da operação de criação de BD/contêiner
``` js
const { database }  = client.databases.create({ id: 'my-database', throughput: 10000 })
database.containers.create({ id: 'my-container', throughput: 10000 })
```

##### <a name="azurecosmos-sign"></a>@azure/cosmos-sign
A geração de tokens de cabeçalho foi dividida em uma @azure/cosmos-signnova biblioteca,. Qualquer pessoa que chamar a API REST do cosmos diretamente pode usar isso para assinar cabeçalhos usando o mesmo código @azure/cosmosque chamamos de dentro.

##### <a name="uuid-for-generated-ids"></a>UUID para IDs geradas
v2 tinha código personalizado para gerar IDs de item. Mudamos para o UUID da biblioteca de comunidade bem conhecido e mantido.

##### <a name="connection-strings"></a>Cadeias de conexão
Agora é possível passar uma cadeia de conexão copiada do portal do Azure:

``` js
const client = new CosmosClient("AccountEndpoint=https://test-account.documents.azure.com:443/;AccountKey=c213asdasdefgdfgrtweaYPpgoeCsHbpRTHhxuMsTaw==;")
Add DISTINCT and LIMIT/OFFSET queries (#306)
 const { results } = await items.query('SELECT DISTINCT VALUE r.name FROM ROOT').fetchAll()
 const { results } = await items.query('SELECT * FROM root r OFFSET 1 LIMIT 2').fetchAll()
```

#### <a name="improved-browser-experience"></a>Experiência de navegador aprimorada
Embora fosse possível usar o SDK V2 no navegador, não era uma experiência ideal. Você precisava semipreencher várias bibliotecas internas do node. js e usar um Agrupador como webpack ou remessa. O SDK do v3 torna a experiência pronta para uso muito melhor para os usuários do navegador.

* Substituir os elementos internos da solicitação por busca (#245)
* Remover uso do buffer (#330)
* Remover o uso interno do nó em favor de pacotes/APIs universais (#328)
* Alternar para node-Abort-Controller (#294)

#### <a name="bug-fixes"></a>Correções de bug
* Corrigir testes da oferta de leitura e de retirada (#224)
* Corrigir EnableEndpointDiscovery (#207)
* Corrigir RUs ausente nos resultados paginados (#360)
* Expandir tipo de parâmetro de consulta SQL (#346)
* Adicionar TTL a mydefinition (#341)
* Corrigir métricas de consulta CP (#311)
* Adicionar ActivityId a FeedResponse (#293)
* Alternar tipo de _ts da cadeia de caracteres para o número (#252) (#295)
* Corrigir a agregação de encargo de solicitação (#289)
* Permitir chaves de partição de cadeia de caracteres em branco (#277)
* Adicionar cadeia de caracteres ao tipo de consulta de conflito (#237)
* Adicionar uniqueKeyPolicy ao contêiner (#234)

#### <a name="engineering-systems"></a>Sistemas de engenharia
Nem sempre as alterações mais visíveis, mas ajudam nossa equipe a fornecer um código melhor, mais rápido.

* Usar ROLLUP para compilações de produção (#104)
* Atualizar para o typescript 3,5 (#327)
* Converter em referências de projeto TS. Extrair pasta de teste (#270)
* Habilitar noUnusedLocals e noUnusedParameters (#275)
* Azure Pipelines YAML para compilações de CI (#298)

### <a name=""></a><a name="2.1.5"/>2.1.5</a>
* Nenhuma alteração de código. Corrige um problema em que alguns arquivos extras foram incluídos no pacote 2.1.4.

### <a name=""></a><a name="2.1.4"/>2.1.4</a>
* Corrigir failover regional na política de repetição
* Corrigir a propriedade ChangeFeed hasMoreResults
* Atualizações de dependência de desenvolvimento
* Adicionar PolicheckExclusions. txt

### <a name=""></a><a name="2.1.3"/>2.1.3</a>
* Alternar tipo de _ts da cadeia de caracteres para o número
* Corrigir testes de indexação padrão
* Backport uniqueKeyPolicy a v2
* Correções de depuração para demonstração e demonstração

### <a name=""></a><a name="2.1.2"/>2.1.2</a>
* Correções de oferta do backport da ramificação v3
* Corrigir o bug na assinatura do tipo executeNext ()
* Correções de digitação

### <a name=""></a><a name="2.1.1"/>2.1.1</a>
* Criar reestruturação. Permite a extração da versão do SDK no momento da compilação.

### <a name=""></a><a name="2.1.0"/>2.1.0</a>
#### <a name="new-features"></a>Novos recursos
* Adicionado suporte a ChangeFeed (#196)
* MultiPolygon DataType adicionado para indexação (#191)
* Adicionar a propriedade "Key" ao construtor como alias para masterKey (#202)

#### <a name="fixes"></a>Correções
* Correção do bug em que Next () estava retornando valor incorreto no iterador

#### <a name="engineering-improvements"></a>Aprimoramentos de engenharia
* Adicionar teste de integração para consumo de typescript (#199)
* Habilitar a instalação diretamente do GitHub (#194)

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
* Esta versão do SDK requer a versão mais recente do [emulador de Azure Cosmos DB](https://aka.ms/cosmosdb-emulator).

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
* Adicionada a opção para desabilitar a verificação de TLS ao executar no emulador de Azure Cosmos DB.
* Taxa de transferência mínima reduzida em coleções particionadas de 10.100 RU/s a 2500 RU/s.
* Correção do bug de token de continuação para a coleta de partição única. Problema nº 107 do GitHub.
* Correção do bug executeStoredProcedure no tratamento de 0 como parâmetro único. Problema nº 155 do GitHub.

### <a name=""></a><a name="1.10.2"/>1.10.2</a>
* Cabeçalho de agente do usuário fixo para incluir a versão do SDK.
* Limpeza de código secundária.

### <a name=""></a><a name="1.10.1"/>1.10.1</a>
* Desabilitar a verificação de TLS ao usar o SDK para direcionar o emulador (hostname = localhost).
* Adicionado suporte para habilitar o registro em log de script durante a execução do procedimento armazenado.

### <a name=""></a><a name="1.10.0"/>1.10.0</a>
* Adicionado suporte para várias consultas paralelas de partição.
* Adição de suporte a consultas TOP/ORDER BY de coleções particionadas.

### <a name=""></a><a name="1.9.0"/>1.9.0</a>
* Suporte à política de repetições para solicitações limitadas adicionado. (As solicitações limitadas recebem uma exceção de taxa de solicitação muito grande, código de erro 429.) Por padrão, Azure Cosmos DB repete nove vezes para cada solicitação quando o código de erro 429 for encontrado, respeitando o tempo de retryAfter no cabeçalho de resposta. Um intervalo de repetição fixo agora poderá ser definido como parte da propriedade RetryOptions no objeto ConnectionPolicy, se você quiser ignorar o tempo retryAfter retornado pelo servidor entre as repetições. O Azure Cosmos DB agora aguarda um período máximo de 30 segundos para cada solicitação que está sendo limitada (independentemente da contagem de repetições) e retorna a resposta com o código de erro 429. Esse tempo também pode ser substituído na propriedade RetryOptions, no objeto ConnectionPolicy.
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
* Corrige o problema [nº 100](https://github.com/Azure/azure-documentdb-node/issues/100) — Agente HTTPS Dedicado: evite modificar o agente global para os fins do Azure Cosmos DB. Use um agente dedicado para todas as solicitações da lib.

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
* Valida a propriedade de ID de todos os recursos. As IDs dos recursos não podem conter?,/, #,  &#47;&#47;, caracteres ou terminar com um espaço.
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
> Todas as versões **1. x** do SDK do cliente do nó para a API do SQL serão desativadas em **30 de agosto de 2020**. Isso afeta apenas o SDK do nó do lado do cliente e não afeta scripts do lado do servidor (procedimentos armazenados, gatilhos e UDFs).
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

