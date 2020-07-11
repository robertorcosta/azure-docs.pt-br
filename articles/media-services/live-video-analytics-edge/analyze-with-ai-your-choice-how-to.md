---
title: Analise vídeos ao vivo com o ia de sua escolha – Azure
description: Neste artigo, você aprenderá a criar um módulo IoT Edge que pode ser integrado à análise de vídeo ao vivo no IoT Edge para analisar o vídeo ao vivo usando um modelo de visão computacional de sua escolha.
ms.topic: how-to
ms.date: 04/27/2020
ms.openlocfilehash: 6a1ea3ebd8c7de4c691d7a982dbc08e9d08d9e38
ms.sourcegitcommit: ec682dcc0a67eabe4bfe242fce4a7019f0a8c405
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/09/2020
ms.locfileid: "86182858"
---
# <a name="analyze-live-video-with-ai-of-your-choice"></a>Analisar vídeo ao vivo com a IA de sua preferência

Neste artigo, você aprenderá a criar um módulo IoT Edge que pode ser integrado à análise de vídeo ao vivo no IoT Edge para analisar o vídeo ao vivo usando um modelo de visão computacional de sua escolha. 

## <a name="pre-reading"></a>Lendo previamente

[Conceito de grafo de mídia](media-graph-concept.md)

## <a name="media-graph-extension"></a>Extensão do grafo de mídia

