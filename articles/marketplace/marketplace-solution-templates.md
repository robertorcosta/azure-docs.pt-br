---
title: Azure Applications Solution Template Solution Publishing Guide | Mercado Azure
description: Este artigo descreve os requisitos para publicar um modelo de solução no Azure Marketplace.
services: Azure, Marketplace, Compute, Storage, Networking, Blockchain, Security
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 9/25/2019
ms.author: dsindona
ms.openlocfilehash: c84436015ad37b57f6603551f1d328ac76181836
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80288726"
---
# <a name="azure-applications-solution-template-offer-publishing-guide"></a>Aplicativos do Azure: Guia de Publicação de Oferta de Modelo de Solução

Os modelos de solução são uma das principais formas de publicar uma solução no Marketplace. Use este guia para compreender os requisitos dessa oferta. 

Use o Azure App: oferta de modelo de solução quando a solução exigir mais automação de implantação e configuração do que um VM simples. Você pode automatizar o provisionamento de uma ou mais VMs usando Aplicativos do Azure: modelos de solução. Você também pode provisionar recursos de rede e armazenamento. O tipo de oferta Aplicativos do Azure: modelos de solução oferece benefícios de automação para VMs únicas e soluções inteiras baseadas em IaaS.

Esses modelos de soluções não são ofertas de transações, mas podem ser usados para implantar ofertas pagas de VM faturadas através do mercado comercial da Microsoft. A chamada à ação que um usuário vê é "Obtenha agora".


## <a name="requirements-for-solution-templates"></a>Requisitos dos modelos de solução

| **Requisitos** | **Detalhes**  |
| ---------------  | -----------  |
|Cobrança e medição    |  Os recursos serão provisionados na assinatura do Azure do cliente. As máquinas virtuais Pay-as-you-go (PAYGO) serão transacionadas com o cliente via Microsoft, faturadas através da assinatura do Cliente Azure (PAYGO).  <br/> No caso do modelo traga sua própria licença (BYOL), enquanto a Microsoft cobrará os custos de infraestrutura incorridos na assinatura do cliente, você negociará os valores referentes ao licenciamento de software diretamente com o cliente.   |
|VHD (disco rígido virtual) compatível com Azure  |   As VMs devem ser criadas em Windows ou Linux.  Para obter mais informações, [confira Criar um VHD compatível com o Azure](./cloud-partner-portal/virtual-machine/cpp-create-vhd.md). |
| Atribuição de uso do cliente | Habilitar a atribuição de uso do cliente é necessário em todos os modelos de solução publicados no Azure Marketplace. Para obter mais informações sobre atribuição de uso do cliente e como habilitá-lo, consulte [atribuição de uso do cliente de parceiro do Azure](./azure-partner-customer-usage-attribution.md).  |
| Usar o Managed Disks | [Discos gerenciados](https://docs.microsoft.com/azure/virtual-machines/windows/managed-disks-overview) é a opção padrão para discos persistidos de VMs IaaS no Azure. Você deve usar discos gerenciados em modelos de solução. <br> <br> 1. Siga as [orientações](https://docs.microsoft.com/azure/virtual-machines/windows/using-managed-disks-template-deployments) e [amostras](https://github.com/Azure/azure-quickstart-templates/blob/master/managed-disk-support-list.md) para usar discos gerenciados nos modelos ARM do Azure para atualizar seus modelos de solução. <br> <br> 2. Siga as instruções abaixo para importar o VHD subjacente dos Discos Gerenciados para uma conta de armazenamento para publicar o VHD como uma imagem no Marketplace: <br> <ul> <li> [Powershell](https://docs.microsoft.com/azure/virtual-machines/scripts/virtual-machines-windows-powershell-sample-copy-managed-disks-vhd?toc=%2fpowershell%2fmodule%2ftoc.json) </li> <li> [Cli](https://docs.microsoft.com/azure/virtual-machines/scripts/virtual-machines-linux-cli-sample-copy-managed-disks-vhd?toc=%2fcli%2fmodule%2ftoc.json) </li> </ul> |

## <a name="next-steps"></a>Próximas etapas
Se você ainda não fez isso, [registre](https://azuremarketplace.microsoft.com/sell) no Marketplace.

Se você estiver registrado e estiver criando uma nova oferta ou trabalhando em um existente, entre no [Portal do Cloud Partner](https://cloudpartner.azure.com) para criar ou concluir sua oferta.
