---
title: Entender cotas e limitação do Hub IoT do Azure | Microsoft Docs
description: Guia do desenvolvedor ‑ Descrição das cotas que se aplicam ao Hub IoT e o comportamento de limitação esperado.
author: robinsh
ms.author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 08/08/2019
ms.openlocfilehash: 71a5737434e78bc39bccdfeb950e0dbc32ed0052
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79284688"
---
# <a name="reference---iot-hub-quotas-and-throttling"></a>Referência - Cotas e limitação do Hub IoT

Este artigo explica as cotas de um Hub de IoT e fornece informações para ajudá-lo a entender como funciona o estrangulamento.

## <a name="quotas-and-throttling"></a>Cotas e limitação

Cada assinatura do Azure pode ter no máximo 50 hubs IoT e pelo menos um hub Gratuito.

Cada Hub IoT é provisionado com um determinado número de unidades em uma camada específica. A camada e o número de unidades determinam a cota diária máxima de mensagens que você pode enviar. O tamanho da mensagem usado para calcular a cota diária é de 0,5 KB para um hub de nível gratuito e de 4KB para todos os outros níveis. Para obter mais informações, consulte [Preços do Hub Azure IoT](https://azure.microsoft.com/pricing/details/iot-hub/).

A camada também determina os limites de limitação que o Hub IoT aplicam em todas as operações.

### <a name="iot-plug-and-play"></a>IoT Plug and Play

Durante a visualização pública, os dispositivos IoT Plug and Play enviarão mensagens separadas por interface, o que pode aumentar o número de mensagens contadas para sua cota de mensagens.

## <a name="operation-throttles"></a>Restrições de operação

Os aceleradores de operação são limitações de taxa que são aplicadas em intervalos de minutos e visam prevenir abusos. Eles também estão sujeitos à [modelagem do tráfego.](#traffic-shaping)

A tabela a seguir mostra as limitações impostas. Os valores referem-se a um hub individual.

| Restrição | Gratuito, B1 e S1 | B2 e S2 | B3 e S3 | 
| -------- | ------- | ------- | ------- |
| [Operações de registro de identidade](#identity-registry-operations-throttle) (criar, recuperar, listar, atualizar, excluir) | 1,67/s/unidade (100/min/unidade) | 1,67/s/unidade (100/min/unidade) | 83,33/seg/unidade (5.000/min/unidade) |
| [Novas conexões de dispositivos](#device-connections-throttle) (esse limite se aplica à taxa de _novas conexões,_ não ao número total de conexões) | Máximo de 100/s ou 12/s/unidade <br/> Por exemplo, duas unidades de S1 são 2\*12 = 24 novas conexões/s, mas você tem pelo menos 100 novas conexões s em suas unidades. Com nove unidades S1 você tem 108/s novas conexões/s (9\*12) em suas uniddes. | 120 novas conexões/s/unidade | 6.000 novas conexões/seg/unidade |
| Envios do dispositivo para a nuvem | Maior de 100 operações de envio/seg ou 12 de envio de operações/seg/unidade <br/> Por exemplo, duas unidades\*S1 são 2 12 = 24/seg, mas você tem pelo menos 100 operações de envio/seg em suas unidades. Com nove unidades S1, você tem 108\*operações de envio/seg (9 12) em suas unidades. | 120 enviar operações/seg/unidade | 6.000 enviar operações/seg/unidade |
| Envios de nuvem para dispositivo<sup>1</sup> | 1.67 enviar operações/seg/unidade (100 mensagens/min/unidade) | 1.67 enviar operações/seg/unidade (100 operações de envio/min/unidade) | 83.33 enviar operações/seg/unidade (5.000 operações de envio/min/unidade) |
| Recebimentos de nuvem para dispositivo<sup>1</sup> <br/> (somente quando o dispositivo usar HTTPS)| 16.67 receber operações/seg/unidade (1.000 receber operações/min/unidade) | 16.67 receber operações/seg/unidade (1.000 receber operações/min/unidade) | 833.33 receber operações/seg/unidade (50.000 receber operações/min/unidade) |
| Upload de arquivos | 1.67 iniciações de upload de arquivos/seg/unidade (100/min/unidade) | 1.67 iniciações de upload de arquivos/seg/unidade (100/min/unidade) | 83.33 iniciações de upload de arquivos/seg/unidade (5.000/min/unidade) |
| Métodos diretos<sup>1</sup> | 160KB/seg/unidade<sup>2</sup> | 480KB/seg/unidade<sup>2</sup> | 24MB/seg/unidade<sup>2</sup> | 
| Consultas | 20/min/unidade | 20/min/unidade | 1.000/min/unidade |
| Leituras de (dispositivos e módulos) gêmeos <sup>1</sup> | 100/seg | Maior de 100/seg ou 10/seg/unidade | 500/seg/unidade |
| Atualizações de (dispositivo e módulo) gêmeos <sup>1</sup> | 50/seg | Maior de 50/seg ou 5/seg/unidade | 250/seg/unidade |
| Operações de trabalhos<sup>1</sup> <br/> (criar, atualizar, listar, excluir) | 1,67/s/unidade (100/min/unidade) | 1,67/s/unidade (100/min/unidade) | 83,33/seg/unidade (5.000/min/unidade) |
| Operações de dispositivo de trabalhos<sup>1</sup> <br/> (atualizar gêmeos, invocar o método direto) | 10/s | Máximo de 10/s ou 1/s/unidade | 50/s/unidade |
| Configurações e implantações de borda <sup>1</sup> <br/> (criar, atualizar, listar, excluir) | 0,33/s/unidade (20/min/unidade) | 0,33/s/unidade (20/min/unidade) | 0,33/s/unidade (20/min/unidade) |
| Taxa de iniciação do fluxo do dispositivo<sup>1</sup> | 5 novos fluxos/s | 5 novos fluxos/s | 5 novos fluxos/s |
| Número máximo de fluxos de dispositivos conectados simultaneamente<sup>1</sup> | 50 | 50 | 50 |
| Transferência máxima de dados do fluxo de dispositivos<sup>1</sup> (volume agregado por dia) | 300 MB | 300 MB | 300 MB |

<sup>1</sup>Esse recurso não está disponível na camada básica do Hub IoT. Para obter mais informações, consulte [Como escolher o Hub IoT correto](iot-hub-scaling.md). <br/><sup>2</sup> O tamanho do medidor de estrangulamento é de 4 KB.

### <a name="throttling-details"></a>Detalhes de estrangulamento

* O tamanho do medidor determina em que incrementa o seu limite de estrangulamento é consumido. Se a carga da sua chamada direta estiver entre 0 e 4 KB, ela será contada como 4 KB. Você pode fazer até 40 chamadas por segundo por unidade antes de atingir o limite de 160 KB/seg/unidade.

   Da mesma forma, se a sua carga estiver entre 4 KB e 8 KB, cada chamada é responsável por 8 KB e você pode fazer até 20 chamadas por segundo por unidade antes de atingir o limite máximo.

   Finalmente, se o seu tamanho de carga estiver entre 156KB e 160 KB, você poderá fazer apenas 1 chamada por segundo por unidade em seu hub antes de atingir o limite de 160 KB/seg/unidade.

*  Para *operações de dispositivos Jobs (atualizar dois, invocar método direto)* para o nível S2, 50/seg/unidade só se aplica quando você invoca métodos usando trabalhos. Se você invocar métodos diretos diretamente, o limite original de estrangulamento de 24 MB/seg/unidade (para S2) será aplicado.

*  **Cota** é o número agregado de mensagens que você pode enviar em seu hub *por dia*. Você pode encontrar o limite de cotas do seu hub a coluna **Número total de mensagens /dia** na página de preços do [IoT Hub](https://azure.microsoft.com/pricing/details/iot-hub/).

*  Seus aceleradores nuvem-para-dispositivo e dispositivo-para-nuvem determinam a *taxa* máxima em que você pode enviar mensagens - número de mensagens, independentemente de 4 pedaços de KB. Cada mensagem pode ser de até 256 KB, que é o tamanho máximo da [mensagem](iot-hub-devguide-quotas-throttling.md#other-limits).

*  É uma boa prática para estrangular suas chamadas para que você não atinja/exceda os limites de estrangulamento. Se você atingir o limite, o IoT Hub responderá com o código de erro 429 e o cliente deve fazer o backup e a tentativa novamente. Esses limites são por hub (ou em alguns casos por hub/unidade). Para obter mais informações, consulte [Gerenciar conectividade e padrões confiáveis de mensagens/repetição](iot-hub-reliability-features-in-sdks.md#retry-patterns).

### <a name="traffic-shaping"></a>Modelagem de tráfego

Para acomodar o tráfego estourado, o IoT Hub aceita solicitações acima do acelerador por um tempo limitado. Os primeiros pedidos são processados imediatamente. No entanto, se o número de solicitações continuar a violar o acelerador, o IoT Hub começa a colocar as solicitações em uma fila e processadas na taxa limite. Esse efeito é chamado *de modelagem de tráfego*. Além disso, o tamanho desta fila é limitado. Se a violação do acelerador continuar, eventualmente a fila se enche, e `429 ThrottlingException`o IoT Hub começa a rejeitar solicitações com .

Por exemplo, você usa um dispositivo simulado para enviar 200 mensagens de dispositivo para nuvem por segundo para o seu Hub IoT S1 (que tem um limite de envios d2C de 100/seg). No primeiro minuto ou dois, as mensagens são processadas imediatamente. No entanto, como o dispositivo continua a enviar mais mensagens do que o limite do acelerador, o IoT Hub começa a processar apenas 100 mensagens por segundo e coloca o resto em uma fila. Você começa a notar aumento de latência. Eventualmente, você `429 ThrottlingException` começa a ficar à medida que a fila se enche, e o "número de erros de aceleração" nas [métricas do IoT Hub](iot-hub-metrics.md) começa a aumentar.

### <a name="identity-registry-operations-throttle"></a>Aceleração das operações de registro de identidade

As operações de registro de identidade do dispositivo destinam-se ao uso em tempo de execução em cenários de gerenciamento e provisionamento de dispositivos. Há suporte para leitura ou atualização de grandes números de identidades de dispositivo por meio de [trabalhos de importação e exportação](iot-hub-devguide-identity-registry.md#import-and-export-device-identities).

### <a name="device-connections-throttle"></a>Acelerador de conexões do dispositivo

As *conexões de dispositivo* controla a taxa em que novas conexões de dispositivo podem ser estabelecidas com um Hub IoT. A restrição de *conexões de dispositivo* não controla o número máximo de dispositivos conectados simultaneamente. A limitação da taxa de *conexões de dispositivo* depende do número de unidades provisionadas para o hub IoT.

Por exemplo, se você comprar uma única unidade S1, obterá uma restrição de 100 conexões por segundo. Portanto, para conectar 100.000 dispositivos, leva pelo menos 1.000 segundos (aproximadamente 16 minutos). No entanto, você pode conectar ao mesmo tempo todos os seus dispositivos registrados no registro de identidade.

## <a name="other-limits"></a>Outros limites

IoT Hub impõe outros limites operacionais:

| Operação | Limite |
| --------- | ----- |
| Dispositivos | O número total de dispositivos mais módulos que podem ser registrados em um único hub de IoT é limitado a 1.000.000. A única maneira de aumentar esse limite é entrar em contato com o [Microsoft Support](https://azure.microsoft.com/support/options/).|
| Carregamentos de arquivos | 10 uploads de arquivos simultâneos por dispositivo. |
| Trabalhos<sup>1</sup> | O máximo de vagas simultâneas é 1 (para Livre e S1), 5 (para S2) e 10 (para S3). No entanto, o máximo de trabalhos de [importação/exportação de dispositivos](iot-hub-bulk-identity-mgmt.md) simultâneos é de 1 para todos os níveis. <br/>O histórico de trabalho é mantido até 30 dias. |
| Pontos de extremidade adicionais | Hubs SKU pagos podem ter 10 pontos de extremidade adicionais. Hubs SKU gratuitos podem ter um ponto de extremidade adicional. |
| Consultas de roteamento de mensagens | Os hubs SKU pagos podem ter 100 consultas de roteamento. Os hubs SKU gratuitos podem ter cinco consultas de roteamento. |
| Aprimoramentos de mensagem | Os hubs SKU pagos podem ter até 10 enriquecimentos de mensagens. Os hubs SKU gratuitos podem ter até 2 enriquecimentos de mensagens.|
| Mensagens do dispositivo para a nuvem | Tamanho máximo da mensagem 256 KB |
| Mensagens de nuvem para dispositivo<sup>1</sup> | Tamanho máximo da mensagem 64 KB. O máximo de mensagens pendentes para entrega é de 50 por dispositivo. |
| Método direto<sup>1</sup> | O tamanho de payload do método direto máximo é 128 KB. |
| Configurações automáticas de dispositivos e módulos<sup>1</sup> | 100 configurações por hub SKU pago. 20 configurações por hub SKU gratuito. |
| Implantações automáticas ioT edge<sup>1</sup> | 50 módulos por implantação. 100 implantações (incluindo implantações em camadas) por hub SKU pago. 10 implantações por hub SKU gratuito. |
| Gêmeos<sup>1</sup> | O tamanho máximo das propriedades desejadas e as seções de propriedades relatadas são de 32 KB cada. O tamanho máximo da seção de tags é de 8 KB. |
| Políticas de acesso compartilhado | O número máximo de políticas de acesso compartilhado é de 16 |

<sup>1</sup>Esse recurso não está disponível na camada básica do Hub IoT. Para obter mais informações, consulte [Como escolher o Hub IoT correto](iot-hub-scaling.md).

## <a name="increasing-the-quota-or-throttle-limit"></a>Aumento do limite de cota ou acelerador

A qualquer momento, você pode aumentar as cotas ou os limites do acelerador [aumentando o número de unidades provisionadas em um hub de IoT](iot-hub-upgrade.md).

## <a name="latency"></a>Latency

O Hub IoT busca oferecer baixa latência para todas as operações. No entanto, devido às condições da rede e outros fatores imprevisíveis, não pode garantir uma certa latência. Ao projetar sua solução, você deve:

* Evitar fazer suposições sobre a latência máxima de qualquer operação de IoT Hub.
* Provisionar o hub IoT na região do Azure mais próxima de seus dispositivos.
* Considere usar o Azure IoT Edge para executar operações sensíveis à latência no dispositivo ou no gateway perto do dispositivo.

Várias unidades de Hub IoT afetam limitação, conforme descrito anteriormente, mas não oferecem nenhum benefício de latência nem garantia adicional.

No caso de aumentos inesperados na latência da operação, entre em contato com o [Suporte da Microsoft](https://azure.microsoft.com/support/options/).

## <a name="next-steps"></a>Próximas etapas

Para uma discussão aprofundada do comportamento de limitação do Hub IoT, veja a postagem do blog [A limitação do Hub IoT e você](https://azure.microsoft.com/blog/iot-hub-throttling-and-you/).

Outros tópicos de referência neste Guia do desenvolvedor do Hub IoT incluem:

* [Pontos de extremidade do Hub IoT](iot-hub-devguide-endpoints.md)
