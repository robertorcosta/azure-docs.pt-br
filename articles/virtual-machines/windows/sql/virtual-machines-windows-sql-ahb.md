---
title: Alterar o modelo de licença para um VM do Servidor SQL no Azure
description: Aprenda a alternar o licenciamento de uma máquina virtual SQL Server no Azure do pay-as-you-go para trazer sua própria licença usando o Azure Hybrid Benefit.
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
manager: jroth
tags: azure-resource-manager
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 11/13/2019
ms.author: mathoma
ms.reviewer: jroth
ms.openlocfilehash: 502d1fe599accb29ccc99c9e527f8d1c8e1d52b8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "77201808"
---
# <a name="change-the-license-model-for-a-sql-server-virtual-machine-in-azure"></a>Alterar o modelo de licença para uma máquina virtual SQL Server no Azure
Este artigo descreve como alterar o modelo de licença para uma máquina virtual SQL Server (VM) no Azure usando o novo provedor de recursos SQL VM, **Microsoft.SqlVirtualMachine**.

Existem três modelos de licença para uma VM que hospeda o SQL Server: pay-as-you-go, Azure Hybrid Benefit e disaster recovery (DR). Você pode modificar o modelo de licença do seu VM sql server usando o portal Azure, o Azure CLI ou PowerShell. 

