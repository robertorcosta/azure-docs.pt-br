---
title: 'Conceitos: fluxo de dados no conector IoT (visualização) recurso da API do Azure para FHIR'
description: Entenda o fluxo de dados do conector IoT. O conector IoT é ingerido, normalizado, agrupa, transforma e persiste dados de IoMT para a API do Azure para FHIR.
services: healthcare-apis
author: ms-puneet-nagpal
ms.service: healthcare-apis
ms.subservice: iomt
ms.topic: conceptual
ms.date: 05/13/2020
ms.author: punagpal
ms.openlocfilehash: c2d150fcd35bc51478a1d7f4a0407abce1446c06
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/23/2020
ms.locfileid: "87096020"
---
# <a name="iot-connector-preview-data-flow"></a>Fluxo de dados do conector IoT (visualização)

Este artigo fornece uma visão geral do fluxo de dados no conector IoT. Você aprenderá sobre diferentes estágios de processamento de dados no conector IoT que transformam dados de dispositivo em recursos de [Observação](https://www.hl7.org/fhir/observation.html) baseados em FHIR.

![Fluxo de dados do Conector IoT](media/concepts-iot-data-flow/iot-connector-data-flow.png)

O diagrama acima mostra diferentes estágios de fluxo de dados no conector IoT. 

## <a name="ingest"></a>Ingerir
Ingestão é o primeiro estágio em que os dados do dispositivo são recebidos no conector IoT. O ponto de extremidade de ingestão para dados do dispositivo é hospedado em um [Hub de eventos do Azure](https://docs.microsoft.com/azure/event-hubs/). A plataforma do hub de eventos do Azure dá suporte a alta escala e taxa de transferência com a capacidade de receber e processar milhões de mensagens por segundo. Ele também permite que o conector IoT consuma mensagens de forma assíncrona, removendo a necessidade de que os dispositivos aguardem enquanto os dados do dispositivo são processados.

> [!NOTE]
> JSON é o único formato com suporte no momento para dados do dispositivo.

## <a name="normalize"></a>Normaliza
Normalize é o próximo estágio em que os dados do dispositivo são recuperados do hub de eventos do Azure acima e processados usando modelos de mapeamento de dispositivo. Esse processo de mapeamento resulta na transformação de dados do dispositivo em um esquema normalizado. 

O processo de normalização não apenas simplifica o processamento de dados em estágios posteriores, mas também fornece a capacidade de projetar uma mensagem de entrada em várias mensagens normalizadas. Por exemplo, um dispositivo poderia enviar vários sinais vitais para temperatura do corpo, taxa de pulso, pressão de sangue e taxa de Respiration em uma única mensagem. Essa mensagem de entrada criaria quatro recursos de FHIR separados. Cada recurso representaria um sinal vital diferente, com a mensagem de entrada projetada em quatro mensagens normalizadas diferentes.

## <a name="group"></a>Agrupar
O grupo é o próximo estágio em que as mensagens normalizadas disponíveis no estágio anterior são agrupadas usando três parâmetros diferentes: identidade do dispositivo, tipo de medida e período de tempo.

A identidade do dispositivo e o agrupamento do tipo de medição habilitam o uso do tipo de medição [SampledData](https://www.hl7.org/fhir/datatypes.html#SampledData) . Esse tipo fornece uma maneira concisa de representar uma série de medidas com base em tempo de um dispositivo no FHIR. E o período de tempo controla a latência na qual os recursos de observação gerados pelo conector IoT são gravados na API do Azure para FHIR.

> [!NOTE]
> O valor do período de tempo é padronizado para 15 minutos e não pode ser configurado para visualização.

## <a name="transform"></a>Transformar
No estágio de transformação, as mensagens de normalização agrupadas são processadas por meio de modelos de mapeamento FHIR. As mensagens que correspondem a um tipo de modelo são transformadas em recursos de observação com base em FHIR conforme especificado por meio do mapeamento.

Neste ponto, o recurso de [dispositivo](https://www.hl7.org/fhir/device.html) , juntamente com seu recurso de [paciente](https://www.hl7.org/fhir/patient.html) associado, também é recuperado do servidor FHIR usando o identificador de dispositivo presente na mensagem. Esses recursos são adicionados como uma referência ao recurso de observação que está sendo criado.

> [!NOTE]
> Todas as pesquisas de identidade são armazenadas em cache uma vez resolvidos para diminuir a carga no servidor FHIR. Se você planeja reutilizar dispositivos com vários pacientes, é aconselhável criar um recurso de dispositivo virtual que seja específico ao paciente e enviar o identificador de dispositivo virtual na carga da mensagem. O dispositivo virtual pode ser vinculado ao recurso de dispositivo real como um pai.

Se nenhum recurso de dispositivo para um determinado identificador de dispositivo existir no servidor FHIR, o resultado dependerá do valor `Resolution Type` definido no momento da criação. Quando definido como `Lookup` , a mensagem específica é ignorada e o pipeline continuará processando outras mensagens de entrada. Se definido como `Create` , o conector IOT criará um dispositivo básico e recursos de paciente no servidor FHIR.  

## <a name="persist"></a>Persist
Quando o recurso de observação FHIR é gerado no estágio de transformação, o recurso é salvo na API do Azure para FHIR. Se o recurso FHIR for novo, ele será criado no servidor. Se o recurso FHIR já existir, ele será atualizado.

## <a name="next-steps"></a>Próximas etapas

Clique abaixo da próxima etapa para aprender a criar modelos de mapeamento de dispositivo e FHIR.

>[!div class="nextstepaction"]
>[Modelos de mapeamento do Conector IoT](iot-mapping-templates.md)


FHIR é uma marca registrada da HL7, usada com permissão da HL7.
