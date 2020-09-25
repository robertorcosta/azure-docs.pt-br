---
title: Alterar o modelo de licença para uma VM do SQL no Azure
description: Saiba como alternar o licenciamento para uma VM do SQL Server no Azure de "pago conforme o uso" para "traga sua própria licença" com o Benefício Híbrido do Azure.
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
tags: azure-resource-manager
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 11/13/2019
ms.author: mathoma
ms.reviewer: jroth
ms.openlocfilehash: b09d808201d58b571b2fe5ceb2e228d4e1c21d11
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/25/2020
ms.locfileid: "91316946"
---
# <a name="change-the-license-model-for-a-sql-virtual-machine-in-azure"></a>Alterar o modelo de licença para uma máquina virtual do SQL no Azure
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]


Este artigo descreve como alterar o modelo de licença de uma VM (máquina virtual) do SQL Server no Azure usando o novo provedor de recursos de VM do SQL, o **Microsoft.SqlVirtualMachine**.

Há três modelos de licença para uma VM que está hospedando o SQL Server: pago conforme o uso, AHB (Benefício Híbrido do Azure) e DR (Recuperação de Desastre). É possível modificar o modelo de licença da VM do SQL Server no portal do Azure, na CLI do Azure ou no PowerShell. 

- No modelo **pago conforme o uso**, o custo por segundo da execução da VM do Azure inclui o custo da licença do SQL Server.
- O [Benefício Híbrido do Azure](https://azure.microsoft.com/pricing/hybrid-benefit/) permite que você use sua licença do SQL Server em uma VM que está executando o SQL Server. 
- O tipo de licença de **recuperação de desastre** é usado para a [réplica de DR gratuita](business-continuity-high-availability-disaster-recovery-hadr-overview.md#free-dr-replica-in-azure) no Azure. 

O Benefício Híbrido do Azure permite o uso de licenças do SQL Server com o Software Assurance ("Licença Qualificada") em máquinas virtuais do Azure. Com o Benefício Híbrido do Azure, os clientes não são cobrados pelo uso de uma licença do SQL Server em uma VM. Mas eles ainda precisam pagar pelo custo da computação em nuvem subjacente (ou seja, a taxa base), o armazenamento e os backups. Também devem pagar pela E/S associada ao uso dos serviços (conforme aplicável).

De acordo com os Termos de Produto da Microsoft: "Os clientes devem indicar que estão usando o Banco de Dados SQL do Azure (Instância Gerenciada, Pool Elástico e Banco de Dados Individual), o Azure Data Factory, o SQL Server Integration Services ou as Máquinas Virtuais do SQL Server no Benefício Híbrido do Azure para SQL Server ao configurar cargas de trabalho no Azure."

Existem três opções para indicar o uso do Benefício Híbrido do Azure para SQL Server em uma VM do Azure e ficar em conformidade:

- Provisionar uma máquina virtual usando uma imagem do SQL Server do tipo "traga sua própria licença" do Azure Marketplace. Essa opção está disponível somente para clientes que têm um Contrato Enterprise.
- Provisionar uma máquina virtual usando uma imagem do SQL Server do tipo "pago conforme o uso" do Azure Marketplace e ativar o Benefício Híbrido do Azure.
- Instalar automaticamente o SQL Server em uma VM do Azure, [registrar-se manualmente no provedor de recursos de VM do SQL](sql-vm-resource-provider-register.md) e ativar o Benefício Híbrido do Azure.

O tipo de licença do SQL Server pode ser configurado quando a VM é provisionada ou a qualquer momento depois disso. Alternar entre os modelos de licença não gera tempo de inatividade, não reinicia a VM ou o serviço SQL Server, não gera custos adicionais e entra em vigor imediatamente. Na verdade, ativar o Benefício Híbrido do Azure *reduz* o custo.

## <a name="prerequisites"></a>Pré-requisitos

Para alterar o modelo de licenciamento da VM do SQL Server, é preciso cumprir os seguintes requisitos: 

- Uma [assinatura do Azure](https://azure.microsoft.com/free/).
- Uma [VM do SQL Server](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-portal-sql-server-provision) registrada no [provedor de recursos de VM do SQL](sql-vm-resource-provider-register.md).
- O [Software Assurance](https://www.microsoft.com/licensing/licensing-programs/software-assurance-default) é um requisito para usar o [Benefício Híbrido do Azure](https://azure.microsoft.com/pricing/hybrid-benefit/). 


## <a name="vms-already-registered-with-the-resource-provider"></a>VMs já registradas no provedor de recursos 

# <a name="the-azure-portal"></a>[O portal do Azure](#tab/azure-portal)

[!INCLUDE [windows-virtual-machines-sql-use-new-management-blade](../../../../includes/windows-virtual-machines-sql-new-resource.md)]

É possível modificar o modelo de licença diretamente no portal: 

1. Abra o [portal do Azure](https://portal.azure.com) e o [recurso de máquinas virtuais do SQL](manage-sql-vm-portal.md#access-the-sql-virtual-machines-resource) da VM do SQL Server. 
1. Selecione **Configurar** em **Configurações**. 
1. Selecione a opção **Benefício Híbrido do Azure** e marque a caixa de seleção para confirmar que você tem uma licença do SQL Server com Software Assurance. 
1. Clique em **Aplicar** na parte inferior da página **Configurar**. 

![Benefício Híbrido do Azure no portal](./media/licensing-model-azure-hybrid-benefit-ahb-change/ahb-in-portal.png)


# <a name="the-azure-cli"></a>[A CLI do Azure](#tab/azure-cli)

É possível usar a CLI do Azure para alterar o modelo de licença.  


**Benefício Híbrido do Azure**

```azurecli-interactive
# Switch your SQL Server VM license from pay-as-you-go to bring-your-own
# example: az sql vm update -n AHBTest -g AHBTest --license-type AHUB

az sql vm update -n <VMName> -g <ResourceGroupName> --license-type AHUB
```

**Pago conforme o uso**: 

```azurecli-interactive
# Switch your SQL Server VM license from bring-your-own to pay-as-you-go
# example: az sql vm update -n AHBTest -g AHBTest --license-type PAYG

az sql vm update -n <VMName> -g <ResourceGroupName> --license-type PAYG
```

**DR (recuperação de desastre)**

```azurecli-interactive
# Switch your SQL Server VM license from bring-your-own to pay-as-you-go
# example: az sql vm update -n AHBTest -g AHBTest --license-type DR

az sql vm update -n <VMName> -g <ResourceGroupName> --license-type DR
```

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

É possível usar o PowerShell para alterar o modelo de licença.

**Benefício Híbrido do Azure**

```powershell-interactive
# Switch your SQL Server VM license from pay-as-you-go to bring-your-own
Update-AzSqlVM -ResourceGroupName <resource_group_name> -Name <VM_name> -LicenseType AHUB
```

**Pago conforme o uso**

```powershell-interactive
# Switch your SQL Server VM license from bring-your-own to pay-as-you-go
Update-AzSqlVM -ResourceGroupName <resource_group_name> -Name <VM_name> -LicenseType PAYG
```

**Recuperação de Desastre** 

```powershell-interactive
# Switch your SQL Server VM license from bring-your-own to pay-as-you-go
Update-AzSqlVM -ResourceGroupName <resource_group_name> -Name <VM_name> -LicenseType DR
```

---

## <a name="vms-not-registered-with-the-resource-provider"></a>VMs não registradas no provedor de recursos

Se você tiver provisionado uma VM do SQL Server com base em imagens do Azure Marketplace pagas conforme o uso, o tipo de licença do SQL Server será pago conforme o uso. Se você tiver provisionado uma VM do SQL Server usando uma imagem do tipo "traga sua própria licença" do Azure Marketplace, o tipo de licença será AHUB. Todas as VMs do SQL Server provisionadas com base em imagens do Azure Marketplace padrão (pagas conforme o uso) ou do tipo "traga sua própria licença" serão automaticamente registradas no provedor de recursos de VM do SQL, de modo que seja possível alterar o [tipo da licença](#vms-already-registered-with-the-resource-provider).

Você está qualificado para instalar automaticamente o SQL Server em uma VM do Azure somente por meio do Benefício Híbrido do Azure. É preciso [registrar essas VMs no provedor de recursos de VM do SQL](sql-vm-resource-provider-register.md) configurando a licença do SQL Server como Benefício Híbrido do Azure, para indicar o uso do Benefício Híbrido do Azure de acordo com os Termos de Produto da Microsoft.

Você poderá alterar o tipo de licença de uma VM do SQL Server como pago conforme o uso ou Benefício Híbrido do Azure somente se a VM do SQL Server estiver registrada no provedor de recursos de VM do SQL.

## <a name="remarks"></a>Comentários

- Os clientes do CSP (Provedor de Soluções de Nuvem) do Azure poderão usar o Benefício Híbrido do Azure primeiro implantando uma VM paga conforme o uso e, em seguida, convertendo-a para traga sua própria licença, se tiverem o Software Assurance ativo.
- Se você remover o recurso de VM do SQL Server, voltará para a configuração de licença embutida em código da imagem. 
- A capacidade de alterar o modelo de licença é um recurso do provedor de recursos de VM do SQL. Implantar uma imagem do Azure Marketplace por meio do portal do Azure registra automaticamente uma VM do SQL Server no provedor de recursos. Mas os clientes que estão instalando automaticamente o SQL Server precisarão [registrar manualmente a VM do SQL Server](sql-vm-resource-provider-register.md). 
- Para adicionar uma VM do SQL Server a um conjunto de disponibilidade, é preciso criar a VM novamente. Assim, todas as VMs adicionadas a um conjunto de disponibilidade voltarão para o tipo de licença padrão paga conforme o uso. Será necessário habilitar o Benefício Híbrido do Azure novamente. 


## <a name="limitations"></a>Limitações

A alteração do modelo de licença:
   - Está disponível somente para clientes com [Software Assurance](https://www.microsoft.com/en-us/licensing/licensing-programs/software-assurance-overview).
   - Só é compatível com as edições Standard e Enterprise do SQL Server. As alterações de licença não são compatíveis com o Express, o Web e o Developer. 
   - Só é compatível com as máquinas virtuais implantadas por meio do modelo do Azure Resource Manager. As máquinas virtuais implantadas por meio do modelo clássico não são compatíveis. 
   - Está disponível somente para as nuvens públicas ou do Azure Government. 
   - Só é compatível com as máquinas virtuais que têm um NIC (adaptador de rede) exclusivo. 


## <a name="known-errors"></a>Erros conhecidos

Examine os erros comumente conhecidos e suas resoluções. 

**O recurso ' Microsoft. SqlVirtualMachine/SqlVirtualMachines/ \<resource-group> ' no grupo de recursos ' \<resource-group> ' não foi encontrado.**

Esse erro ocorre quando você tenta alterar o modelo de licença em uma VM do SQL Server que não foi registrada no provedor de recursos de VM do SQL:

`The Resource 'Microsoft.SqlVirtualMachine/SqlVirtualMachines/\<resource-group>' under resource group '\<resource-group>' was not found. The property 'sqlServerLicenseType' cannot be found on this object. Verify that the property exists and can be set.`

Você precisará registrar sua assinatura no provedor de recursos e, em seguida, [registrar a VM do SQL Server no provedor de recursos](sql-vm-resource-provider-register.md). 


**A máquina virtual ' \<vmname\> ' tem mais de uma NIC associada**

Esse erro ocorre em máquinas virtuais que têm mais de um NIC. Remova um dos NICs antes de alterar o modelo de licenciamento. Embora seja possível adicionar o NIC de volta à VM depois de alterar o modelo de licença, as operações no portal do Azure, como backup automático e aplicação de patches, não terão mais compatibilidade. 


## <a name="next-steps"></a>Próximas etapas

Para obter mais informações, consulte os seguintes artigos: 

* [Visão geral do SQL Server em uma VM do Windows](sql-server-on-azure-vm-iaas-what-is-overview.md)
* [Perguntas frequentes sobre o SQL Server em uma VM do Windows](frequently-asked-questions-faq.md)
* [Diretrizes de preço para o SQL Server em uma VM do Windows](pricing-guidance.md)
* [Notas sobre a versão do SQL Server em uma VM do Windows](../../database/doc-changes-updates-release-notes.md)


