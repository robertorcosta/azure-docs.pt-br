---
title: Schemas de eventos — Azure Event Grid IoT Edge | Microsoft Docs
description: Esquemas de eventos em Event Grid em IoT Edge.
author: VidyaKukke
manager: rajarv
ms.author: vkukke
ms.reviewer: spelluru
ms.date: 10/06/2019
ms.topic: article
ms.service: event-grid
services: event-grid
ms.openlocfilehash: ba261aeedf6574f69d3c05f8fd005c912dcc59d1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "73242472"
---
# <a name="event-schemas"></a>Esquemas de evento

O módulo Event Grid aceita e entrega eventos em formato JSON. Atualmente existem três esquemas que são suportados pela Event Grid: -

* **EventGridSchema**
* **CustomSchema**
* **CloudEventSchema**

Você pode configurar o esquema ao que um editor deve se conformar durante a criação do tópico. Se não especificado, ele é padrão para **EventGridSchema**. Eventos que não estejam de acordo com o esquema esperado serão rejeitados.

Os assinantes também podem configurar o esquema no qual querem que os eventos sejam entregues. Se não especificado, o padrão é o esquema do tópico.
Atualmente, o esquema de entrega de assinantes tem que corresponder ao esquema de entrada do seu tópico. 

## <a name="eventgrid-schema"></a>Esquema EventGrid

O esquema EventGrid consiste em um conjunto de propriedades necessárias que uma entidade editorial deve se conformar. Cada editor tem que preencher os campos de alto nível.

```json
[
  {
    "topic": string,
    "subject": string,
    "id": string,
    "eventType": string,
    "eventTime": string,
    "data":{
      object-unique-to-each-publisher
    },
    "dataVersion": string,
    "metadataVersion": string
  }
]
```

### <a name="eventgrid-schema-properties"></a>Propriedades do esquema eventGrid

Todos os eventos têm os seguintes dados de alto nível:

| Propriedade | Type | Obrigatório | Descrição |
| -------- | ---- | ----------- |-----------
| topic | string | Não | Deve coincidir com o tópico em que é publicado. Event Grid preenche-o com o nome do tópico no qual é publicado se não especificado. |
| subject | string | Sim | Caminho definido pelo fornecedor para o assunto do evento. |
| eventType | string | Sim | Tipo de evento para esta fonte de evento, por exemplo, BlobCreated. |
| eventTime | string | Sim | A hora em que o evento é gerado com base na hora UTC do provedor. |
| ID | string | Não | Identificador exclusivo do evento. |
| data | objeto | Não | Usado para capturar dados de eventos específicos para a entidade editorial. |
| dataVersion | string | Sim | A versão do esquema do objeto de dados. O fornecedor define a versão do esquema. |
| metadataVersion | string | Não | A versão do esquema do metadados de evento. Grade de Eventos define o esquema de propriedades de nível superior. Grade de Eventos fornece esse valor. |

### <a name="example--eventgrid-schema-event"></a>Exemplo — Evento de esquema eventGrid

```json
[
  {
    "id": "1807",
    "eventType": "recordInserted",
    "subject": "myapp/vehicles/motorcycles",
    "eventTime": "2017-08-10T21:03:07+00:00",
    "data": {
      "make": "Ducati",
      "model": "Monster"
    },
    "dataVersion": "1.0"
  }
]
```

## <a name="customevent-schema"></a>Esquema de evento personalizado

No esquema personalizado, não há propriedades obrigatórias que sejam aplicadas como o esquema EventGrid. A entidade editora pode controlar o esquema do evento inteiramente. Ele fornece a máxima flexibilidade e permite cenários onde você tem um sistema baseado em eventos já em vigor e gostaria de reutilizar eventos existentes e/ou não quer ser vinculado a um esquema específico.

### <a name="custom-schema-properties"></a>Propriedades de esquema personalizados

Sem propriedades obrigatórias. Cabe à editora determinar a carga.

### <a name="example--custom-schema-event"></a>Exemplo — Evento de Esquema Personalizado

```json
[
  {
    "eventdata": {
      "make": "Ducati",
      "model": "Monster"
    }
  }
]
```

## <a name="cloudevent-schema"></a>Esquema de CloudEvent

Além dos esquemas acima, event grid suporta nativamente eventos no [esquema CloudEvents JSON](https://github.com/cloudevents/spec/blob/master/json-format.md). CloudEvents é uma especificação aberta para descrever dados de eventos. Simplifica a interoperabilidade, fornecendo um esquema de eventos comuns para publicação e consumo de eventos. Ele faz parte do [CNCF](https://www.cncf.io/) e a versão atualmente disponível é 1.0-rc1.

### <a name="cloudevent-schema-properties"></a>Propriedades do esquema cloudevent

Consulte a [especificação CloudEvents](https://github.com/cloudevents/spec/blob/master/json-format.md#3-envelope) sobre as propriedades obrigatórias do envelope.

### <a name="example--cloud-event"></a>Exemplo — evento em nuvem
```json
[{
       "id": "1807",
       "type": "recordInserted",
       "source": "myapp/vehicles/motorcycles",
       "time": "2017-08-10T21:03:07+00:00",
       "datacontenttype": "application/json",
       "data": {
            "make": "Ducati",
            "model": "Monster"
        },
        "dataVersion": "1.0",
        "specVersion": "1.0-rc1"
}]
```
