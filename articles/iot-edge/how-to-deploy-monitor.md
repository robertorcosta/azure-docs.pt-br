---
title: Implantar módulos em escala no portal Azure - Azure IoT Edge
description: Usar o portal do Azure para criar dispositivos de implantações automáticas para grupos do IoT Edge
keywords: ''
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 12/30/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 0a20ea4236683e26c51bc75309435c65e24271d7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79271428"
---
# <a name="deploy-and-monitor-iot-edge-modules-at-scale-using-the-azure-portal"></a>Implante e monitore módulos de IoT Edge em escala usando o portal do Azure

Crie uma **implantação automática do IoT Edge** no portal Azure para gerenciar implantações contínuas para muitos dispositivos ao mesmo tempo. As implantações automáticas para IoT Edge fazem parte do recurso automático de gerenciamento de [dispositivos](/azure/iot-hub/iot-hub-automatic-device-management) do IoT Hub. Implantações são processos dinâmicos que permitem implantar vários módulos em vários dispositivos, acompanhar o status e a saúde dos módulos e fazer alterações quando necessário.

Para obter mais informações, consulte [Entenda implantações automáticas do IoT Edge para dispositivos únicos ou em escala](module-deployment-monitoring.md).

## <a name="identify-devices-using-tags"></a>Identificar dispositivos usando marcações

Antes de criar uma implantação, você precisa conseguir especificar quais dispositivos você deseja afetar. O Azure IoT Edge identifica os dispositivos usando **marcações** no dispositivo gêmeo. Cada dispositivo pode ter várias tags que você define de qualquer forma que faça sentido para sua solução.

Por exemplo, se você gerenciar um campus de edifícios inteligentes, você pode adicionar marcas de localização, tipo de quarto e ambiente a um dispositivo:

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

O IoT Edge fornece dois tipos diferentes de implantações automáticas que você pode usar para personalizar seu cenário. Você pode criar uma *implantação*padrão, que inclui os módulos de tempo de execução do sistema e quaisquer módulos e rotas adicionais. Cada dispositivo só pode aplicar uma implantação. Ou você pode criar uma *implantação em camadas,* que inclui apenas módulos e rotas personalizadas, não o tempo de execução do sistema. Muitas implantações em camadas podem ser combinadas em um dispositivo, além de uma implantação padrão. Para obter mais informações sobre como os dois tipos de implantações automáticas funcionam em conjunto, consulte [Entenda implantações automáticas do IoT Edge para dispositivos únicos ou em escala](module-deployment-monitoring.md).

As etapas para criar uma implantação e uma implantação em camadas são muito semelhantes. Quaisquer diferenças são chamadas nas etapas seguintes.

