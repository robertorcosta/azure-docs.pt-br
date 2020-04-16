---
title: Upgrades automáticos de imagem do Sistema Operacional com conjuntos de escala de máquinavirtual do Azure
description: Saiba como atualizar automaticamente a imagem do sistema operacional em instâncias de VM em um conjunto de escalas
author: mimckitt
tags: azure-resource-manager
ms.service: virtual-machine-scale-sets
ms.topic: conceptual
ms.date: 04/14/2020
ms.author: mimckitt
ms.openlocfilehash: ee6a25ac5a4cc7de8b8340afb186d170cc147a38
ms.sourcegitcommit: d6e4eebf663df8adf8efe07deabdc3586616d1e4
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81393782"
---
# <a name="azure-virtual-machine-scale-set-automatic-os-image-upgrades"></a>Atualizações automáticas de imagem do sistema operacional do conjunto de dimensionamento de máquinas virtuais do Azure

Habilitar as atualizações automáticas de imagem do sistema operacional em seu conjunto de dimensionamento ajuda a facilitar o gerenciamento de atualizações, atualizando de modo automático e seguro o disco do sistema operacional para todas as instâncias no conjunto de dimensionamento.

A atualização de sistema operacional do Azure tem as seguintes características:

- Depois de configurada, a imagem mais recente do sistema operacional publicada pelos editores de imagem é aplicada automaticamente ao conjunto de dimensionamento sem intervenção do usuário.
- Atualiza lotes de instâncias de forma rolando cada vez que uma nova imagem é publicada pelo editor.
- Integra-se com as investigações de integridade do aplicativo e a [extensão de Integridade do Aplicativo](virtual-machine-scale-sets-health-extension.md).
- Funciona para todos os tamanhos de VM, e para imagens do Windows e Linux.
- É possível recusar os upgrades automáticos a qualquer momento (os upgrades do sistema operacional podem ser iniciados manualmente também).
- O disco do sistema operacional de uma VM é substituído pelo novo disco do sistema operacional criado com a versão mais recente da imagem. As extensões configuradas e os scripts de dados personalizados são executados, enquanto os discos de dados persistentes são retidos.
- O [sequenciamento de extensões](virtual-machine-scale-sets-extension-sequencing.md) é compatível.
- A atualização automática da imagem do sistema operacional pode ser ativada em um conjunto de escalas de qualquer tamanho.

## <a name="how-does-automatic-os-image-upgrade-work"></a>Como a atualização automática de imagem do sistema operacional funciona?

A atualização trabalha substituindo o disco do sistema operacional de uma VM por um novo disco criado usando a versão mais recente da imagem. As extensões configuradas e os scripts de dados personalizados são executados no disco do sistema operacional, enquanto os discos de dados persistentes são retidos. Para minimizar o tempo de inatividade do aplicativo, as atualizações ocorrem em lotes, com até 20% do conjunto de dimensionamento sendo atualizado de cada vez. Também existe a opção de integrar uma investigação de integridade do aplicativo do Azure Load Balancer ou [extensão de Integridade do Aplicativo](virtual-machine-scale-sets-health-extension.md). É recomendável incorporar uma pulsação do aplicativo e validar o sucesso da atualização para cada lote no processo de atualização.

