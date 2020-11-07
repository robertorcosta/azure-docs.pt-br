---
title: Atualizar endereços IP públicos
titleSuffix: Azure Virtual Network
description: Atualize os endereços IP públicos do básico para o Standard.
services: virtual-network
documentationcenter: na
author: blehr
editor: ''
ms.assetid: bb71abaf-b2d9-4147-b607-38067a10caf6
ms.service: virtual-network
ms.subservice: ip-services
ms.devlang: NA
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/07/2020
ms.author: blehr
ms.custom: references_regions
ms.openlocfilehash: a1bd303390626eaea71e588e325fedbd2d8fa4b9
ms.sourcegitcommit: 0b9fe9e23dfebf60faa9b451498951b970758103
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/07/2020
ms.locfileid: "94353349"
---
# <a name="upgrade-public-ip-addresses"></a>Atualizar endereços IP públicos

Os endereços IP públicos do Azure são criados com um SKU--básico ou standard – que determina os aspectos de sua funcionalidade (incluindo o método de alocação, o uso em zonas de disponibilidade e a quais recursos eles podem ser associados). 

Os cenários a seguir são revisados neste artigo:
* Como atualizar um IP público de SKU básico para um IP público de SKU Standard (usando o portal, o PowerShell ou a CLI)
* Como migrar um IP Reservado do Azure clássico para um IP público de SKU Azure Resource Manager Basic

## <a name="upgrade-public-ip-address-from-basic-to-standard-sku"></a>Atualizar o endereço IP público do SKU básico para o Standard