1. No [portal Azure,](https://portal.azure.com)vá para o seu Hub de IoT.
1. No menu no painel esquerdo, selecione **IoT Edge** em **Automatic Device Management**.
1. Na barra superior, selecione **Criar implantação** ou criar implantação **em camadas**.

Há cinco etapas para criar uma implantação. As seções a seguir explicam cada uma delas.

### <a name="step-1-name-and-label"></a>Passo 1: Nome e rótulo

1. Dê à sua implantação um nome exclusivo com até 128 letras minúsculas. Evite usar espaços e os seguintes caracteres inválidos: `& ^ [ ] { } \ | " < > /`.
1. Você pode adicionar rótulos como pares chave-valor para ajudar a acompanhar suas implantações. Por exemplo, **HostPlatform** e **Linux** ou **Versão** e **3.0.1**.
1. Selecione **A seguir: Módulos** para passar para a etapa dois.

### <a name="step-2-modules"></a>Passo 2: Módulos

Você pode adicionar até 20 módulos a uma implantação. Se você criar uma implantação sem módulos, ela removerá quaisquer módulos atuais dos dispositivos de destino.

Em implantações, você pode gerenciar as configurações para os módulos de hub IoT Edge e IoT Edge. Selecione **Configurações de tempo de execução** para configurar os dois módulos de tempo de execução. Na implantação em camadas, os módulos de tempo de execução não estão incluídos, portanto, não podem ser configurados.

Você pode adicionar três tipos de módulos:

* Módulo de borda IoT
* Módulo de Marketplace
* Módulo de Análise de Fluxo Azure

#### <a name="add-an-iot-edge-module"></a>Adicione um módulo IoT Edge

Para adicionar um código personalizado como um módulo ou para adicionar um módulo de serviço do Azure manualmente, siga estas etapas:

1. Na seção **Credenciais** de Registro de Contêiner da página, forneça os nomes e credenciais para quaisquer registros privados de contêineres que contenham as imagens do módulo para esta implantação. O IoT Edge Agent reportará erro 500 se não encontrar a credencial de registro de contêiner para uma imagem do Docker.
1. Na seção **Módulos do IoT Edge** da página, clique em **Adicionar**.
1. Selecione **Módulo de Borda IoT** no menu suspenso.
1. Dê ao seu módulo um **nome de módulo de borda IoT**.
1. No campo **URI da Imagem**, insira a imagem de contêiner para o módulo.
1. Use o menu suspenso para selecionar uma **Política de reinicialização**. Escolha uma das seguintes opções:
   * **sempre** - O módulo sempre reinicia se ele desliga por qualquer motivo.
   * **nunca** - O módulo nunca reinicia se ele desliga por qualquer motivo.
   * **on-failure** - O módulo reinicia se falhar, mas não se desligar limpamente.
   * **on-insalubre** - O módulo reinicia se ele trava ou retorna um estado insalubre. Cabe a cada módulo implementar a função de status da integridade.
1. Use o menu suspenso para selecionar o **Status** desejado do módulo. Escolha uma das seguintes opções:
   * **executando** - Correr é a opção padrão. O módulo será iniciado imediatamente depois de ser implantado.
   * **parado** - Após ser implantado, o módulo permanecerá ocioso até ser chamado para iniciar por você ou por outro módulo.
1. Especifique qualquer **Opção de criação de contêiner** que deverá ser passada para o contêiner. Para obter mais informações, consulte [criar docker](https://docs.docker.com/engine/reference/commandline/create/).
1. Selecione **Configurações duplas do módulo** se quiser adicionar tags ou outras propriedades ao módulo gêmeo.
1. Digite **Variáveis de ambiente** para este módulo. As variáveis de ambiente fornecem informações de configuração para um módulo.
1. Selecione **Adicionar** para adicionar seu módulo à implantação.

#### <a name="add-a-module-from-the-marketplace"></a>Adicione um módulo do Marketplace

Para adicionar um módulo do Azure Marketplace, siga estas etapas:

1. Na seção **Módulos do IoT Edge** da página, clique em **Adicionar**.
1. Selecione Módulo de **Marketplace** no menu suspenso.
1. Escolha um módulo na página **IoT Edge Module Marketplace.** O módulo selecionado é configurado automaticamente para sua assinatura, grupo de recursos e dispositivo. Em seguida, ele aparece em sua lista de módulos IoT Edge. Alguns módulos podem exigir configuração adicional. Para obter mais informações, consulte [Implantar módulos do mercado Azure](how-to-deploy-modules-portal.md#deploy-modules-from-azure-marketplace).

#### <a name="add-a-stream-analytics-module"></a>Adicionar um módulo de Stream Analytics

Para adicionar um módulo do Azure Stream Analytics, siga estas etapas:

1. Na seção **Módulos do IoT Edge** da página, clique em **Adicionar**.
1. Selecione **o módulo Azure Stream Analytics** no menu suspenso.
1. No painel direito, escolha sua **Assinatura**.
1. Escolha seu trabalho no IoT **Edge**.
1. Selecione **Salvar** para adicionar o módulo à implantação.

#### <a name="configure-module-settings"></a>Configurar configurações do módulo

Depois de adicionar um módulo a uma implantação, você pode selecionar seu nome para abrir a página **Atualizar módulo de borda ioT.** Nesta página, você pode editar as configurações do módulo, variáveis de ambiente, criar opções e módulo gêmeo. Se você adicionou um módulo do marketplace, ele pode já ter alguns desses parâmetros preenchidos.

Se você estiver criando uma implantação em camadas, você pode estar configurando um módulo que existe em outras implantações direcionadas aos mesmos dispositivos. Para atualizar o módulo gêmeo sem substituir **outras versões,** abra a guia Configurações duplas do módulo. Crie uma nova `properties.desired.settings`Propriedade Gêmea do **Módulo** com um nome exclusivo para uma subseção dentro das propriedades desejadas do módulo gêmeo, por exemplo . Se você definir propriedades `properties.desired` dentro apenas do campo, ele substituirá as propriedades desejadas para o módulo definido em quaisquer implantações de menor prioridade.

![Definir a propriedade twin do módulo para implantação em camadas](./media/how-to-deploy-monitor/module-twin-property.png)

Para obter mais informações sobre a configuração do módulo duplo em implantações em camadas, consulte [implantação em camadas](module-deployment-monitoring.md#layered-deployment).

Depois de ter todos os módulos para uma implantação configurados, selecione **Next: Rotas** para mover-se para a etapa três.

### <a name="step-3-routes"></a>Passo 3: Rotas

As rotas definem como os módulos se comunicam entre si em uma implantação. Por padrão, o assistente lhe dá uma rota chamada **upstream** e definida como **FROM\* /messages/ INTO $upstream,** o que significa que qualquer saída de mensagens por quaisquer módulos são enviadas para o seu hub de IoT.  

Adicionar ou atualizar as rotas com informações de [declarar rotas](module-composition.md#declare-routes), em seguida, selecione **próximo** para continuar para a seção de revisão.

Selecione **A seguir: Métricas**.

### <a name="step-4-metrics"></a>Passo 4: Métricas

As métricas fornecem contagens de resumos de vários estados em que um dispositivo pode relatar como resultado da aplicação de conteúdo da configuração.

1. Digite um nome para **Nome Métrico**.

1. Insira uma consulta para **Critérios da métrica**. A consulta é baseada nas [propriedades relatadas](module-edgeagent-edgehub.md#edgehub-reported-properties) do módulo gêmeo do hub do IoT Edge. A métrica representa o número de linhas retornadas pela consulta.

   Por exemplo: 

   ```sql
   SELECT deviceId FROM devices
     WHERE properties.reported.lastDesiredStatus.code = 200
   ```

Selecione **A seguir: Dispositivos de destino**.

### <a name="step-5-target-devices"></a>Passo 5: Dispositivos-alvo

Use a propriedade tags dos dispositivos para direcionar os dispositivos específicos que devem receber essa implantação.

Como várias implantações podem direcionar o mesmo dispositivo, você deve atribuir a cada implantação um número de prioridade. Se houver um conflito, a implantação com a maior prioridade (valores maiores indicam maior prioridade) ganha. Se duas implantações tiverem o mesmo número de prioridade, aquela que foi criada mais recentemente ganhará.

Se várias implantações atingirem o mesmo dispositivo, apenas aquele com maior prioridade será aplicado. Se várias implantações em camadas atingirem o mesmo dispositivo, todas serão aplicadas. No entanto, se alguma propriedade for duplicada, como se houver duas rotas com o mesmo nome, a da implantação em camadas de maior prioridade sobregrava o resto.

Qualquer implantação em camadas direcionada a um dispositivo deve ter uma prioridade maior do que a implantação da base para ser aplicada.

1. Insira um inteiro positivo para a **Prioridade** da implantação.
1. Insira uma **Condição de destino** para determinar quais dispositivos serão direcionados com essa implantação.A condição se baseia nas marcas do dispositivo gêmeo ou propriedades reportadas do dispositivo gêmeo e deve corresponder ao formato da expressão.Por exemplo, `tags.environment='test'` ou `properties.reported.devicemodel='4000x'`.

Selecione **A seguir: Revisão + Criar** para passar para a etapa final.

### <a name="step-6-review-and-create"></a>Passo 6: Revisar e criar

Revise suas informações de implantação e selecione **Criar**.

## <a name="monitor-a-deployment"></a>Monitorar uma implantação

Para exibir os detalhes de uma implantação e monitorar os dispositivos que a executam, use as seguintes etapas:

1. Entre no [portal do Azure](https://portal.azure.com) e navegue até o seu Hub IoT.
1. Selecione **IoT Edge**.
1. Selecione a guia **implantações ioT Edge.**

   ![Exibir implantações do IoT Edge](./media/how-to-deploy-monitor/iot-edge-deployments.png)

1. Inspecione a lista de implantação.Para cada implantação, você pode exibir os seguintes detalhes:
   * **ID** – o nome da implantação.
   * **Tipo** - o tipo de implantação, **implantação** ou **implantação em camadas**.
   * **Condição de destino** - a tag usada para definir dispositivos-alvo.
   * **Prioridade** – o número de prioridade atribuído à implantação.
   * **Métricas** - do**sistema O Targeted** especifica o número de gêmeos de dispositivos no IoT Hub que correspondem à condição de segmentação, e o **Applied** especifica o número de dispositivos que tiveram o conteúdo de implantação aplicado aos gêmeos de seu módulo no IoT Hub.
   * **Métricas de dispositivos** - o número de dispositivos IoT Edge na implantação relatando sucesso ou erros do tempo de execução do cliente IoT Edge.
   * **Métricas personalizadas** - o número de dispositivos IoT Edge nos dados de relatórios de implantação para quaisquer métricas definidas para a implantação.
   * **Tempo de criação** - o carimbo de tempo de quando a implantação foi criada. Esse carimbo de data/hora é usado para desempate quando duas implantações têm a mesma prioridade.
1. Selecione a implantação que deseja monitorar.  
1. Inspecione os detalhes da implantação. Você pode usar guias para revisar os detalhes da implantação.

## <a name="modify-a-deployment"></a>Modificar uma implantação

Quando você modifica uma implantação, as alterações são replicadas imediatamente para todos os dispositivos direcionados.

Se você atualizar a condição de destino, ocorrerão as seguintes atualizações:

* Caso um dispositivo não tenha atendido à condição de destino antiga, mas atenda à nova condição de destino e essa implantação tiver a prioridade mais alta para o dispositivo, essa implantação será aplicada ao dispositivo.
* Caso um dispositivo que executa essa implantação no momento não atenda mais à condição de destino, ele desinstalará essa implantação e usará a próxima implantação com a prioridade mais alta.
* Caso um dispositivo que executa essa implantação no momento não atenda mais à condição de destino e não atenda à condição de destino de todas as outras implantações, nenhuma alteração ocorrerá no dispositivo. O dispositivo continua executando seus módulos atuais em seu estado atual, mas não é mais gerenciado como parte dessa implantação. Depois que ele atende à condição de destino de qualquer outra implantação, ele desinstala essa implantação e usa a nova.

Para modificar uma implantação, use as seguintes etapas:

1. Entre no [portal do Azure](https://portal.azure.com) e navegue até o seu Hub IoT.
1. Selecione **IoT Edge**.
1. Selecione a guia **Implantações de borda de IoT.**

   ![Exibir implantações do IoT Edge](./media/how-to-deploy-monitor/iot-edge-deployments.png)

1. Selecione a implantação que deseja modificar.
1. Faça atualizações nas seguintes guias:
   * **Condição de destino**
   * **Métricas** - você pode modificar ou excluir métricas que definiu ou adicionar novas.
   * **Rótulos**
   * **Módulos**
   * **Rotas**
   * **Implantação**

1. Selecione **Salvar**.
1. Siga as etapas em [Monitorar uma implantação](#monitor-a-deployment) para observar as alterações serem distribuídas.

## <a name="delete-a-deployment"></a>Excluir uma implantação

Quando você exclui uma implantação, todos os dispositivos implantados assumem sua próxima implantação de alta prioridade. Se os dispositivos não atenderem à condição de destino de qualquer outra implantação, os módulos não serão removidos quando a implantação for excluída.

1. Entre no [portal do Azure](https://portal.azure.com) e navegue até o seu Hub IoT.
1. Selecione **IoT Edge**.
1. Selecione a guia **Implantações de borda de IoT.**

   ![Exibir implantações do IoT Edge](./media/how-to-deploy-monitor/iot-edge-deployments.png)

1. Use a caixa de seleção para selecionar a implantação que deseja excluir.
1. Selecione **Excluir**.
1. Um prompt informará que essa ação excluirá essa implantação e reverterá para o estado anterior de todos os dispositivos.Isso significa que uma implantação com uma prioridade mais baixa será aplicada.Se nenhuma outra implantação for direcionada, nenhum módulo será removido. Se desejar remover todos os módulos do seu dispositivo, crie uma implantação com zero módulos e implante-a nos mesmos dispositivos.Selecione **Sim** para continuar.

## <a name="next-steps"></a>Próximas etapas

Saiba mais sobre [a implantação de módulos para dispositivos IoT Edge](module-deployment-monitoring.md).