O processo de atualização funciona da seguinte maneira:
1. Antes de iniciar o processo de atualização, o orquestrador garantirá que no máximo 20% das instâncias em todo o conjunto de dimensionamento não estejam íntegras (por qualquer motivo).
2. O orquestrador de upgrade identifica o lote de instâncias de VM para atualização, com qualquer lote tendo um máximo de 20% da contagem total de instâncias, sujeito a um tamanho mínimo de lote de uma máquina virtual.
3. O disco do sistema operacional do lote selecionado de instâncias de VM é substituído por um novo disco do SISTEMA OPERACIONAL criado a partir da imagem mais recente. Todas as extensões e configurações especificadas no modelo de conjunto de escalas são aplicadas à instância atualizada.
4. Para conjuntos de dimensionamento com investigações de integridade do aplicativo ou extensão de Integridade do Aplicativo, a atualização aguardará até cinco minutos para que a instância se torne íntegra antes de passar para a atualização do próximo lote. Se uma instância não recuperar sua saúde em 5 minutos após uma atualização, então, por padrão, o disco anterior do SISTEMA OPERACIONAL, por exemplo, será restaurado.
5. O orquestrador de atualização também acompanha o percentual das instâncias que se tornarão não íntegro após uma atualização. A atualização será interrompida se mais de 20% das instâncias atualizadas se tornarem não íntegras durante o processo de atualização.
6. O processo acima continua até todas as instâncias no conjunto de dimensionamento serem atualizadas.

O orquestrador de atualização do sistema operacional do conjunto de dimensionamento verifica a integridade geral do conjunto de dimensionamento antes de atualizar cada lote. Ao atualizar um lote, pode haver outras atividades manutenção planejada ou não planejada simultânea acontecendo que podem afetar a integridade de suas instâncias de conjunto de dimensionamento. Nesses casos, se há mais de 20% das instâncias do conjunto de dimensionamento que se tornam não íntegras, a atualização do conjunto de dimensionamento é interrompida no final do lote atual.

