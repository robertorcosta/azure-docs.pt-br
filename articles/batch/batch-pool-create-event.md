---
title: Evento de criação de pool do Azure Batch
description: Referência para o evento De criar pool batch, que é emitido uma vez que um pool foi criado. O conteúdo do log expõe as informações gerais do pool.
services: batch
author: LauraBrenner
manager: evansma
ms.assetid: ''
ms.service: batch
ms.topic: article
ms.tgt_pltfrm: ''
ms.workload: big-compute
ms.date: 04/20/2017
ms.author: labrenne
ms.openlocfilehash: dea025b274278aa5fed2900c95b4a274541ffef9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "77022182"
---
# <a name="pool-create-event"></a>Evento de criação de pool

 Esse evento é emitido quando um pool é criado. O conteúdo do log expõe as informações gerais do pool. Observe que, se o tamanho de destino do pool for maior que 0 nós de computação, um evento inicial de redimensionamento do pool seguirá imediatamente esse evento.

 O exemplo a seguir mostra o corpo de um `CloudServiceConfiguration` evento de criação de piscina para um pool criado usando a propriedade.

```
{
    "id": "myPool1",
    "displayName": "Production Pool",
    "vmSize": "Standard_F1s",
    "imageType": "VirtualMachineConfiguration",
    "cloudServiceConfiguration": {
        "osFamily": "3",
        "targetOsVersion": "*"
    },
    "networkConfiguration": {
        "subnetId": " "
    },
    "virtualMachineConfiguration": {
          "imageReference": {
            "publisher": " ",
            "offer": " ",
            "sku": " ",
            "version": " "
          },
          "nodeAgentId": " "
        },
    "resizeTimeout": "300000",
    "targetDedicatedNodes": 2,
    "targetLowPriorityNodes": 2,
    "maxTasksPerNode": 1,
    "vmFillType": "Spread",
    "enableAutoScale": false,
    "enableInterNodeCommunication": false,
    "isAutoPool": false
}
```

