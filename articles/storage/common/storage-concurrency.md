---
title: Gerenciando simultaneidade
titleSuffix: Azure Storage
description: Saiba como gerenciar a simultaneidade no armazenamento do Azure para os serviços BLOB, fila, tabela e arquivo. Entenda as três estratégias principais de simultaneidade de dados usadas.
services: storage
author: tamram
ms.service: storage
ms.devlang: dotnet
ms.topic: conceptual
ms.date: 12/20/2019
ms.author: tamram
ms.subservice: common
ms.custom: devx-track-csharp
ms.openlocfilehash: b83a8bfbc79af344c4d158ee65134034db714e9c
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/25/2020
ms.locfileid: "96008898"
---
# <a name="managing-concurrency-in-microsoft-azure-storage"></a>Gerenciando a simultaneidade no Armazenamento do Microsoft Azure

Os aplicativos modernos baseados na Internet normalmente consistem em vários usuários exibindo e atualizando dados de maneira simultânea. Os desenvolvedores de aplicativos precisam pensar atentamente sobre como fornecer uma experiência de usuário previsível, particularmente onde vários usuários podem atualizar os mesmos dados. Existem três estratégias de simultaneidade de dados principais que os desenvolvedores normalmente consideram:

1. Simultaneidade otimista – um aplicativo fazendo uma atualização irá, como parte de sua atualização, verificar se os dados foram alterados desde a última leitura dos dados do aplicativo. Por exemplo, se dois usuários que visualizam uma página wiki fizerem uma atualização para a mesma página, a plataforma wiki deverá garantir que a segunda atualização não substitua a primeira atualização – e que os dois usuários entendam se a atualização foi bem-sucedida ou não. Essa estratégia é usada com mais frequência em aplicativos Web.
2. Simultaneidade pessimista – um aplicativo que procura fazer uma atualização usará um bloqueio em um objeto, impedindo que outros usuários atualizem os dados até que o bloqueio seja liberado.
3. Último gravador vence – uma abordagem que permite que qualquer operação de atualização continue sem verificar se algum outro aplicativo atualizou os dados desde que o aplicativo leu primeiro os dados. Essa estratégia geralmente é usada quando os dados são particionados, portanto, não há probabilidade de que vários usuários acessem os mesmos dados. Ela também pode ser útil em locais em que fluxos de dados de curta duração estão sendo processados.

Este artigo fornece uma visão geral de como o armazenamento do Azure simplifica o desenvolvimento ao dar suporte a todas as três estratégias de simultaneidade.

## <a name="azure-storage-simplifies-cloud-development"></a>Armazenamento do Azure – simplifica o desenvolvimento na nuvem

O armazenamento do Azure dá suporte a todas as três estratégias, embora seja distintivo em sua capacidade de fornecer suporte completo para simultaneidade otimista e pessimista. O armazenamento do Azure foi projetado para adotar um modelo de consistência forte, garantindo que quando ele confirmar uma operação de atualização ou inserção de dados, todos os acessos adicionais a esses dados verão a atualização mais recente. As plataformas de armazenamento que usam um modelo de consistência eventual têm um atraso entre o momento em que uma gravação é executada por um usuário e quando os dados atualizados podem ser vistos por outros usuários.

Os desenvolvedores também devem estar cientes de como uma plataforma de armazenamento isola a alteração – particularmente as alterações no mesmo objeto entre as transações. O serviço de armazenamento do Azure usa o isolamento de instantâneo para permitir que as operações de leitura ocorram simultaneamente com operações de gravação em uma única partição. Diferente de outros níveis de isolamento, o isolamento de instantâneo garante que todas as leituras vejam um instantâneo consistente dos dados mesmo durante atualizações, retornando basicamente os últimos valores confirmados enquanto uma transação de atualização é processada.

## <a name="managing-concurrency-in-blob-storage"></a>Como gerenciar a simultaneidade no armazenamento de Blobs

Você pode optar por usar os modelos de simultaneidade otimista ou pessimista para gerenciar o acesso a blobs e contêineres no serviço Blob. Se você não especificar uma estratégia explicitamente, a última gravação vence será o padrão.

### <a name="optimistic-concurrency-for-blobs-and-containers"></a>Simultaneidade otimista para blobs e contêineres

