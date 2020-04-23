---
title: Gerenciamento automático de dispositivos em escala com o Azure IoT Hub | Microsoft Docs
description: Use configurações automáticas do Azure IoT Hub para gerenciar vários dispositivos e módulos de IoT
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 12/13/2019
ms.author: robinsh
ms.openlocfilehash: 276f115f579fbd1ab077722b220a4a0c6c571850
ms.sourcegitcommit: 75089113827229663afed75b8364ab5212d67323
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/22/2020
ms.locfileid: "82025060"
---
# <a name="automatic-iot-device-and-module-management-using-the-azure-portal"></a>Gerenciamento automático de dispositivos ioT e módulos usando o portal Azure

[!INCLUDE [iot-edge-how-to-deploy-monitor-selector](../../includes/iot-hub-auto-device-config-selector.md)]

O gerenciamento automático de dispositivos no Azure IoT Hub automatiza muitas das tarefas repetitivas e complexas de gerenciar grandes frotas de dispositivos. Com o gerenciamento automático de dispositivos, você pode segmentar um conjunto de dispositivos com base em suas propriedades, definir uma configuração desejada e, em seguida, deixar o IoT Hub atualizar os dispositivos quando eles entrarem em escopo. Esta atualização é feita usando uma _configuração automática do dispositivo_ ou _configuração automática do módulo,_ que permite resumir a conclusão e a conformidade, lidar com a fusão e conflitos e implementar configurações em uma abordagem em fases.

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-whole.md)]

O gerenciamento automático do dispositivo funciona atualizando um conjunto de gêmeos de dispositivos ou gêmeos de módulo com propriedades desejadas e relatando um resumo baseado em propriedades relatadas por gêmeos.  Ele introduz uma nova classe e um documento JSON chamado *configuração* que tem três partes:

* A **condição de destino** define o escopo de gêmeos de dispositivo ou gêmeos de módulo a ser atualizado. A condição de destino é especificada como uma consulta em tags duplas e/ou propriedades relatadas.

* O **conteúdo de destino** define as propriedades desejadas a serem adicionadas ou atualizadas nos gêmeos do dispositivo-alvo ou gêmeos do módulo. O conteúdo inclui um caminho para a seção de propriedades desejadas a ser alterado.

* As **métricas** definem as contagens de resumos de vários estados de configuração como **êxito**, **em andamento**, e **erro**. As métricas personalizadas são especificadas como consultas em propriedades relatadas por gêmeos.  As métricas do sistema são as métricas padrão que medem o status de atualização dupla, como o número de gêmeos que são alvo e o número de gêmeos que foram atualizados com sucesso.

As configurações automáticas são executadas pela primeira vez logo após a configuração ser criada e, em seguida, em intervalos de cinco minutos. As consultas métricas são executadas cada vez que a configuração automática é executada.

## <a name="implement-twins"></a>Implementar gêmeos

As configurações automáticas de dispositivo requerem o uso de dispositivos gêmeos para sincronizar o estado entre a nuvem e os dispositivos.  Para obter mais informações, consulte [Entender e usar gêmeos de dispositivos no IoT Hub](iot-hub-devguide-device-twins.md).

As configurações automáticas do módulo exigem o uso de gêmeos de módulo para sincronizar o estado entre a nuvem e os módulos. Para obter mais informações, consulte [Entender e usar gêmeos de módulo no IoT Hub](iot-hub-devguide-module-twins.md).

## <a name="use-tags-to-target-twins"></a>Use tags para atingir gêmeos

Antes de criar uma configuração, você deve especificar quais dispositivos ou módulos deseja afetar. O Azure IoT Hub identifica dispositivos e usando tags no dispositivo twin, e identifica módulos usando tags no módulo gêmeo. Cada dispositivo ou módulo pode ter várias tags, e você pode defini-los de qualquer maneira que faça sentido para sua solução. Por exemplo, se você gerenciar dispositivos em diferentes locais, adicione as seguintes tags a um dispositivo gêmeo:

```json
"tags": {
    "location": {
        "state": "Washington",
        "city": "Tacoma"
    }
},
```

## <a name="create-a-configuration"></a>Criar uma configuração

