---
title: Implantar módulos em escala em portal do Azure Azure IoT Edge
description: Usar o portal do Azure para criar dispositivos de implantações automáticas para grupos do IoT Edge
keywords: ''
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 4/21/2020
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: e55d3f704c76d2783c3e442a90c829448129a4d0
ms.sourcegitcommit: edccc241bc40b8b08f009baf29a5580bf53e220c
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/24/2020
ms.locfileid: "82134410"
---
# <a name="deploy-iot-edge-modules-at-scale-using-the-azure-portal"></a>Implantar módulos IoT Edge em escala usando o portal do Azure

Crie um **IOT Edge implantação automática** no portal do Azure para gerenciar implantações em andamento para vários dispositivos de uma vez. As implantações automáticas para IoT Edge fazem parte do recurso de [gerenciamento automático de dispositivo](/azure/iot-hub/iot-hub-automatic-device-management) do Hub IOT. As implantações são processos dinâmicos que permitem implantar vários módulos em vários dispositivos, acompanhar o status e a integridade dos módulos e fazer alterações quando necessário.

Para obter mais informações, consulte [entender IOT Edge implantações automáticas para dispositivos únicos ou em escala](module-deployment-monitoring.md).

## <a name="identify-devices-using-tags"></a>Identificar dispositivos usando marcações

Antes de criar uma implantação, você precisa conseguir especificar quais dispositivos você deseja afetar. O Azure IoT Edge identifica os dispositivos usando **marcações** no dispositivo gêmeo. Cada dispositivo pode ter várias marcas que você define de qualquer forma que faça sentido para sua solução.

Por exemplo, se você gerenciar um campus de prédios inteligentes, poderá adicionar localização, tipo de sala e marcas de ambiente a um dispositivo:

```json
"tags":{
  "location":{
    "building": "20",
    "floor": "2"
  },
  "roomtype": "conference",
  "environment": "prod"
}
```

Para obter mais informações sobre dispositivos gêmeos e marcações, consulte [Entender e usar dispositivos gêmeos no Hub IoT](../iot-hub/iot-hub-devguide-device-twins.md).

## <a name="create-a-deployment"></a>Criar uma implantação

O IoT Edge fornece dois tipos diferentes de implantações automáticas que você pode usar para personalizar seu cenário. Você pode criar uma *implantação*padrão, que inclui os módulos de tempo de execução do sistema e todos os módulos e rotas adicionais. Cada dispositivo só pode aplicar uma implantação. Ou você pode criar uma *implantação em camadas*, que inclui apenas módulos e rotas personalizadas, não o tempo de execução do sistema. Muitas implantações em camadas podem ser combinadas em um dispositivo, além de uma implantação padrão. Para obter mais informações sobre como os dois tipos de implantações automáticas funcionam em conjunto, consulte [entender IOT Edge implantações automáticas para dispositivos únicos ou em escala](module-deployment-monitoring.md).

As etapas para criar uma implantação e uma implantação em camadas são muito semelhantes. Todas as diferenças são chamadas nas etapas a seguir.

