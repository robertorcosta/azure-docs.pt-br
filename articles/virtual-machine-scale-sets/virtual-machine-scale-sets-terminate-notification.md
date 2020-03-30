---
title: Encerrar a notificação para as instâncias de conjunto de escala de máquina virtual do Azure
description: Saiba como ativar a notificação de rescisão para instâncias de conjunto de escala de máquina virtual do Azure
author: avirishuv
tags: azure-resource-manager
ms.service: virtual-machine-scale-sets
ms.topic: conceptual
ms.date: 02/26/2020
ms.author: avverma
ms.openlocfilehash: 6023e9bf7539b79446d0135ba731b61be166dd6e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79250745"
---
# <a name="terminate-notification-for-azure-virtual-machine-scale-set-instances"></a>Encerrar a notificação para as instâncias de conjunto de escala de máquina virtual do Azure
As instâncias de definição de escala podem optar por receber notificações de rescisão de instância e definir um tempo de atraso pré-definido para a operação de término. A notificação de rescisão é enviada através do Azure Metadata Service – [Eventos Programados](../virtual-machines/windows/scheduled-events.md), que fornece notificações e atraso de operações impactantes, como reinicializações e reimplantação. A solução adiciona outro evento – Terminar – à lista de Eventos Agendados, e o atraso associado do evento encerrado dependerá do limite de atraso especificado pelos usuários em suas configurações de modelo de conjunto de escalas.

Uma vez inscritos no recurso, as instâncias de conjunto de escala não precisam esperar que o tempo de intervalo especificado expire antes que a instância seja excluída. Após receber uma notificação de término, a instância pode optar por ser excluída a qualquer momento antes que o tempo de término expire.

## <a name="enable-terminate-notifications"></a>Habilite notificações de término
Existem várias maneiras de habilitar notificações de rescisão em instâncias de conjunto de escala, conforme detalhado nos exemplos abaixo.

### <a name="azure-portal"></a>Portal do Azure

As etapas a seguir permitem encerrar a notificação ao criar um novo conjunto de escalas. 

1. Vá para **conjuntos de escala de máquinas virtuais**.
1. Selecione **+ Adicione** para criar um novo conjunto de escalas.
1. Vá para a guia **De Gestão.** 
1. Localize a seção **Demissão de Ocorrência.**
1. Por **exemplo, notificação de rescisão,** selecione **Em**.
1. Para **o atraso de rescisão (minutos)**, defina o tempo de intervalo padrão desejado.
1. Quando terminar de criar o novo conjunto de escalas, selecione **'Revisar + criar'.** 

> [!NOTE]
> Você não pode definir notificações de término em conjuntos de escala existentes no portal Azure

### <a name="rest-api"></a>API REST

O exemplo a seguir permite encerrar a notificação no modelo de conjunto de escalas.

```
PUT on `/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/virtualMachineScaleSets/{vmScaleSetName}?api-version=2019-03-01`
```

```json
{
  "properties": {
    "virtualMachineProfile": {
            "scheduledEventsProfile": {
                "terminateNotificationProfile": {
                    "notBeforeTimeout":"PT5M",
                    "enable":true
                }
            }
        }
    }        
}

```

O bloco acima especifica um atraso de tempo de 5 minutos (conforme indicado pelo *PT5M)* para qualquer operação de término em todas as instâncias do seu conjunto de escalas. O campo *nãoBeforeTimeout* pode levar qualquer valor entre 5 e 15 minutos no formato ISO 8601. Você pode alterar o tempo de tempo padrão para a operação encerrar modificando a propriedade *notBeforeTimeout* em *'terminateNotificationProfile'* descrito acima.

