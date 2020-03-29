---
title: Implantação automática para grupos de dispositivos - Azure IoT Edge | Microsoft Docs
description: Use implantações automáticas do Azure IoT Edge para gerenciar grupos de dispositivos com base em marcas compartilhadas
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 01/30/2020
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 8aaac6100ba980301ff3e85a3ac3959bfee89b49
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "76895974"
---
# <a name="understand-iot-edge-automatic-deployments-for-single-devices-or-at-scale"></a>Noções básicas sobre implantações do IoT Edge para dispositivos únicos ou em escala

Implantações automáticas e implantação em camadas ajudam você a gerenciar e configurar módulos em um grande número de dispositivos IoT Edge.

O Azure IoT Edge fornece duas maneiras de configurar os módulos a serem executados em dispositivos IoT Edge. O primeiro método é implantar módulos por dispositivo. Você cria um manifesto de implantação e, em seguida, aplicá-lo a um determinado dispositivo pelo nome. O segundo método é implantar módulos automaticamente em qualquer dispositivo registrado que atenda a um conjunto de condições definidas. Você cria um manifesto de implantação e, em seguida, define quais dispositivos ele se aplica com base em [tags](../iot-edge/how-to-deploy-monitor.md#identify-devices-using-tags) no dispositivo gêmeo.

Este artigo se concentra na configuração e monitoramento de frotas de dispositivos, coletivamente referidos como *implantações automáticas IoT Edge*.As etapas básicas de implantação são as seguintes:

1. Um operador define uma implantação que descreve um conjunto de módulos e dispositivos de destino.Cada implantação tem um manifesto de implantação que reflete essa informação.
2. O serviço IoT Hub se comunica com todos os dispositivos-alvo para configurá-los com os módulos declarados.
3. O serviço do Hub IoT recupera o status dos dispositivos do IoT Edge e as disponibiliza para o operador.Por exemplo, um operador pode ver quando um dispositivo Edge não está configurado com sucesso ou se um módulo falha durante o tempo de execução.
4. A qualquer momento, novos dispositivos IoT Edge que atendem às condições de destino são configurados para a implantação.

Este artigo descreve cada componente envolvido na configuração e no monitoramento de uma implantação. Para obter instruções de criação e atualização de uma implantação, confira [Implantar e monitorar os módulos do IoT Edge em larga escala](how-to-deploy-monitor.md).

## <a name="deployment"></a>Implantação

Uma implantação automática do IoT Edge atribui imagens do módulo do IoT Edge para execução como instâncias em um conjunto de dispositivos IoT Edge de destino. Ele funciona com a configuração de um manifesto de implantação do IoT Edge para incluir uma lista de módulos com os parâmetros de inicialização.Uma implantação pode ser atribuída a um único dispositivo (com base na ID de dispositivo) ou a um grupo de dispositivos (com base em marcas).Depois que um dispositivo IoT Edge recebe um manifesto de implantação, ele baixa e instala as imagens de contêiner dos repositórios do respectivo contêiner e os configura adequadamente.Depois de criar uma implantação, um operador pode monitorar o status da implantação para ver se os dispositivos de destino estão configurados corretamente.

Somente os dispositivos do IoT Edge podem ser configurados com uma implantação. Os pré-requisitos seguintes devem estar no dispositivo antes de ele poder receber a implantação:

* O sistema operacional base
* Um sistema de gerenciamento de contêiner, como Moby ou Docker
* Provisionamento do runtime do IoT Edge

### <a name="deployment-manifest"></a>Manifesto de implantação

Um manifesto de implantação é um documento JSON que descreve os módulos a serem configurados nos dispositivos IoT Edge de destino. Ele contém os metadados de configuração para todos os módulos, incluindo os módulos do sistema necessários (especificamente o agente do IoT Edge e o hub IoT Edge).  

Os metadados de configuração para cada módulo incluem:

* Versão
* Type
* Status (por exemplo, em execução ou parado)
* Política de reinicialização
* Imagem ou registro de contêiner
* Rotas para dados de entrada e saída

Se o módulo de imagem for armazenado em um registro de contêiner privado, o agente IoT Edge segura as credenciais de registro.

### <a name="target-condition"></a>Condição de destino

A condição de destino é continuamente avaliada durante toda a vida útil da implantação. Os novos dispositivos que atendem aos requisitos são incluídos e qualquer dispositivo existente não é removido. A implantação é reativada se o serviço detectar qualquer alteração de condição de destino.

Por exemplo, você tem uma implantação com uma condição de destino tags.ambiente = 'prod'. Quando você iniciar a implantação, há 10 dispositivos de produção. Os módulos são instalados com êxito nesses 10 dispositivos. O status do agente IoT Edge mostra 10 dispositivos totais, 10 respostas bem sucedidas, 0 respostas de falha e 0 respostas pendentes. Agora, adicione mais 5 dispositivos com tags.environment = 'prod'. O serviço detecta a alteração e o status do agente IoT Edge torna-se 15 dispositivos totais, 10 respostas bem sucedidas, 0 respostas de falha e 5 respostas pendentes enquanto ele é implantado nos cinco novos dispositivos.

Use qualquer condição booleana em tags duplas do dispositivo, propriedades relatadas por dispositivo ou dispositivoId para selecionar os dispositivos de destino. Se você quiser usar a condição com marcas, você precisa adicionar a seção "tags":{} no dispositivo gêmeo no mesmo nível como propriedades. [Saiba mais sobre as marcas em dispositivo gêmeo](../iot-hub/iot-hub-devguide-device-twins.md)

Exemplos de condições de destino:

* deviceId ='linuxprod1'
* tags.environment ='prod'
* tags.environment = 'prod' E tags.location = 'westus'
* tags.environment = 'prod' OU tags.location = 'westus'
* tags.operator = 'John' E tags.environment = 'prod' NÃO deviceId = 'linuxprod1'
* propriedades.reported.devicemodel = '4000x'

Considere essas restrições quando você constrói uma condição de destino:

* No dispositivo gêmeo, você só pode construir uma condição de destino usando tags, propriedades relatadas ou dispositivoId.
* Aspas duplas não são permitidas em nenhuma parte da condição de destino. Use aspas simples.
* Aspas simples representam os valores da condição de destino. Portanto, você deve usar as aspas simples com outras aspas simples se isso fizer parte do nome do dispositivo. Por exemplo, para um dispositivo de destino chamado `operator'sDevice`, escreva `deviceId='operator''sDevice'`.
* Números, letras e os caracteres a seguir são permitidos nos valores de condição de destino: `-:.+%_#*?!(),=@;$`.

### <a name="priority"></a>Prioridade

Uma prioridade define se uma implantação deve ser aplicada a um dispositivo de destino em relação a outras implantações. Uma prioridade de implantação é um inteiro positivo, e os maiores números indicam as prioridades mais altas. Se um dispositivo IoT Edge é afetado por mais de uma implantação, a implantação com a prioridade mais alta será aplicada.As implantações com prioridades inferiores não são aplicadas nem mescladas.Se um dispositivo é afetado com duas ou mais implantações com a mesma prioridade, a implantação criada mais recentemente (determinada pelo carimbo de hora de criação) se aplica.

### <a name="labels"></a>Rótulos

Os rótulos são pares de tecla/valor de seqüência que você pode usar para filtrar e agrupar implantações.Uma implantação pode ter vários rótulos. As etiquetas são opcionais e não afetam a configuração real dos dispositivos IoT Edge.

### <a name="metrics"></a>Métricas

Por padrão, todas as implantações são reportadas em quatro métricas:

* **O alvo** mostra os dispositivos IoT Edge que correspondem à condição de segmentação de implantação.
* **A aplicação** mostra os dispositivos IoT Edge direcionados que não são visados por outra implantação de maior prioridade.
* **O Reporting Success** mostra os dispositivos IoT Edge que relataram que os módulos foram implantados com sucesso.
* **A falha de relatórios** mostra os dispositivos IoT Edge que relataram que um ou mais módulos não foram implantados com sucesso. Para investigar o erro mais detalhadamente, conecte-se remotamente a esses dispositivos e exiba os arquivos de log.

Além disso, você pode definir suas próprias métricas personalizadas para ajudar a monitorar e gerenciar a implantação.

As métricas fornecem contagens sumárias dos vários estados que os dispositivos podem reportar como resultado da aplicação de uma configuração de implantação. As métricas podem consultar as [propriedades relatadas do módulo edgeHub,](module-edgeagent-edgehub.md#edgehub-reported-properties)como *lastDesiredStatus* ou *lastConnectTime*. Por exemplo: 

```sql
SELECT deviceId FROM devices
  WHERE properties.reported.lastDesiredStatus.code = 200
```

Adicionar suas próprias métricas é opcional e não afeta a configuração real de dispositivos IoT Edge.

## <a name="layered-deployment"></a>Implantação em camadas

Implantações em camadas são implantações automáticas que podem ser combinadas para reduzir o número de implantações exclusivas que precisam ser criadas. Implantações em camadas são úteis em cenários onde os mesmos módulos são reutilizados em diferentes combinações em muitas implantações automáticas.

As implantações em camadas têm os mesmos componentes básicos de qualquer implantação automática. Eles visam dispositivos com base em tags nos gêmeos do dispositivo e fornecem a mesma funcionalidade em torno de rótulos, métricas e relatórios de status. Implantações em camadas também têm prioridades atribuídas a eles, mas em vez de usar a prioridade para determinar qual implantação é aplicada a um dispositivo, a prioridade determina como várias implantações são classificadas em um dispositivo. Por exemplo, se duas implantações em camadas tiverem um módulo ou uma rota com o mesmo nome, a implantação em camadas com a maior prioridade será aplicada enquanto a prioridade inferior for substituída.

Os módulos de tempo de execução do sistema, edgeAgent e edgeHub, não estão configurados como parte de uma implantação em camadas. Qualquer dispositivo IoT Edge direcionado por uma implantação em camadas precisa de uma implantação automática padrão aplicada a ele primeiro. A implantação automática fornece a base sobre a qual as implantações em camadas podem ser adicionadas.

Um dispositivo IoT Edge pode aplicar uma única implantação automática padrão, mas pode aplicar várias implantações automáticas em camadas. Qualquer implantação em camadas direcionada a um dispositivo deve ter uma prioridade maior do que a implantação automática para esse dispositivo.

Por exemplo, considere o seguinte cenário de uma empresa que gerencia edifícios. Eles desenvolveram módulos IoT Edge para coletar dados de câmeras de segurança, sensores de movimento e elevadores. No entanto, nem todos os seus edifícios podem usar os três módulos. Com implantações automáticas padrão, a empresa precisa criar implantações individuais para todas as combinações de módulos que seus edifícios precisam.

![Implantações automáticas padrão precisam acomodar cada combinação de módulos](./media/module-deployment-monitoring/standard-deployment.png)

No entanto, uma vez que a empresa muda para implantações automáticas em camadas, eles descobrem que podem criar as mesmas combinações de módulos para seus edifícios com menos implantações para gerenciar. Cada módulo tem sua própria implantação em camadas, e as tags do dispositivo identificam quais módulos são adicionados a cada edifício.

![Implantações automáticas em camadas simplificam cenários onde os mesmos módulos são combinados de diferentes maneiras](./media/module-deployment-monitoring/layered-deployment.png)

### <a name="module-twin-configuration"></a>Configuração do módulo gêmeo

Quando você trabalha com implantações em camadas, você pode, intencionalmente ou não, ter duas implantações com o mesmo módulo direcionado a um dispositivo. Nesses casos, você pode decidir se a implantação de prioridade superior deve substituir o módulo gêmeo ou apêndice a ele. Por exemplo, você pode ter uma implantação que aplica o mesmo módulo a 100 dispositivos diferentes. No entanto, 10 desses dispositivos estão em instalações seguras e precisam de configuração adicional para se comunicar através de servidores proxy. Você pode usar uma implantação em camadas para adicionar propriedades gêmeas de módulo que permitem que esses 10 dispositivos se comuniquem com segurança sem substituir as informações duplas do módulo existente a partir da implantação da base.

Você pode anexar as propriedades desejadas no módulo twin desejado no manifesto de implantação. Onde em uma implantação padrão você adicionaria propriedades na seção **properties.desired** do módulo twin, em uma implantação em camadas você pode declarar um novo subconjunto de propriedades desejadas.

Por exemplo, em uma implantação padrão, você pode adicionar o módulo de sensor de temperatura simulado com as seguintes propriedades desejadas que dizem para enviar dados em intervalos de 5 segundos:

```json
"SimulatedTemperatureSensor": {
  "properties.desired": {
    "SendData": true,
    "SendInterval": 5
  }
}
```

Em uma implantação em camadas que visa alguns ou todos os mesmos dispositivos, você pode adicionar uma propriedade que diz ao sensor simulado para enviar 1000 mensagens e, em seguida, parar. Você não deseja substituir as propriedades existentes, então você cria uma nova seção dentro das propriedades desejadas, que `layeredProperties`contém a nova propriedade:

```json
"SimulatedTemperatureSensor": {
  "properties.desired.layeredProperties": {
    "StopAfterCount": 1000
  }
}
```

Um dispositivo que tenha ambas as implantações aplicadas refletirá as seguintes propriedades no módulo gêmeo para o sensor de temperatura simulado:

```json
"properties": {
  "desired": {
    "SendData": true,
    "SendInterval": 5,
    "layeredProperties": {
      "StopAfterCount": 1000
    }
  }
}
```

Se você definir `properties.desired` o campo do módulo gêmeo em uma implantação em camadas, ele substituirá as propriedades desejadas para esse módulo em quaisquer implantações de menor prioridade.

## <a name="phased-rollout"></a>Distribuição em fases

Uma distribuição em fases é um processo geral no qual um operador implanta alterações em um conjunto maior de dispositivos IoT Edge. O objetivo é fazer alterações gradualmente para reduzir o risco de fazer alterações significativas em larga escala. Implantações automáticas ajudam a gerenciar implantações em fases através de uma frota de dispositivos IoT Edge.

Uma distribuição em fases é executada nas seguintes fases e etapas:

1. Estabeleça um ambiente de teste de dispositivos IoT Edge provisionando-os e definindo uma marca de gêmeo de dispositivo como `tag.environment='test'`.O ambiente de teste deve espelhar o ambiente de produção que a que se destinará a implantação.
2. Crie uma implantação, incluindo as configurações e os módulos desejados. A condição de destino deve ter como destino o ambiente de teste do dispositivo IoT Edge.
3. Valide a nova configuração do módulo no ambiente de teste.
4. Atualize a implantação para incluir um subconjunto de dispositivos IoT Edge de produção, adicionando uma nova marca à condição de destino. Além disso, verifique se a prioridade da implantação é maior do que a de outras implantações atualmente destinadas a esses dispositivos
5. Verifique se a implantação foi bem-sucedida nos dispositivos IoT de destino exibindo o status da implantação.
6. Atualize a implantação para ter como destino todos os dispositivos IoT Edge de produção restantes.

## <a name="rollback"></a>Reversão

As implantações podem ser revertidas em se você receber erros ou problemas de configuração.Como uma implantação define a configuração absoluta do módulo para um dispositivo do IoT Edge, outras implantações também devem estar voltadas para o mesmo dispositivo em uma prioridade mais baixa, mesmo se o objetivo é remover todos os módulos.  

A exclusão de uma implantação não remove os módulos dos dispositivos-alvo. Deve haver outra implantação que defina uma nova configuração para os dispositivos, mesmo que seja uma implantação vazia.

Execute as reversões na sequência abaixo:

1. Confirme que uma segunda implantação também tem como destino o mesmo conjunto de dispositivos. Se a meta da reversão é remover todos os módulos, a segunda implantação não deve incluir todos os módulos.
2. Modifique ou remova a expressão de condição de destino da implantação que você deseja reverter para que os dispositivos não atendam à condição de destino.
3. Verifique se a reversão teve êxito exibindo o status da implantação.
   * A implantação revertida não deve mostrar o status dos dispositivos que foram revertidos.
   * A segunda implantação agora deve incluir o status da implantação dos dispositivos que foram revertidos.

## <a name="next-steps"></a>Próximas etapas

* Siga as etapas para criar, atualizar ou excluir uma implantação em [Implantar e monitorar os módulos do IoT Edge em larga escala](how-to-deploy-monitor.md).
* Saiba mais sobre outros conceitos de IoT Edge, como o [runtime do IoT Edge](iot-edge-runtime.md) e os [módulos do IoT Edge](iot-edge-modules.md).