1. Na [portal do Azure](https://portal.azure.com), vá para o Hub IOT.
1. No menu no painel esquerdo, selecione **IOT Edge** em gerenciamento de **dispositivo automático**.
1. Na barra superior, selecione **criar implantação** ou **criar implantação em camadas**.

Há cinco etapas para criar uma implantação. As seções a seguir explicam cada uma delas.

### <a name="step-1-name-and-label"></a>Etapa 1: nome e rótulo

1. Dê à sua implantação um nome exclusivo com até 128 letras minúsculas. Evite usar espaços e os seguintes caracteres inválidos: `& ^ [ ] { } \ | " < > /`.
1. Você pode adicionar rótulos como pares chave-valor para ajudar a acompanhar suas implantações. Por exemplo, **HostPlatform** e **Linux** ou **Versão** e **3.0.1**.
1. Selecione **Avançar: módulos** para mover para a etapa dois.

### <a name="step-2-modules"></a>Etapa 2: módulos

Você pode adicionar até 20 módulos a uma implantação. Se você criar uma implantação sem módulos, ela removerá os módulos atuais dos dispositivos de destino.

Em implantações, você pode gerenciar as configurações para os módulos agente de IoT Edge e IoT Edge Hub. Selecione **configurações de tempo de execução** para configurar os dois módulos de tempo de execução. Em implantação em camadas, os módulos de tempo de execução não são incluídos, portanto não podem ser configurados.

Você pode adicionar três tipos de módulos:

* Módulo IoT Edge
* Módulo do Marketplace
* Módulo Azure Stream Analytics

#### <a name="add-an-iot-edge-module"></a>Adicionar um módulo IoT Edge

Para adicionar um código personalizado como um módulo ou para adicionar um módulo de serviço do Azure manualmente, siga estas etapas:

1. Na seção **credenciais de registro de contêiner** da página, forneça os nomes e as credenciais para qualquer registro de contêiner privado que contenha as imagens de módulo para essa implantação. O agente de IoT Edge relatará o erro 500 se ele não conseguir encontrar a credencial do registro de contêiner para uma imagem do Docker.
1. Na seção **Módulos do IoT Edge** da página, clique em **Adicionar**.
1. Selecione **IOT Edge módulo** no menu suspenso.
1. Dê ao seu módulo um **nome de módulo IOT Edge**.
1. No campo **URI da Imagem**, insira a imagem de contêiner para o módulo.
1. Use o menu suspenso para selecionar uma **Política de reinicialização**. Escolha uma das seguintes opções:
   * **sempre** -o módulo sempre reinicia se for desligado por qualquer motivo.
   * **nunca** -o módulo nunca será reiniciado se for desligado por qualquer motivo.
   * **em** caso de falha-o módulo será reiniciado se ele falhar, mas não se for desligado corretamente.
   * Não **íntegro** – o módulo será reiniciado se ele falhar ou retornar um status não íntegro. Cabe a cada módulo implementar a função de status da integridade.
1. Use o menu suspenso para selecionar o **Status** desejado do módulo. Escolha uma das seguintes opções:
   * **executando-em** execução é a opção padrão. O módulo será iniciado imediatamente depois de ser implantado.
   * **parado** -depois de ser implantado, o módulo permanecerá ocioso até ser chamado para ser iniciado por você ou por outro módulo.
1. Especifique qualquer **Opção de criação de contêiner** que deverá ser passada para o contêiner. Para obter mais informações, consulte [criar docker](https://docs.docker.com/engine/reference/commandline/create/).
1. Selecione **configurações de módulo de configuração** se desejar adicionar marcas ou outras propriedades ao módulo.
1. Insira as **variáveis de ambiente** para este módulo. As variáveis de ambiente fornecem informações de configuração para um módulo.
1. Selecione **Adicionar** para adicionar o módulo à implantação.

#### <a name="add-a-module-from-the-marketplace"></a>Adicionar um módulo do Marketplace

Para adicionar um módulo do Azure Marketplace, siga estas etapas:

1. Na seção **Módulos do IoT Edge** da página, clique em **Adicionar**.
1. Selecione o **módulo Marketplace** no menu suspenso.
1. Escolha um módulo na página **IOT Edge do Marketplace do módulo** . O módulo selecionado é configurado automaticamente para sua assinatura, grupo de recursos e dispositivo. Em seguida, ele aparece na lista de módulos de IoT Edge. Alguns módulos podem exigir configuração adicional. Para obter mais informações, consulte [implantar módulos do Azure Marketplace](how-to-deploy-modules-portal.md#deploy-modules-from-azure-marketplace).

#### <a name="add-a-stream-analytics-module"></a>Adicionar um módulo Stream Analytics

Para adicionar um módulo do Azure Stream Analytics, siga estas etapas:

1. Na seção **Módulos do IoT Edge** da página, clique em **Adicionar**.
1. Selecione **Azure Stream Analytics módulo** no menu suspenso.
1. No painel direito, escolha sua **assinatura**.
1. Escolha seu trabalho do IoT **Edge**.
1. Selecione **Salvar** para adicionar o módulo à implantação.

#### <a name="configure-module-settings"></a>Definir configurações de módulo

Depois de adicionar um módulo a uma implantação, você pode selecionar seu nome para abrir a página **atualizar IOT Edge módulo** . Nessa página, você pode editar as configurações do módulo, as variáveis de ambiente, as opções criar e o módulo. Se você adicionou um módulo do Marketplace, talvez ele já tenha alguns desses parâmetros preenchidos.

Se você estiver criando uma implantação em camadas, poderá estar Configurando um módulo que existe em outras implantações direcionando os mesmos dispositivos. Para atualizar o módulo de texto sem substituir outras versões, abra a guia **configurações de configuração do módulo** . Crie uma nova **Propriedade MyModule** com um nome exclusivo para uma subseção dentro das propriedades desejadas do módulo `properties.desired.settings`r, por exemplo. Se você definir propriedades somente dentro do `properties.desired` campo, ele substituirá as propriedades desejadas para o módulo definido em qualquer implantação de prioridade mais baixa.

![Definir a propriedade addmodule para implantação em camadas](./media/how-to-deploy-monitor/module-twin-property.png)

Para obter mais informações sobre a configuração do módulo "em implantações em camadas", consulte [implantação em camadas](module-deployment-monitoring.md#layered-deployment).

Depois de ter todos os módulos de uma implantação configurada, selecione **Avançar: rotas** para mover para a etapa três.

### <a name="step-3-routes"></a>Etapa 3: rotas

As rotas definem como os módulos se comunicam entre si em uma implantação. Por padrão, o assistente fornece uma rota chamada **upstream** e definido como **de/messages/\* para $upstream**, o que significa que qualquer saída de mensagens por qualquer módulo é enviada para o Hub IOT.  

Adicionar ou atualizar as rotas com informações de [declarar rotas](module-composition.md#declare-routes), em seguida, selecione **próximo** para continuar para a seção de revisão.

Selecione **Avançar: métricas**.

### <a name="step-4-metrics"></a>Etapa 4: métricas

As métricas fornecem contagens de resumos de vários estados em que um dispositivo pode relatar como resultado da aplicação de conteúdo da configuração.

1. Insira um nome para o **nome da métrica**.

1. Insira uma consulta para **Critérios da métrica**. A consulta é baseada nas [propriedades relatadas](module-edgeagent-edgehub.md#edgehub-reported-properties) do módulo gêmeo do hub do IoT Edge. A métrica representa o número de linhas retornadas pela consulta.

   Por exemplo:

   ```sql
   SELECT deviceId FROM devices
     WHERE properties.reported.lastDesiredStatus.code = 200
   ```

Selecione **Avançar: dispositivos de destino**.

### <a name="step-5-target-devices"></a>Etapa 5: dispositivos de destino

Use a propriedade tags dos dispositivos para direcionar os dispositivos específicos que devem receber essa implantação.

Como várias implantações podem direcionar o mesmo dispositivo, você deve atribuir a cada implantação um número de prioridade. Se houver algum conflito, a implantação com a prioridade mais alta (valores maiores indicam prioridade mais alta) vence. Se duas implantações tiverem o mesmo número de prioridade, aquela que foi criada mais recentemente ganhará.

Se várias implantações tiverem como destino o mesmo dispositivo, somente aquela com a prioridade mais alta será aplicada. Se várias implantações em camadas tiverem como destino o mesmo dispositivo, elas serão todas aplicadas. No entanto, se alguma das propriedades for duplicada, como se houver duas rotas com o mesmo nome, a da implantação em camadas de prioridade mais alta substituirá o restante.

Qualquer implantação em camadas direcionada a um dispositivo deve ter uma prioridade mais alta do que a implantação base para ser aplicada.

1. Insira um inteiro positivo para a **Prioridade** da implantação.
1. Insira uma **Condição de destino** para determinar quais dispositivos serão direcionados com essa implantação.A condição se baseia nas marcas do dispositivo gêmeo ou propriedades reportadas do dispositivo gêmeo e deve corresponder ao formato da expressão.Por exemplo, `tags.environment='test'` ou `properties.reported.devicemodel='4000x'`.

Selecione **Avançar: revisar + criar** para passar para a etapa final.

### <a name="step-6-review-and-create"></a>Etapa 6: examinar e criar

Examine as informações de implantação e, em seguida, selecione **criar**.

Para monitorar a implantação, consulte [monitorar implantações de IOT Edge](how-to-monitor-iot-edge-deployments.md).

## <a name="modify-a-deployment"></a>Modificar uma implantação

Quando você modifica uma implantação, as alterações são replicadas imediatamente para todos os dispositivos direcionados. Você pode modificar as seguintes configurações e recursos para uma implantação existente:

* Condições de destino
* Métricas personalizadas
* Rótulos
* Marcas
* Propriedades desejadas

### <a name="modify-target-conditions-custom-metrics-and-labels"></a>Modificar condições de destino, métricas personalizadas e rótulos

1. No Hub IoT, selecione **IOT Edge** no menu do painel esquerdo.
1. Selecione a guia **implantações IOT Edge** e, em seguida, selecione a implantação que deseja configurar.
1. Selecione a guia **condição de destino** . Altere a condição de **destino** para direcionar os dispositivos pretendidos. Você também pode ajustar a **prioridade**.  Clique em **Salvar**.

    Se você atualizar a condição de destino, ocorrerão as seguintes atualizações:

    * Caso um dispositivo não tenha atendido à condição de destino antiga, mas atenda à nova condição de destino e essa implantação tiver a prioridade mais alta para o dispositivo, essa implantação será aplicada ao dispositivo.
    * Caso um dispositivo que executa essa implantação no momento não atenda mais à condição de destino, ele desinstalará essa implantação e usará a próxima implantação com a prioridade mais alta.
    * Caso um dispositivo que executa essa implantação no momento não atenda mais à condição de destino e não atenda à condição de destino de todas as outras implantações, nenhuma alteração ocorrerá no dispositivo. O dispositivo continua executando seus módulos atuais em seu estado atual, mas não é mais gerenciado como parte dessa implantação. Depois que ele atende à condição de destino de qualquer outra implantação, ele desinstala essa implantação e usa a nova.

1. Selecione a guia **métricas** e clique no botão **Editar métricas** . Adicione ou modifique métricas personalizadas usando a sintaxe de exemplo como um guia. Clique em **Salvar**.

    ![Editar métricas personalizadas em uma implantação](./media/how-to-deploy-monitor/metric-list.png)

1. Selecione a guia **Rótulos** e faça as alterações desejadas e selecione **salvar**.

## <a name="delete-a-deployment"></a>Excluir uma implantação

Quando você exclui uma implantação, todos os dispositivos implantados assumem a próxima implantação de prioridade mais alta. Se os dispositivos não atenderem à condição de destino de qualquer outra implantação, os módulos não serão removidos quando a implantação for excluída.

1. Entre no [portal do Azure](https://portal.azure.com) e navegue até o seu Hub IoT.
1. Selecione **IoT Edge**.
1. Selecione a guia **Implantações IOT Edge** .

   ![Exibir implantações do IoT Edge](./media/how-to-deploy-monitor/iot-edge-deployments.png)

1. Use a caixa de seleção para selecionar a implantação que deseja excluir.
1. Selecione **Excluir**.
1. Um prompt informará que essa ação excluirá essa implantação e reverterá para o estado anterior de todos os dispositivos.Uma implantação com prioridade mais baixa será aplicada.Se nenhuma outra implantação for direcionada, nenhum módulo será removido. Se desejar remover todos os módulos do seu dispositivo, crie uma implantação com zero módulos e implante-a nos mesmos dispositivos.Selecione **Sim** para continuar.

## <a name="next-steps"></a>Próximas etapas

Saiba mais sobre [a implantação de módulos em dispositivos IOT Edge](module-deployment-monitoring.md).