O serviço de Armazenamento atribui um identificador a todo objeto armazenado. Esse identificador é atualizado toda vez que uma operação de atualização é feita em um objeto. O identificador é retornado para o cliente como parte de uma resposta de HTTP GET usando o cabeçalho ETag (marca de entidade) que está definido no protocolo HTTP. Um usuário fazendo uma atualização em tal objeto pode enviar na ETag original junto com um cabeçalho condicional para garantir que uma atualização só ocorra se uma determinada condição for atendida – nesse caso, a condição é um cabeçalho "If-Match", que requer o serviço de armazenamento para garantir que o valor da ETag especificado na solicitação de atualização seja o mesmo que o armazenado no serviço de armazenamento.

A estrutura desse processo é a seguinte:

1. Recupere um blob do serviço de Armazenamento, a resposta inclui um valor de cabeçalho HTTP ETag que identifica a versão atual do objeto no serviço de Armazenamento.
2. Ao atualizar o blob, inclua o valor de ETag recebido na etapa 1 no cabeçalho condicional **If-Match** da solicitação enviada para o serviço.
3. O serviço compara o valor da ETag na solicitação com o valor da ETag atual do blob.
4. Se o valor da ETag atual do blob for uma versão diferente da ETag no cabeçalho condicional **If-Match** na solicitação, o serviço retornará um erro 412 para o cliente. Esse erro indica ao cliente que outro processo atualizou o blob desde que o cliente o recuperou.
5. Se o valor atual de ETag do blob for a mesma versão de ETag no cabeçalho condicional **If-Match** na solicitação, o serviço realizará a operação solicitada e atualizará o valor da ETag atual do blob para mostrar que foi criada uma nova versão.

O snippet de C# a seguir (usando a Client Storage Library 4.2.0) mostra um exemplo simples de como criar um **If-Match AccessCondition** com base no valor da ETag acessado nas propriedades de um blob que foi recuperado ou inserido anteriormente. Ele usa então o objeto **AccessCondition** quando atualiza o blob: o objeto **AccessCondition** adiciona o cabeçalho **If-Match** à solicitação. Se outro processo atualizou o blob, o serviço Blob retorna uma mensagem de status HTTP 412 (Falha de precondição). Você pode baixar o exemplo completo aqui: [Gerenciando a simultaneidade usando o Armazenamento do Azure](https://code.msdn.microsoft.com/Managing-Concurrency-using-56018114).

```csharp
// Retrieve the ETag from the newly created blob
// Etag is already populated as UploadText should cause a PUT Blob call
// to storage Blob service which returns the ETag in response.
string originalETag = blockBlob.Properties.ETag;

// This code simulates an update by a third party.
string helloText = "Blob updated by a third party.";

// No ETag provided so original blob is overwritten (thus generating a new ETag)
blockBlob.UploadText(helloText);
Console.WriteLine("Blob updated. Updated ETag = {0}",
blockBlob.Properties.ETag);

// Now try to update the blob using the original ETag provided when the blob was created
try
{
    Console.WriteLine("Trying to update blob using original ETag to generate if-match access condition");
    blockBlob.UploadText(helloText,accessCondition:
    AccessCondition.GenerateIfMatchCondition(originalETag));
}
catch (StorageException ex)
{
    if (ex.RequestInformation.HttpStatusCode == (int)HttpStatusCode.PreconditionFailed)
    {
        Console.WriteLine("Precondition failure as expected. Blob's original ETag no longer matches");
        // TODO: client can decide on how it wants to handle the 3rd party updated content.
    }
    else
        throw;
}
```

O armazenamento do Azure também inclui suporte para cabeçalhos condicionais, como **If-Modified-Since**, **If-inmodified-Since**, **If-None-Match** e combinações desses cabeçalhos. Para obter mais informações, confira [Como especificar cabeçalhos condicionais para operações de serviço Blob](/rest/api/storageservices/Specifying-Conditional-Headers-for-Blob-Service-Operations).

A tabela a seguir resume as operações de contêiner que aceitam cabeçalhos condicionais como **If-Match** na solicitação e retornam um valor de ETag na resposta.