Depois de habilitar *o programaagendarEventosPerfil* no modelo de conjunto de escalas e definir o *notBeforeTimeout,* atualize as instâncias individuais para o [modelo mais recente](virtual-machine-scale-sets-upgrade-scale-set.md#how-to-bring-vms-up-to-date-with-the-latest-scale-set-model) para refletir as alterações.

> [!NOTE]
>Encerrar notificações em instâncias de conjunto de escala só pode ser habilitado com a versão aPI 2019-03-01 e acima

### <a name="azure-powershell"></a>Azure PowerShell
Ao criar um novo conjunto de escalas, você pode habilitar notificações de terminação na escala definida usando o cmdlet [New-AzVmssConfig.](/powershell/module/az.compute/new-azvmssconfig)

Este script de exemplo percorre a criação de um conjunto de escalas e recursos associados usando o arquivo de configuração: [Crie um conjunto completo de escala de máquina virtual](./scripts/powershell-sample-create-complete-scale-set.md). Você pode fornecer a notificação configurar término adicionando os parâmetros *TerminateScheduledEvents* e *TerminateScheduledEventNotBeforeTimeoutInMinutes* ao objeto de configuração para criar o conjunto de escalas. O exemplo a seguir permite o recurso com um tempo de atraso de 10 minutos.

```azurepowershell-interactive
New-AzVmssConfig `
  -Location "VMSSLocation" `
  -SkuCapacity 2 `
  -SkuName "Standard_DS2" `
  -UpgradePolicyMode "Automatic" `
  -TerminateScheduledEvents $true `
  -TerminateScheduledEventNotBeforeTimeoutInMinutes 10
```

Use o [cmdlet Update-AzVmss](/powershell/module/az.compute/update-azvmss) para habilitar notificações de terminação em um conjunto de escalas existente.

```azurepowershell-interactive
Update-AzVmss `
  -ResourceGroupName "myResourceGroup" `
  -VMScaleSetName "myScaleSet" `
  -TerminateScheduledEvents $true
  -TerminateScheduledEventNotBeforeTimeoutInMinutes 15
```
O exemplo acima permite encerrar notificações em um conjunto de escala existente e define um intervalo de 15 minutos para o evento encerrado.

Depois de habilitar eventos agendados no modelo de conjunto de escalas e definir o tempo máximo, atualize as instâncias individuais para o [modelo mais recente](virtual-machine-scale-sets-upgrade-scale-set.md#how-to-bring-vms-up-to-date-with-the-latest-scale-set-model) para refletir as alterações.

### <a name="azure-cli-20"></a>CLI do Azure 2.0

O exemplo a seguir é para ativar a notificação de rescisão ao criar um novo conjunto de escalas.

```azurecli-interactive
az group create --name <myResourceGroup> --location <VMSSLocation>
az vmss create \
  --resource-group <myResourceGroup> \
  --name <myVMScaleSet> \
  --image UbuntuLTS \
  --admin-username <azureuser> \
  --generate-ssh-keys \
  --terminate-notification-time 10
```

O exemplo acima cria primeiro um grupo de recursos e, em seguida, cria um novo conjunto de escala sustais com notificações de término ativadas para um tempo de 10 minutos padrão.

O exemplo a seguir é para habilitar a notificação de rescisão em um conjunto de escalas existente.

```azurecli-interactive
az vmss update \  
  --resource-group <myResourceGroup> \
  --name <myVMScaleSet> \
  --enable-terminate-notification true \
  --terminate-notification-time 10
```

## <a name="get-terminate-notifications"></a>Receba notificações de término

As notificações de término são entregues através [de Eventos Agendados](../virtual-machines/windows/scheduled-events.md), que é um Serviço de Metadados do Azure. O Serviço de Metadados do Azure expõe informações sobre a execução de Máquinas Virtuais usando um Ponto de Extremidade REST acessível de dentro da VM. As informações estão disponíveis através de um IP não routable para que não seja exposto fora da VM.

Eventos agendados está habilitado para sua escala definida na primeira vez que você faz uma solicitação de eventos. Você pode esperar uma resposta atrasada em sua primeira chamada de até dois minutos. Consulta o ponto final periodicamente para detectar os próximos eventos de manutenção e o status das atividades de manutenção em andamento.

Eventos agendados são desativados para sua escala definida se as instâncias de conjunto de escala não fizerem uma solicitação por 24 horas.

### <a name="endpoint-discovery"></a>Descoberta de endpoint
Para VMs habilitados para VNET, o Serviço de Metadados está disponível a partir de um IP estático não routable, 169.254.169.254.

O ponto de extremidade completo para a versão mais recente dos eventos agendados é:
> 'http://169.254.169.254/metadata/scheduledevents?api-version=2019-01-01'

### <a name="query-response"></a>Resposta de consulta
Uma resposta contém uma matriz de eventos agendados. Uma matriz vazia significa que não há eventos agendados no momento.

No caso de haver eventos agendados, a resposta contém uma matriz de eventos. Para um evento "Terminar", a resposta será a seguinte:
```
{
    "DocumentIncarnation": {IncarnationID},
    "Events": [
        {
            "EventId": {eventID},
            "EventType": "Terminate",
            "ResourceType": "VirtualMachine",
            "Resources": [{resourceName}],
            "EventStatus": "Scheduled",
            "NotBefore": {timeInUTC},
        }
    ]
}
```
O *DocumentIncarnation* é um ETag e fornece uma maneira fácil de inspecionar se a carga de eventos mudou desde a última consulta.

Para obter mais informações sobre cada um dos campos acima, consulte a documentação de Eventos Agendados para [Windows](../virtual-machines/windows/scheduled-events.md#event-properties) e [Linux](../virtual-machines/linux/scheduled-events.md#event-properties).

### <a name="respond-to-events"></a>Responder a eventos
Depois de saber de um evento próximo e concluir sua lógica para o desligamento gracioso, você pode aprovar o evento excepcional fazendo uma chamada POST para o serviço de metadados com o EventId. A chamada POST indica ao Azure que ele pode continuar com a exclusão da VM.

Abaixo está o json esperado no órgão de solicitação do POST. A solicitação deve conter uma lista de Solicitações iniciais. Cada StartRequest contém o EventId para o evento que você deseja agilizar:
```
{
    "StartRequests" : [
        {
            "EventId": {EventId}
        }
    ]
}
```

Certifique-se de que todas as VM no conjunto de escalas estão apenas aprovando o EventID relevante apenas para essa VM. Uma VM pode obter seu próprio nome VM [através de metadados de ocorrência](virtual-machine-scale-sets-instance-ids.md#instance-metadata-vm-name). Esse nome toma o formulário "{scale-set-name}_{instance-id}", e será exibido na seção 'Recursos' da resposta de consulta descrita acima.

Você também pode consultar scripts de amostras para consultar e responder a eventos usando [PowerShell](../virtual-machines/windows/scheduled-events.md#powershell-sample) e [Python](../virtual-machines/linux/scheduled-events.md#python-sample).

## <a name="tips-and-best-practices"></a>Dicas e práticas recomendadas
-   Encerrar as notificações apenas em operações 'excluir' – Todas as operações de exclusão (exclusão manual ou escala iniciada em escala automática) gerarão eventos encerrados se o conjunto de *escalativer programadoEventsProfile* ativado. Outras operações como reinicialização, reimagem, reimplantação e stop/deallocate não geram eventos de término. As notificações de término não podem ser habilitadas para VMs de baixa prioridade.
-   Não há espera obrigatória para o intervalo – Você pode iniciar a operação de término a qualquer momento após o evento ter sido recebido e antes que o tempo não *antes* do evento expirar.
-   Exclusão obrigatória no intervalo – Não há qualquer capacidade de estender o valor do tempo após a geração de um evento. Uma vez que o tempo expirar, o evento de término pendente será processado e a VM será excluída.
-   Valor de tempo mínimo - Você pode modificar o valor de tempo antes de uma instância ser excluída, modificando a propriedade *notBeforeTimeout* no modelo de conjunto de escalas e atualizando as instâncias de VM para o modelo mais recente.
-   Aprove todas as exclusões pendentes – Se houver uma exclusão pendente no VM_1 que não for aprovada, e você tiver aprovado outro evento de término no VM_2, então VM_2 não será excluído até que o evento de término para VM_1 seja aprovado, ou seu tempo decorrido. Depois de aprovar o evento de término para VM_1, VM_1 e VM_2 serão excluídos.
-   Aprovar todas as exclusões simultâneas – Estendendo o exemplo acima, se VM_1 e VM_2 tiver o mesmo *NãoAntes* do tempo, então ambos os eventos encerrados devem ser aprovados ou nem a VM é excluída antes do intervalo expirar.

## <a name="troubleshoot"></a>Solução de problemas
### <a name="failure-to-enable-scheduledeventsprofile"></a>Falha em habilitar agendadoEventosPerfil
Se você receber um erro 'BadRequest' com uma mensagem de erro informando "Não foi possível encontrar 'scheduledEventsProfile' do membro no objeto do tipo 'VirtualMachineProfile'", verifique a versão da API usada para as operações de conjunto de escala. A versão aPI de computação **2019-03-01** ou superior é necessária. 

### <a name="failure-to-get-terminate-events"></a>Falha em obter eventos de término
Se você não estiver recebendo nenhum evento **de término** através de Eventos Agendados, verifique a versão de API usada para obter os eventos. A versão aPI do Serviço de Metadados **2019-01-01 ou** superior é necessária para eventos encerrados.
>'http://169.254.169.254/metadata/scheduledevents?api-version=2019-01-01'

### <a name="getting-terminate-event-with-incorrect-notbefore-time"></a>Obtendo evento de término com notbefore tempo incorreto  
Depois de habilitar *o programaagendarEventosPerfil* no modelo de conjunto de escalas e definir o *notBeforeTimeout,* atualize as instâncias individuais para o [modelo mais recente](virtual-machine-scale-sets-upgrade-scale-set.md#how-to-bring-vms-up-to-date-with-the-latest-scale-set-model) para refletir as alterações.

## <a name="next-steps"></a>Próximas etapas
Aprenda como [Implantar o aplicativo](virtual-machine-scale-sets-deploy-app.md) em conjuntos de dimensionamento de máquinas virtuais
