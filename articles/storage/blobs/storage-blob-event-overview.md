---
title: Reagir aos eventos de armazenamento de Blobs do Azure | Microsoft Docs
description: Use a Grade de Eventos do Azure para assinar eventos de Armazenamento de Blobs.
author: normesta
ms.author: normesta
ms.date: 04/06/2020
ms.topic: conceptual
ms.service: storage
ms.subservice: blobs
ms.reviewer: cbrooks
ms.openlocfilehash: e4dd6bab6198546dc5acab78ec59d92387328dbb
ms.sourcegitcommit: 441db70765ff9042db87c60f4aa3c51df2afae2d
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/06/2020
ms.locfileid: "80755007"
---
# <a name="reacting-to-blob-storage-events"></a>Reagir aos eventos de armazenamento de Blobs

Os eventos do Azure Storage permitem que os aplicativos reajam a eventos, como a criação e exclusão de blobs. Isso é feito sem a necessidade de código complicado ou serviços de sondagem caros e ineficientes. A melhor parte é que você só paga pelo que usa.

Os eventos de armazenamento blob são empurrados usando [o Azure Event Grid](https://azure.microsoft.com/services/event-grid/) para assinantes como Funções Azure, Azure Logic Apps ou até mesmo para seu próprio ouvinte http. Event Grid fornece entrega de eventos confiáveis para seus aplicativos através de políticas de reavaliação ricas e letras mortas.

Consulte o artigo do esquema de eventos de [armazenamento Blob](../../event-grid/event-schema-blob-storage.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json) para visualizar a lista completa dos eventos que o armazenamento Blob suporta.

Os cenários comuns de eventos de armazenamento de Blobs incluem processamento de vídeo ou imagem, indexação de pesquisa ou qualquer fluxo de trabalho orientado a arquivos. Os carregamentos de arquivo assíncronos são uma excelente opção para eventos. Quando as alterações não forem frequentes, mas seu cenário exigir uma capacidade de resposta imediata, a arquitetura baseada em eventos pode ser especialmente eficaz.

Se você quiser experimentar eventos de armazenamento blob, consulte qualquer um desses artigos de início rápido:

|Se você quiser usar esta ferramenta:    |Veja este artigo: |
|--|-|
|Portal do Azure    |[Quickstart: Eventos de armazenamento do Route Blob para o endpoint da Web com o portal Azure](https://docs.microsoft.com/azure/event-grid/blob-event-quickstart-portal?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)|
|PowerShell    |[Quickstart: Eventos de armazenamento de rota para o ponto final da Web com o PowerShell](https://docs.microsoft.com/azure/storage/blobs/storage-blob-event-quickstart-powershell?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)|
|CLI do Azure    |[Quickstart: Eventos de armazenamento de rota para o ponto final da Web com o Azure CLI](https://docs.microsoft.com/azure/storage/blobs/storage-blob-event-quickstart?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)|

Para ver exemplos aprofundados de reação a eventos de armazenamento Blob usando funções do Azure, consulte estes artigos:

- [Tutorial: Use eventos Azure Data Lake Storage Gen2 para atualizar uma tabela Databricks Delta](data-lake-storage-events.md).
- [Tutorial: Automatize redimensionar imagens enviadas usando event grid](https://docs.microsoft.com/azure/event-grid/resize-images-on-storage-blob-upload-event?tabs=dotnet)

>[!NOTE]
> Apenas contas de armazenamento do tipo **StorageV2 (objetivo geral v2),** **BlockBlobStorage**e **blobStorage** suportam integração de eventos. Contas do tipo **Armazenamento (v1 de uso geral)***não* dão suporte à integração com a Grade de Eventos.

## <a name="the-event-model"></a>O modelo do evento

Event Grid usa [assinaturas de eventos](../../event-grid/concepts.md#event-subscriptions) para encaminhar mensagens de eventos aos assinantes. Esta imagem ilustra a relação entre editores de eventos, assinaturas de eventos e manipuladores de eventos.

![Modelo da Grade de Eventos](./media/storage-blob-event-overview/event-grid-functional-model.png)

Primeiro, inscreva-se um ponto final para um evento. Em seguida, quando um evento é acionado, o serviço Event Grid enviará dados sobre esse evento para o ponto final.

Consulte o artigo do esquema de eventos de [armazenamento Blob](../../event-grid/event-schema-blob-storage.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json) para visualizar:

> [!div class="checklist"]
> * Uma lista completa de eventos de armazenamento Blob e como cada evento é acionado.
> * Um exemplo dos dados que a Grade de Eventos enviaria para cada um desses eventos.
> * O propósito de cada par de valores-chave que aparece nos dados.

## <a name="filtering-events"></a>Filtragem de eventos

Os eventos blob [podem ser filtrados](/cli/azure/eventgrid/event-subscription?view=azure-cli-latest) pelo tipo de evento, nome do contêiner ou nome do objeto que foi criado/excluído. Os filtros em Event Grid correspondem ao início ou ao final do assunto para que eventos com um assunto correspondente vão para o assinante.

Para saber mais sobre como aplicar filtros, consulte [Filter events for Event Grid](https://docs.microsoft.com/azure/event-grid/how-to-filter-events).

O assunto de eventos do Armazenamento de Blobs usa o formato:

```
/blobServices/default/containers/<containername>/blobs/<blobname>
```

Para corresponder todos os eventos de uma conta de armazenamento, você pode deixar os filtros de assunto vazios.

Para corresponder os eventos de blobs criados em um conjunto de contêineres que compartilham um prefixo, use um filtro `subjectBeginsWith` como:

```
/blobServices/default/containers/containerprefix
```

Para corresponder os eventos de blobs criados em um contêiner específico, use um filtro `subjectBeginsWith` como:

```
/blobServices/default/containers/containername/
```

Para corresponder os eventos de blobs criados em um contêiner específico que compartilha um prefixo de nome de blob, use um filtro `subjectBeginsWith` como:

```
/blobServices/default/containers/containername/blobs/blobprefix
```

Para corresponder os eventos de blobs criados em um contêiner específico que compartilha um sufixo de nome de blob, use um filtro `subjectEndsWith` como “.log” ou “.jpg”. Para saber mais, confira [Conceitos da Grade de Eventos](../../event-grid/concepts.md#event-subscriptions).

## <a name="practices-for-consuming-events"></a>Práticas para consumo de eventos

Aplicativos que manipulam eventos de Armazenamento de Blobs devem seguir algumas práticas recomendadas:
> [!div class="checklist"]
> * Como várias assinaturas podem ser configuradas para eventos de rota para o mesmo manipulador de eventos, é importante não supor que os eventos sejam de uma fonte específica, mas para verificar o tópico de mensagem a fim de garantir que ela venha da conta de armazenamento que você está esperando.
> * Da mesma forma, verifique se o eventType é do tipo que você está preparado para processar, e não suponha que todos os eventos recebidos serão os tipos esperados.
> * Como as mensagens podem chegar após algum atraso, use os campos de etag para entender se suas informações sobre objetos ainda estão atualizadas. Para saber como usar o campo etag, consulte [Gerenciando a concorrência no armazenamento Blob](https://docs.microsoft.com/azure/storage/common/storage-concurrency?toc=%2fazure%2fstorage%2fblobs%2ftoc.json#managing-concurrency-in-blob-storage). 
> * Como as mensagens podem sair fora de ordem, use os campos sequenciadores para entender a ordem dos eventos em qualquer objeto em particular. O campo sequenciador é um valor de seqüência que representa a seqüência lógica de eventos para qualquer nome blob em particular. Você pode usar a comparação de string padrão para entender a seqüência relativa de dois eventos no mesmo nome blob.
> Eventos de armazenamento garantem, pelo menos uma vez, a entrega aos assinantes, o que garante que todas as mensagens sejam saídas. No entanto, devido a tentativas ou disponibilidade de assinaturas, mensagens duplicadas podem ocorrer ocasionalmente.
> * Use o campo blobType para entender os tipos de operações permitidos no blob, e quais tipos de biblioteca de cliente você deve usar para acessar o blob. Os valores válidos são `BlockBlob` ou `PageBlob`. 
> * Use o campo de url com os construtores `CloudBlockBlob` e `CloudAppendBlob` para acessar o blob.
> * Ignore os campos que você não entende. Essa prática ajudará você a manter-se resiliente a novos recursos que possam ser adicionados no futuro.
> * Se você quiser garantir que o evento **Microsoft.Storage.BlobCreated** seja acionado somente quando um Block `CopyBlob`Blob estiver completamente comprometido, filtre o evento para chamadas de `PutBlob`API , ou `PutBlockList` `FlushWithClose` REST. Essas chamadas de API acionam o evento **Microsoft.Storage.BlobCreated** somente após os dados serem totalmente comprometidos com um Block Blob. Para saber como criar um filtro, consulte [Filtrar eventos para A Grade de Eventos](https://docs.microsoft.com/azure/event-grid/how-to-filter-events).


## <a name="next-steps"></a>Próximas etapas

Saiba mais sobre a Grade de Eventos e experimente os eventos do Armazenamento de Blobs:

- [Sobre a Grade de Eventos](../../event-grid/overview.md)
- [Esquema de eventos de armazenamento blob](../../event-grid/event-schema-blob-storage.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)
- [Route Blob storage Eventos para um ponto final da Web personalizado](storage-blob-event-quickstart.md)