| Operação | Retorna o valor de ETag do contêiner | Aceita cabeçalhos condicionais |
|:--- |:--- |:--- |
| Create Container |Sim |Não |
| Get Container Properties |Sim |Não |
| Get Container Metadata |Sim |Não |
| Set Container Metadata |Sim |Sim |
| Get Container ACL |Sim |Não |
| Set Container ACL |Sim |Sim (\*) |
| Delete Container |Não |Sim |
| Lease Container |Sim |Sim |
| Listar Blobs |Não |Não |

(*) As permissões definidas por SetContainerACL são armazenadas em cache e as atualizações para essas permissões levam 30 segundos para serem propagadas durante o qual as atualizações de período não têm garantia de serem consistentes.

A tabela a seguir resume as operações de blob que aceitam cabeçalhos condicionais como **If-Match** na solicitação e retornam um valor de ETag na resposta.

| Operação | Retorna o valor de ETag | Aceita cabeçalhos condicionais |
|:--- |:--- |:--- |
| Put Blob |Sim |Sim |
| Get Blob |Sim |Sim |
| Get Blob Properties |Sim |Sim |
| Set Blob Properties |Sim |Sim |
| Get Blob Metadata |Sim |Sim |
| Set Blob Metadata |Sim |Sim |
| Lease Blob (\*) |Sim |Sim |
| Blob de instantâneo |Sim |Sim |
| Copiar blob |Sim |Sim (para o blob de origem e destino) |
| Anular copiar Blob |Não |Não |
| Delete Blob |Não |Sim |
| Put Block |Não |Não |
| Put Block List |Sim |Sim |
| Get Block List |Sim |Não |
| Put Page |Sim |Sim |
| Get Page Ranges |Sim |Sim |

(*) O blob de concessão não altera a ETag em um blob.

### <a name="pessimistic-concurrency-for-blobs"></a>Simultaneidade pessimista para blobs

Para bloquear um blob para uso exclusivo, adquira uma [concessão](/rest/api/storageservices/Lease-Blob) para ele. Ao adquirir uma concessão, você especifica um período de tempo para a concessão. O período de tempo varia de 15 a 60 segundos ou infinito, que é o valor de um bloqueio exclusivo. Renove uma concessão finita para estendê-la. Libere uma concessão quando tiver terminado. O armazenamento de BLOBs lança automaticamente concessões finitas quando elas expiram.

As concessões permitem que diferentes estratégias de sincronização sejam suportadas. As estratégias incluem *gravação exclusiva/leitura compartilhada*, *gravação exclusiva/leitura exclusiva* e *gravação compartilhada/leitura exclusiva*. Onde existe uma concessão, o armazenamento do Azure impõe gravações exclusivas (operações put, set e Delete), no entanto, garantir que exclusividade para operações de leitura exija que o desenvolvedor garanta que todos os aplicativos cliente usem uma ID de concessão e que apenas um cliente de cada vez tenha uma ID de concessão válida. As operações de leitura que não incluem uma ID de concessão resultam em leituras compartilhadas.

O snippet de C# a seguir mostra um exemplo de aquisição de uma concessão exclusiva por 30 segundos em um blob, atualizando o conteúdo do blob e liberando a concessão. Se já houver uma concessão válida no blob quando você tentar adquirir uma nova concessão, o serviço blob retornará um resultado de status "conflito de HTTP (409)". O snippet de código a seguir usa um objeto **AccessCondition** para encapsular as informações da concessão quando ela faz uma solicitação para atualizar o blob no serviço de Armazenamento.  Você pode baixar o exemplo completo aqui: [Gerenciando a simultaneidade usando o Armazenamento do Azure](https://code.msdn.microsoft.com/Managing-Concurrency-using-56018114).

```csharp
// Acquire lease for 15 seconds
string lease = blockBlob.AcquireLease(TimeSpan.FromSeconds(15), null);
Console.WriteLine("Blob lease acquired. Lease = {0}", lease);

// Update blob using lease. This operation will succeed
const string helloText = "Blob updated";
var accessCondition = AccessCondition.GenerateLeaseCondition(lease);
blockBlob.UploadText(helloText, accessCondition: accessCondition);
Console.WriteLine("Blob updated using an exclusive lease");

//Simulate third party update to blob without lease
try
{
    // Below operation will fail as no valid lease provided
    Console.WriteLine("Trying to update blob without valid lease");
    blockBlob.UploadText("Update without lease, will fail");
}
catch (StorageException ex)
{
    if (ex.RequestInformation.HttpStatusCode == (int)HttpStatusCode.PreconditionFailed)
        Console.WriteLine("Precondition failure as expected. Blob's lease does not match");
    else
        throw;
}
```

