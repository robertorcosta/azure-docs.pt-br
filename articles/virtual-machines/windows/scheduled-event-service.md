---
title: Monitore eventos agendados para suas VMs do Windows no Azure
description: Saiba como monitorar suas máquinas virtuais do Azure para eventos agendados.
services: virtual-machines-windows
documentationcenter: ''
author: mysarn
manager: gwallace
ms.service: virtual-machines-windows
ms.tgt_pltfrm: vm-windows
ms.date: 08/20/2019
ms.author: sarn
ms.topic: conceptual
ms.openlocfilehash: 1cda07c18e4f5ef2a8c00b6a275f22ecc0935751
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "74073308"
---
# <a name="monitoring-scheduled-events"></a>Monitoramento de eventos programados

As atualizações são aplicadas a diferentes partes do Azure todos os dias, para manter os serviços executados neles seguros e atualizados. Além das atualizações planejadas, eventos não planejados também podem ocorrer. Por exemplo, se alguma degradação ou falha de hardware for detectada, os serviços do Azure podem precisar realizar manutenção não planejada. Usando migração ao vivo, atualizações de preservação de memória e geralmente mantendo uma barra rigorosa sobre o impacto das atualizações, na maioria dos casos esses eventos são quase transparentes para os clientes, e eles não têm impacto ou, no máximo, causam alguns segundos de congelamento da máquina virtual. No entanto, para algumas aplicações, mesmo alguns segundos de congelamento da máquina virtual podem causar um impacto. Saber com antecedência sobre a próxima manutenção do Azure é importante, para garantir a melhor experiência para essas aplicações. [O serviço de Eventos Agendados](scheduled-events.md) fornece uma interface programática para ser notificada sobre a próxima manutenção e permite que você manuseie graciosamente a manutenção. 

Neste artigo, mostraremos como você pode usar eventos agendados para ser notificado sobre eventos de manutenção que podem estar afetando suas VMs e construir alguma automação básica que possa ajudar no monitoramento e análise.


## <a name="routing-scheduled-events-to-log-analytics"></a>Roteamento de eventos agendados para O Log Analytics

Eventos agendados estão disponíveis como parte do [Azure Instance Metadata Service](instance-metadata-service.md), que está disponível em todas as máquinas virtuais do Azure. Os clientes podem escrever automação para consultar o ponto final de suas máquinas virtuais para encontrar notificações de manutenção programadas e realizar mitigações, como salvar o estado e tirar a máquina virtual da rotação. Recomendamos a automação predial para registrar os Eventos Agendados para que você possa ter um registro de auditoria de eventos de manutenção do Azure. 

Neste artigo, mostraremos como capturar eventos agendados de manutenção para o Log Analytics. Em seguida, vamos desencadear algumas ações básicas de notificação, como enviar um e-mail para sua equipe e obter uma visão histórica de todos os eventos que afetaram suas máquinas virtuais. Para a agregação e automação do evento, usaremos [o Log Analytics,](/azure/azure-monitor/learn/quick-create-workspace)mas você pode usar qualquer solução de monitoramento para coletar esses logs e ativar a automação.

![Diagrama mostrando o ciclo de vida do evento](./media/notifications/events.png)

## <a name="prerequisites"></a>Pré-requisitos

