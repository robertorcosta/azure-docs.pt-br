---
title: Reagir aos eventos de armazenamento de Blobs do Azure | Microsoft Docs
description: Use a Grade de Eventos do Azure para assinar eventos de Armazenamento de Blobs.
author: normesta
ms.author: normesta
ms.date: 01/30/2018
ms.topic: conceptual
ms.service: storage
ms.subservice: blobs
ms.reviewer: cbrooks
ms.openlocfilehash: 5281dab8fd42326d88964614fd20a81621b5e9dd
ms.sourcegitcommit: 72c2da0def8aa7ebe0691612a89bb70cd0c5a436
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/10/2020
ms.locfileid: "79082073"
---
# <a name="reacting-to-blob-storage-events"></a>Reagir aos eventos de armazenamento de Blobs

Os eventos de armazenamento do Azure permitem que os aplicativos reajam a eventos, como a criação e a exclusão de BLOBs. Isso é feito sem a necessidade de código complicado ou serviços de sondagem caros e ineficientes.

Os eventos são enviados por push usando a [grade de eventos do Azure](https://azure.microsoft.com/services/event-grid/) para assinantes como Azure functions, aplicativos lógicos do Azure ou até mesmo para seu próprio ouvinte http. A melhor parte é pagar apenas pelo que usar.

O armazenamento de BLOBs envia eventos para a grade de eventos que fornece uma entrega de eventos confiável para seus aplicativos por meio de políticas de repetição avançadas e mensagens mortas.

Os cenários comuns de eventos de armazenamento de Blobs incluem processamento de vídeo ou imagem, indexação de pesquisa ou qualquer fluxo de trabalho orientado a arquivos. Os carregamentos de arquivo assíncronos são uma excelente opção para eventos. Quando as alterações não forem frequentes, mas seu cenário exigir uma capacidade de resposta imediata, a arquitetura baseada em eventos pode ser especialmente eficaz.

Se você quiser experimentar isso agora, consulte qualquer um destes artigos de início rápido:

|Se você quiser usar essa ferramenta:    |Consulte este artigo: |
|--|-|
|Portal do Azure    |[Início rápido: rotear eventos de armazenamento de BLOB para o ponto de extremidade da Web com o portal do Azure](https://docs.microsoft.com/azure/event-grid/blob-event-quickstart-portal?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)|
|PowerShell    |[Início rápido: rotear eventos de armazenamento para o ponto de extremidade da Web com o PowerShell](https://docs.microsoft.com/azure/storage/blobs/storage-blob-event-quickstart-powershell?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)|
|CLI do Azure    |[Início rápido: rotear eventos de armazenamento para o ponto de extremidade da Web com CLI do Azure](https://docs.microsoft.com/azure/storage/blobs/storage-blob-event-quickstart?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)|

Para exibir exemplos detalhados de como reagir a eventos de armazenamento de BLOBs usando o Azure functions, consulte estes artigos:

- [Tutorial: usar eventos de Azure data Lake Storage Gen2 para atualizar uma tabela Delta do databricks](data-lake-storage-events.md).
- [Tutorial: automatizar o redimensionamento de imagens carregadas usando a grade de eventos](https://docs.microsoft.com/azure/event-grid/resize-images-on-storage-blob-upload-event?tabs=dotnet)

>[!NOTE]
> Somente contas de armazenamento do tipo **StorageV2 (v2 de uso geral)** e **BlobStorage** dão suporte à integração de eventos. Contas do tipo **Armazenamento (v1 de uso geral)** *não* dão suporte à integração com a Grade de Eventos.

## <a name="the-event-model"></a>O modelo de evento

A grade de eventos usa [assinaturas de evento](../../event-grid/concepts.md#event-subscriptions) para rotear mensagens de evento para assinantes. Essa imagem ilustra a relação entre os editores de eventos, as assinaturas de evento e os manipuladores de eventos.

![Modelo da Grade de Eventos](./media/storage-blob-event-overview/event-grid-functional-model.png)

Primeiro, assine um ponto de extremidade para um evento. Em seguida, quando um evento for disparado, o serviço de grade de eventos enviará dados sobre esse evento para o ponto de extremidade.

Consulte o artigo [esquema de eventos de armazenamento de BLOBs](../../event-grid/event-schema-blob-storage.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json) para exibir:

> [!div class="checklist"]
> * Uma lista completa de eventos de armazenamento de BLOBs e como cada evento é disparado.
> * Um exemplo dos dados que a grade de eventos enviaria para cada um desses eventos.
> * A finalidade de cada par chave-valor que aparece nos dados.

## <a name="filtering-events"></a>Filtrando eventos

Os [eventos de blob podem ser filtrados](/cli/azure/eventgrid/event-subscription?view=azure-cli-latest) pelo tipo de evento, nome do contêiner ou nome do objeto que foi criado/excluído. Os filtros na grade de eventos correspondem ao início ou ao fim do assunto, de forma que os eventos com um assunto correspondente vá para o Assinante.

Para saber mais sobre como aplicar filtros, consulte [filtrar eventos para a grade de eventos](https://docs.microsoft.com/azure/event-grid/how-to-filter-events).

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
> * À medida que as mensagens podem chegar após algum atraso, use os campos ETag para entender se suas informações sobre objetos ainda estão atualizadas. Para saber como usar o campo ETag, consulte [Gerenciando a simultaneidade no armazenamento de BLOBs](https://docs.microsoft.com/azure/storage/common/storage-concurrency?toc=%2fazure%2fstorage%2fblobs%2ftoc.json#managing-concurrency-in-blob-storage). 
> * À medida que as mensagens podem chegar fora de ordem, use os campos do Sequencer para entender a ordem dos eventos em qualquer objeto específico. O campo Sequencer é um valor de cadeia de caracteres que representa a sequência lógica de eventos para qualquer nome de blob específico. Você pode usar a comparação de cadeia de caracteres padrão para entender a sequência relativa de dois eventos no mesmo nome de BLOB.
> * Use o campo blobType para entender os tipos de operações permitidos no blob, e quais tipos de biblioteca de cliente você deve usar para acessar o blob. Os valores válidos são `BlockBlob` ou `PageBlob`. 
> * Use o campo de url com os construtores `CloudBlockBlob` e `CloudAppendBlob` para acessar o blob.
> * Ignore os campos que você não entende. Essa prática ajudará você a manter-se resiliente a novos recursos que possam ser adicionados no futuro.
> * Se você quiser garantir que o evento **Microsoft. Storage. BlobCreated** seja disparado somente quando um blob de blocos for completamente confirmado, filtre o evento para as chamadas `CopyBlob`, `PutBlob`, `PutBlockList` ou `FlushWithClose` API REST. Essas chamadas de API disparam o evento **Microsoft. Storage. BlobCreated** somente depois que os dados são totalmente confirmados em um blob de blocos. Para saber como criar um filtro, consulte [filtrar eventos para a grade de eventos](https://docs.microsoft.com/azure/event-grid/how-to-filter-events).


## <a name="next-steps"></a>{1&gt;{2&gt;Próximas etapas&lt;2}&lt;1}

Saiba mais sobre a Grade de Eventos e experimente os eventos do Armazenamento de Blobs:

- [Sobre a Grade de Eventos](../../event-grid/overview.md)
- [Rotear eventos do Armazenamento de Blobs para um ponto de extremidade da Web personalizado](storage-blob-event-quickstart.md)