Se você tentar realizar uma operação de gravação em um blob sob concessão sem enviar a ID de concessão, a solicitação falhará com um erro 412. Se a concessão expirar antes de chamar o método **UploadText** , mas você ainda passar a ID de concessão, a solicitação também falhará com um erro **412** . Para obter mais informações sobre o gerenciamento de tempos de expiração de concessão e IDs de concessão, consulte a documentação de REST de [Lease Blob](/rest/api/storageservices/Lease-Blob).

As operações de blob a seguir podem usar concessões para gerenciar a simultaneidade pessimista:

* Put Blob
* Get Blob
* Get Blob Properties
* Set Blob Properties
* Get Blob Metadata
* Set Blob Metadata
* Delete Blob
* Put Block
* Put Block List
* Get Block List
* Put Page
* Get Page Ranges
* Snapshot Blob - ID de concessão opcional se existir uma concessão
* Copy Blob - ID de concessão obrigatória se existir uma concessão no blob de destino
* Abort Copy Blob - ID de concessão obrigatória se existir uma concessão infinita no blob de destino
* Lease Blob

### <a name="pessimistic-concurrency-for-containers"></a>Simultaneidade pessimista para contêineres

As concessões em contêineres permitem que as mesmas estratégias de sincronização tenham suporte como em BLOBs (*gravação exclusiva/leitura compartilhada*, *gravação exclusiva/leitura* exclusiva e *gravação compartilhada/leitura exclusiva*) no entanto, ao contrário dos BLOBs, o serviço de armazenamento impõe apenas exclusividade em operações de exclusão. Para excluir um contêiner com uma concessão ativa, o cliente deve incluir a ID da concessão ativa com a solicitação de exclusão. Todas as outras operações de contêiner obtiveram êxito em um contêiner sob concessão sem incluir a ID de concessão, caso em que são operações compartilhadas. Se a exclusividade das operações de leitura ou atualização (colocação ou definição) for obrigatória, os desenvolvedores devem garantir que todos os clientes usem uma ID de concessão e que apenas um cliente de cada vez tenha uma ID de concessão válida.

As operações de contêiner a seguir podem usar concessões para gerenciar a simultaneidade pessimista:

* Delete Container
* Get Container Properties
* Get Container Metadata
* Set Container Metadata
* Get Container ACL
* Set Container ACL
* Lease Container

Para obter mais informações, consulte:

* [Especificando cabeçalhos condicionais para operações do serviço Blob](/rest/api/storageservices/Specifying-Conditional-Headers-for-Blob-Service-Operations)
* [Lease Container](/rest/api/storageservices/Lease-Container)
* [Concessão de blob](/rest/api/storageservices/Lease-Blob)

## <a name="managing-concurrency-in-table-storage"></a>Como gerenciar a simultaneidade no armazenamento de Tabela

O serviço tabela usa verificações de simultaneidade otimista como o comportamento padrão quando você está trabalhando com entidades, diferentemente do serviço BLOB, onde você deve optar explicitamente por fazer verificações de simultaneidade otimista. A outra diferença entre os serviços de tabela e blob é que você só pode gerenciar o comportamento de simultaneidade de entidades, mas com o serviço BLOB, você pode gerenciar a simultaneidade dos contêineres e blobs.

Para usar a simultaneidade otimista e verificar se outro processo modificou uma entidade desde que você a recuperou do serviço de armazenamento de tabela, é possível usar o valor da ETag recebido quando o serviço Tabela retorna uma entidade. A estrutura desse processo é a seguinte:

1. Recupere uma entidade do serviço de armazenamento de tabela, a resposta inclui um valor de ETag que identifica o identificador atual associado a essa entidade no serviço de Armazenamento.
2. Ao atualizar a entidade, inclua o valor da ETag recebido na etapa 1 no cabeçalho obrigatório **If-Match** da solicitação enviada para o serviço.
3. O serviço compara o valor da ETag na solicitação com o valor da ETag atual da entidade.
4. Se o valor da ETag atual da entidade for diferente da ETag no cabeçalho obrigatório **If-Match** na solicitação, o serviço retornará um erro 412 para o cliente. Isso indica para o cliente que outro processo atualizou a entidade desde que ele a recuperou.
5. Se o valor de ETag atual da entidade for o mesmo que o ETag no cabeçalho obrigatório **If-Match** na solicitação ou o cabeçalho **If-Match** contiver o caractere curinga (*), o serviço fará a operação solicitada e atualizará o valor de eTag atual da entidade para mostrar que ela foi atualizada.

