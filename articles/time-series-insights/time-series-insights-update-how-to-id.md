---
title: Melhores práticas para escolher um ID da Série Do Tempo - Azure Time Series Insights | Microsoft Docs
description: Conheça as melhores práticas ao escolher um ID da série de tempo no Azure Time Series Insights Preview.
author: deepakpalled
ms.author: dpalled
manager: cshankar
ms.workload: big-data
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 02/07/2020
ms.custom: seodec18
ms.openlocfilehash: a62c2460698408f6a2bfa51c6638bdeaf88bb31f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "77083536"
---
# <a name="best-practices-for-choosing-a-time-series-id"></a>Práticas recomendadas para escolher uma ID do Time Series

Este artigo resume a importância do ID da série do tempo para o ambiente de visualização de insights da série do tempo do Azure e as melhores práticas para escolher um.

## <a name="choose-a-time-series-id"></a>Escolha uma ID do Time Series

A seleção de um ID de série temporal apropriado é fundamental. Escolher uma ID do Time Series é como escolher uma chave de partição para um banco de dados. É necessário quando você cria um ambiente de visualização de insights de séries tempo. 

> [!IMPORTANT]
> As IDs da série temporal são:
> * Uma propriedade *sensível a maiúsculas* e minúsculas: os invólucros de letras e caracteres são usados em pesquisas, comparações, atualizações e quando particionam.
> * Uma propriedade *imutável:* uma vez criada, ela não pode ser alterada.

> [!TIP]
> Se a sua fonte de evento for um hub de IoT, seu ID da série do tempo provavelmente será ***iothub-conexão-dispositivo-id***.

As principais práticas recomendadas a seguir incluem:

* Escolha uma chave de partição com muitos valores distintos (por exemplo, centenas ou milhares). Em muitos casos, este pode ser o ID do dispositivo, o ID do sensor ou o ID da tag no seu JSON.
* A ID do Time Series deve ser exclusiva no nível do nó folha do seu [Modelo do Time Series](./time-series-insights-update-tsm.md).
* O limite de caracteres para a seqüência de nomes de propriedade da Série Tempo é 128. Para o valor de propriedade da Série Tempo ID, o limite de caracteres é de 1.024.
* Se um valor de propriedade único para o ID da série temporal estiver faltando, ele é tratado como um valor nulo e segue a mesma regra da restrição de exclusividade.
* Você também pode selecionar até *três* propriedades-chave como seu ID da série do tempo. Sua combinação será uma chave composta que representa o ID da série temporal.  
  > [!NOTE]
  > Suas três propriedades principais devem ser cordas.
  > Você teria que consultar esta chave composta em vez de uma propriedade de cada vez.

## <a name="select-more-than-one-key-property"></a>Selecione mais de uma propriedade-chave

Os cenários a seguir descrevem a seleção de mais de uma propriedade-chave como seu ID da série temporal.  

### <a name="example-1-time-series-id-with-a-unique-key"></a>Exemplo 1: ID da série do tempo com uma chave única

* Você tem frotas herdadas de ativos. Cada um tem uma chave única.
* Uma frota é identificada exclusivamente pelo dispositivo de **propriedadeId**. Para outra frota, a propriedade única é **objectId**. Nenhuma das duas frotas contém a propriedade única da outra frota. Neste exemplo, você selecionaria duas teclas, **deviceId** e **objectId,** como chaves exclusivas.
* Aceitamos valores nulos, e a falta de presença de um imóvel no caso de carga conta como um valor nulo. Esta também é a maneira apropriada de lidar com o envio de dados para duas fontes de eventos onde os dados em cada fonte de evento tem uma Identificação de Série temporal única.

### <a name="example-2-time-series-id-with-a-composite-key"></a>Exemplo 2: ID da série de tempo com uma chave composta

* Você precisa que várias propriedades sejam exclusivas dentro do mesma frota de ativos. 
* Você é um fabricante de edifícios inteligentes e implanta sensores em todos os cômodos. Em cada sala, você normalmente tem os mesmos valores para **sensorId**. Exemplos são **sensor1,** **sensor2**e **sensor3**.
* Seu prédio tem números de pisos e quartos sobrepostos em todos os locais da **propriedade.** Esses números têm valores como **1a**, **2b**e **3a**.
* Você tem uma propriedade, **localização,** que contém valores como **Redmond,** **Barcelona**e **Tóquio.** Para criar exclusividade, você designa as seguintes três propriedades como suas teclas de identificação da série do tempo: **sensorId,** **flrRm**e **localização**.

Exemplo de evento bruto:

```JSON
{
  "sensorId": "sensor1",
  "flrRm": "1a",
  "location": "Redmond",
  "temperature": 78
}
```

No portal Azure, você pode inserir a chave composta da seguinte forma: 

```JSON
[{"name":"sensorId","type":"String"},{"name":"flrRm","type":"String"},{"name":"location","type":"string"}]
```

## <a name="next-steps"></a>Próximas etapas

* Leia mais sobre [modelagem de dados](./time-series-insights-update-tsm.md).

* Planeje [o ambiente de visualização de insights da série do tempo do Azure](./time-series-insights-update-plan.md).