## <a name="supported-os-images"></a>Imagens do sistema operacional com suporte
No momento, há suporte apenas determinadas imagens de plataforma do sistema operacional. O suporte personalizado de imagem está disponível [na pré-visualização](virtual-machine-scale-sets-automatic-upgrade.md#automatic-os-image-upgrade-for-custom-images-preview) para imagens personalizadas através [da Galeria de Imagens Compartilhadas](shared-image-galleries.md).

As Seguintes Plataformas SKUs são suportadas atualmente (e mais são adicionadas periodicamente):

| Publicador               | Oferta de sistema operacional      |  Sku               |
|-------------------------|---------------|--------------------|
| Canônico               | UbuntuServer  | 16.04-LTS          |
| Canônico               | UbuntuServer  | 18.04-LTS          |
| Rogue Wave (OpenLogic)  | CentOS        | 7.5                |
| CoreOS                  | CoreOS        | Estável             |
| Microsoft Corporation   | WindowsServer | 2012-R2-Datacenter |
| Microsoft Corporation   | WindowsServer | 2016-Datacenter    |
| Microsoft Corporation   | WindowsServer | 2016-Datacenter-Smalldisk |
| Microsoft Corporation   | WindowsServer | 2016-Datacenter-with-Containers |
| Microsoft Corporation   | WindowsServer | 2019-Datacenter |
| Microsoft Corporation   | WindowsServer | 2019-Datacenter-Smalldisk |
| Microsoft Corporation   | WindowsServer | 2019-Datacenter-with-Containers |
| Microsoft Corporation   | WindowsServer | Datacenter-Core-1903-com-Containers-smalldisk |


## <a name="requirements-for-configuring-automatic-os-image-upgrade"></a>Requisitos para configurar a atualização automática de imagem do sistema operacional

- A propriedade de *versão* da imagem deve ser definida como *mais recente*.
- Use as investigações de integridade do aplicativo ou a [extensão de Integridade do Aplicativo](virtual-machine-scale-sets-health-extension.md) para conjuntos de dimensionamento que não sejam do Service Fabric.
- Use a versão aPI computacional 2018-10-01 ou superior.
- Verifique se os recursos externos especificados no modelo de conjunto de dimensionamento estão disponíveis e atualizados. Exemplos incluem o URI SAS para conteúdo de inicialização nas propriedades de extensão da VM, o conteúdo na conta de armazenamento, a referência a segredos no modelo e outros.
- Para conjuntos de escala usando máquinas virtuais do Windows, começando com a versão de API de computação 2019-03-01, a propriedade *virtualMachineProfile.osProfile.windowsConfiguration.enableAutomaticUpdates* property deve definir como *falsa* na definição do modelo de conjunto de escalas. A propriedade acima permite atualizações in-VM onde "Windows Update" aplica patches do sistema operacional sem substituir o disco do SISTEMA OPERACIONAL. Com atualizações automáticas de imagem do sistema operacional habilitadas no seu conjunto de escalas, uma atualização adicional através do "Windows Update" não é necessária.

### <a name="service-fabric-requirements"></a>Requisitos de malha de serviço

Se você estiver usando o Service Fabric, certifique-se de que as seguintes condições sejam atendidas:
-   O [nível de durabilidade](../service-fabric/service-fabric-cluster-capacity.md#the-durability-characteristics-of-the-cluster) do tecido de serviço é prata ou ouro, e não bronze.
-   A extensão Service Fabric na definição do modelo de conjunto de escalas deve ter TypeHandlerVersion 1.1 ou superior.
-   O nível de durabilidade deve ser o mesmo no cluster de malha de serviço e na extensão Service Fabric na definição do modelo do conjunto de escalas.

Certifique-se de que as configurações de durabilidade não sejam incompatíveis com o cluster Service Fabric e a extensão Service Fabric, pois uma incompatibilidade resultará em erros de upgrade. Os níveis de durabilidade podem ser modificados de acordo com as diretrizes descritas [nesta página](../service-fabric/service-fabric-cluster-capacity.md#changing-durability-levels).


## <a name="automatic-os-image-upgrade-for-custom-images-preview"></a>Atualização automática de imagem do sistema operacional para imagens personalizadas (visualização)

> [!IMPORTANT]
> A atualização automática da imagem do sistema operacional para imagens personalizadas está atualmente na Visualização Pública. Um procedimento de opt-in é necessário para usar a funcionalidade de visualização pública descrita abaixo.
> Esta versão prévia é fornecida sem um contrato de nível de serviço e não é recomendada para cargas de trabalho de produção. Alguns recursos podem não ter suporte ou podem ter restrição de recursos.
> Para obter mais informações, consulte [Termos de Uso Complementares de Versões Prévias do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

A atualização automática da imagem do sistema operacional está disponível na pré-visualização para imagens personalizadas implantadas através [da Galeria de Imagens Compartilhadas](shared-image-galleries.md). Outras imagens personalizadas não são suportadas para upgrades automáticos de imagem do sistema operacional.

Ativar a funcionalidade de visualização requer um opt-in único para o recurso *AutomaticOSUpgradeWithGalleryImage* por assinatura, conforme detalhado abaixo.

### <a name="rest-api"></a>API REST
O exemplo a seguir descreve como ativar a visualização para sua assinatura:

```
POST on `/subscriptions/{subscriptionId}/providers/Microsoft.Features/providers/Microsoft.Compute/features/AutomaticOSUpgradeWithGalleryImage/register?api-version=2015-12-01`
```

A inscrição de recursos pode levar até 15 minutos. Para verificar a situação cadastral:

```
GET on `/subscriptions/{subscriptionId}/providers/Microsoft.Features/providers/Microsoft.Compute/features/AutomaticOSUpgradeWithGalleryImage?api-version=2015-12-01`
```

Uma vez que o recurso tenha sido registrado para sua assinatura, complete o processo de opt-in propagando a alteração no provedor de recursos Compute.

```
POST on `/subscriptions/{subscriptionId}/providers/Microsoft.Compute/register?api-version=2019-12-01`
```

### <a name="azure-powershell"></a>Azure PowerShell
Use o [cmdlet Register-AzProviderFeature](/powershell/module/az.resources/register-azproviderfeature) para ativar a visualização de sua assinatura.

```azurepowershell-interactive
Register-AzProviderFeature -FeatureName AutomaticOSUpgradeWithGalleryImage -ProviderNamespace Microsoft.Compute
```

A inscrição de recursos pode levar até 15 minutos. Para verificar a situação cadastral:

```azurepowershell-interactive
Get-AzProviderFeature -FeatureName AutomaticOSUpgradeWithGalleryImage -ProviderNamespace Microsoft.Compute
```

Uma vez que o recurso tenha sido registrado para sua assinatura, complete o processo de opt-in propagando a alteração no provedor de recursos Compute.

```azurepowershell-interactive
Register-AzResourceProvider -ProviderNamespace Microsoft.Compute
```

### <a name="azure-cli-20"></a>CLI do Azure 2.0
Use [o registro de recursos az](/cli/azure/feature#az-feature-register) para ativar a visualização de sua assinatura.

```azurecli-interactive
az feature register --namespace Microsoft.Compute --name AutomaticOSUpgradeWithGalleryImage
```

A inscrição de recursos pode levar até 15 minutos. Para verificar a situação cadastral:

```azurecli-interactive
az feature show --namespace Microsoft.Compute --name AutomaticOSUpgradeWithGalleryImage
```

Uma vez que o recurso tenha sido registrado para sua assinatura, complete o processo de opt-in propagando a alteração no provedor de recursos Compute.

```azurecli-interactive
az provider register --namespace Microsoft.Compute
```

### <a name="additional-requirements-for-custom-images"></a>Requisitos adicionais para imagens personalizadas
- O processo de opt-in descrito acima precisa ser concluído apenas uma vez por assinatura. Após a conclusão do opt-in, os upgrades automáticos do Sistema Operacional podem ser habilitados para qualquer escala definida nessa assinatura.
- A Galeria de Imagens Compartilhadas pode estar em qualquer assinatura e não precisa ser optada separadamente. Apenas a assinatura de conjunto de escala requer o opt-in do recurso.
- O processo de configuração para atualização automática de imagem do SISTEMA OPERACIONAL é o mesmo para todos os conjuntos de escala, conforme detalhado na seção de [configuração](virtual-machine-scale-sets-automatic-upgrade.md#configure-automatic-os-image-upgrade) desta página.
- Os conjuntos de escalaconfigurados para upgrades automáticos de imagem do SISTEMA OPERACIONAL serão atualizados para a versão mais recente da imagem da Galeria de Imagens Compartilhadas quando uma nova versão da imagem for publicada e [replicada](shared-image-galleries.md#replication) na região desse conjunto de escalas. Se a nova imagem não for replicada na região onde a escala é implantada, as instâncias de conjunto de escala não serão atualizadas para a versão mais recente. A replicação regional da imagem permite controlar a implantação da nova imagem para seus conjuntos de escala.
- A nova versão de imagem não deve ser excluída da versão mais recente para essa imagem da galeria. As versões de imagem excluídas da versão mais recente da imagem da galeria não são distribuídas para a escala definida através de atualização automática de imagem do Sistema Operacional.

> [!NOTE]
>Pode levar até 2 horas para um conjunto de escala para obter a primeira implantação de imagem depois que o conjunto de escala é configurado para upgrades automáticos do sistema operacional. Este é um atraso único por conjunto de escala. Os lançamentos de imagem subseqüentes são aplicados ao conjunto de escalas sem esse atraso.


## <a name="configure-automatic-os-image-upgrade"></a>Configurar a atualização automática da imagem do sistema operacional
Para configurar a atualização automática da imagem do sistema operacional, verifique se *automaticOSUpgradePolicy.enableAutomaticOSUpgrade* está definido como *true* na definição do modelo do conjunto de dimensionamento.

### <a name="rest-api"></a>API REST
O exemplo a seguir descreve como configurar atualizações automáticas do sistema operacional em um modelo de conjunto de dimensionamento:

```
PUT or PATCH on `/subscriptions/subscription_id/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myScaleSet?api-version=2019-12-01`
```

```json
{
  "properties": {
    "upgradePolicy": {
      "automaticOSUpgradePolicy": {
        "enableAutomaticOSUpgrade":  true
      }
    }
  }
}
```

### <a name="azure-powershell"></a>Azure PowerShell
Use o [cmdlet Update-AzVmss](/powershell/module/az.compute/update-azvmss) para configurar upgrades automáticos de imagem do sistema operacional para o seu conjunto de escalas. O exemplo a seguir configura upgrades automáticos para o conjunto de escalas chamado *myScaleSet* no grupo de recursos chamado *myResourceGroup*:

```azurepowershell-interactive
Update-AzVmss -ResourceGroupName "myResourceGroup" -VMScaleSetName "myScaleSet" -AutomaticOSUpgrade $true
```

### <a name="azure-cli-20"></a>CLI do Azure 2.0
Use [a atualização az vmss](/cli/azure/vmss#az-vmss-update) para configurar upgrades automáticos de imagem do sistema operacional para o seu conjunto de escalas. Use a CLI do Azure 2.0.47 ou posterior. O exemplo a seguir configura upgrades automáticos para o conjunto de escalas chamado *myScaleSet* no grupo de recursos chamado *myResourceGroup*:

```azurecli-interactive
az vmss update --name myScaleSet --resource-group myResourceGroup --set UpgradePolicy.AutomaticOSUpgradePolicy.EnableAutomaticOSUpgrade=true
```

## <a name="using-application-health-probes"></a>Usando investigações de integridade do aplicativo

Durante a atualização do sistema operacional, as instâncias de VM em um conjunto de dimensionamento são atualizadas em um lote por vez. A atualização deverá continuar apenas se o aplicativo do cliente for íntegro nas instâncias de VM atualizadas. Recomendamos que o aplicativo ofereça sinais de integridade ao mecanismo de atualização do sistema operacional do conjunto de dimensionamento. Por padrão, durante os upgrades do sistema operacional, a plataforma considera o estado de energia da VM e o estado de provisionamento da extensão para determinar se uma instância VM é íntegra após uma atualização. Durante a atualização do sistema operacional de uma instância VM, o disco do sistema operacional em uma instância VM é substituído por um novo com base na versão mais recente da imagem. Após a conclusão de atualização do sistema operacional, as extensões configuradas são executadas nessas VMs. O aplicativo é considerado íntegro somente quando todas as extensões na instância são provisionadas com êxito.

Um conjunto de dimensionamento pode opcionalmente ser configurado com Investigações de integridade do aplicativo para oferecer à plataforma informações precisas sobre o estado em andamento do aplicativo. As Investigações de integridade do aplicativo são Investigações personalizadas do Load Balancer usadas como um sinal de integridade. O aplicativo em execução em uma instância VM do conjunto de dimensionamento pode responder a solicitações HTTP ou TCP externas que indica se ele está íntegro. Para obter mias informações sobre como as Investigações personalizadas do Load Balancer funcionam, consulte [Noções básicas de investigações do balanceador de carga](../load-balancer/load-balancer-custom-probe-overview.md). Os testes de saúde do aplicativo não são suportados para conjuntos de escala de malha de serviço. Conjuntos de dimensionamento que não são do Service Fabric exigem investigações de integridade do aplicativo do Load Balancer ou a [extensão de Integridade do Aplicativo](virtual-machine-scale-sets-health-extension.md).

Se o conjunto de dimensionamento estiver configurado para usar vários grupos de posicionamento, as investigações que usarem o [Load Balancer Standard](https://docs.microsoft.com/azure/load-balancer/load-balancer-standard-overview) precisarão ser usadas.

### <a name="configuring-a-custom-load-balancer-probe-as-application-health-probe-on-a-scale-set"></a>Configurando uma Investigação personalizada do Load Balancer como uma investigação de integridade do aplicativo em um conjunto de dimensionamento
Como uma melhor prática, crie uma investigação do balanceador de carga explicitamente para a integridade do conjunto de dimensionamento. O mesmo ponto de extremidade para uma investigação de HTTP ou TCP existente pode ser usado, mas uma investigação de integridade pode exigir um comportamento diferente de uma investigação de balanceador de carga tradicional. Por exemplo, uma investigação tradicional do balanceador de carga poderá ser retornada não íntegra se a carga na instância for alta demais, enquanto que isso pode não ser adequado para determinar a integridade da instância durante uma atualização automática do sistema operacional. Configure a investigação para que ela tenha uma alta taxa de sondagem de menos de 2 minutos.

A investigação do balanceador de carga pode ser referenciada no *networkProfile* do conjunto de dimensionamento e associada a um balanceador de carga interno ou público da seguinte maneira:

```json
"networkProfile": {
  "healthProbe" : {
    "id": "[concat(variables('lbId'), '/probes/', variables('sshProbeName'))]"
  },
  "networkInterfaceConfigurations":
  ...
}
```

> [!NOTE]
> Ao usar as atualizações automáticas do sistema operacional com o Service Fabric, a nova imagem do sistema operacional é implementada ao domínio de atualização pelo domínio de atualização para manter a alta disponibilidade dos serviços em execução no Service Fabric. Para utilizar atualizações automáticas do sistema operacional no Service Fabric, o cluster deve ser configurado para usar a camada de durabilidade Prata ou superior. Para obter mais informações sobre as características de durabilidade de clusters do Service Fabric, consulte [esta documentação](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-capacity#the-durability-characteristics-of-the-cluster).

### <a name="keep-credentials-up-to-date"></a>Mantenha as credenciais atualizadas
Se o conjunto de escalas usar quaisquer credenciais para acessar recursos externos, como uma extensão VM configurada para usar um token SAS para conta de armazenamento, certifique-se de que as credenciais sejam atualizadas. Se quaisquer credenciais, incluindo certificados e tokens, tiverem expirado, a atualização falhará e o primeiro lote de VMs será deixado em um estado de falha.

As etapas recomendadas para recuperar VMs e reativar a atualização automática do sistema operacional se houver uma falha de autenticação de recurso são:

* Gerar novamente o token (ou qualquer outra credencial) passado para suas extensões.
* Certificar-se de que qualquer credencial usada de dentro da VM para se comunicar com entidades externas esteja atualizada.
* Atualizar extensões no modelo do conjunto de escala com quaisquer tokens novos.
* Implantar o conjunto de escala atualizado, que atualizará todas as instâncias de VM, incluindo aquelas com falha.

## <a name="using-application-health-extension"></a>Usando a extensão de Integridade do Aplicativo
A extensão de integridade do Application Health é implantada dentro de uma instância de conjunto de dimensionamento de máquinas virtuais e os relatórios de integridade da VM de dentro da instância de conjunto de dimensionamento. Você pode configurar a extensão para investigação em um ponto de extremidade do aplicativo e atualizar o status do aplicativo nessa instância. Esse status da instância é verificada pelo Azure para determinar se uma instância é elegível para operações de atualização.

Já que a extensão relata a integridade de dentro de uma VM, a extensão pode ser usada em situações em que investigações externas como investigações de Integridade do Aplicativo (que utilizam [investigações](../load-balancer/load-balancer-custom-probe-overview.md) personalizadas do Azure Load Balancer) não podem ser usadas.

Há várias maneiras de implantar a extensão de Integridade do Aplicativo para conjuntos de dimensionamento, conforme detalhado nos exemplos [neste artigo](virtual-machine-scale-sets-health-extension.md#deploy-the-application-health-extension).

## <a name="get-the-history-of-automatic-os-image-upgrades"></a>Obter o histórico de atualizações automáticas de imagem do sistema operacional
Você pode verificar o histórico da atualização do sistema operacional mais recente executado no conjunto de dimensionamento com o Azure PowerShell, a CLI do Azure 2.0 ou as APIs REST. Você pode obter o histórico das últimas cinco tentativas de atualização do sistema operacional nos últimos dois meses.

### <a name="rest-api"></a>API REST
O exemplo a seguir usa [a API REST](/rest/api/compute/virtualmachinescalesets/getosupgradehistory) para verificar o status do conjunto de escalas chamado *myScaleSet* no grupo de recursos chamado *myResourceGroup*:

```
GET on `/subscriptions/subscription_id/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myScaleSet/osUpgradeHistory?api-version=2019-12-01`
```

A chamada GET retorna propriedades semelhantes à saída de exemplo a seguir:

```json
{
    "value": [
        {
            "properties": {
        "runningStatus": {
          "code": "RollingForward",
          "startTime": "2018-07-24T17:46:06.1248429+00:00",
          "completedTime": "2018-04-21T12:29:25.0511245+00:00"
        },
        "progress": {
          "successfulInstanceCount": 16,
          "failedInstanceCount": 0,
          "inProgressInstanceCount": 4,
          "pendingInstanceCount": 0
        },
        "startedBy": "Platform",
        "targetImageReference": {
          "publisher": "MicrosoftWindowsServer",
          "offer": "WindowsServer",
          "sku": "2016-Datacenter",
          "version": "2016.127.20180613"
        },
        "rollbackInfo": {
          "successfullyRolledbackInstanceCount": 0,
          "failedRolledbackInstanceCount": 0
        }
      },
      "type": "Microsoft.Compute/virtualMachineScaleSets/rollingUpgrades",
      "location": "westeurope"
    }
  ]
}
```

### <a name="azure-powershell"></a>Azure PowerShell
Use o cmdlet [Get-AzVmss](/powershell/module/az.compute/get-azvmss) para verificar o histórico de atualização do sistema operacional para seu conjunto de dimensionamento. O exemplo a seguir detalha como você revisa o status de atualização do SISTEMA OPERACIONAL para um conjunto de escalas chamado *myScaleSet* no grupo de recursos chamado *myResourceGroup*:

```azurepowershell-interactive
Get-AzVmss -ResourceGroupName "myResourceGroup" -VMScaleSetName "myScaleSet" -OSUpgradeHistory
```

### <a name="azure-cli-20"></a>CLI do Azure 2.0
Use [az vmss get-os-upgrade-history](/cli/azure/vmss#az-vmss-get-os-upgrade-history) para verificar o histórico de atualização do sistema operacional para seu conjunto de dimensionamento. Use a CLI do Azure 2.0.47 ou posterior. O exemplo a seguir detalha como você revisa o status de atualização do SISTEMA OPERACIONAL para um conjunto de escalas chamado *myScaleSet* no grupo de recursos chamado *myResourceGroup*:

```azurecli-interactive
az vmss get-os-upgrade-history --resource-group myResourceGroup --name myScaleSet
```

## <a name="how-to-get-the-latest-version-of-a-platform-os-image"></a>Como obter a versão mais recente de uma imagem do sistema operacional da plataforma?

Você pode obter as versões de imagem disponíveis para SKUs com suporte a upgrade automático do Sistema Operacional usando os exemplos abaixo:

### <a name="rest-api"></a>API REST
```
GET on `/subscriptions/subscription_id/providers/Microsoft.Compute/locations/{location}/publishers/{publisherName}/artifacttypes/vmimage/offers/{offer}/skus/{skus}/versions?api-version=2019-12-01`
```

### <a name="azure-powershell"></a>Azure PowerShell
```azurepowershell-interactive
Get-AzVmImage -Location "westus" -PublisherName "Canonical" -Offer "UbuntuServer" -Skus "16.04-LTS"
```

### <a name="azure-cli-20"></a>CLI do Azure 2.0
```azurecli-interactive
az vm image list --location "westus" --publisher "Canonical" --offer "UbuntuServer" --sku "16.04-LTS" --all
```

## <a name="manually-trigger-os-image-upgrades"></a>Acione manualmente atualizações de imagem do Sistema Operacional
Com a atualização automática da imagem do Sistema Operacional habilitada no seu conjunto de escalas, você não precisa acionar manualmente atualizações de imagem no seu conjunto de escala. O orquestrador de upgrade do SISTEMA OPERACIONAL aplicará automaticamente a versão de imagem mais recente disponível às instâncias do conjunto de escalas sem qualquer intervenção manual.

Para casos específicos em que você não deseja esperar o orquestrador aplicar a imagem mais recente, você pode acionar uma atualização de imagem do sistema operacional manualmente usando os exemplos abaixo.

> [!NOTE]
> O gatilho manual das atualizações de imagem do Sistema Operacional não fornece recursos automáticos de reversão. Se uma instância não recuperar sua saúde após uma operação de atualização, seu disco anterior do Sistema Operacional não poderá ser restaurado.

### <a name="rest-api"></a>API REST
Use a chamada API [start os upgrade](/rest/api/compute/virtualmachinescalesetrollingupgrades/startosupgrade) para iniciar uma atualização de rolamento para mover todas as instâncias de conjunto de escala de máquina virtual para a versão mais recente disponível do sistema operacional de imagem. As instâncias que já estão executando a versão mais recente disponível do Sistema Operacional não são afetadas. O exemplo a seguir detalha como você pode iniciar uma atualização do sistema operacional em um conjunto de escalas chamado *myScaleSet* no grupo de recursos chamado *myResourceGroup*:

```
POST on `/subscriptions/subscription_id/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myScaleSet/osRollingUpgrade?api-version=2019-12-01`
```

### <a name="azure-powershell"></a>Azure PowerShell
Use o [cmdlet Start-AzVmssRollingOSUpgrade](/powershell/module/az.compute/Start-AzVmssRollingOSUpgrade) para verificar o histórico de atualizações do SISTEMA OPERACIONAL para o seu conjunto de escalas. O exemplo a seguir detalha como você pode iniciar uma atualização do sistema operacional em um conjunto de escalas chamado *myScaleSet* no grupo de recursos chamado *myResourceGroup*:

```azurepowershell-interactive
Start-AzVmssRollingOSUpgrade -ResourceGroupName "myResourceGroup" -VMScaleSetName "myScaleSet"
```

### <a name="azure-cli-20"></a>CLI do Azure 2.0
Use [aaz vmss rolling-upgrade start](/cli/azure/vmss/rolling-upgrade#az-vmss-rolling-upgrade-start) para verificar o histórico de atualização do sistema operacional para o seu conjunto de escalas. Use a CLI do Azure 2.0.47 ou posterior. O exemplo a seguir detalha como você pode iniciar uma atualização do sistema operacional em um conjunto de escalas chamado *myScaleSet* no grupo de recursos chamado *myResourceGroup*:

```azurecli-interactive
az vmss rolling-upgrade start --resource-group "myResourceGroup" --name "myScaleSet" --subscription "subscriptionId"
```

## <a name="deploy-with-a-template"></a>Implantar com um modelo

Você pode usar modelos para implantar um conjunto de dimensionamento com atualizações de sistema operacional automáticas para imagens compatíveis, tais como [Ubuntu 16.04-LTS](https://github.com/Azure/vm-scale-sets/blob/master/preview/upgrade/autoupdate.json).

<a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fvm-scale-sets%2Fmaster%2Fpreview%2Fupgrade%2Fautoupdate.json" target="_blank"><img src="https://azuredeploy.net/deploybutton.png"/></a>

## <a name="next-steps"></a>Próximas etapas
Para ver mais exemplos sobre como usar atualizações automáticas do sistema operacional com conjuntos de dimensionamento, examine o [repositório do GitHub](https://github.com/Azure/vm-scale-sets/tree/master/preview/upgrade).
