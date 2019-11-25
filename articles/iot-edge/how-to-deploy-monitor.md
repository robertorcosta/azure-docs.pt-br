---
title: Criar implantações automáticas do portal do Azure - Azure IoT Edge | Microsoft Docs
description: Usar o portal do Azure para criar dispositivos de implantações automáticas para grupos do IoT Edge
keywords: ''
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 06/17/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 286bab7b7fdbe42190c32dabb42c59d6fc094b2a
ms.sourcegitcommit: 12d902e78d6617f7e78c062bd9d47564b5ff2208
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/24/2019
ms.locfileid: "74457356"
---
# <a name="deploy-and-monitor-iot-edge-modules-at-scale-using-the-azure-portal"></a>Implante e monitore módulos de IoT Edge em escala usando o portal do Azure

Create an **IoT Edge automatic deployment** in the Azure portal to manage ongoing deployments for many devices at once. Automatic deployments for IoT Edge are part of the [automatic device management](/azure/iot-hub/iot-hub-automatic-device-management) feature of IoT Hub. Deployments are dynamic processes that enable you to deploy multiple modules to multiple devices, track the status and health of the modules, and make changes when necessary. 

For more information, see [Understand IoT Edge automatic deployments for single devices or at scale](module-deployment-monitoring.md).

## <a name="identify-devices-using-tags"></a>Identificar dispositivos usando marcações

Antes de criar uma implantação, você precisa conseguir especificar quais dispositivos você deseja afetar. O Azure IoT Edge identifica os dispositivos usando **marcações** no dispositivo gêmeo. Each device can have multiple tags that you define in any way that makes sense for your solution. Por exemplo, se você gerencia um campus de edifícios inteligentes, poderá adicionar as seguintes marcações a um dispositivo:

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

Para obter mais informações sobre tags e gêmeos de dispositivos, consulte [Entender e usar gêmeos de dispositivos no Hub IoT](../iot-hub/iot-hub-devguide-device-twins.md).

## <a name="create-a-deployment"></a>Criar uma implantação

