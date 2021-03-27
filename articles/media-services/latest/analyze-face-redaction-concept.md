---
title: Redação faces na API dos serviços de mídia do Azure v3 | Microsoft Docs
description: Os serviços de mídia do Azure v3 fornecem uma predefinição de detecção e redação facial que permite que você envie um arquivo de vídeo, detecte faces e aplique desfoque a eles em uma única passagem combinada ou por meio de uma operação de duas etapas, permitindo a edição. Este artigo demonstra como redação faces com a predefinição de detector facial na API v3.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 03/25/2021
ms.author: johndeu
ms.custom: devx-track-csharp
ms.openlocfilehash: 6db93aa369366936c90446c41406eafe9ee6e414
ms.sourcegitcommit: a9ce1da049c019c86063acf442bb13f5a0dde213
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2021
ms.locfileid: "105630326"
---
# <a name="redact-faces-with-the-face-detector-preset"></a>Redação faces com a predefinição de detector facial

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

A API dos serviços de mídia do Azure v3 inclui uma predefinição de detector facial que oferece detecção e redação de face escalonável (Desfoque) na nuvem. A edição facial permite que você modifique seu vídeo para desfocar rostos de pessoas selecionadas. Você pode querer usar o serviço de edição facial em cenários de segurança pública e de notícias veiculadas. Alguns minutos de seqüência de imagens que contém vários rostos podem levar horas para serem editados manualmente, mas com essa predefinição o processo de edição facial exigirá apenas algumas etapas simples.

Este artigo fornece detalhes sobre a **predefinição de detector facial** e mostra como usá-lo com o SDK dos serviços de mídia do Azure para .net.

## <a name="compliance-privacy-and-security"></a>Conformidade, privacidade e segurança
 
