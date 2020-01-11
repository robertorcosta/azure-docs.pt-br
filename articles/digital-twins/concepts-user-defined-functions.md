---
title: Processamento de dados e funções definidas pelo usuário – gêmeos digital do Azure | Microsoft Docs
description: Visão geral do processamento de dados, dos correspondentes e das funções definidas pelo usuário com os Gêmeos Digitais do Azure.
ms.author: alinast
author: alinamstanciu
manager: bertvanhoof
ms.service: digital-twins
services: digital-twins
ms.topic: conceptual
ms.date: 01/03/2020
ms.openlocfilehash: 75ed2029582438ede43687addfd54c0a187e0120
ms.sourcegitcommit: 12a26f6682bfd1e264268b5d866547358728cd9a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/10/2020
ms.locfileid: "75861091"
---
# <a name="data-processing-and-user-defined-functions"></a>Processamento de dados e funções definidas pelo usuário

O recurso Gêmeos Digitais do Azure oferece funcionalidades de computação avançadas. Os desenvolvedores podem definir e executar funções personalizadas contra mensagens de telemetria recebidas para enviar eventos para pontos de extremidade predefinidos.

## <a name="data-processing-flow"></a>Fluxo de processamento de dados

Depois que os dispositivos enviam dados de telemetria para o Azure Digital Twins, os desenvolvedores podem processar dados em quatro fases: *validar*, *corresponder*, *computar*, e *despachar*.

