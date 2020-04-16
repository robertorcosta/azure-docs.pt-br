---
title: Crie gatilhos baseados em eventos na fábrica de dados do Azure
description: Saiba como criar um gatilho no Azure Data Factory que executa um pipeline em resposta a um evento.
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
author: djpmsft
ms.author: daperlov
manager: jroth
ms.reviewer: maghan
ms.topic: conceptual
ms.date: 10/18/2018
ms.openlocfilehash: d697fb8afe3e92dfe54eb5d89a2ef59425cb0cde
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/16/2020
ms.locfileid: "81414923"
---
# <a name="create-a-trigger-that-runs-a-pipeline-in-response-to-an-event"></a>Criar um gatilho que executa um pipeline em resposta a um evento
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Este artigo descreve os gatilhos baseados em evento que você pode criar nos pipelines do Data Factory.

A EDA (arquitetura controlada por evento) é um padrão de integração de dados comum que envolve produção, detecção, consumo e reação a eventos. Os cenários de integração de dados geralmente exigem que os clientes da Data Factory acionem os pipelines com base em eventos como a chegada ou exclusão de um arquivo em sua conta do Azure Storage. O Data Factory agora está integrado ao [Azure Event Grid](https://azure.microsoft.com/services/event-grid/), que permite acionar pipelines em um evento.

Para uma introdução de dez minutos e demonstração desse recurso, assista ao vídeo a seguir:

> [!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/Event-based-data-integration-with-Azure-Data-Factory/player]


> [!NOTE]
> A integração descrita neste artigo depende na [Grade de Eventos do Azure](https://azure.microsoft.com/services/event-grid/). Verifique se a assinatura está registrada no provedor de recursos da Grade de Eventos. Para obter mais informações, confira [Provedores e tipos de recursos](../azure-resource-manager/management/resource-providers-and-types.md#azure-portal).

## <a name="data-factory-ui"></a>IU do Data Factory

Esta seção mostra como criar um gatilho de evento dentro da Interface de Usuário da Fábrica de Dados do Azure.

1. Ir para a **Tela de Autoria**

1. No canto inferior esquerdo, clique no botão **Gatilhos**

1. Clique **em + Novo** que abrirá o nav lado do gatilho de criação

1. Selecione **o** tipo de gatilho Evento

    ![Criar novo gatilho de evento](media/how-to-create-event-trigger/event-based-trigger-image1.png)

1. Selecione sua conta de armazenamento na parada de assinatura do Azure ou usando manualmente seu ID de recurso de conta de armazenamento. Escolha em qual contêiner deseja que os eventos ocorram. A seleção de contêineres é opcional, mas esteja ciente de que a seleção de todos os contêineres pode levar a um grande número de eventos.

   > [!NOTE]
   > O Event Trigger atualmente suporta apenas contas de armazenamento azure Data Lake Storage Gen2 e de uso geral da versão 2. Devido a uma limitação da Grade de Eventos do Azure, a Azure Data Factory só suporta um máximo de 500 gatilhos de evento por conta de armazenamento.

1. O **caminho Blob começa com** e o caminho **blob termina com** propriedades que permitem especificar os nomes de contêineres, pastas e blob para os quais você deseja receber eventos. Seu gatilho de evento requer que pelo menos uma dessas propriedades seja definida. É possível usar diversos padrões tanto para o **caminho do Blob que começa com** como para o **caminho do Blob que termina com propriedades**, conforme mostrado nos exemplos mais adiante neste artigo.

    * **O caminho blob começa com:** O caminho blob deve começar com um caminho de pasta. Os valores válidos incluem `2018/` e `2018/april/shoes.csv`. Este campo não pode ser selecionado se um contêiner não for selecionado.
    * **O caminho blob termina com:** O caminho blob deve terminar com um nome de arquivo ou extensão. Os valores válidos incluem `shoes.csv` e `.csv`. O nome do contêiner e da pasta são opcionais, `/blobs/` mas, quando especificados, devem ser separados por um segmento. Por exemplo, um contêiner chamado 'orders' `/orders/blobs/2018/april/shoes.csv`pode ter um valor de . Para especificar uma pasta em qualquer contêiner, omita o caractere '/'principal'. Por exemplo, `april/shoes.csv` acionará um evento `shoes.csv` em qualquer arquivo nomeado na pasta chamado 'abril' em qualquer contêiner. 

1. Selecione se o seu gatilho responderá a um evento **criado pelo Blob,** o evento **excluído do Blob** ou ambos. Em seu local de armazenamento especificado, cada evento acionará os pipelines da Fábrica de Dados associados ao gatilho.

    ![Configurar o gatilho de evento](media/how-to-create-event-trigger/event-based-trigger-image2.png)

1. Selecione se o seu gatilho ignora ou não bolhas com zero bytes.

1. Depois de configurar, clique em **Next: Data preview**. Esta tela mostra as bolhas existentes combinadas com a configuração do gatilho de evento. Certifique-se de ter filtros específicos. A configuração de filtros muito amplos pode corresponder a um grande número de arquivos criados/excluídos e pode afetar significativamente o seu custo. Uma vez verificadas as condições do filtro, clique **em Terminar**.

    ![Visualização de dados do gatilho de eventos](media/how-to-create-event-trigger/event-based-trigger-image3.png)

1. Para anexar um pipeline a este gatilho, vá para a tela do pipeline e clique **em Adicionar gatilho** e selecione **Novo/Editar**. Quando o nav lateral aparecer, clique no **gatilho Escolher...** parada e selecione o gatilho que você criou. Clique **em Next: A visualização de dados** para confirmar se a configuração está correta e, em **seguida, a próxima** para validar a visualização de dados está correta.

1. Se o pipeline tiver parâmetros, você pode especificá-los no gatilho executa nav lado parâmetro. O gatilho do evento captura o caminho da pasta `@triggerBody().folderPath` e `@triggerBody().fileName`o nome do arquivo da bolha nas propriedades e . Para usar os valores dessas propriedades em um pipeline, é necessário mapear as propriedades para parâmetros de pipeline. Depois de mapear as propriedades para parâmetros, acesse os valores capturados pelo gatilho por meio da expressão `@pipeline().parameters.parameterName` em todo o pipeline. Clique **em Terminar** assim que terminar.

    ![Mapeando propriedades para parâmetros de pipeline](media/how-to-create-event-trigger/event-based-trigger-image4.png)

No exemplo anterior, o gatilho é configurado para disparar quando um caminho blob terminando em .csv é criado no teste de evento da pasta nos dados de amostra de contêiner. As propriedades **folderPath** e **fileName** capturam a localização da nova bolha. Por exemplo, quando MoviesDB.csv é adicionado ao path `@triggerBody().folderPath` sample-data/event-testing, tem um valor `sample-data/event-testing` e `@triggerBody().fileName` tem um valor de `moviesDB.csv`. Esses valores são mapeados `sourceFolder` no `sourceFile` exemplo para os parâmetros do gasoduto e que podem ser usados em todo o gasoduto como `@pipeline().parameters.sourceFolder` e `@pipeline().parameters.sourceFile` respectivamente.

## <a name="json-schema"></a>JSON schema

A tabela a seguir fornece uma visão geral dos elementos do esquema relacionados aos gatilhos baseados em eventos:

| **Elemento JSON** | **Descrição** | **Tipo** | **Valores permitidos** | **Necessário** |
| ---------------- | --------------- | -------- | ------------------ | ------------ |
| **scope** | A ID do recurso do Azure Resource Manager da Conta de Armazenamento. | String | ID do Azure Resource Manager | Sim |
| **Eventos** | O tipo de eventos que causam o acionamento desse gatilho. | Array    | Microsoft.Storage.BlobCreated, Microsoft.Storage.BlobDeleted | Sim, qualquer combinação desses valores. |
| **blobPathBeginsWith** | O caminho do blob deve começar com o padrão fornecido para o gatilho ser acionado. Por exemplo, `/records/blobs/december/` só aciona o gatilho para blobs na pasta `december` no contêiner `records`. | String   | | Você precisa fornecer um valor para pelo menos uma dessas propriedades: `blobPathBeginsWith` ou `blobPathEndsWith`. |
| **blobPathEndsWith** | O caminho do blob deve terminar com o padrão fornecido para o gatilho ser acionado. Por exemplo, `december/boxes.csv` só aciona o gatilho de blobs denominado `boxes` em uma pasta `december`. | String   | | Você precisa fornecer um valor para pelo menos uma dessas propriedades: `blobPathBeginsWith` ou `blobPathEndsWith`. |
| **ignorarEmptyBlobs** | Se as bolhas de byte zero ou não desencadearão uma corrida de gasodutos. Por padrão, isso é definido como verdadeiro. | Boolean | true ou false | Não |

## <a name="examples-of-event-based-triggers"></a>Exemplos de gatilhos baseados em eventos

Esta seção fornece exemplos de configurações de gatilho baseado em evento.

> [!IMPORTANT]
> Você precisa incluir o segmento `/blobs/` do caminho, como mostrado nos exemplos a seguir, sempre que especificar contêiner e pasta, contêiner e arquivo ou contêiner, pasta e arquivo. Para **blobPathBeginsWith**, a UI data `/blobs/` factory adicionará automaticamente entre a pasta e o nome do contêiner no gatilho JSON.

| Propriedade | Exemplo | Descrição |
|---|---|---|
| **O caminho de blob começa com** | `/containername/` | Recebe eventos para qualquer blob no contêiner. |
| **O caminho de blob começa com** | `/containername/blobs/foldername/` | Recebe eventos para todos os blobs no contêiner `containername` e na pasta `foldername`. |
| **O caminho de blob começa com** | `/containername/blobs/foldername/subfoldername/` | Você também pode fazer referência a uma subpasta. |
| **O caminho de blob começa com** | `/containername/blobs/foldername/file.txt` | Recebe eventos para um blob denominado `file.txt` na pasta `foldername` no contêiner `containername`. |
| **O caminho de blob termina com** | `file.txt` | Recebe eventos para um blob denominado `file.txt` em qualquer caminho. |
| **O caminho de blob termina com** | `/containername/blobs/file.txt` | Recebe eventos para um blob denominado `file.txt` no contêiner `containername`. |
| **O caminho de blob termina com** | `foldername/file.txt` | Recebe eventos para um blob denominado `file.txt` na pasta `foldername` em qualquer contêiner. |

## <a name="next-steps"></a>Próximas etapas
Para obter mais informações detalhadas sobre gatilhos, consulte [Gatilhos e execução de pipeline](concepts-pipeline-execution-triggers.md#trigger-execution).