A análise de vídeo ao vivo em IoT Edge define um modelo de extensibilidade que permite estender os recursos de processamento do grafo de mídia para seus próprios componentes de análise de mídia por meio de uma extensão de grafo. Seu componente de análise pode fazer uso de técnicas de processamento de imagem tradicionais ou modelos de ia da visão do computador. As extensões de grafo são habilitadas incluindo o nó do [processador de extensão http](media-graph-concept.md#http-extension-processor) em um grafo de mídia. O processador de extensão HTTP pode retransmitir quadros de vídeo para um ponto de extremidade HTTP especificado por você e atua como a interface para o componente por meio dele. A conexão pode ser feita com um ponto de extremidade local ou remoto e pode ser protegida por autenticação e criptografia TLS, se necessário. Além disso, o processador permite o dimensionamento e a codificação opcionais dos quadros de vídeo antes que eles sejam retransmitidos para o futuro.

Você pode optar por executar o modelo de inferência de sua escolha em qualquer tempo de execução de inferência disponível, como ONNX, TensorFlow, PyTorch ou outros em seu próprio contêiner do Docker. Você também pode usar o idioma de programação e as bibliotecas de sua escolha para expor os recursos de inferência de imagem por meio de um servidor HTTP em seu próprio contêiner. O contêiner inferência deve ser implantado junto com o módulo de borda de análise de vídeo ao vivo para obter o melhor desempenho e, em seguida, será invocado por meio do processador de extensão HTTP incluído na topologia do grafo.
Além disso, a frequência das chamadas em seu módulo personalizado pode ser limitada por meio da adição opcional de um [processador de detector de movimento](media-graph-concept.md#motion-detection-processor) e um upstream do processador de filtro de taxa de [quadros](media-graph-concept.md#frame-rate-filter-processor) para o processador de extensão http.

O diagrama a seguir descreve o fluxo de dados de alto nível:

![Dispositivo de borda](./media/analyze-live-video-with-ai-your-choice-how-to/edge-device.png)

Isso permite que você analise vídeos usando modelos de ia de sua escolha para atender às suas necessidades de negócios exclusivas. Os modelos de ia podem ser da comunidade de código-fonte aberto ou de seus próprios cientistas de dados ou de um provedor de ia especializado.

## <a name="media-graph-http-extension-contract-definitions"></a>Definições de contrato de extensão HTTP do grafo de mídia

Esta seção define o contrato HTTP que define o fluxo de dados.

### <a name="http-extensibility-protocol"></a>Protocolo de extensibilidade HTTP  

O contrato HTTP é definido da seguinte maneira:

* Seu módulo atua como o servidor HTTP.
* A análise de vídeo ao vivo no módulo IoT Edge atua como o cliente HTTP.

### <a name="request"></a>Solicitação

As solicitações do módulo análise de vídeo ao vivo para seu módulo seriam as seguintes:

| Chave | Valor |
|---|---|
|POST| `https://hostname/optional-path?optional-query`|
|Aceitar|aplicativo/JSON,*/*|
|Autorização| Básico, Digest, portador (por meio do suporte a cabeçalho personalizado)|
|Tipo de conteúdo|image/jpeg<br/>image/png<br/>image/bmp<br/>imagem/x-Raw|
|Content-Length|Tamanho do corpo, em bytes|
|User-Agent|Serviços de Mídia do Azure|
|Corpo|Bytes de imagem, codificados em binários em um dos tipos de conteúdo com suporte.|

#### <a name="example"></a>Exemplo

```
POST http://localhost:8080/inference HTTP/1.1
Host: localhost:8080
x-ms-client-request-id: d6050cd4-c9f2-42d3-9adc-53ba7e440f17
Content-Type: image/bmp
Content-Length: 519222

(Image Binary Content)
```

### <a name="response"></a>Resposta

As respostas de seu módulo para o módulo análise de vídeo ao vivo devem ser as seguintes

| Chave | Valor |
|---|---|
|Códigos de status|200 OK-resultados da inferência encontrados<br/>204 nenhum conteúdo-nenhum conteúdo encontrado pela ia<br/>400 solicitação inválida-não esperado<br/>500 erro interno do servidor-não esperado<br/>503 servidor ocupado-o AMS será retirado com base no cabeçalho "tentar novamente após" ou com base em um período de tempo padrão no cabeçalho de caso não predefinido.|
|Tipo de conteúdo|aplicativo/json|
|Content-Length|    Tamanho do corpo, em bytes|
|Corpo|Objeto JSON com uma única propriedade "inferências".|

#### <a name="example"></a>Exemplo

```
HTTP/1.1 200 OK
Content-Type: application/json
Content-Length: 468
Server: Microsoft-HTTPAPI/2.0
Date: Fri, 17 Apr 2020 04:44:01 GMT

{
  "inferences": [
    {
      "type": "entity",
      "entity": {
        "tag": { "value": "car", "confidence": 0.9048132 },
        "box": { "l": 0.42681578, "t": 0.47660735, "w": 0.019501392, "h": 0.020954132 }
      }
    },
    {
      "type": "entity",
      "entity": {
        "tag": { "value": "car", "confidence": 0.8953932 },
        "box": { "l": 0.55083525, "t": 0.4843858, "w": 0.046550274, "h": 0.046502113 }
      }
    }    
  ]
}
```

É altamente recomendável que as respostas sejam retornadas usando documentos JSON válidos seguindo o esquema preestabelecido definido abaixo. Isso garantirá melhor a interoperabilidade com outros componentes e possíveis recursos futuros adicionados ao módulo análise de vídeo ao vivo.

Se o módulo retornar uma resposta em que o tipo de conteúdo não é "Application/JSON", a análise de vídeo ao vivo codificará a mensagem como um conteúdo de base 64 e a serializará como uma carga JSON opaca.

Se o módulo retornar uma resposta com o tipo de conteúdo como "Application/JSON", mas o esquema JSON não seguir o [esquema de metadados de inferência descrito abaixo](#inference-metadata-schema), a carga da mensagem será encaminhada por meio do pipeline, mas a interoperabilidade será reduzida.

> [!NOTE]
> Se o módulo não produzir nenhum resultado, ele deverá retornar o código de status HTTP 204 (sem conteúdo) com um corpo de resposta vazio. A análise de vídeo ao vivo entenderá isso como um resultado vazio e não encaminhará o evento em todo o pipeline.

### <a name="inference-metadata-schema"></a>Esquema de metadados de inferência

Cada objeto de inferência segue este esquema de superconjunto:

```
{
  "type": "[classification|motion|entity|text|other]",
  "subtype": "",              // Free form subtype id

  // Object has *at most one of* the following
  "classification":
  {
    "tag": 
    {
      "value": "",            // Tag value
      "confidence": 0.0       // Tag confidence
    },
    "attributes":[            // Optional attributes
      {
        "name": "",           
        "value": "",          
        "confidence": 0.0     
      }
    ]
  },

  "motion":
  {
    "box": { "l": 0.0, "t": 0.0, "w": 0.0, "h": 0.0 }
    // "regionId": ""
  },

  "entity":
  {
    "tag": 
    {
      "value": "",            // Tag value
      "confidence": 0.0       // Tag confidence
    },
    "attributes":[            // Optional attributes
      {
        "name": "",           
        "value": "",          
        "confidence": 0.0     
      }
    ],
    "box": { "l": 0.0, "t": 0.0, "w": 0.0, "h": 0.0 }
  },

  "text":
  {
    "value": "",              // Text value
    "language": "",           // Optional BCP47 Language Code (https://tools.ietf.org/html/bcp47)
    "startTimestamp": 0,      // Optional start timestamp
    "endTimestamp": 0         // Optional end timestamp
  },

  // Plus every object can have zero or more extensions
  "extensions":
  {
    "name1": "value1",
    "name2": "value2"
    // ...
  }
}
```
 
### <a name="data-contracts---class-hierarchy"></a>Contratos de dados – hierarquia de classe

![Contratos de dados – hierarquia de classe](./media/analyze-live-video-with-ai-your-choice-how-to/data-contracts.png)

### <a name="examples"></a>Exemplos  

O exemplo a seguir contém um único evento com todos os tipos de inferência com suporte:

```
{
  "inferences": [
    // Light detection
    {
      "type": "classification",
      "subtype": "lightDetection",
      "classification": {
        "tag": { "value": "daylight", "confidence": 0.86 },
        "attributes": [
            { "name": "isBlackAndWhite", "value": "false", "confidence": 0.71 }
        ]
      }
    },

    // Motion detection
    {
      "type": "motion",
      "subtype": "motionDetection",
      "motion":
      {
        "box": { "l": 0.0, "t": 0.0, "w": 0.0, "h": 0.0 }
      }
    },

    // Object detection
    {
      "type": "entity",
      "subtype": "objectDetection",    
      "entity":
      {
        "tag": { "value": "dog", "confidence": 0.97 },
        "box": { "l": 0.0, "t": 0.0, "w": 0.0, "h": 0.0 }
      }
    },

    // Vehicle identification
    {
      "type": "entity",
      "subtype": "vehicleIdentification",    
      "entity":
      {
        "tag": { "value": "007-SPY", "confidence": 0.82 },
        "attributes":[  
          { "name": "color", "value": "black", "confidence": 0.90 },
          { "name": "body", "value": "coupe", "confidence": 0.87 },
          { "name": "make", "value": "Aston Martin", "confidence": 0.35 },
          { "name": "model", "value": "DBS V12", "confidence": 0.33 }
        ],
        "box": { "l": 0.0, "t": 0.0, "w": 0.0, "h": 0.0 }
      }
    },

    // People identification
    {
      "type": "entity",
      "subtype": "peopleIdentification",    
      "entity":
      {
        "tag": { "value":"Erwin Schrödinger", "confidence": 0.50 },
        "attributes":[  
          { "name": "age", "value": "73", "confidence": 0.87 },
          { "name": "glasses", "value": "yes", "confidence": 0.94 }
        ],
        "box": { "l": 0.0, "t": 0.0, "w": 0.0, "h": 0.0 }
      },
    }
  ]
}
```
 
## <a name="sample-http-extension-modules"></a>Módulos de extensão HTTP de exemplo

Alguns módulos de extensão HTTP de exemplo podem ser encontrados no [repositório GitHub de análise de vídeo ao vivo](https://github.com/Azure/live-video-analytics/tree/master/utilities/video-analysis). Um desses [exemplos de análise de vídeo](https://github.com/Azure/live-video-analytics/tree/master/utilities/video-analysis/yolov3-onnx) mostra como usar o modelo [YOLOv3](https://pjreddie.com/darknet/yolo/) [ONNX](http://onnx.ai/) para criar um módulo IOT Edge para detecção de objeto. Outro [exemplo de análise de vídeo](https://github.com/Azure/live-video-analytics/tree/master/utilities/video-analysis/yolov3-onnx-tiny) mostra como usar o Tiny YOLOv3, uma versão leve do modelo YOLOv3 ONNX. Você pode usar a mesma abordagem para criar um módulo por conta própria com um modelo de ia de sua escolha.

## <a name="next-steps"></a>Próximas etapas

[Solucionar problemas](troubleshoot-how-to.md)
