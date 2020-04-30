---
title: Guia de publicação da oferta de modelo de solução de aplicativos do Azure | Azure Marketplace
description: Este artigo descreve os requisitos para publicar um modelo de solução no Azure Marketplace.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/22/2020
ms.author: dsindona
ms.openlocfilehash: 6533fa930716552c91fffd13b196bdbf78158816
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/28/2020
ms.locfileid: "82084848"
---
# <a name="azure-applications-solution-template-offer-publishing-requirements"></a>Aplicativos do Azure: requisitos de publicação de oferta de modelo de solução

Este artigo explica os requisitos para o tipo de oferta de modelo de solução, que é uma maneira de publicar uma oferta de aplicativo do Azure no Azure Marketplace. O tipo de oferta de modelo de solução requer um [modelo de Azure Resource Manager (modelo ARM)](../azure-resource-manager/templates/overview.md) para implantar automaticamente sua infraestrutura de solução.

Use o tipo de oferta de modelo de solução de aplicativo do Azure quando as seguintes condições forem necessárias:

- Sua solução requer implantação adicional e automação de configuração além de uma única VM, como uma combinação de VMs, rede e recursos de armazenamento.
- Seu cliente vai gerenciar a solução em si.

O plano de ação que um usuário vê para esse tipo de oferta é "obter agora".

## <a name="requirements-for-solution-template-offers"></a>Requisitos para ofertas de modelo de solução

| **Requisitos** | **Detalhes**  |
| ---------------  | -----------  |
|Cobrança e medição    |  As ofertas de modelo de solução não são ofertas do Transact, mas podem ser usadas para implantar ofertas de VM pagas cobradas pelo Microsoft Commercial Marketplace. Os recursos implantados pelo modelo ARM da solução serão provisionados na assinatura do Azure do cliente. As máquinas virtuais PAYGO (pré-pagas) serão transacionadas com o cliente pela Microsoft, cobradas por meio da assinatura do Azure do cliente.<br/> No caso do modelo traga sua própria licença (BYOL), enquanto a Microsoft cobrará os custos de infraestrutura incorridos na assinatura do cliente, você negociará os valores referentes ao licenciamento de software diretamente com o cliente.   |
|VHD (disco rígido virtual) compatível com Azure  |   As VMs devem ser criadas em Windows ou Linux. Para obter mais informações, consulte: <ul> <li>[Criar uma oferta de aplicativo do Azure](./partner-center-portal/create-new-azure-apps-offer.md)(para VHDs do Windows)</li><li>[Distribuições do Linux endossadas no Azure](https://docs.microsoft.com/azure/virtual-machines/linux/endorsed-distros) (para VHDs do Linux).</li></ul> |
| Atribuição de uso do cliente | Habilitar a atribuição de uso do cliente é necessário em todos os modelos de solução publicados no Azure Marketplace. Para obter mais informações sobre atribuição de uso do cliente e como habilitá-lo, consulte [atribuição de uso do cliente de parceiro do Azure](./azure-partner-customer-usage-attribution.md).  |
| Usar o Managed Disks | [Managed disks](https://docs.microsoft.com/azure/virtual-machines/windows/managed-disks-overview) é a opção padrão para discos persistentes de VMs de IaaS no Azure. Você deve usar Managed Disks em modelos de solução. <br> <br> 1. siga as [orientações](https://docs.microsoft.com/azure/virtual-machines/windows/using-managed-disks-template-deployments) e [exemplos](https://github.com/Azure/azure-quickstart-templates/blob/master/managed-disk-support-list.md) para usar Managed DISKS nos modelos de ARM do Azure para atualizar seus modelos de solução. <br> <br> 2. siga as instruções abaixo para importar o VHD subjacente do Managed Disks para uma conta de armazenamento para publicar o VHD como uma imagem no Marketplace: <br> <ul> <li> [PowerShell](https://docs.microsoft.com/azure/virtual-machines/scripts/virtual-machines-windows-powershell-sample-copy-managed-disks-vhd?toc=%2fpowershell%2fmodule%2ftoc.json) </li> <li> [CLI](https://docs.microsoft.com/azure/virtual-machines/scripts/virtual-machines-linux-cli-sample-copy-managed-disks-vhd?toc=%2fcli%2fmodule%2ftoc.json) </li> </ul> |

## <a name="next-steps"></a>Próximas etapas

- Se você ainda não fez isso, [saiba mais](https://azuremarketplace.microsoft.com/sell) sobre o Azure Marketplace.
- [Entre no Partner Center](https://partner.microsoft.com/dashboard/account/v3/enrollment/introduction/partnership) para criar ou concluir sua oferta.
- [Crie uma oferta de aplicativo do Azure](./partner-center-portal/create-new-azure-apps-offer.md) para obter mais informações.