1. No [portal do Azure](https://portal.azure.com), vá para o hub IoT. 
1. Selecione **IoT Edge**.
1. Selecione **Adicionar implantação do IoT Edge**.

Há cinco etapas para criar uma implantação. As seções a seguir explicam cada uma delas. 

### <a name="step-1-name-and-label"></a>Etapa 1: Nome e rótulo

1. Dê à sua implantação um nome exclusivo com até 128 letras minúsculas. Evite usar espaços e os seguintes caracteres inválidos: `& ^ [ ] { } \ | " < > /`.
1. Você pode adicionar rótulos como pares chave-valor para ajudar a acompanhar suas implantações. Por exemplo, **HostPlatform** e **Linux** ou **Versão** e **3.0.1**.
1. Selecione **Avançar** para ir para a etapa 2. 

### <a name="step-2-add-modules-optional"></a>Etapa 2: Adicionar módulos (opcional)

You can add up to 20 modules to a deployment. 

If you create a deployment with no modules, it removes any current modules from the target devices. 

Para adicionar um módulo do Azure Stream Analytics, siga estas etapas:

1. Na seção **Módulos de Implantação** da página, clique em **Adicionar**.
1. Selecione **módulo do Azure Stream Analytics**.
1. Escolha uma **Assinatura** no menu de lista suspensa.
1. Choose your IoT **Edge job** from the drop-down menu.
1. Selecione **Salvar** para adicionar o módulo à implantação. 

Para adicionar um código personalizado como um módulo ou para adicionar um módulo de serviço do Azure manualmente, siga estas etapas:

1. Na seção **Configurações de Registro de Contêiner** da página, forneça os nomes e as credenciais para os registros de contêiner privados que contêm as imagens de módulo dessa implantação. The IoT Edge Agent will report error 500 if it can't find the container registry credential for a Docker image.
1. Na seção **Módulos de Implantação** da página, clique em **Adicionar**.
1. Selecione **Módulo IoT Edge**.
1. Dê um **Nome** ao módulo.
1. No campo **URI da Imagem**, insira a imagem de contêiner para o módulo. 
1. Especifique qualquer **Opção de criação de contêiner** que deverá ser passada para o contêiner. Para obter mais informações, consulte [criar docker](https://docs.docker.com/engine/reference/commandline/create/).
1. Use o menu suspenso para selecionar uma **Política de reinicialização**. Escolha uma das seguintes opções: 
   * **Sempre** – o módulo sempre é reiniciado se é desligado por qualquer motivo.
   * **never** - The module never restarts if it shuts down for any reason.
   * **on-failure** - The module restarts if it crashes, but not if it shuts down cleanly. 
   * **on-unhealthy** - The module restarts if it crashes or returns an unhealthy status. Cabe a cada módulo implementar a função de status da integridade. 
1. Use o menu suspenso para selecionar o **Status** desejado do módulo. Escolha uma das seguintes opções:
   * **running** - Running is the default option. O módulo será iniciado imediatamente depois de ser implantado.
   * **stopped** - After being deployed, the module will remain idle until called upon to start by you or another module.
1. Selecione **Definir propriedades desejadas do módulo gêmeo** se você quiser adicionar marcas ou outras propriedades ao módulo gêmeo.
1. Insira **Variáveis de Ambiente** para este módulo. Environment variables provide configuration information to a module.
1. Selecione **Salvar** para adicionar o módulo à implantação. 

Depois de configurar todos os módulos para uma implantação, selecione **Avançar** para ir para a etapa 3.

### <a name="step-3-specify-routes-optional"></a>Etapa 3: Especificar rotas (opcional)

As rotas definem como os módulos se comunicam entre si em uma implantação. Por padrão, o assistente fornece uma rota chamada **rota** e definida como **FROM /* INTO $upstream**, que significa que quaisquer mensagens de saída por quaisquer módulos são enviadas para o seu Hub IoT.  

Adicionar ou atualizar as rotas com informações de [declarar rotas](module-composition.md#declare-routes), em seguida, selecione **próximo** para continuar para a seção de revisão.

### <a name="step-4-specify-metrics-optional"></a>Step 4: Specify Metrics (optional)

As métricas fornecem contagens de resumos de vários estados em que um dispositivo pode relatar como resultado da aplicação de conteúdo da configuração.

1. Insira um nome para **nome da métrica**.

1. Insira uma consulta para **Critérios da métrica**. A consulta é baseada nas [propriedades relatadas](module-edgeagent-edgehub.md#edgehub-reported-properties) do módulo gêmeo do hub do IoT Edge. A métrica representa o número de linhas retornadas pela consulta.

   Por exemplo:

   ```sql
   SELECT deviceId FROM devices
     WHERE properties.reported.lastDesiredStatus.code = 200
   ```

### <a name="step-5-target-devices"></a>Step 5: Target Devices

Use a propriedade tags dos dispositivos para direcionar os dispositivos específicos que devem receber essa implantação. 

Como várias implantações podem direcionar o mesmo dispositivo, você deve atribuir a cada implantação um número de prioridade. If there's ever a conflict, the deployment with the highest priority (larger values indicate higher priority) wins. Se duas implantações tiverem o mesmo número de prioridade, aquela que foi criada mais recentemente ganhará. 

1. Insira um inteiro positivo para a **Prioridade** da implantação.
1. Insira uma **Condição de destino** para determinar quais dispositivos serão direcionados com essa implantação. The condition is based on device twin tags or device twin reported properties and should match the expression format. For example, `tags.environment='test'` or `properties.reported.devicemodel='4000x'`. 
1. Selecione **Avançar** para ir para a etapa final.

### <a name="step-6-review-deployment"></a>Step 6: Review Deployment

Examine as informações da implantação e, em seguida, selecione **Enviar**.

## <a name="deploy-modules-from-azure-marketplace"></a>Implantar módulos do Azure Marketplace

O Azure Marketplace é um marketplace online de aplicativos e serviços onde você pode procurar por uma ampla variedade de aplicativos e soluções corporativas que são certificadas e otimizadas para execução no Azure, incluindo [módulos do IoT Edge](https://azuremarketplace.microsoft.com/marketplace/apps/category/internet-of-things?page=1&subcategories=iot-edge-modules). O Azure Marketplace também pode ser acessado pelo portal do Azure em **Criar um Recurso**.

You can deploy an IoT Edge module from either Azure Marketplace or the Azure portal:

1. Localize um módulo e inicie o processo de implantação.

   * Azure portal: Find a module and select **Create**.

   * Azure Marketplace:

     1. Encontre um módulo e selecione **Obter agora**.
     1. Confirme os termos de uso e a política de privacidade do provedor selecionando **Continuar**.

1. Escolha sua assinatura e o Hub IoT ao qual o dispositivo de destino está anexado.

1. Escolha **Implantar em Escala**.

1. Escolha se deseja adicionar o módulo a uma nova implantação ou a um clone de uma implantação existente; se for usar o clone, selecione a implantação existente na lista.

1. Selecione **Criar** para continuar o processo de criação de uma implantação em escala. Você poderá especificar os mesmos detalhes como faria em qualquer implantação.

## <a name="monitor-a-deployment"></a>Monitorar uma implantação

Para exibir os detalhes de uma implantação e monitorar os dispositivos que a executam, use as seguintes etapas:

1. Entre no [Portal do Azure](https://portal.azure.com) e navegue até o Hub IoT. 
1. Selecione **IoT Edge**.
1. Selecione **Implantações do IoT Edge**. 

   ![Exibir implantações do IoT Edge](./media/how-to-deploy-monitor/iot-edge-deployments.png)

1. Inspecione a lista de implantação. For each deployment, you can view the following details:
   * **ID** – o nome da implantação.
   * **Condição de destino** – a marcação usada para definir os dispositivos direcionados.
   * **Prioridade** – o número de prioridade atribuído à implantação.
   * **Métricas de sistema** - **Desejados** especifica o número de dispositivos gêmeos em Hub IoT que batem com a condição desejada, e **Aplicados** especifica o número de dispositivos que tiveram o conteúdo de implantação aplicado aos seus módulos gêmeos no Hub IoT. 
   * **Device metrics** - the number of IoT Edge devices in the deployment reporting success or errors from the IoT Edge client runtime.
   * **Custom metrics** - the number of IoT Edge devices in the deployment reporting data for any metrics that you defined for the deployment.
   * **Hora de criação** – o carimbo de data/hora de quando a implantação foi criada. Esse carimbo de data/hora é usado para desempate quando duas implantações têm a mesma prioridade. 
1. Selecione a implantação que deseja monitorar.  
1. Inspecione os detalhes da implantação. Você pode usar guias para revisar os detalhes da implantação.

## <a name="modify-a-deployment"></a>Modificar uma implantação

Quando você modifica uma implantação, as alterações são replicadas imediatamente para todos os dispositivos direcionados. 

Se você atualizar a condição de destino, ocorrerão as seguintes atualizações:

* Caso um dispositivo não tenha atendido à condição de destino antiga, mas atenda à nova condição de destino e essa implantação tiver a prioridade mais alta para o dispositivo, essa implantação será aplicada ao dispositivo. 
* Caso um dispositivo que executa essa implantação no momento não atenda mais à condição de destino, ele desinstalará essa implantação e usará a próxima implantação com a prioridade mais alta. 
* Caso um dispositivo que executa essa implantação no momento não atenda mais à condição de destino e não atenda à condição de destino de todas as outras implantações, nenhuma alteração ocorrerá no dispositivo. O dispositivo continua executando seus módulos atuais em seu estado atual, mas não é mais gerenciado como parte dessa implantação. Depois que ele atende à condição de destino de qualquer outra implantação, ele desinstala essa implantação e usa a nova. 

Para modificar uma implantação, use as seguintes etapas: 

1. Entre no [Portal do Azure](https://portal.azure.com) e navegue até o Hub IoT. 
1. Selecione **IoT Edge**.
1. Selecione **Implantações do IoT Edge**. 

   ![Exibir implantações do IoT Edge](./media/how-to-deploy-monitor/iot-edge-deployments.png)

1. Selecione a implantação que deseja modificar. 
1. Faça atualizações nos seguintes campos: 
   * Condição de destino
   * Métricas – você pode modificar ou excluir as métricas que definiu ou adicionar novas métricas.
   * Rótulos
   * Prioridade
1. Clique em **Salvar**.
1. Siga as etapas em [monitorar uma implantação](#monitor-a-deployment) para observar as alterações a distribuir. 

## <a name="delete-a-deployment"></a>Excluir uma implantação

Quando você exclui uma implantação, todos os dispositivos usam sua próxima implantação com a prioridade mais alta. Se os dispositivos não atenderem à condição de destino de qualquer outra implantação, os módulos não serão removidos quando a implantação for excluída. 

1. Entre no [Portal do Azure](https://portal.azure.com) e navegue até o Hub IoT. 
1. Selecione **IoT Edge**.
1. Selecione **Implantações do IoT Edge**. 

   ![Exibir implantações do IoT Edge](./media/how-to-deploy-monitor/iot-edge-deployments.png)

1. Use a caixa de seleção para selecionar a implantação que deseja excluir. 
1. Selecione **Excluir**.
1. Um prompt informará que essa ação excluirá essa implantação e reverterá para o estado anterior de todos os dispositivos.  This means that a deployment with a lower priority will apply.  If no other deployment is targeted, no modules will be removed. Se desejar remover todos os módulos do seu dispositivo, crie uma implantação com zero módulos e implante-a nos mesmos dispositivos. Select **Yes** to continue. 

## <a name="next-steps"></a>Próximos passos

Learn more about [Deploying modules to IoT Edge devices](module-deployment-monitoring.md).
