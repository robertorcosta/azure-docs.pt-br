---
title: Modelo de solução de aplicativos do Azure oferece guia de publicação | Mercado Azure
description: Este artigo descreve os requisitos para publicar um modelo de solução no Azure Marketplace.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/22/2020
ms.author: dsindona
ms.openlocfilehash: 6533fa930716552c91fffd13b196bdbf78158816
ms.sourcegitcommit: 09a124d851fbbab7bc0b14efd6ef4e0275c7ee88
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2020
ms.locfileid: "82084848"
---
# <a name="azure-applications-solution-template-offer-publishing-requirements"></a>Aplicativos Azure: Modelo de solução oferece requisitos de publicação

Este artigo explica os requisitos para o tipo de oferta de modelo de solução, que é uma maneira de publicar uma oferta de aplicativo Azure no Azure Marketplace. O tipo de oferta de modelo de solução requer um [modelo ARM (Azure Resource Manager)](../azure-resource-manager/templates/overview.md) para implantar automaticamente sua infra-estrutura de solução.

Use o modelo de oferta de solução de aplicativo do Azure quando as seguintes condições forem necessárias:

- Sua solução requer implantação e automação de configuração adicionais além de uma única VM, como uma combinação de VMs, recursos de rede e armazenamento.
- Seu cliente vai gerenciar a solução sozinho.

A chamada para ação que um usuário vê para este tipo de oferta é "Get It Now".

## <a name="requirements-for-solution-template-offers"></a>Requisitos para ofertas de modelo de solução

| **Requisitos** | **Detalhes**  |
| ---------------  | -----------  |
|Cobrança e medição    |  As ofertas de modelos de soluções não são ofertas de transação, mas podem ser usadas para implantar ofertas pagas de VM faturadas através do mercado comercial da Microsoft. Os recursos que o modelo ARM da solução implanta serão provisionados na assinatura do Azure do cliente. As máquinas virtuais Pay-as-you-go (PAYGO) serão transacionadas com o cliente via Microsoft, faturadas através da assinatura do Cliente Azure.<br/> No caso do modelo traga sua própria licença (BYOL), enquanto a Microsoft cobrará os custos de infraestrutura incorridos na assinatura do cliente, você negociará os valores referentes ao licenciamento de software diretamente com o cliente.   |
|VHD (disco rígido virtual) compatível com Azure  |   As VMs devem ser criadas em Windows ou Linux. Para obter mais informações, consulte: <ul> <li>[Crie uma oferta de aplicativo do Azure](./partner-center-portal/create-new-azure-apps-offer.md)(para VHDs do Windows)</li><li>[Distribuições Linux endossadas no Azure](https://docs.microsoft.com/azure/virtual-machines/linux/endorsed-distros) (para VHDs Linux).</li></ul> |
| Atribuição de uso do cliente | Habilitar a atribuição de uso do cliente é necessário em todos os modelos de solução publicados no Azure Marketplace. Para obter mais informações sobre atribuição de uso do cliente e como habilitá-lo, consulte [atribuição de uso do cliente de parceiro do Azure](./azure-partner-customer-usage-attribution.md).  |
| Usar o Managed Disks | [Discos gerenciados](https://docs.microsoft.com/azure/virtual-machines/windows/managed-disks-overview) é a opção padrão para discos persistidos de VMs IaaS no Azure. Você deve usar Discos Gerenciados em modelos de solução. <br> <br> 1. Siga as [orientações](https://docs.microsoft.com/azure/virtual-machines/windows/using-managed-disks-template-deployments) e [amostras](https://github.com/Azure/azure-quickstart-templates/blob/master/managed-disk-support-list.md) para usar discos gerenciados nos modelos ARM do Azure para atualizar seus modelos de solução. <br> <br> 2. Siga as instruções abaixo para importar o VHD subjacente dos Discos Gerenciados para uma conta de armazenamento para publicar o VHD como uma imagem no Marketplace: <br> <ul> <li> [PowerShell](https://docs.microsoft.com/azure/virtual-machines/scripts/virtual-machines-windows-powershell-sample-copy-managed-disks-vhd?toc=%2fpowershell%2fmodule%2ftoc.json) </li> <li> [CLI](https://docs.microsoft.com/azure/virtual-machines/scripts/virtual-machines-linux-cli-sample-copy-managed-disks-vhd?toc=%2fcli%2fmodule%2ftoc.json) </li> </ul> |

## <a name="next-steps"></a>Próximas etapas

- Se você ainda não fez isso, [saiba](https://azuremarketplace.microsoft.com/sell) mais sobre o Azure Marketplace.
- [Faça login no Partner Center](https://partner.microsoft.com/dashboard/account/v3/enrollment/introduction/partnership) para criar ou concluir sua oferta.
- [Crie uma oferta de aplicativo do Azure](./partner-center-portal/create-new-azure-apps-offer.md) para obter mais informações.
