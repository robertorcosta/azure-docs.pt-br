---
title: Tutorial – Monitorar Máquinas Virtuais do Linux no Azure | Microsoft Docs
description: Neste tutorial, você aprenderá a monitorar o desempenho e os componentes do aplicativo descobertos em execução nas Máquinas Virtuais do Linux.
services: virtual-machines-linux
documentationcenter: virtual-machines
author: mgoedtel
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-linux
ms.topic: tutorial
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 09/30/2019
ms.author: magoedte
ms.custom: mvc
ms.openlocfilehash: 9c6458eea2b1352e7d13ea6691eac4498182ecd3
ms.sourcegitcommit: 5f0f1accf4b03629fcb5a371d9355a99d54c5a7e
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/30/2019
ms.locfileid: "71679406"
---
# <a name="tutorial-monitor-a-linux-virtual-machine-in-azure"></a>Tutorial: Monitorar uma máquina virtual do Linux no Azure

O monitoramento do Azure usa agentes para coletar dados de desempenho e inicialização de VMs do Azure, armazenar esses dados no armazenamento do Azure e torná-los acessíveis por meio do portal, do módulo do Azure PowerShell e da CLI do Azure. O monitoramento avançado é fornecido com o Azure Monitor para VMs coletando métricas de desempenho, descobre componentes do aplicativo instalados na VM e inclui gráficos de desempenho e o mapa de dependência.

Neste tutorial, você aprenderá como:

> [!div class="checklist"]
> * Habilitar o diagnóstico de inicialização em uma VM
> * Exibir diagnóstico de inicialização
> * Exibir métricas de host VM
> * Habilitar o Azure Monitor para VMs
> * Exibir métricas de desempenho da VM
> * Criar um alerta

## <a name="launch-azure-cloud-shell"></a>Iniciar o Azure Cloud Shell