Para este exemplo, você precisará criar uma [máquina virtual do Windows em um conjunto de disponibilidade](tutorial-availability-sets.md). Eventos agendados fornecem notificações sobre alterações que podem afetar qualquer uma das máquinas virtuais em seu conjunto de disponibilidade, Cloud Service, Virtual Machine Scale Set ou VMs autônomos. Estaremos executando um [serviço](https://github.com/microsoft/AzureScheduledEventsService) que vota para eventos agendados em uma das VMs que atuará como um coletor, para obter eventos para todas as outras VMs no conjunto de disponibilidade.    

Não exclua o grupo de recursos do grupo no final do tutorial.

Você também precisará [criar um espaço de trabalho do Log Analytics](/azure/azure-monitor/learn/quick-create-workspace) que usaremos para agregar informações das VMs no conjunto de disponibilidade.

## <a name="set-up-the-environment"></a>Configurar o ambiente

Agora você deve ter 2 VMs iniciais em um conjunto de disponibilidade. Agora precisamos criar uma 3ª VM, chamada myCollectorVM, no mesmo conjunto de disponibilidade. 

```azurepowershell-interactive
New-AzVm `
   -ResourceGroupName "myResourceGroupAvailability" `
   -Name "myCollectorVM" `
   -Location "East US" `
   -VirtualNetworkName "myVnet" `
   -SubnetName "mySubnet" `
   -SecurityGroupName "myNetworkSecurityGroup" `
   -OpenPorts 3389 `
   -PublicIpAddressName "myPublicIpAddress3" `
   -AvailabilitySetName "myAvailabilitySet" `
   -Credential $cred
```
 

Baixe o arquivo .zip de instalação do projeto do [GitHub](https://github.com/microsoft/AzureScheduledEventsService/archive/master.zip).

Conecte-se ao **myCollectorVM** e copie o arquivo .zip para a máquina virtual e extraia todos os arquivos. Em sua VM, abra um prompt PowerShell. Mova seu prompt para `SchService.ps1`a pasta `PS C:\Users\azureuser\AzureScheduledEventsService-master\AzureScheduledEventsService-master\Powershell>`que contém, por exemplo: , e configure o serviço.

```powershell
.\SchService.ps1 -Setup
```

Inicie o serviço.

```powershell
.\SchService.ps1 -Start
```

O serviço começará agora a votação a cada 10 segundos para quaisquer eventos programados e aprovará os eventos para agilizar a manutenção.  Freeze, Reboot, Redeploy e Preempt são os eventos capturados pelos eventos agendados.   Observe que você pode estender o script para desencadear algumas atenuações antes de aprovar o evento.

Valide o status do serviço e certifique-se de que ele está sendo executado.

```powershell
.\SchService.ps1 -status  
```

Isso deve `Running`voltar.

O serviço começará agora a votação a cada 10 segundos para quaisquer eventos programados e aprovará os eventos para agilizar a manutenção.  Freeze, Reboot, Redeploy e Preempt são os eventos capturados pelos eventos agendados. Você pode estender o script para desencadear algumas atenuações antes de aprovar o evento.

Quando qualquer um dos eventos acima for capturado pelo serviço Agenda Event, ele será registrado no Status do Evento de Registro de Eventos de Inscrição, Tipo de Evento, Recursos (Nomes de máquinas virtuais) e NotBefore (período mínimo de aviso prévio). Você pode localizar os eventos com o ID 1234 no Registro de Eventos do Aplicativo.

Uma vez que o serviço seja configurado e iniciado, ele registrará eventos nos registros do Aplicativo Windows.   Para verificar se isso funciona, reinicie uma das máquinas virtuais no conjunto de disponibilidade e você deve ver um evento sendo registrado no Visualizador de eventos no Windows Logs > log de aplicativo mostrando a VM reiniciada. 

![Captura de tela do visualizador de eventos.](./media/notifications/event-viewer.png)

Quando os eventos são capturados pelo serviço Agenda Event, ele será logado no aplicativo até mesmo registrar com Status do evento, Tipo de Evento, Recursos (nome vM) e NotBefore (período mínimo de aviso prévio). Você pode localizar os eventos com o ID 1234 no Registro de Eventos do Aplicativo.

> [!NOTE] 
> Neste exemplo, as máquinas virtuais estavam em um conjunto de disponibilidade, o que nos permitiu designar uma única máquina virtual como coletor para ouvir e encaminhar eventos agendados para o nosso espaço de trabalho satisfaz o log analytics. Se você tiver máquinas virtuais autônomas, você pode executar o serviço em cada máquina virtual e, em seguida, conectá-las individualmente ao seu espaço de trabalho de análise de log.
>
> Para nossa configuração, escolhemos o Windows, mas você pode projetar uma solução semelhante no Linux.

A qualquer momento você pode parar/remover o Serviço `–stop` `–remove`de Eventos Agendados usando os switches e .

## <a name="connect-to-the-workspace"></a>Conecte-se ao espaço de trabalho


Agora queremos conectar um Espaço de Trabalho do Log Analytics ao Coletor VM. O espaço de trabalho do Log Analytics funciona como um repositório e configuraremos a coleta de registros de eventos para capturar os registros do aplicativo da VM coletora. 

 Para encaminhar os Eventos Agendados para o Registro de Eventos, que serão salvos como log de aplicativo pelo nosso serviço, você precisará conectar sua máquina virtual ao seu espaço de trabalho log analytics.  
 
1. Abra a página para o espaço de trabalho que você criou.
1. Em **Conectar a uma fonte de dados** selecione **máquinas virtuais Azure (VMs)**.

    ![Conecte-se a uma VM como fonte de dados](./media/notifications/connect-to-data-source.png)

1. Procure e selecione **myCollectorVM**. 
1. Na nova página para **myCollectorVM,** selecione **Conectar**.

Isso instalará o [agente Microsoft Monitoring](/azure/virtual-machines/extensions/oms-windows) em sua máquina virtual. Levará alguns minutos para conectar sua VM ao espaço de trabalho e instalar a extensão. 

## <a name="configure-the-workspace"></a>Configure o espaço de trabalho

1. Abra a página para o seu espaço de trabalho e selecione **Configurações avançadas**.
1. Selecione **Dados** no menu esquerdo e selecione **Registros de eventos do Windows**.
1. Em **Coletar a partir dos logs de evento a seguir,** comece a digitar o *aplicativo* e, em seguida, selecione **Aplicativo** na lista.

    ![Selecione configurações avançadas](./media/notifications/advanced.png)

1. Erro **de**saída, **AVISO**e **INFORMAÇÕES selecionadas** e, em seguida, **selecione Salvar** para salvar as configurações.


> [!NOTE]
> Haverá algum atraso, e pode levar até 10 minutos antes que o registro esteja disponível. 


## <a name="creating-an-alert-rule-with-azure-monitor"></a>Criando uma regra de alerta com o Azure Monitor 


Uma vez que os eventos são empurrados para o Log Analytics, você pode executar a seguinte [consulta](/azure/azure-monitor/log-query/get-started-portal) para procurar o cronograma Eventos.

1. Na parte superior da página, **selecione Logs** e cole o seguinte na caixa de texto:

    ```
    Event
    | where EventLog == "Application" and Source contains "AzureScheduledEvents" and RenderedDescription contains "Scheduled" and RenderedDescription contains "EventStatus" 
    | project TimeGenerated, RenderedDescription
    | extend ReqJson= parse_json(RenderedDescription)
    | extend EventId = ReqJson["EventId"]
    ,EventStatus = ReqJson["EventStatus"]
    ,EventType = ReqJson["EventType"]
    ,NotBefore = ReqJson["NotBefore"]
    ,ResourceType = ReqJson["ResourceType"]
    ,Resources = ReqJson["Resources"]
    | project-away RenderedDescription,ReqJson
    ```

1. Selecione **Salvar**e *digite logQuery* para o nome, deixe **a Consulta** como o tipo, digite *VMLogs* como **categoria**e selecione **Salvar**. 

    ![Salvar a consulta](./media/notifications/save-query.png)

1. Selecione **Nova regra de alerta**. 
1. Na página **Criar regra,** deixe `collectorworkspace` como **o Recurso**.
1. Em **Condição,** selecione a entrada *Sempre que a pesquisa de log do cliente for <login undefined> *. A **página configurar a lógica do sinal** será aberta.
1. Em **Valor limite,** digite *0* e selecione **Feito**.
1. Em **Ações,** selecione **Criar grupo de ação**. A página **do grupo de ação Adicionar** será aberta.
1. Em **Nome do grupo Action,** digite *myActionGroup*.
1. Em **nome curto,** digite **myActionGroup**.
1. No **grupo Resource,** selecione **myResourceGroupAvailability**.
1. Em Ações, no tipo **NOME DE AÇÃO** **e-mail**e, em seguida, selecione **E-mail/SMS/Push/Voice**. A página **E-mail/SMS/Push/Voice** será aberta.
1. Selecione **E-mail,** digite seu endereço de e-mail e selecione **OK**.
1. Na página **adicionar grupo de ação,** selecione **OK**. 
1. Na página **Criar regra,** em **DETALHES DE ALERTA,** digite *myAlert* para o nome da **regra Alerta**e digite a regra de alerta de *e-mail* para a **Descrição**.
1. Quando terminar, selecione **Criar regra de alerta**.
1. Reinicie uma das VMs no conjunto de disponibilidade. Dentro de alguns minutos, você deve receber um e-mail que o alerta foi acionado.

Para gerenciar suas regras de alerta, vá para o grupo de recursos, selecione **Alertas** no menu esquerdo e, em seguida, selecione **Gerenciar regras** de alerta na parte superior da página.

     
## <a name="next-steps"></a>Próximas etapas

Para saber mais, consulte a página [de serviço de eventos agendados](https://github.com/microsoft/AzureScheduledEventsService) no GitHub.