O serviço tabela requer que o cliente inclua um cabeçalho **If-Match** em solicitações de atualização. No entanto, é possível forçar uma atualização incondicional (estratégia último a gravar vence) e ignorar as verificações de simultaneidade se o cliente definir o cabeçalho **If-Match** com o caractere curinga (\*) na solicitação.

O snippet de C# a seguir mostra uma entidade de cliente que foi criada ou recuperada anteriormente tendo seu endereço de email atualizado. A operação de recuperação ou inserção inicial armazena o valor da ETag no objeto do cliente e, como a amostra usa a mesma instância de objeto ao executar a operação de substituição, ela automaticamente envia o valor da ETag de volta ao serviço Tabela, permitindo que o serviço verifique se há violações de simultaneidade. Se outro processo atualizou a entidade no armazenamento de tabela, o serviço retorna uma mensagem de status HTTP 412 (Falha de precondição).  Você pode baixar o exemplo completo aqui: [Gerenciando a simultaneidade usando o Armazenamento do Azure](https://code.msdn.microsoft.com/Managing-Concurrency-using-56018114).

```csharp
try
{
    customer.Email = "updatedEmail@contoso.org";
    TableOperation replaceCustomer = TableOperation.Replace(customer);
    customerTable.Execute(replaceCustomer);
    Console.WriteLine("Replace operation succeeded.");
}
catch (StorageException ex)
{
    if (ex.RequestInformation.HttpStatusCode == 412)
        Console.WriteLine("Optimistic concurrency violation – entity has changed since it was retrieved.");
    else
        throw;
}
```

Para desabilitar explicitamente a verificação de simultaneidade, você deve definir a propriedade **ETag** do objeto **employee** para "\*" antes de executar a operação de substituição.

```csharp
customer.ETag = "*";
```

A tabela a seguir resume como as operações de entidade de tabela usam os valores de ETag:

| Operação | Retorna o valor de ETag | Requer o cabeçalho de solicitação If-Match |
|:--- |:--- |:--- |
| Query Entities |Sim |Não |
| Insert Entity |Sim |Não |
| Update Entity |Sim |Sim |
| Merge Entity |Sim |Sim |
| Delete Entity |Não |Sim |
| Insert or Replace Entity |Sim |Não |
| Insert or Merge Entity |Sim |Não |

Observe que as operações **Inserir ou substituir entidade** e **Inserir ou mesclar entidade** *não* realizam nenhuma verificação de simultaneidade, pois não enviam um valor de eTag para o serviço tabela.

Em geral, os desenvolvedores que usam tabelas devem contar com a simultaneidade otimista. Se você precisar de bloqueio pessimista ao acessar tabelas, atribua um blob escolhido para cada tabela e tente fazer uma concessão no blob antes de operar na tabela. Essa abordagem requer que o aplicativo garanta que todos os caminhos de acesso a dados obtenham a concessão antes de operar na tabela. Você também deve observar que o tempo mínimo de concessão é de 15 segundos, o que exige uma consideração cuidadosa para a escalabilidade.

Para obter mais informações, consulte:

* [Operações em entidades](/rest/api/storageservices/Operations-on-Entities)

## <a name="managing-concurrency-in-the-queue-service"></a>Gerenciando a simultaneidade no serviço Fila

Um cenário em que a simultaneidade é uma preocupação no serviço de filas ocorre quando vários clientes recuperam mensagens de uma fila. Quando uma mensagem é recuperada da fila, a resposta inclui a mensagem e um valor de recebimento pop, que é necessário para excluir a mensagem. A mensagem não é excluída automaticamente da fila, mas depois de ela ter sido recuperada, ela não é visível para outros clientes pelo intervalo de tempo especificado pelo parâmetro de tempo limite de visibilidade. O cliente que recupera a mensagem deve excluir a mensagem depois que ela foi processada e antes da hora especificada pelo elemento TimeNextVisible da resposta, que é calculada com base no valor do parâmetro de tempo limite de visibilidade. O valor do tempo limite de visibilidade é adicionado à hora em que a mensagem é recuperada para determinar o valor de TimeNextVisible.

