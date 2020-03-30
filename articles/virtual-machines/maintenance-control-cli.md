---
title: Controle de manutenção
description: Saiba como controlar quando a manutenção é aplicada às Suas VMs do Azure usando o Controle de Manutenção.
author: cynthn
ms.service: virtual-machines
ms.topic: article
ms.workload: infrastructure-services
ms.date: 11/21/2019
ms.author: cynthn
ms.openlocfilehash: 58c0964d170f49066802b955f09dab01eaf998a7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79250173"
---
# <a name="preview-control-updates-with-maintenance-control-and-the-azure-cli"></a>Visualização: Atualizações de controle com controle de manutenção e o Cli do Azure

Gerenciar atualizações de plataforma, que não requerem uma reinicialização, usando o controle de manutenção. O Azure atualiza frequentemente sua infra-estrutura para melhorar a confiabilidade, o desempenho, a segurança ou lançar novos recursos. A maioria das atualizações são transparentes para os usuários. Algumas cargas de trabalho sensíveis, como jogos, streaming de mídia e transações financeiras, não podem tolerar nem mesmo alguns segundos de uma VM congelando ou desconectando para manutenção. O controle de manutenção lhe dá a opção de esperar pelas atualizações da plataforma e aplicá-las dentro de uma janela de rolamento de 35 dias. 

O controle de manutenção permite que você decida quando aplicar atualizações às suas VMs isoladas e hosts dedicados do Azure.

Com o controle de manutenção, você pode:
- Atualizações em lote em um pacote de atualização.
- Aguarde até 35 dias para aplicar atualizações. 
- Automatize as atualizações da plataforma para sua janela de manutenção usando funções do Azure.
- As configurações de manutenção funcionam entre assinaturas e grupos de recursos. 

> [!IMPORTANT]
> O Controle de Manutenção está atualmente em visualização pública.
> Essa versão prévia é fornecida sem um contrato de nível de serviço e não é recomendada para cargas de trabalho de produção. Alguns recursos podem não ter suporte ou podem ter restrição de recursos. Para obter mais informações, consulte [Termos de Uso Suplementares para Visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).
>

## <a name="limitations"></a>Limitações

- As VMs devem estar em um [host dedicado](./linux/dedicated-hosts.md)ou serem criadas usando um tamanho [de VM isolado](./linux/isolation.md).
- Após 35 dias, uma atualização será aplicada automaticamente.
- O usuário deve ter acesso **ao Contribuinte de Recursos.**


## <a name="install-the-maintenance-extension"></a>Instale a extensão de manutenção

Se você optar por instalar o [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli) localmente, você precisará da versão 2.0.76 ou posterior.

Instale `maintenance` a extensão CLI de visualização localmente ou no Cloud Shell. 

```azurecli-interactive
az extension add -n maintenance
```


## <a name="create-a-maintenance-configuration"></a>Criar uma configuração de manutenção

Use `az maintenance configuration create` para criar uma configuração de manutenção. Este exemplo cria uma configuração de manutenção chamada *myConfig* escopo para o host. 

```azurecli-interactive
az group create \
   --location eastus \
   --name myMaintenanceRG
az maintenance configuration create \
   -g myMaintenanceRG \
   --name myConfig \
   --maintenanceScope host\
   --location  eastus
```

Copie o ID de configuração da saída para usar mais tarde.

O `--maintenanceScope host` uso garante que a configuração de manutenção seja usada para controlar as atualizações do host.

Se você tentar criar uma configuração com o mesmo nome, mas em um local diferente, você terá um erro. Os nomes de configuração devem ser exclusivos da sua assinatura.

Você pode consultar as configurações `az maintenance configuration list`de manutenção disponíveis usando .

```azurecli-interactive
az maintenance configuration list --query "[].{Name:name, ID:id}" -o table 
```

## <a name="assign-the-configuration"></a>Atribuir a configuração

Use `az maintenance assignment create` para atribuir a configuração ao seu VM isolado ou host dedicado do Azure.

### <a name="isolated-vm"></a>VM isolado

Aplique a configuração em um VM usando o ID da configuração. Especificar `--resource-type virtualMachines` e fornecer o nome `--resource-name`da VM para , e `--resource-group`o grupo de recursos `--location`para a VM em , e a localização da VM para . 