O Azure Cloud Shell é um shell interativo grátis que pode ser usado para executar as etapas neste artigo. Ele tem ferramentas do Azure instaladas e configuradas para usar com sua conta. 

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Se você optar por instalar e usar a CLI localmente, este tutorial exigirá que você execute a CLI do Azure versão 2.0.30 ou posterior. Execute `az --version` para encontrar a versão. Se você precisar instalar ou atualizar, confira [Instalar a CLI do Azure](https://docs.microsoft.com/cli/azure/install-azure-cli).

## <a name="create-vm"></a>Criar VM

Para ver os diagnósticos e as métricas em ação, você precisa de uma VM. Primeiro, crie um grupo de recursos com [az group create](https://docs.microsoft.com/cli/azure/group?view=azure-cli-latest#az-group-create). O exemplo a seguir cria um grupo de recursos chamado *myResourceGroupMonitor* no local *eastus*.

```azurecli-interactive
az group create --name myResourceGroupMonitor --location eastus
```

Agora, crie uma VM com [az vm create](https://docs.microsoft.com/cli/azure/vm?view=azure-cli-latest#az-vm-create). O seguinte exemplo cria uma VM chamada *myVM* e gera as chaves SSH, caso ainda não existam em *~/.ssh/* :

```azurecli-interactive
az vm create \
  --resource-group myResourceGroupMonitor \
  --name myVM \
  --image UbuntuLTS \
  --admin-username azureuser \
  --generate-ssh-keys
```

## <a name="enable-boot-diagnostics"></a>Habilitar o diagnóstico de inicialização

Durante a inicialização das VMs do Linux, a extensão de diagnóstico de inicialização captura a saída de inicialização e a armazena no Armazenamento do Azure. Esses dados podem ser usados para solucionar problemas de inicialização da VM. Os diagnósticos de inicialização não são habilitados automaticamente quando você cria uma VM do Linux usando a CLI do Azure.

Antes de habilitar o diagnóstico de inicialização, é necessário criar uma conta de armazenamento para armazenar os logs de inicialização. As contas de armazenamento devem ter um nome exclusivo globalmente, com três a 24 caracteres e conter apenas números e letras minúsculas. Crie uma conta de armazenamento com o comando [az storage account create](https://docs.microsoft.com/cli/azure/storage/account?view=azure-cli-latest#az-storage-account-create). Neste exemplo, uma cadeia de caracteres aleatória é usada para criar um nome de conta de armazenamento exclusivo.

```azurecli-interactive
storageacct=mydiagdata$RANDOM

az storage account create \
  --resource-group myResourceGroupMonitor \
  --name $storageacct \
  --sku Standard_LRS \
  --location eastus
```

Ao habilitar o diagnóstico de inicialização, o URI do contêiner de armazenamento de blobs é exigido. O comando a seguir consulta a conta de armazenamento para retornar esse URI. O valor do URI é armazenado em uma variável chamada *bloburi*, que é usada na próxima etapa.

```azurecli-interactive
bloburi=$(az storage account show --resource-group myResourceGroupMonitor --name $storageacct --query 'primaryEndpoints.blob' -o tsv)
```

Agora, habilite o diagnóstico de inicialização com [az vm boot-diagnostics enable](https://docs.microsoft.com/cli/azure/vm/boot-diagnostics#az-vm-boot-diagnostics-enable). O valor `--storage` é o URI do blob coletado na etapa anterior.

```azurecli-interactive
az vm boot-diagnostics enable \
  --resource-group myResourceGroupMonitor \
  --name myVM \
  --storage $bloburi
```

## <a name="view-boot-diagnostics"></a>Exibir diagnóstico de inicialização

Quando o diagnóstico de inicialização for habilitado, sempre que você parar e iniciar a VM, as informações sobre o processo de inicialização serão gravadas em um arquivo de log. Para este exemplo, primeiro desaloque a VM com o comando [az vm deallocate](https://docs.microsoft.com/cli/azure/vm?view=azure-cli-latest#az-vm-deallocate) da seguinte maneira:

```azurecli-interactive
az vm deallocate --resource-group myResourceGroupMonitor --name myVM
```

Agora, inicie a VM com o comando [az vm start](https://docs.microsoft.com/cli/azure/vm?view=azure-cli-latest#az-vm-start) da seguinte maneira:

```azurecli-interactive
az vm start --resource-group myResourceGroupMonitor --name myVM
```

Você pode obter os dados de diagnóstico de inicialização para *myVM* com o comando [az vm boot-diagnostics get-boot-log](https://docs.microsoft.com/cli/azure/vm/boot-diagnostics#az-vm-boot-diagnostics-get-boot-log) da seguinte maneira:

```azurecli-interactive
az vm boot-diagnostics get-boot-log --resource-group myResourceGroupMonitor --name myVM
```

## <a name="view-host-metrics"></a>Exibir métricas de host

Uma VM do Linux tem um host dedicado no Azure com o qual ela interage. As métricas são coletadas automaticamente para o host e podem ser exibidas no Portal do Azure da seguinte maneira:

1. No portal do Azure, selecione **Grupos de Recursos**, escolha **myResourceGroupMonitor** e, em seguida, selecione **myVM** na lista de recursos.
1. Para ver como está o desempenho da VM do host, selecione **Métricas** na janela da VM e, em seguida, escolha qualquer uma das métricas de *Host* em **Métricas disponíveis**.

    ![Exibir métricas de host](./media/tutorial-monitoring/monitor-host-metrics.png)

## <a name="enable-advanced-monitoring"></a>Habilitar o monitoramento avançado

Para habilite o monitoramento da VM do Azure com o Azure Monitor para VMs:

1. No Portal do Azure, clique em **Grupos de Recursos**, selecione **myResourceGroupMonitor** e, em seguida, selecione **myVM** na lista de recursos.

2. Na página da VM, na seção **Monitoramento**, selecione **Insights (versão prévia)** .

3. Na página **Insights (versão prévia)** , selecione **Experimentar agora**.

    ![Habilitar o Azure Monitor para VMs em uma VM](../../azure-monitor/insights/media/vminsights-enable-single-vm/enable-vminsights-vm-portal-01.png)

4. Na página **Integração de Insights do Azure Monitor**, caso você tenha um espaço de trabalho do Log Analytics existente na mesma assinatura, selecione-o na lista suspensa.  

    A lista selecione previamente o workspace e a localização padrão em que a VM está implantada na assinatura. 

    >[!NOTE]
    >Para criar um workspace do Log Analytics para armazenar os dados de monitoramento da VM, confira [Criar um workspace do Log Analytics](../../azure-monitor/learn/quick-create-workspace.md). Seu workspace do Log Analytics deve pertencer a uma das [regiões com suporte](../../azure-monitor/insights/vminsights-enable-overview.md#log-analytics).

Depois de habilitar o monitoramento, talvez você precise aguardar vários minutos antes de poder exibir as métricas de desempenho da VM.

![Habilitar o Azure Monitor para o processamento da implantação do monitoramento das VMs](../../azure-monitor/insights/media/vminsights-enable-single-vm/onboard-vminsights-vm-portal-status.png)

## <a name="view-vm-performance-metrics"></a>Exibir métricas de desempenho da VM

O Monitor do Azure para VMs inclui um conjunto de gráficos de desempenho que segmentam vários KPIs (principais indicadores de desempenho) para ajudá-lo a determinar o desempenho de uma máquina virtual. Para acessar em sua VM, siga estas etapas.

1. No Portal do Azure, clique em **Grupos de Recursos**, selecione **myResourceGroupMonitor** e, em seguida, selecione **myVM** na lista de recursos.

2. Na página da VM, na seção **Monitoramento**, selecione **Insights (versão prévia)** .

3. Selecione o **desempenho** guia.

Esta página inclui não apenas gráficos de utilização de desempenho, mas também uma tabela mostrando cada disco lógico descoberto, sua capacidade, utilização e média total de cada medida.

## <a name="create-alerts"></a>Criar alertas

Você pode criar alertas com base em métricas de desempenho específicas. Alertas podem ser usados para lhe notificar quando uso médio da CPU excede um determinado limite ou o espaço em disco livre disponível cai abaixo de um determinado valor, por exemplo. Os alertas são exibidos no Portal do Azure, ou podem ser enviados por email. Você também pode disparar Runbooks da Automação do Azure ou Aplicativos Lógicos do Azure em resposta à geração de alertas.

O exemplo a seguir cria um alerta para uso médio da CPU.

1. No Portal do Azure, clique em **Grupos de Recursos**, selecione **myResourceGroupMonitor** e, em seguida, selecione **myVM** na lista de recursos.

2. Clique em **Regras de alerta** na folha da VM e, em seguida, clique em **Adicionar alerta de métrica** na parte superior da folha de alertas.

3. Forneça um **Nome** para o alerta, como *myAlertRule*

4. Para disparar um alerta quando o percentual de CPU excede 1.0 por cinco minutos, deixe todos os outros padrões selecionados.

5. Opcionalmente, marque a caixa de *Proprietários, colaboradores e leitores de email* para enviar uma notificação por email. A ação padrão é apresentar uma notificação no portal.

6. Clique no botão **OK**.

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você configurou e exibiu o desempenho de sua VM. Você aprendeu como:

> [!div class="checklist"]
> * Criar um grupo de recursos e uma VM
> * Habilitar o diagnóstico de inicialização na VM
> * Exibir diagnóstico de inicialização
> * Exibir métricas de host
> * Habilitar o Azure Monitor para VMs
> * Exibir métricas de VM
> * Criar um alerta

Avance para o próximo tutorial para saber mais sobre a Central de Segurança do Azure.

> [!div class="nextstepaction"]
> [Gerenciar a segurança da VM](../../security/fundamentals/overview.md)