[![o fluxo de processamento de dados do Azure digital gêmeos](media/concepts/digital-twins-data-processing-flow.png)](media/concepts/digital-twins-data-processing-flow.png#lightbox)

1. A fase de validação transforma a mensagem de telemetria recebida em um formato de objeto de transferência de dados [comumente entendido](https://docs.microsoft.com/aspnet/web-api/overview/data/using-web-api-with-entity-framework/part-5). Essa fase também executa validação de dispositivo e sensor.
1. A fase corresponder localiza as funções definidas pelo usuário a serem executadas. Os correspondentes predefinidos descobrirão as funções definidas pelo usuário com base em informações de dispositivo, sensor e espaço da mensagem de telemetria de entrada.
1. O computador executa as funções definidas pelo usuário correspondente na fase anterior. Essas funções podem ler e atualizar valores calculados em nós de gráfico espacial e podem emitir notificações personalizadas.
1. A fase de expedição encaminha quaisquer notificações personalizadas da fase de cálculo para os pontos finais definidos no gráfico.

## <a name="data-processing-objects"></a>Objetos de processamento de dados

O processamento de dados no Gêmeos Digitais do Azure consiste na definição de três objetos: *correspondentes*, *funções definidas pelo usuário* e *atribuições de funções*.

[![objetos de processamento de dados do Azure digital gêmeos](media/concepts/digital-twins-user-defined-functions.png)](media/concepts/digital-twins-user-defined-functions.png#lightbox)

### <a name="matchers"></a>Correspondências

Correspondentes definem um conjunto de condições que avaliam quais ações ocorrem com base na telemetria do sensor de entrada. As condições para determinar a correspondência podem incluir propriedades do sensor, do dispositivo pai do sensor e do espaço pai do sensor. As condições são expressas como comparações em relação a um [caminho JSON](https://jsonpath.com/), conforme descrito neste exemplo:

- Todos os sensores de Datatype **Temperatura** representados pelo valor de cadeia de caracteres de escape `\"Temperature\"`
- Tendo `01` em sua porta
- Que pertencem a dispositivos com a chave de propriedade estendida **Fabricante** definido para o valor da cadeia de caracteres de escape`\"Contoso\"`
- Que pertencem aos espaços do tipo especificado pela cadeia de caracteres com escape `\"Venue\"`
- Que são descendentes do **spaceid** pai `DE8F06CA-1138-4AD7-89F4-F782CC6F69FD`

```JSON
{
  "id": "23535afafd-f39b-46c0-9b0c-0dd3892a1c30",
  "name": "My custom matcher",
  "spaceId": "DE8F06CA-1138-4AD7-89F4-F782CC6F69FD",
  "description": "All sensors of datatype Temperature with 01 in their port that belong to devices with the extended property key Manufacturer set to the value Contoso and that belong to spaces of type Venue that are somewhere below space Id DE8F06CA-1138-4AD7-89F4-F782CC6F69FD",
  "conditions": [
    {
      "id": "43898sg43-e15a-4e9c-abb8-2gw464364",
      "target": "Sensor",
      "path": "$.dataType",
      "value": "\"Temperature\"",
      "comparison": "Equals"
    },
    {
      "id": "wt3th44-e15a-35sg-seg3-235wf3ga463",
      "target": "Sensor",
      "path": "$.port",
      "value": "01",
      "comparison": "Contains"
    },
    {
      "id": "735hs33-e15a-37jj-23532-db901d550af5",
      "target": "SensorDevice",
      "path": "$.properties[?(@.name == 'Manufacturer')].value",
      "value": "\"Contoso\"",
      "comparison": "Equals"
    },
    {
      "id": "222325-e15a-49fg-5744-463643644",
      "target": "SensorSpace",
      "path": "$.type",
      "value": "\"Venue\"",
      "comparison": "Equals"
    }
  ]
}
```

> [!IMPORTANT]
> - Demarcadores JSON diferenciam maiúsculas de minúsculas.
> - O conteúdo JSON é o mesmo que o conteúdo que é retornado por:
>   - `/sensors/{id}?includes=properties,types` para o sensor.
>   - `/devices/{id}?includes=properties,types,sensors,sensorsproperties,sensorstypes` para o dispositivo pai do sensor.
>   - `/spaces/{id}?includes=properties,types,location,timezone` para o espaço do pai do sensor.
> - As comparações são insensíveis a maiúsculas e minúsculas.

### <a name="user-defined-functions"></a>Funções definidas pelo usuário

Uma função definida pelo usuário é uma função personalizada executada em um ambiente isolado nos Gêmeos Digitais do Azure. As funções definidas pelo usuário têm acesso à mensagem de telemetria do sensor bruto à medida que foi recebida. As funções definidas pelo usuário também têm acesso ao serviço de gráfico espacial e despachante. Depois que a função definida pelo usuário é registrada no gráfico, um correspondente (detalhado [acima](#matchers)) deve ser criado para especificar quando executar a função. Por exemplo, quando os Gêmeos Digitais do Azure recebem nova telemetria de um determinado sensor, a função definida pelo usuário correspondente pode calcular uma média móvel das últimas poucas leituras do sensor.

As funções definidas pelo usuário podem ser gravadas no JavaScript. Os métodos auxiliares interagem com o gráfico no ambiente de execução definido pelo usuário. Os desenvolvedores podem executar snippets personalizados de código contra mensagens de telemetria do sensor. Por exemplo:

- Defina a leitura do sensor diretamente para o objeto do sensor no grafo.
- Execute uma ação com base em diferentes leituras de sensor dentro de um espaço no grafo.
- Crie uma notificação quando determinadas condições forem atendidas para uma leitura de sensor de entrada.
- Anexe metadados de grafo à leitura do sensor antes de enviar uma notificação do sensor.

Para obter mais informações, leia [como usar funções definidas pelo usuário](./how-to-user-defined-functions.md).

#### <a name="examples"></a>Exemplos

O [repositório GitHub para a amostra C# dos Gêmeos Digitais](https://github.com/Azure-Samples/digital-twins-samples-csharp/) contém alguns exemplos das funções definidas pelo usuário:
- [Essa função](https://github.com/Azure-Samples/digital-twins-samples-csharp/blob/master/occupancy-quickstart/src/actions/userDefinedFunctions/availabilityForTutorial.js) procura os valores de dióxido de carbono, de movimento e de temperatura para determinar se uma sala está disponível com esses valores no intervalo. Os [tutoriais dos Gêmeos Digitais](tutorial-facilities-udf.md) exploram essa função mais detalhadamente. 
- [Essa função](https://github.com/Azure-Samples/digital-twins-samples-csharp/blob/master/occupancy-quickstart/src/actions/userDefinedFunctions/multiplemotionsensors.js) procura os dados de vários sensores de movimento e determina que o espaço está disponível se nenhum deles detecta qualquer movimento. Você pode substituir com facilidade a função definida pelo usuário usada no [início rápido](quickstart-view-occupancy-dotnet.md), ou nos [tutoriais](tutorial-facilities-setup.md), fazendo as alterações mencionadas na seção de comentários do arquivo. 

### <a name="role-assignment"></a>Atribuição de função

As ações de uma função definida pelo usuário estão sujeitas a controle de [acesso baseado em função](./security-role-based-access-control.md) de Gêmeos Digitais para proteger os dados dentro do serviço. As atribuições de função definem quais funções definidas pelo usuário têm as permissões adequadas para interagir com o gráfico espacial e suas entidades. Por exemplo, uma função definida pelo usuário pode ter a capacidade e a permissão para *CIAR*, *LER*, *ATUALIZAR*, ou *EXCLUIR* dados de gráfico em um determinado espaço. Um nível da função definido pelo usuário de acesso é verificado quando a função definida pelo usuário solicita o gráfico de dados ou tenta efetuar uma ação. Para obter mais informações, leia [controle de acesso baseado em função](./security-create-manage-role-assignments.md).

É possível uma correspondência disparar uma função definida pelo usuário que não tenha atribuições de função. Nesse caso, a função definida pelo usuário não consegue ler nenhum dado do gráfico.

## <a name="next-steps"></a>Próximos passos

- Para saber mais sobre como rotear eventos e mensagens de telemetria para outros serviços do Azure, leia [Rotear eventos e mensagens](./concepts-events-routing.md).

- Para saber mais sobre como criar correspondentes, funções definidas pelo usuário e atribuições de função, leia o [Guia para usar funções definidas pelo usuário](./how-to-user-defined-functions.md).

- Revise a documentação de [referência da biblioteca de clientes de função definida pelo usuário](./reference-user-defined-functions-client-library.md).