- O modelo **pay-as-you-go** significa que o custo por segundo de execução do Azure VM inclui o custo da licença do SQL Server.
- [O Azure Hybrid Benefit](https://azure.microsoft.com/pricing/hybrid-benefit/) permite que você use sua própria licença sql server com uma VM que está executando o SQL Server. 
- O tipo **de licença de recuperação de desastres** é usado para a réplica gratuita de [DR](virtual-machines-windows-sql-high-availability-dr.md#free-dr-replica-in-azure) no Azure. 

O Azure Hybrid Benefit permite o uso de licenças SQL Server com garantia de software ("Licença Qualificada") em máquinas virtuais Do Zure. Com o Azure Hybrid Benefit, os clientes não são cobrados pelo uso de uma licença sql server em uma VM. Mas eles ainda devem pagar pelo custo da computação em nuvem subjacente (ou seja, a taxa básica), armazenamento e backups. Eles também devem pagar por I/O associado ao uso dos serviços (conforme aplicável).

De acordo com os Termos do Produto Da Microsoft: Os clientes devem indicar que estão usando o Banco de Dados SQL do Azure (Instância Gerenciada, Pool Elástico e Banco de Dados Único), Fábrica de Dados Azure, Serviços de Integração de Servidores SQL ou Máquinas Virtuais do SQL Server o Azure Benefício híbrido para sql server ao configurar cargas de trabalho no Azure."

Para indicar o uso do Azure Hybrid Benefit para SQL Server em uma VM Azure e estar em conformidade, você tem três opções:

- Provisionar uma máquina virtual usando uma imagem do SQL Server de sua própria licença do Azure Marketplace. Esta opção está disponível apenas para clientes que possuem um Contrato Empresarial.
- Provisionar uma máquina virtual usando uma imagem pay-as-you-go SQL Server do Azure Marketplace e ativar o Azure Hybrid Benefit.
- Instale o SQL Server em uma VM Azure, registre-se manualmente [no provedor de recursos SQL VM](virtual-machines-windows-sql-register-with-resource-provider.md)e ative o Azure Hybrid Benefit.

O tipo de licença do SQL Server é definido quando a VM é provisionada. Você pode mudá-lo a qualquer hora depois. A troca entre modelos de licença não incorre em tempo de inatividade, não reinicia o VM ou o serviço SQL Server, não adiciona custos adicionais e é eficaz imediatamente. De fato, ativar o Azure Hybrid Benefit *reduz o* custo.

## <a name="prerequisites"></a>Pré-requisitos

Alterar o modelo de licenciamento do vm do servidor SQL tem os seguintes requisitos: 

- Uma [assinatura do Azure.](https://azure.microsoft.com/free/)
- Um [VM do SQL Server](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-portal-sql-server-provision) registrado no [provedor de recursos SQL VM](virtual-machines-windows-sql-register-with-resource-provider.md).
- [A Garantia de Software](https://www.microsoft.com/licensing/licensing-programs/software-assurance-default) é um requisito para utilizar o [Azure Hybrid Benefit](https://azure.microsoft.com/pricing/hybrid-benefit/). 


## <a name="vms-already-registered-with-the-resource-provider"></a>VMs já cadastrados no provedor de recursos 

# <a name="portal"></a>[Portal](#tab/azure-portal)

[!INCLUDE [windows-virtual-machines-sql-use-new-management-blade](../../../../includes/windows-virtual-machines-sql-new-resource.md)]

Você pode modificar o modelo de licença diretamente do portal: 

1. Abra o [portal Azure](https://portal.azure.com) e abra o [recurso de máquinas virtuais SQL](virtual-machines-windows-sql-manage-portal.md#access-the-sql-virtual-machines-resource) para o seu VM do Servidor SQL. 
1. Selecione **Configurar** em **Configurações**. 
1. Selecione a opção **Azure Hybrid Benefit** e selecione a caixa de seleção para confirmar se você tem uma licença do SQL Server com garantia de software. 
1. Selecione **Aplicar** na parte inferior da página **Configurar.** 

![Azure Hybrid Benefit no portal](media/virtual-machines-windows-sql-ahb/ahb-in-portal.png)


# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Você pode usar o Azure CLI para alterar seu modelo de licença.  


**Benefício híbrido do Azure**

```azurecli-interactive
# Switch your SQL Server VM license from pay-as-you-go to bring-your-own
# example: az sql vm update -n AHBTest -g AHBTest --license-type AHUB

az sql vm update -n <VMName> -g <ResourceGroupName> --license-type AHUB
```

**Pague como você vai:** 

```azurecli-interactive
# Switch your SQL Server VM license from bring-your-own to pay-as-you-go
# example: az sql vm update -n AHBTest -g AHBTest --license-type PAYG

az sql vm update -n <VMName> -g <ResourceGroupName> --license-type PAYG
```

**Recuperação de desastres (DR)**

```azurecli-interactive
# Switch your SQL Server VM license from bring-your-own to pay-as-you-go
# example: az sql vm update -n AHBTest -g AHBTest --license-type DR

az sql vm update -n <VMName> -g <ResourceGroupName> --license-type DR
```

# <a name="powershell"></a>[Powershell](#tab/azure-powershell)

Você pode usar o PowerShell para alterar seu modelo de licença.

**Benefício híbrido do Azure**

```powershell-interactive
# Switch your SQL Server VM license from pay-as-you-go to bring-your-own
Update-AzSqlVM -ResourceGroupName <resource_group_name> -Name <VM_name> -LicenseType AHUB
```

**Pague como você vai**

```powershell-interactive
# Switch your SQL Server VM license from bring-your-own to pay-as-you-go
Update-AzSqlVM -ResourceGroupName <resource_group_name> -Name <VM_name> -LicenseType PAYG
```

**Recuperação de Desastres** 

```powershell-interactive
# Switch your SQL Server VM license from bring-your-own to pay-as-you-go
Update-AzSqlVM -ResourceGroupName <resource_group_name> -Name <VM_name> -LicenseType DR
```

---

## <a name="vms-not-registered-with-the-resource-provider"></a>VMs não registrados no provedor de recursos

Se você provisionou um VM do SQL Server a partir de imagens do Azure Marketplace, então o tipo de licença do SQL Server será pago à medida que você for. Se você provisionou um VM do SQL Server usando uma imagem de trazer sua própria licença do Azure Marketplace, então o tipo de licença será AHUB. Todas as VMs do SQL Server provisionadas de padrão (pay-as-you-go) ou de trazer sua própria licença, as imagens do Azure Marketplace serão automaticamente registradas no provedor de recursos SQL VM, para que possam alterar o [tipo de licença](#vms-already-registered-with-the-resource-provider).

Você só é elegível para auto-instalar o SQL Server em uma VM Azure via Azure Hybrid Benefit. Você deve [registrar essas VMs com o provedor de recursos SQL VM](virtual-machines-windows-sql-register-with-resource-provider.md) definindo a licença do SQL Server como Azure Hybrid Benefit, para indicar o uso do Azure Hybrid Benefit de acordo com os Termos do Produto Microsoft.

Você pode alterar o tipo de licença de um VM sql server como pay-as-you-go ou Azure Hybrid Benefit somente se o VM do Servidor SQL estiver registrado no provedor de recursos SQL VM.

## <a name="remarks"></a>Comentários

- Os clientes do Azure Cloud Solution Provider (CSP) podem usar o Azure Hybrid Benefit, primeiro implantando uma VM paga e, em seguida, convertendo-a em sua própria licença, se tiverem garantia de software ativa.
- Se você soltar o recurso VM do SQL Server, você voltará para a configuração de licença codificada da imagem. 
- A capacidade de alterar o modelo de licença é uma característica do provedor de recursos SQL VM. A implantação de uma imagem do Azure Marketplace através do portal Azure registra automaticamente um VM do SQL Server com o provedor de recursos. Mas os clientes que estão auto-instalando o SQL Server precisarão registrar manualmente [seu VM do Servidor SQL](virtual-machines-windows-sql-register-with-resource-provider.md). 
- Adicionar uma VM do SQL Server a um conjunto de disponibilidade requer a recriação da VM. Como tal, todas as VMs adicionadas a um conjunto de disponibilidade voltarão para o tipo de licença padrão de pagamento. O Azure Hybrid Benefit precisará ser habilitado novamente. 


## <a name="limitations"></a>Limitações

Mudar o modelo de licença é:
   - Disponível apenas para clientes com [Garantia de Software](https://www.microsoft.com/en-us/licensing/licensing-programs/software-assurance-overview).
   - Apenas suportado para as edições Padrão e Corporativa do SQL Server. As alterações de licença para Express, Web e Developer não são suportadas. 
   - Apenas suportado para máquinas virtuais implantadas através do modelo Azure Resource Manager. As máquinas virtuais implantadas através do modelo clássico não são suportadas. 
   - Disponível apenas para o público ou nuvens do governo Azure. 
   - Apenas suportado em máquinas virtuais que possuem uma única interface de rede (NIC). 


## <a name="known-errors"></a>Erros conhecidos

### <a name="the-resource-microsoftsqlvirtualmachinesqlvirtualmachinesresource-group-under-resource-group-resource-group-was-not-found"></a>O recurso 'Microsoft.SqlVirtualMachine/SqlVirtualMachines/\<grupo de recursos\<>' em> de grupo de recursos do grupo de recursos não foi encontrado.

Esse erro ocorre quando você tenta alterar o modelo de licença em uma VM do Servidor SQL que não foi registrada no provedor de recursos SQL VM:

`The Resource 'Microsoft.SqlVirtualMachine/SqlVirtualMachines/\<resource-group>' under resource group '\<resource-group>' was not found. The property 'sqlServerLicenseType' cannot be found on this object. Verify that the property exists and can be set.`

Você precisará registrar sua assinatura com o provedor de recursos e, em seguida, [registrar sua VM sql server com o provedor de recursos](virtual-machines-windows-sql-register-with-resource-provider.md). 


### <a name="the-virtual-machine-vmname-has-more-than-one-nic-associated"></a>A máquina\<virtual '\>vmname ' tem mais de uma NIC associada

Esse erro ocorre em máquinas virtuais que possuem mais de uma NIC. Remova um dos NICs antes de alterar o modelo de licenciamento. Embora você possa adicionar a NIC de volta à VM depois de alterar o modelo de licença, as operações no portal Azure, como backup automático e patches, não serão mais suportadas. 


## <a name="next-steps"></a>Próximas etapas

Para obter mais informações, consulte os seguintes artigos: 

* [Visão geral do SQL Server em um VM windows](virtual-machines-windows-sql-server-iaas-overview.md)
* [FaQ para SQL Server em um VM Windows](virtual-machines-windows-sql-server-iaas-faq.md)
* [Orientação de preços para SQL Server em um VM Windows](virtual-machines-windows-sql-server-pricing-guidance.md)
* [Notas de versão para SQL Server em um VM windows](virtual-machines-windows-sql-server-iaas-release-notes.md)


