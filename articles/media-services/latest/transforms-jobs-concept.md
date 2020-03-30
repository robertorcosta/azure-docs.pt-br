---
title: Transformações e Empregos em Serviços de Mídia
titleSuffix: Azure Media Services
description: Aprenda a criar um Transforms para descrever as regras para processar seus vídeos no Azure Media Services.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 08/19/2019
ms.author: juliako
ms.openlocfilehash: ab99b974aed6f8cd5e1da2ee9b427f593b405889
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "73571229"
---
# <a name="transforms-and-jobs-in-media-services"></a>Transformações e Empregos em Serviços de Mídia

Este tópico dá detalhes sobre [Transformações](https://docs.microsoft.com/rest/api/media/transforms) e [Empregos](https://docs.microsoft.com/rest/api/media/jobs) e explica a relação entre essas entidades.

## <a name="overview"></a>Visão geral

### <a name="transformsjobs-workflow"></a>Transformações/Empregos fluxo de trabalho

O diagrama a seguir mostra transformações/fluxo de trabalho de empregos:

![Transforma e empregos fluxo de trabalho nos Serviços de Mídia do Azure](./media/encoding/transforms-jobs.png)

#### <a name="typical-workflow"></a>Fluxo de trabalho típico

1. Criar uma Transformação.
2. Envie empregos essa Transformação.
3. Lista transforma.
4. Exclua uma Transformação, se você não estiver planejando usá-la no futuro.

#### <a name="example"></a>Exemplo

Suponha que você queria extrair o primeiro quadro de todos os seus vídeos como uma imagem em miniatura – os passos que você tomaria são:

1. Defina a receita, ou a regra para o processamento de seus vídeos: "use o primeiro quadro do vídeo como miniatura".
2. Para cada vídeo, você diria ao serviço:
    1. Onde encontrar esse vídeo.
    2. Onde gravar a saída a imagem em miniatura de saída.

Uma **Transformação** ajuda você a criar uma vez a receita (Etapa 1) e enviar trabalhos usando essa receita (Etapa 2).

> [!NOTE]
> As propriedades de **Transformar** e **Trabalho** do tipo Datetime estão sempre no formato UTC.

## <a name="transforms"></a>Transformações

Use **Transformações** para configurar tarefas comuns para codificar ou analisar vídeos. Cada **Transformação** descreve uma receita ou um fluxo de trabalho de tarefas para processar seus arquivos de vídeo ou áudio. Uma única transformação pode aplicar mais de uma regra. Por exemplo, uma transformação poderia especificar que cada vídeo seja codificado em um arquivo MP4 em uma determinada taxa de bits, e que uma imagem em miniatura gerada desde o primeiro quadro do vídeo. Você precisaria adicionar uma entrada de TransformOutput para cada regra que você deseja incluir em sua Transformação. Você usa predefinições para dizer ao Transform como os arquivos de mídia de entrada devem ser processados.

### <a name="viewing-schema"></a>Esquema de visualização

No Media Services v3, as predefinições são entidades fortemente digitadas na própria API. Você pode encontrar a definição de "esquema" para esses objetos em [Especificação de API Aberta (ou Swagger)](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/mediaservices/resource-manager/Microsoft.Media/stable/2018-07-01). Você também pode visualizar as definições predefinidas (como **StandardEncoderPreset)** na [API REST,](https://docs.microsoft.com/rest/api/media/transforms/createorupdate#standardencoderpreset) [.NET SDK](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.media.models.standardencoderpreset?view=azure-dotnet)ou outra documentação de referência do Media Services v3 SDK.

### <a name="creating-transforms"></a>Criando transformações

Você pode criar Transformações usando REST, CLI ou qualquer um dos SDKs publicados. A API é controlada pelo Azure Resource Manager v3, portanto, você também pode usar modelos do Azure Resource Manager para criar Transformações na sua conta dos Serviços de Mídia do Microsoft Azure. O controle de acesso baseado em função pode ser usado para bloquear o acesso a transformações.

### <a name="updating-transforms"></a>Atualizando transformações

Se você precisar atualizar o [transform,](https://docs.microsoft.com/rest/api/media/transforms)use a operação **Atualizar.** Destina-se a fazer alterações na descrição ou nas prioridades das Transformações subjacentes. Recomenda-se que tais atualizações sejam feitas quando todos os trabalhos em andamento tiverem sido concluídos. Se você pretende reescrever a receita, você precisa criar uma nova Transformação.

### <a name="transform-object-diagram"></a>Transformar diagrama de objeto

O diagrama a seguir mostra o objeto **Transformar** e os objetos que ele faz referência, incluindo as relações de derivação. As setas cinzamostram um tipo que as referências de Jó e as setas verdes mostram relações de derivação de classe.

Selecione a imagem para vê-la em tamanho real.  

<a href="./media/api-diagrams/transform-large.png" target="_blank"><img src="./media/api-diagrams/transform-small.png"></a>

## <a name="jobs"></a>Trabalhos

Um **Trabalho** é o pedido real aos Serviços de Mídia para aplicar o **Transform** a um determinado conteúdo de entrada de vídeo ou áudio. Quando a Transformação for criada, você poderá enviar trabalhos usando as APIs dos Serviços de Mídia ou um dos SDKs publicados. O **Trabalho** especifica informações como a localização do vídeo de entrada e a localização da saída. Você pode especificar a localização do seu vídeo de entrada usando: URLs HTTPS, URLs SAS ou [Ativos](https://docs.microsoft.com/rest/api/media/assets).  

### <a name="job-input-from-https"></a>Entrada de emprego de HTTPS

Use [a entrada de trabalho do HTTPS](job-input-from-http-how-to.md) se o seu conteúdo já estiver acessível através de uma URL e você não precisar armazenar o arquivo de origem no Azure (por exemplo, importar do S3). Este método também é adequado se você tiver o conteúdo no armazenamento Azure Blob, mas não tiver necessidade de o arquivo estar em um Ativo. Atualmente, este método suporta apenas um único arquivo para entrada.

### <a name="asset-as-job-input"></a>Ativo como entrada de emprego

Use [o Asset como entrada de trabalho](job-input-from-local-file-how-to.md) se o conteúdo de entrada já estiver em um Ativo ou o conteúdo for armazenado em arquivo local. Também é uma boa opção se você planeja publicar o ativo de entrada para streaming ou download (digamos que você quer publicar o mp4 para download, mas também quer fazer fala para texto ou detecção facial). Este método suporta ativos de vários arquivos (por exemplo, conjuntos de streaming MBR que foram codificados localmente).

### <a name="checking-job-progress"></a>Verificando o progresso do trabalho

O progresso e o estado de trabalhos podem ser obtidos pelo monitoramento de eventos com a Grade de Eventos do Azure. Para obter mais informações, consulte [Monitorar eventos usando EventGrid](job-state-events-cli-how-to.md).

### <a name="updating-jobs"></a>Atualização de empregos

A operação Atualização na entidade [do Trabalho](https://docs.microsoft.com/rest/api/media/jobs) pode ser usada para modificar a *descrição* e as propriedades *prioritárias* após o envio do trabalho. Uma alteração na propriedade *priority* só será eficaz se o trabalho ainda estiver na fila. Se o trabalho tiver iniciado o processamento ou tiver sido concluído, a alteração da prioridade não terá qualquer efeito.

### <a name="job-object-diagram"></a>Diagrama de objeto de trabalho

O diagrama a seguir mostra o objeto **Jó** e os objetos que ele faz referência, incluindo as relações de derivação.

Clique na imagem para exibi-la em tamanho normal.  

<a href="./media/api-diagrams/job-large.png" target="_blank"><img src="./media/api-diagrams/job-small.png"></a>

## <a name="configure-media-reserved-units"></a>Configurar unidades reservadas de mídia

Para os trabalhos de análise de áudio e análise de vídeo que são acionados pelo Media Services v3 ou Video Indexer, é altamente recomendável provisionar sua conta com 10 Unidades Reservadas de Mídia S3 (MRUs). Se você precisar de mais de 10 MRUs do S3, abra um ticket de suporte usando o [Portal do Microsoft Azure](https://portal.azure.com/).

Para obter detalhes, confira [Dimensionar o processamento de mídia com a CLI](media-reserved-units-cli-how-to.md).

## <a name="ask-questions-give-feedback-get-updates"></a>Fazer perguntas, comentar, obter atualizações

Confira o artigo [comunidade dos Serviços de Mídia do Azure](media-services-community.md) para ver diferentes maneiras de fazer perguntas, comentários e obter atualizações sobre os serviços de mídia.

## <a name="see-also"></a>Confira também

* [Códigos de erro](https://docs.microsoft.com/rest/api/media/jobs/get#joberrorcode)
* [Filtragem, classificação, paginação de entidades dos Serviços de Mídia](entities-overview.md)

## <a name="next-steps"></a>Próximas etapas

- Antes de começar a desenvolver, [revise o desenvolvimento com apis v3 do Media Services](media-services-apis-overview.md) (inclui informações sobre acesso a APIs, convenções de nomeação, etc.)
- Confira estes tutoriais:

    - [Tutorial: Encodifique um arquivo remoto baseado em URL e transmita o vídeo](stream-files-tutorial-with-rest.md)
    - [Tutorial: Enviar, codificar e transmitir vídeos](stream-files-tutorial-with-api.md)
    - [Tutorial: Analise vídeos com media services v3](analyze-videos-tutorial-with-api.md)