```azurecli-interactive
az maintenance assignment create \
   --resource-group myMaintenanceRG \
   --location eastus \
   --resource-name myVM \
   --resource-type virtualMachines \
   --provider-name Microsoft.Compute \
   --configuration-assignment-name myConfig \
   --maintenance-configuration-id "/subscriptions/1111abcd-1a11-1a2b-1a12-123456789abc/resourcegroups/myMaintenanceRG/providers/Microsoft.Maintenance/maintenanceConfigurations/myConfig"
```

### <a name="dedicated-host"></a>Host dedicado

Para aplicar uma configuração a um host `--resource-type hosts` `--resource-parent-name` dedicado, você precisa incluir `--resource-parent-type hostGroups`, com o nome do grupo host, e . 

O parâmetro `--resource-id` é o ID do hospedeiro. Você pode usar [az vm host get-instance-view](/cli/azure/vm/host#az-vm-host-get-instance-view) para obter o ID do seu host dedicado.

```azurecli-interactive
az maintenance assignment create \
   -g myDHResourceGroup \
   --resource-name myHost \
   --resource-type hosts \
   --provider-name Microsoft.Compute \
   --configuration-assignment-name myConfig \
   --maintenance-configuration-id "/subscriptions/1111abcd-1a11-1a2b-1a12-123456789abc/resourcegroups/myDhResourceGroup/providers/Microsoft.Maintenance/maintenanceConfigurations/myConfig" \
   -l eastus \
   --resource-parent-name myHostGroup \
   --resource-parent-type hostGroups 
```

## <a name="check-configuration"></a>Verificar a configuração

Você pode verificar se a configuração foi aplicada corretamente ou verificar `az maintenance assignment list`qual configuração é aplicada atualmente usando .

### <a name="isolated-vm"></a>VM isolado

```azurecli-interactive
az maintenance assignment list \
   --provider-name Microsoft.Compute \
   --resource-group myMaintenanceRG \
   --resource-name myVM \
   --resource-type virtualMachines \
   --query "[].{resource:resourceGroup, configName:name}" \
   --output table
```

### <a name="dedicated-host"></a>Host dedicado 

```azurecli-interactive
az maintenance assignment list \
   --resource-group myDHResourceGroup \
   --resource-name myHost \
   --resource-type hosts \
   --provider-name Microsoft.Compute \
   --resource-parent-name myHostGroup \
   --resource-parent-type hostGroups 
   --query "[].{ResourceGroup:resourceGroup,configName:name}" \
   -o table
```


## <a name="check-for-pending-updates"></a>Verifique se há atualizações pendentes

Use `az maintenance update list` para ver se há atualizações pendentes. Atualizar --assinatura para ser o ID da assinatura que contém a VM.

Se não houver atualizações, o comando retornará uma mensagem `Resource not found...StatusCode: 404`de erro, que conterá o texto: .

Se houver atualizações, apenas uma será devolvida, mesmo que haja várias atualizações pendentes. Os dados desta atualização serão devolvidos em um objeto:

```text
[
  {
    "impactDurationInSec": 9,
    "impactType": "Freeze",
    "maintenanceScope": "Host",
    "notBefore": "2020-03-03T07:23:04.905538+00:00",
    "resourceId": "/subscriptions/9120c5ff-e78e-4bd0-b29f-75c19cadd078/resourcegroups/DemoRG/providers/Microsoft.Compute/hostGroups/demoHostGroup/hosts/myHost",
    "status": "Pending"
  }
]
  ```

### <a name="isolated-vm"></a>VM isolado

Verifique se há atualizações pendentes para uma VM isolada. Neste exemplo, a saída é formatada como uma tabela de legibilidade.

```azurecli-interactive
az maintenance update list \
   -g myMaintenanceRg \
   --resource-name myVM \
   --resource-type virtualMachines \
   --provider-name Microsoft.Compute \
   -o table
```

### <a name="dedicated-host"></a>Host dedicado

Para verificar se há atualizações pendentes para um host dedicado. Neste exemplo, a saída é formatada como uma tabela de legibilidade. Substitua os valores dos recursos pelos seus.

```azurecli-interactive
az maintenance update list \
   --subscription 1111abcd-1a11-1a2b-1a12-123456789abc \
   -g myHostResourceGroup \
   --resource-name myHost \
   --resource-type hosts \
   --provider-name Microsoft.Compute \
   --resource-parentname myHostGroup \
   --resource-parent-type hostGroups \
   -o table
```

## <a name="apply-updates"></a>Aplicar atualizações

Use `az maintenance apply update` para aplicar atualizações pendentes. Com o sucesso, este comando retornará JSON contendo os detalhes da atualização.

### <a name="isolated-vm"></a>VM isolado

Crie uma solicitação para aplicar atualizações a uma VM isolada.

```azurecli-interactive
az maintenance applyupdate create \
   --subscription 1111abcd-1a11-1a2b-1a12-123456789abc \
   --resource-group myMaintenanceRG \
   --resource-name myVM \
   --resource-type virtualMachines \
   --provider-name Microsoft.Compute
```


### <a name="dedicated-host"></a>Host dedicado

Aplique atualizações a um host dedicado.

```azurecli-interactive
az maintenance applyupdate create \
   --subscription 1111abcd-1a11-1a2b-1a12-123456789abc \
   --resource-group myHostResourceGroup \
   --resource-name myHost \
   --resource-type hosts \
   --provider-name Microsoft.Compute \
   --resource-parent-name myHostGroup \
   --resource-parent-type hostGroups
```

## <a name="check-the-status-of-applying-updates"></a>Verifique o status da aplicação de atualizações 

Você pode verificar o andamento das `az maintenance applyupdate get`atualizações usando . 

Você pode `default` usar como nome de atualização para ver `myUpdateName` os resultados da última atualização ou `az maintenance applyupdate create`substituir pelo nome da atualização que foi devolvida quando você foi executado .

```text
Status         : Completed
ResourceId     : /subscriptions/12ae7457-4a34-465c-94c1-17c058c2bd25/resourcegroups/TestShantS/providers/Microsoft.Comp
ute/virtualMachines/DXT-test-04-iso
LastUpdateTime : 1/1/2020 12:00:00 AM
Id             : /subscriptions/12ae7457-4a34-465c-94c1-17c058c2bd25/resourcegroups/TestShantS/providers/Microsoft.Comp
ute/virtualMachines/DXT-test-04-iso/providers/Microsoft.Maintenance/applyUpdates/default
Name           : default
Type           : Microsoft.Maintenance/applyUpdates
```
LastUpdateTime será o momento em que a atualização foi concluída, iniciada por você ou pela plataforma no caso de janela de auto-manutenção não ter sido usada. Se nunca houve uma atualização aplicada através do controle de manutenção, ela mostrará o valor padrão.

### <a name="isolated-vm"></a>VM isolado

```azurecli-interactive
az maintenance applyupdate get \
   --resource-group myMaintenanceRG \
   --resource-name myVM \
   --resource-type virtualMachines \
   --provider-name Microsoft.Compute \
   --apply-update-name default 
```

### <a name="dedicated-host"></a>Host dedicado

```azurecli-interactive
az maintenance applyupdate get \
   --subscription 1111abcd-1a11-1a2b-1a12-123456789abc \ 
   --resource-group myMaintenanceRG \
   --resource-name myHost \
   --resource-type hosts \
   --provider-name Microsoft.Compute \
   --resource-parent-name myHostGroup \ 
   --resource-parent-type hostGroups \
   --apply-update-name myUpdateName \
   --query "{LastUpdate:lastUpdateTime, Name:name, ResourceGroup:resourceGroup, Status:status}" \
   --output table
```


## <a name="delete-a-maintenance-configuration"></a>Exclua uma configuração de manutenção

Use `az maintenance configuration delete` para excluir uma configuração de manutenção. A exclusão da configuração remove o controle de manutenção dos recursos associados.

```azurecli-interactive
az maintenance configuration delete \
   --subscription 1111abcd-1a11-1a2b-1a12-123456789abc \
   -g myResourceGroup \
   --name myConfig
```

## <a name="next-steps"></a>Próximas etapas
Para saber mais, consulte [Manutenção e atualizações](maintenance-and-updates.md).