1. No [Portal do Azure](https://portal.azure.com), vá ao hub IoT. 

2. Selecione **Configuração do dispositivo IoT**.

3. Selecione **Adicionar configuração do dispositivo** ou Adicionar **configuração do módulo**.

   ![Adicionar configuração do dispositivo ou configuração do módulo](./media/iot-hub-automatic-device-management/create-automatic-configuration.png)

Há cinco etapas para criar uma configuração. As seções a seguir explicam cada uma delas. 

### <a name="name-and-label"></a>Nome e rótulo

1. Dê um nome exclusivo à sua configuração de até 128 letras minúsculas. Evite usar espaços e os seguintes caracteres inválidos: `& ^ [ ] { } \ | " < > /`.

2. Adicione rótulos para ajudar a acompanhar as configurações. Os rótulos são pares de **Nome** e **Valor** que descrevem a configuração. Por exemplo, `HostPlatform, Linux` ou `Version, 3.0.1`.

3. Selecione **Próximo** para ir para o próximo passo. 

### <a name="specify-settings"></a>Especifique as configurações

Esta seção define o conteúdo a ser definido em gêmeos de dispositivo ou módulo direcionados. Há duas entradas para cada conjunto de configurações. O primeiro é o caminho gêmeo, que é o caminho para a seção JSON dentro das propriedades desejadas gêmeas que serão definidas.  O segundo é o conteúdo do JSON a ser inserido nessa seção. 

Por exemplo, você pode definir `properties.desired.chiller-water` o caminho gêmeo e, em seguida, fornecer o seguinte conteúdo JSON: 

```json
{
  "temperature": 66,
  "pressure": 28
}
```

![Defina o caminho gêmeo e o conteúdo](./media/iot-hub-automatic-device-management/module-config-twin-settings.png)


Você também pode definir configurações individuais especificando todo o caminho gêmeo e fornecendo o valor sem suportes. Por exemplo, com `properties.desired.chiller-water.temperature`o caminho duplo, defina o conteúdo como `66`. Em seguida, crie uma nova configuração dupla para a propriedade de pressão. 

Se duas ou mais configurações atingirem o mesmo caminho gêmeo, o conteúdo da configuração de maior prioridade será aplicado (a prioridade é definida na etapa 4).

Se você deseja remover uma propriedade existente, `null`especifique o valor da propriedade para .

Você pode adicionar configurações adicionais selecionando **Adicionar configuração dupla do dispositivo** ou adicionar **configuração dupla do módulo**.

### <a name="specify-metrics-optional"></a>Especificar as métricas (opcionais)

As métricas fornecem contagens sumárias dos vários estados que um dispositivo ou módulo pode relatar após a aplicação do conteúdo da configuração. Por exemplo, você pode criar uma métrica para alterações de configurações pendentes, uma métrica de erros e uma métrica para alterações de configurações bem-sucedidas.

Cada configuração pode ter até cinco métricas personalizadas. 

1. Digite um nome para **Nome Métrico**.

2. Insira uma consulta para **Critérios da métrica**.  A consulta é baseada nas propriedades reportadas de dispositivo gêmeo.  A métrica representa o número de linhas retornadas pela consulta.

Por exemplo:

```sql
SELECT deviceId FROM devices 
  WHERE properties.reported.chillerWaterSettings.status='pending'
```

Você pode incluir uma cláusula na qual a configuração foi aplicada, por exemplo: 

```sql
/* Include the double brackets. */
SELECT deviceId FROM devices 
  WHERE configurations.[[yourconfigname]].status='Applied'
```

Se você estiver construindo uma métrica para relatar `moduleId` em `devices.modules`módulos configurados, selecione a partir de . Por exemplo:

```sql
SELECT deviceId, moduleId FROM devices.modules
  WHERE properties.reported.lastDesiredStatus.code = 200
```

### <a name="target-devices"></a>Dispositivos de destino

Use a propriedade tags de seus gêmeos para segmentar os dispositivos ou módulos específicos que devem receber essa configuração. Você também pode direcionar propriedades relatadas por gêmeos.

As configurações automáticas do dispositivo só podem atingir as marcas gêmeas do dispositivo, e as configurações automáticas do módulo só podem atingir as marcas gêmeas do módulo. 

Uma vez que várias configurações podem atingir o mesmo dispositivo ou módulo, cada configuração precisa de um número de prioridade. Se houver um conflito, a configuração com a prioridade mais alta ganhará. 

1. Insira um inteiro positivo para a **Prioridade** da configuração. O maior valor numérico é considerado a prioridade mais alta. Se duas configurações tiverem o mesmo número de prioridade, aquela que foi criada mais recentemente ganhará. 

2. Digite uma **condição de destino** para determinar quais dispositivos ou módulos serão direcionados com essa configuração. A condição é baseada em tags gêmeas ou propriedades relatadas por gêmeos e deve corresponder ao formato de expressão. 

   Para a configuração automática do dispositivo, você pode especificar apenas a tag ou a propriedade relatada para o destino. Por exemplo, `tags.environment='test'` ou `properties.reported.chillerProperties.model='4000x'`. É possível especificar `*` para direcionar todos os dispositivos. 
   
   Para configuração automática do módulo, use uma consulta para especificar tags ou propriedades relatadas dos módulos registrados no hub IoT. Por exemplo, `from devices.modules where tags.environment='test'` ou `from devices.modules where properties.reported.chillerProperties.model='4000x'`. O curinga não pode ser usado para atingir todos os módulos. 

3. Selecione **Avançar** para ir para a etapa final.

### <a name="review-configuration"></a>Configuração de revisão

Revise as informações da configuração e, em seguida, selecione **Enviar**.

## <a name="monitor-a-configuration"></a>Monitorar uma configuração

Para exibir os detalhes de uma configuração e monitorar os dispositivos que a executam, use as seguintes etapas:

1. No [Portal do Azure](https://portal.azure.com), vá ao hub IoT. 

2. Selecione **Configuração do dispositivo IoT**.

3. Inspecione a lista de configuração. Para cada configuração, você pode exibir os seguintes detalhes:

   * **ID** - o nome da configuração.

   * **Condição de** destino - a consulta usada para definir dispositivos ou módulos direcionados.

   * **Prioridade** - o número de prioridade atribuído à configuração.

   * **Hora de criação** - o carimbo de data/hora de quando a configuração foi criada. Esse carimbo de data/hora é usado para desempate quando duas configurações têm a mesma prioridade. 

   * **As métricas do sistema** - métricas que são calculadas pelo Hub IoT e não podem ser personalizadas pelos desenvolvedores. O destino especifica o número de dispositivos gêmeos que correspondem à condição de destino. Aplica-se o número especificado de dispositivos gêmeos que foram modificados pela configuração, que pode incluir modificações parciais no evento que uma configuração separada de prioridade mais alta também fez alterações. 

   * **Métricas personalizadas** - métricas que foram especificadas pelo desenvolvedor como consultas contra propriedades relatadas por gêmeos.  Até cinco métricas personalizadas podem ser definidas por configuração. 
   
4. Selecione a configuração que deseja monitorar.  

5. Inspecione os detalhes da configuração. Você pode usar guias para exibir detalhes específicos sobre os dispositivos que receberam a configuração.

   * **Condição de destino** - os dispositivos ou módulos que correspondem à condição de destino. 

   * **Métricas** - uma lista de métricas do sistema e métricas personalizadas.  Você pode visualizar uma lista de dispositivos ou módulos que são contados para cada métrica selecionando a métrica na lista de itens abaixados e, em seguida, selecionando Dispositivos de **exibição** ou **Módulos de exibição**.

   * **Configurações duplas do dispositivo** ou **configurações duplas** do módulo - as configurações duplas definidas pela configuração. 

   * **Rótulos de configuração** - pares de chave-valor usados para descrever uma configuração.  Rótulos não têm impacto sobre a funcionalidade. 

## <a name="modify-a-configuration"></a>Modificar uma configuração

Quando você modifica uma configuração, as alterações se replicam imediatamente em todos os dispositivos ou módulos-alvo. 

Se você atualizar a condição de destino, ocorrerão as seguintes atualizações:

* Se um gêmeo não cumpriu a antiga condição de alvo, mas atende à nova condição de destino e esta configuração é a maior prioridade para esse gêmeo, então essa configuração é aplicada. 

* Se um gêmeo atualmente executando essa configuração não atender mais à condição de destino, as configurações da configuração serão removidas e o gêmeo será modificado pela próxima configuração de alta prioridade. 

* Se um gêmeo atualmente executando essa configuração não atender à condição de destino e não atender à condição de destino de qualquer outra configuração, então as configurações da configuração serão removidas e nenhuma outra alteração será feita no gêmeo. 

Para modificar uma configuração, use as seguintes etapas: 

1. No [Portal do Azure](https://portal.azure.com), vá ao hub IoT. 

2. Selecione **Configuração do dispositivo IoT**. 

3. Selecione a configuração que deseja modificar. 

4. Faça atualizações nos seguintes campos: 

   * Condição de destino 
   * Rótulos 
   * Prioridade 
   * Métricas

4. Clique em **Salvar**.

5. Siga as etapas em [Monitorar uma configuração](#monitor-a-configuration) para observar as mudanças. 

## <a name="delete-a-configuration"></a>Excluir uma configuração

Quando você exclui uma configuração, qualquer dispositivo gêmeo assume sua próxima configuração de prioridade mais alta. Se os dispositivos gêmeos não atendem a condição destino de qualquer outra configuração, não serão aplicadas outras configurações. 

1. No [Portal do Azure](https://portal.azure.com), vá ao hub IoT. 

2. Selecione **Configuração do dispositivo IoT**. 

3. Use a caixa de seleção para selecionar a configuração que deseja excluir. 

4. Selecione **Excluir**.

5. Uma mensagem solicitará que você confirme.

## <a name="next-steps"></a>Próximas etapas

Neste artigo, você aprendeu como configurar e monitorar dispositivos IoT em escala. Para saber mais sobre o gerenciamento do Hub IoT do Azure, siga estes links:

* [Gerenciar identidades de dispositivo do Hub IoT em massa](iot-hub-bulk-identity-mgmt.md)
* [Métricas do Hub IoT](iot-hub-metrics.md)
* [Monitoramento de operações](iot-hub-operations-monitoring.md)

Para explorar melhor as funcionalidades do Hub IoT, consulte:

* [Guia de desenvolvedores do IoT Hub](iot-hub-devguide.md)
* [Implantando o AI em dispositivos de borda com o Azure IoT Edge](../iot-edge/tutorial-simulate-device-linux.md)

Para explorar usando o Serviço de Provisionamento de Dispositivos do Hub IoT para habilitar o provisionamento sem toque e Just-In-Time, consulte: 

* [Serviço de Provisionamento de Dispositivos no Hub IoT do Azure](/azure/iot-dps)