O serviço fila não tem suporte para a simultaneidade otimista ou pessimista e, por esse motivo, os clientes que processam mensagens recuperadas de uma fila devem garantir que as mensagens sejam processadas de maneira idempotente. Uma estratégia do último gravador vence é usada para operações de atualização, como SetQueueServiceProperties, SetQueueMetaData, SetQueueACL e UpdateMessage.

Para obter mais informações, consulte:

* [API REST do serviço Fila](/rest/api/storageservices/Queue-Service-REST-API)
* [Receber mensagens](/rest/api/storageservices/Get-Messages)

## <a name="managing-concurrency-in-azure-files"></a>Como gerenciar a simultaneidade em arquivos do Azure

O serviço Arquivo pode ser acessado usando dois pontos de extremidade de protocolo diferentes: SMB e REST. O serviço REST não tem suporte para bloqueio otimista ou pessimista e todas as atualizações seguirão uma estratégia do último gravador vence. Clientes SMB que montam compartilhamentos de arquivos podem usar mecanismos de bloqueio de sistema de arquivos para gerenciar o acesso a arquivos compartilhados, incluindo a capacidade de executar o bloqueio pessimista. Quando um cliente SMB abre um arquivo, ele especifica o modo de compartilhamento e de acesso do arquivo. Configurar uma opção de Acesso ao Arquivo de “Gravação” ou “Leitura/Gravação” juntamente com um modo de Compartilhamento de Arquivo de “Nenhum” resultará no bloqueio do arquivo por um cliente SMB até o arquivo ser fechado. Se houver a tentativa de realização da operação REST em um arquivo em que um cliente SMB tenha o arquivo bloqueado, o serviço REST retornará o código de status 409 (Conflito) com o código de erro SharingViolation.

Quando um cliente SMB abre um arquivo para exclusão, ele marca o arquivo como exclusão pendente até que todos os outros identificadores abertos do cliente SMB em tal arquivo sejam fechados. Enquanto um arquivo estiver marcado como com exclusão pendente, todas as operações REST em tal arquivo retornarão o código de status 409 (Conflito) com o código de erro SMBDeletePending. O código de status 404 (não encontrado) não é retornado, pois é possível que o cliente SMB remova o sinalizador de exclusão pendente antes de fechar o arquivo. Em outras palavras, o código de status 404 (Não encontrado) é esperado apenas se o arquivo tiver sido removido. Observe que enquanto um arquivo estiver em um estado de exclusão pendente de SMB, ele não será incluído nos resultados de Listar Arquivos. Observe também que as operações REST Delete File e REST Delete Directory são confirmadas de forma atômica e não resultam no estado de exclusão pendente.

Para obter mais informações, consulte:

* [Gerenciando bloqueios de arquivo](/rest/api/storageservices/Managing-File-Locks)

## <a name="next-steps"></a>Próximas etapas

Para encontrar o aplicativo de amostra completo citado nesse blog:

* [Gerenciando a simultaneidade usando o Armazenamento do Azure — aplicativo de amostra](https://code.msdn.microsoft.com/Managing-Concurrency-using-56018114)

Para obter mais informações sobre o armazenamento do Azure, consulte:

* [Página inicial do Armazenamento do Microsoft Azure](https://azure.microsoft.com/services/storage/)
* [Introdução ao Armazenamento do Azure](storage-introduction.md)
* Introdução ao Armazenamento para [Blob](../blobs/storage-quickstart-blobs-dotnet.md), [Tabela](../../cosmos-db/tutorial-develop-table-dotnet.md), [Filas](../queues/storage-dotnet-how-to-use-queues.md) e [Arquivos](../files/storage-dotnet-how-to-use-files.md)
* Arquitetura de Armazenamento – [Armazenamento do Azure: Um serviço de armazenamento em nuvem altamente disponível com coerência forte](/archive/blogs/windowsazurestorage/sosp-paper-windows-azure-storage-a-highly-available-cloud-storage-service-with-strong-consistency)