>[!NOTE]
>A capacidade de atualizar IPs públicos de básico para Standard não está disponível em todas as regiões.  Consulte as [**limitações**](#limitations) para obter mais detalhes.

Para atualizar um IP público, ele não deve ser associado a nenhum recurso (consulte [esta página](https://docs.microsoft.com/azure/virtual-network/virtual-network-public-ip-address#view-modify-settings-for-or-delete-a-public-ip-address) para obter mais informações sobre como desassociar IPS públicos).

>[!IMPORTANT]
>Os IPs públicos atualizados do SKU básico para o Standard continuam sem [zonas de disponibilidade](https://docs.microsoft.com/azure/availability-zones/az-overview?toc=/azure/virtual-network/toc.json#availability-zones).  Isso significa que eles não podem ser associados a um recurso do Azure que seja com redundância de zona ou vinculado a uma zona predefinida em regiões onde isso é oferecido.

---
# <a name="basic-to-standard---powershell"></a>[**Básico para Standard-PowerShell**](#tab/option-upgrade-powershell)

O exemplo a seguir pressupõe a criação anterior de um IP público de SKU básico, usando o exemplo fornecido nesta [página](https://docs.microsoft.com/azure/virtual-network/create-public-ip-powershell?tabs=option-create-public-ip-basic) com um **MyBasicPublicIP** IP público básico em **MyResource**.

Para atualizar o IP, basta executar os comandos abaixo usando o PowerShell.  Observação se o endereço IP já estiver alocado estaticamente, essa seção poderá ser ignorada.

```azurepowershell-interactive
## Variables for the command ##
$rg = 'myResourceGroup'
$name = 'myBasicPublicIP'
$newsku = 'Standard'
$pubIP = Get-AzPublicIpAddress -name $name -ResourceGroupName $rg

## This section is only needed if the Basic IP is not already set to Static ##
$pubIP.PublicIpAllocationMethod = 'Static'
Set-AzPublicIpAddress -PublicIpAddress $pubIP

## This section is for conversion to Standard ##
$pubIP.Sku.Name = $newsku
Set-AzPublicIpAddress -PublicIpAddress $pubIP
```

# <a name="basic-to-standard---cli"></a>[**Básico para Standard-CLI**](#tab/option-upgrade-cli)

O exemplo a seguir pressupõe a criação anterior de um IP público de SKU básico, usando o exemplo fornecido nesta [página](https://docs.microsoft.com/azure/virtual-network/create-public-ip-cli?tabs=option-create-public-ip-basic) com um **MyBasicPublicIP** IP público básico em **MyResource**.

Para atualizar o IP, basta executar os comandos abaixo usando o CLI do Azure.  Observação se o endereço IP já estiver alocado estaticamente, essa seção poderá ser ignorada.

```azurecli-interactive
## Variables for the command ##
$rg = 'myResourceGroup'
$name = 'myBasicPublicIP'

## This section is only needed if the Basic IP is not already set to Static ##
az network public-ip update \
--resource-group $rg \
--name $name \
--allocation-method Static 

## This section is for conversion to Standard ##
az network public-ip update \
--resource-group $rg \
--name $name \
--sku Standard
```
---

## <a name="upgrade-migrate-a-classic-reserved-ip-to-a-static-public-ip"></a>Atualizar (migrar) um IP Reservado clássico para um IP público estático

Para se beneficiar dos novos recursos do Azure Resource Manager, você pode migrar o endereço IP estático público existente--chamado de IPs reservados--do modelo clássico para o modelo de Azure Resource Manager moderno.  O IP público migrado será um tipo de SKU básico.


---

# <a name="reserved-to-basic---powershell"></a>[**Reservado para o básico-PowerShell**](#tab/option-migrate-powershell)

O exemplo a seguir pressupõe a criação anterior de um Azure IP Reservado clássico **myReservedIP** no **MyResource**. Outro pré-requisito para a migração é garantir que a assinatura do Azure Resource Manager tenha sido registrada para migração. Isso é abordado em detalhes nas etapas 3 e 4 desta [página](https://docs.microsoft.com/azure/virtual-machines/windows/migration-classic-resource-manager-ps).

Para migrar o IP Reservado, execute os comandos abaixo usando o PowerShell.  Observação se o endereço IP não estiver associado a nenhum serviço (abaixo de um serviço chamado **MyService** ), essa etapa poderá ser ignorada.

```azurepowershell-interactive
## Variables for the command ##
$name = 'myReservedIP'

## This section is only needed if the Reserved IP is not already disassociated from any Cloud Services ##
$serviceName = 'myService'
Remove-AzureReservedIPAssociation -ReservedIPName $name -ServiceName $service

$validate = Move-AzureReservedIP -ReservedIPName $name -Validate
$validate.ValidationMessages
```
O comando anterior exibe todos os avisos e erros que bloqueiam a migração. Se a validação for bem-sucedida, você poderá prosseguir com as etapas a seguir para preparar e confirmar a migração:
```azurepowershell-interactive
Move-AzureReservedIP -ReservedIPName $name -Prepare
Move-AzureReservedIP -ReservedIPName $name -Commit
```
Um novo grupo de recursos no Azure Resource Manager é criado usando o nome do IP Reservado migrado (no exemplo acima, seria o grupo de recursos **myReservedIP-migrado** ).

# <a name="reserved-to-basic---cli"></a>[**Reservado para a CLI básica**](#tab/option-migrate-cli)

O exemplo a seguir pressupõe a criação anterior de um Azure IP Reservado clássico **myReservedIP** no **MyResource**. Outro pré-requisito para a migração é garantir que a assinatura do Azure Resource Manager tenha sido registrada para migração. Isso é abordado em detalhes nas etapas 3 e 4 desta [página](https://docs.microsoft.com/azure/virtual-machines/linux/migration-classic-resource-manager-cli).

Para migrar o IP Reservado, execute os comandos a seguir usando o CLI do Azure.  Observação se o endereço IP não estiver associado a nenhum serviço (abaixo, há um serviço chamado **MyService** e implantação **mydeployment** ), essa etapa pode ser ignorada.

```azurecli-interactive
## Variables for the command ##
$name = 'myReservedIP'

## This section is only needed if the Reserved IP is not already disassociated from any Cloud Services ##
$service = 'myService'
$deployment = 'myDeployment'
azure network reserved-ip disassociate $name $service $deployment

azure network reserved-ip validate-migration $name
```
O comando anterior exibe todos os avisos e erros que bloqueiam a migração. Se a validação for bem-sucedida, você poderá prosseguir com as etapas a seguir para preparar e confirmar a migração:
```azurecli-interactive
azure network reserved-ip prepare-migration $name
azure network reserved-ip commit-migration $name
```
Um novo grupo de recursos no Azure Resource Manager é criado usando o nome do IP Reservado migrado (no exemplo acima, seria o grupo de recursos **myReservedIP-migrado** ).

---

## <a name="limitations"></a>Limitações

* Atualmente, esse recurso só está disponível nas seguintes regiões:<br>
Centro-Oeste dos EUA<br>
Centro-Norte dos EUA<br>
Oeste dos EUA<br>
Oeste dos EUA 2<br>
Leste da Noruega<br>
Norte da África do Sul<br>
Leste dos EUA<br>
Norte da Europa<br>
Coreia Central<br>
Central Indiana<br>
Leste dos EUA 2<br>
Norte da Suíça<br>
Oeste da Índia<br>
Norte da Alemanha<br>
Canadá Central<br>
Sul da França<br>
Oeste da Índia

* Para atualizar um IP público básico, ele não pode ser associado a nenhum recurso do Azure.  Leia [esta página](https://docs.microsoft.com/azure/virtual-network/virtual-network-public-ip-address#view-modify-settings-for-or-delete-a-public-ip-address) para obter mais informações sobre como desassociar IPS públicos.  Da mesma forma, para migrar um IP Reservado, ele não pode ser associado a nenhum serviço de nuvem.  Leia [esta página](https://docs.microsoft.com/azure/virtual-network/remove-public-ip-address-vm) para obter mais informações sobre como desassociar IPS reservados.  
* Os IPs públicos atualizados do SKU básico para o Standard continuarão sem [zonas de disponibilidade](https://docs.microsoft.com/azure/availability-zones/az-overview?toc=/azure/virtual-network/toc.json#availability-zones) e, portanto, não podem ser associados a um recurso do Azure que seja com redundância de zona ou zonas.  Observe que isso só se aplica a regiões que oferecem zonas de disponibilidade.

## <a name="next-steps"></a>Próximas etapas

- Saiba mais sobre [endereços IP públicos](virtual-network-ip-addresses-overview-arm.md#public-ip-addresses) no Azure, incluindo a diferença entre os tipos de SKU, bem como [configurações de endereço IP público](virtual-network-public-ip-address.md#create-a-public-ip-address).
- Saiba como [atualizar os balanceadores de carga públicos do Azure do básico para o Standard](https://docs.microsoft.com/azure/load-balancer/upgrade-basic-standard).
- Entenda os [IPS do Azure reservados](https://docs.microsoft.com/previous-versions/azure/virtual-network/virtual-networks-reserved-public-ip) e [a migração de recursos clássicos para Azure Resource Manager](https://docs.microsoft.com/azure/virtual-machines/windows/migration-classic-resource-manager-overview).