|Elemento|Type|Observações|
|-------------|----------|-----------|
|`id`|String|A id da piscina.|
|`displayName`|String|O nome de exibição do pool.|
|`vmSize`|String|O tamanho das máquinas virtuais no pool. Todas as máquinas virtuais em um pool têm o mesmo tamanho. <br/><br/> Para obter informações sobre tamanhos disponíveis de máquinas virtuais para pools de serviços de nuvem (pools criados com cloudServiceConfiguration), consulte [Tamanhos para serviços de nuvem](https://azure.microsoft.com/documentation/articles/cloud-services-sizes-specs/). O lote dá suporte a todos os tamanhos de VM de serviços de nuvem, exceto `ExtraSmall`.<br/><br/> Para obter informações sobre tamanhos de VM disponíveis para pools usando imagens do Marketplace de máquinas virtuais (pools criados com virtualMachineConfiguration) consulte [Tamanhos de máquinas virtuais](https://azure.microsoft.com/documentation/articles/virtual-machines-linux-sizes/) (Linux) ou [Tamanhos de máquinas virtuais](https://azure.microsoft.com/documentation/articles/virtual-machines-windows-sizes/) (Windows). O Lote dá suporte a todos os tamanhos de VM do Azure, exceto `STANDARD_A0` e aqueles com armazenamento premium (série `STANDARD_GS`, `STANDARD_DS` e `STANDARD_DSV2`).|
|`imageType`|String|O método de implantação da imagem. Valores suportados são `virtualMachineConfiguration` ou`cloudServiceConfiguration`|
|[`cloudServiceConfiguration`](#bk_csconf)|Tipo complexo|A configuração do serviço de nuvem para o pool.|
|[`virtualMachineConfiguration`](#bk_vmconf)|Tipo complexo|A configuração da máquina virtual para o pool.|
|[`networkConfiguration`](#bk_netconf)|Tipo complexo|A configuração de rede para o pool.|
|`resizeTimeout`|Hora|O tempo limite de alocação de nós de computação para o pool especificado para a última operação de redimensionamento no pool.  (O dimensionamento inicial quando o pool é criado conta como um redimensionamento.)|
|`targetDedicatedNodes`|Int32|O número de nós de computação dedicados que são solicitados para o pool.|
|`targetLowPriorityNodes`|Int32|O número de nós computacionais de baixa prioridade que são solicitados para o pool.|
|`enableAutoScale`|Bool|Especifica se o tamanho do pool é ajustado automaticamente com o tempo.|
|`enableInterNodeCommunication`|Bool|Especifica se o pool é configurado para comunicação direta entre nós.|
|`isAutoPool`|Bool|Especifica se o pool foi criado por meio de um mecanismo de AutoPool do trabalho.|
|`maxTasksPerNode`|Int32|O número máximo de tarefas que podem ser executadas simultaneamente em um único nó de computação no pool.|
|`vmFillType`|String|Define como o serviço em lotes distribui tarefas entre nós de computação no pool. Os valores válidos são Difundir ou Empacotar.|

###  <a name="cloudserviceconfiguration"></a><a name="bk_csconf"></a>configuração de serviço sofrindo

|Nome do elemento|Type|Observações|
|------------------|----------|-----------|
|`osFamily`|String|A família do SO convidado do Azure a ser instalada nas máquinas virtuais no pool.<br /><br /> Os valores possíveis são:<br /><br /> **2** – SO Família 2, equivalente ao Windows Server 2008 R2 SP1.<br /><br /> **3** – SO Família 3, equivalente ao Windows Server 2012.<br /><br /> **4** – SO Família 4, equivalente ao Windows Server 2012 R2.<br /><br /> Para obter mais informações, consulte [Lançamentos do SO convidado do Azure](https://azure.microsoft.com/documentation/articles/cloud-services-guestos-update-matrix/#releases).|
|`targetOSVersion`|String|A versão do SO convidado do Azure a ser instalada nas máquinas virtuais no pool.<br /><br /> O valor **\*** padrão é o que especifica a versão mais recente do sistema operacional para a família especificada.<br /><br /> Para outros valores permitidos, consulte [Lançamentos do SO convidado do Azure](https://azure.microsoft.com/documentation/articles/cloud-services-guestos-update-matrix/#releases).|

###  <a name="virtualmachineconfiguration"></a><a name="bk_vmconf"></a>configuração de máquinavirtual

|Nome do elemento|Type|Observações|
|------------------|----------|-----------|
|[`imageReference`](#bk_imgref)|Tipo complexo|Especifica informações sobre a plataforma ou imagem do Marketplace a ser usada.|
|`nodeAgentId`|String|O SKU do agente do nó do lote provisionado em nós de computação.|
|[`windowsConfiguration`](#bk_winconf)|Tipo complexo|Especifica as configurações de sistema operacional do Windows na máquina virtual. Essa propriedade não deverá ser especificada se imageReference fizer referência a uma imagem do SO do Linux.|

###  <a name="imagereference"></a><a name="bk_imgref"></a>referência de imagem

|Nome do elemento|Type|Observações|
|------------------|----------|-----------|
|`publisher`|String|Especifica o editor da imagem.|
|`offer`|String|A oferta da imagem.|
|`sku`|String|A SKU da imagem.|
|`version`|String|A versão da imagem.|

###  <a name="windowsconfiguration"></a><a name="bk_winconf"></a>configuração do windows

|Nome do elemento|Type|Observações|
|------------------|----------|-----------|
|`enableAutomaticUpdates`|Boolean|Indica se a máquina virtual está habilitada para atualizações automáticas. Se essa propriedade não for especificada, o valor padrão será verdadeiro.|

###  <a name="networkconfiguration"></a><a name="bk_netconf"></a>configuração de rede

|Nome do elemento|Type|Observações|
|------------------|--------------|----------|
|`subnetId`|String|Especifica o identificador do recurso da sub-rede em que nós de computação do pool são criados.|