Como lembrete importante, você deve obedecer a todas as leis aplicáveis em seu uso de análises nos serviços de mídia do Azure. Você não deve usar os serviços de mídia do Azure ou qualquer outro serviço do Azure de uma maneira que viole os direitos de outras pessoas. Antes de carregar qualquer vídeo, incluindo dados biométricos, para o serviço dos serviços de mídia do Azure para processamento e armazenamento, você deve ter todos os direitos apropriados, incluindo todos os condados apropriados, dos indivíduos no vídeo. Para saber mais sobre conformidade, privacidade e segurança nos serviços de mídia do Azure, os [termos de serviços cognitivas](https://azure.microsoft.com/support/legal/cognitive-services-compliance-and-privacy/)do Azure. Para as obrigações de privacidade e manipulação de seus dados da Microsoft, examine a [política de privacidade](https://privacy.microsoft.com/PrivacyStatement)da Microsoft, os termos de [serviços online](https://www.microsoft.com/licensing/product-licensing/products) (OST) e o [adendo de processamento de dados](https://www.microsoftvolumelicensing.com/DocumentSearch.aspx?Mode=3&DocumentTypeId=67) ("DPA"). Mais informações de privacidade, incluindo a retenção de dados, exclusão/destruição, estão disponíveis no OST e [aqui](../video-indexer/faq.md). Usando os serviços de mídia do Azure, você concorda em estar associado aos termos de serviços cognitivas, do OST, do DPA e da política de privacidade


## <a name="face-redaction-modes"></a>Modos de edição facial

A edição facial trabalha detectando rostos em cada quadro de vídeo e controlando o objeto de rosto para frente e para trás no tempo, para que a mesma pessoa possa ser desfocada também de outros ângulos. O processo de redação automatizado é complexo e nem sempre desfoca a cada face 100% garantida. Por esse motivo, a predefinição pode ser usada como um modo de duas passagens para melhorar a qualidade e a precisão do Desfoque por meio de um estágio de edição antes de enviar o arquivo para a passagem de desfoque final. 

Além de um modo **combinado** totalmente automático, o fluxo de trabalho de duas passagens permite que você escolha as faces que deseja desfocar (ou não desfocar) por meio de uma lista de IDs de face. Para fazer ajustes arbitrários por quadro, a predefinição usa um arquivo de metadados no formato JSON como entrada para a segunda passagem. Esse fluxo de trabalho é dividido nos modos **Analisar** e **Editar**.

Você também pode simplesmente combinar os dois modos em uma única passagem que executa ambas as tarefas em um trabalho; Esse modo é chamado **combinado**.  Neste artigo, o código de exemplo mostrará como usar o modo **combinado** de passagem única simplificado em um arquivo de origem de exemplo.

### <a name="combined-mode"></a>Modo Combinado

Isso produz um arquivo de vídeo MP4 redigido em uma única passagem sem a necessidade de qualquer edição manual do arquivo JSON. A saída na pasta de ativos para o trabalho será um único arquivo. MP4 que contém faces borradas usando o efeito de desfoque selecionado. Use a propriedade de resolução definida como **SourceResolution** para obter os melhores resultados para a edição.

| Estágio | Nome do Arquivo | Observações |
| --- | --- | --- |
| Ativo de entrada |"ignite-sample.mp4" |Vídeo em formato WMV, MOV ou MP4 |
| Configuração predefinida |Configuração do detector facial | **modo**: FaceRedactorMode. Combined,  **borratype**: borratype. med, **Resolution**: AnalysisResolution. SourceResolution |
| Ativo de saída |"ignite-redacted.mp4 |Vídeo com efeito de desfoque aplicado às faces |

### <a name="analyze-mode"></a>Modo Analisar

A passagem de **análise** do fluxo de trabalho de duas passagens usa uma entrada de vídeo e produz um arquivo JSON com uma lista dos locais de face, das imagens de identificação facial e do jpg de cada face detectada. 

| Estágio | Nome do Arquivo | Observações |
| --- | --- | --- |
| Ativo de entrada |"ignite-sample.mp4" |Vídeo em formato WMV, MPV ou MP4 |
| Configuração predefinida |Configuração do detector facial |**modo**: FaceRedactorMode. Analyze, **Resolution**: AnalysisResolution. SourceResolution|
| Ativo de saída |ignite-sample_annotations.jsem |Dados de anotação da localização dos rostos no formato JSON. Isso pode ser editado pelo usuário para modificar as caixas delimitadoras de desfoque. Confira o exemplo abaixo. |
| Ativo de saída |foo_thumb%06d.jpg [foo_thumb000001.jpg, foo_thumb000002.jpg] |Um jpg cortado de cada rosto detectado, em que o número indica o labelId do rosto |

#### <a name="output-example"></a>Exemplo de saída

```json
{
  "version": 1,
  "timescale": 24000,
  "offset": 0,
  "framerate": 23.976,
  "width": 1280,
  "height": 720,
  "fragments": [
    {
      "start": 0,
      "duration": 48048,
      "interval": 1001,
      "events": [
        [],
        [],
        [],
        [],
        [],
        [],
        [],
        [],
        [],
        [],
        [],
        [],
        [],
        [
          {
            "index": 13,
            "id": 1138,
            "x": 0.29537,
            "y": -0.18987,
            "width": 0.36239,
            "height": 0.80335
          },
          {
            "index": 13,
            "id": 2028,
            "x": 0.60427,
            "y": 0.16098,
            "width": 0.26958,
            "height": 0.57943
          }
        ],

    ... truncated
```

### <a name="redact-blur-mode"></a>Modo de edição (Desfoque)

A segunda etapa do fluxo de trabalho tem um grande número de entradas que precisam ser combinadas em um único ativo.

Isso inclui uma lista de IDs a serem desfocados, o vídeo original e o JSON de anotações. Esse modo usa as anotações para aplicar desfoque ao vídeo de entrada.

O resultado da etapa Analisar não inclui o vídeo original. O vídeo precisa ser carregado no ativo de entrada para a tarefa do modo Editar e selecionado como o arquivo primário.

| Estágio | Nome do Arquivo | Observações |
| --- | --- | --- |
| Ativo de entrada |"ignite-sample.mp4" |Vídeo em formato WMV, MPV ou MP4. O mesmo vídeo da etapa 1. |
| Ativo de entrada |"ignite-sample_annotations.js" |arquivo de metadados de anotações da fase 1, com modificações opcionais, se você quiser alterar as faces desfocadas. Isso deve ser editado em um aplicativo externo, código ou editor de texto. |
| Ativo de entrada | "ignite-sample_IDList.txt" (opcional) | Nova lista opcional separada por linhas de IDs de rostos para editar. Se for deixado em branco, todas as faces na fonte terão o desfoque aplicado. Você pode usar a lista para escolher seletivamente não desfocar rostos específicas. |
| Predefinição do detector facial  |Configuração predefinida  | **modo**: FaceRedactorMode. redação, **borratype**: borratype. med |
| Ativo de saída |"ignite-sample-redacted.mp4" |Vídeo com desfoque aplicado com base nas anotações |

#### <a name="example-output"></a>Saída de exemplo

É a saída de uma IDList com uma ID selecionada.

foo_IDList.txt de exemplo

```
1
2
3
```

## <a name="blur-types"></a>Tipos de desfoque

No modo **combinado** ou **redação** , há cinco modos de desfoque diferentes que podem ser escolhidos por meio da configuração de entrada JSON: **baixo**, **médio**, **alto**, **caixa** e **preto**. Por padrão, **Med** é usado.

Encontre exemplos dos tipos de desfoque abaixo.

### <a name="example-settings-for-face-detector-preset"></a>Configurações de exemplo para a predefinição de detector facial
[!code-csharp[Main](../../../media-services-v3-dotnet/VideoAnalytics/FaceRedactor/Program.cs#FaceDetectorPreset)]


#### <a name="low"></a>Baixo

![Exemplo de configuração de desfoque de baixa resolução.](./media/media-services-face-redaction/blur-1.png)

#### <a name="med"></a>Med

![Exemplo de configuração de desfoque de resolução média.](./media/media-services-face-redaction/blur-2.png)

#### <a name="high"></a>Alto

![Exemplo de configuração de desfoque de alta resolução.](./media/media-services-face-redaction/blur-3.png)

#### <a name="box"></a>Box

![Modo de caixa para uso na depuração da saída.](./media/media-services-face-redaction/blur-4.png)

#### <a name="black"></a>Preto

![O modo de caixa preta cobre todas as faces com caixas pretas.](./media/media-services-face-redaction/blur-5.png)

## <a name="elements-of-the-output-json-file"></a>Elementos do arquivo JSON de saída

O MP de edição fornece detecção facial e acompanhamento de alta precisão local de até 64 rostos humanos em um quadro de vídeo. Faces frontais fornecem os melhores resultados, enquanto as faces laterais e faces pequenas (menores ou iguais a 24x24 pixels) têm resultados menos precisos.

[!INCLUDE [media-services-analytics-output-json](../../../includes/media-services-analytics-output-json.md)]

## <a name="net-sample-code"></a>Código de exemplo do .NET

O programa a seguir mostra como usar o modo de redação de passagem única **combinado** :

- Criar um ativo e carregar um arquivo de mídia nesse ativo.
- Configure a predefinição de detector facial que usa as configurações Mode e blurtype.
- Criar uma nova transformação usando a predefinição do detector facial
- Baixe o arquivo de vídeo de saída redigido.

## <a name="download-and-configure-the-sample"></a>Baixar e configurar o exemplo

Clone um repositório do GitHub que contém o exemplo de .NET em sua máquina usando o comando a seguir:  

 ```bash
 git clone https://github.com/Azure-Samples/media-services-v3-dotnet.git
 ```

O exemplo está localizado na pasta [FaceRedactor](https://github.com/Azure-Samples/media-services-v3-dotnet/tree/main/VideoAnalytics/FaceRedactor) . Abra [appsettings.json](https://github.com/Azure-Samples/media-services-v3-dotnet/blob/main/VideoAnalytics/FaceRedactor/appsettings.json) no projeto que você baixou. Substitua os valores pelas credenciais que você obteve de [acesso às APIs](./access-api-howto.md).

**Opcionalmente** , você pode copiar o arquivo **[Sample. env](https://github.com/Azure-Samples/media-services-v3-dotnet/blob/main/sample.env)** na raiz do repositório e preencher os detalhes ali e renomeá-lo para **. env** (Observe o ponto na frente!) para que ele possa ser usado em todos os projetos de exemplo no repositório.  Isso elimina a necessidade de ter uma appsettings.jspreenchida no arquivo em cada amostra e também o protege de verificar as configurações em seus próprios repositórios clonados do Hub git.

#### <a name="examples"></a>Exemplos

Este código mostra como configurar o **FaceDetectorPreset** para um desfoque de modo **combinado** .

[!code-csharp[Main](../../../media-services-v3-dotnet/VideoAnalytics/FaceRedactor/Program.cs#FaceDetectorPreset)]

Este exemplo de código mostra como a predefinição é passada para um objeto de transformação durante a criação. Depois de criar a transformação, os trabalhos podem ser enviados diretamente a ele.

[!code-csharp[Main](../../../media-services-v3-dotnet/VideoAnalytics/FaceRedactor/Program.cs#FaceDetectorPresetTransform)]

## <a name="next-steps"></a>Próximas etapas

[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Fornecer comentários

[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

