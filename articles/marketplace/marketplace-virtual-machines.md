---
title: Guia de publicação para ofertas de máquina virtual no Azure Marketplace
description: Este artigo descreve os requisitos para a publicação de uma máquina virtual e uma avaliação gratuita de software a ser implantada do Azure Marketplace.
services: Azure, Marketplace, Compute, Storage, Networking, Blockchain, Security
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
author: iqshahmicrosoft
ms.author: iqshah
ms.date: 04/15/2020
ms.openlocfilehash: dac67c6dae801d11866f7c4978a050a0cb2fa1dd
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/23/2020
ms.locfileid: "87065643"
---
# <a name="publishing-guide-for-virtual-machine-offers"></a>Guia de publicação para ofertas de máquina virtual

A publicação de imagens de máquina virtual (VM) é uma das principais maneiras de publicar uma solução no Azure Marketplace. Use este guia para entender os requisitos para esse tipo de oferta. 

Ofertas de máquina virtual são ofertas de transações que são implantadas e cobradas por meio do Azure Marketplace. O plano de ação que um usuário vê é *obtê-lo agora*.

## <a name="free-trial"></a>Avaliação gratuita 

Para organizar os usuários para testar sua oferta, acesse licenças de software de prazo limitado ao usar o modelo de cobrança BYOL (traga sua própria licença). 

## <a name="test-drive"></a>Test drive

Você pode implantar uma ou mais máquinas virtuais por meio de aplicativos de infraestrutura como serviço (IaaS) ou software como serviço (SaaS). Um benefício da opção de publicação *Test Drive* é a configuração automatizada de uma máquina virtual ou uma solução completa, por um tour guiado hospedado pelo parceiro. Uma test drive permite que seus clientes avaliem as VMs sem custo adicional para elas. Um cliente não precisa ser um cliente do Azure existente para se envolver com a experiência de avaliação. 

Para começar, entre em contato conosco por email em [amp-testdrive](mailto:amp-testdrive@microsoft.com). 

|Requisitos  |Detalhes |
|---------|---------|
| Você tem um aplicativo do Azure Marketplace   |  Um ou mais máquinas de virtuais por meio de IaaS ou SaaS.      |

## <a name="interactive-demo"></a>Demonstração interativa

Com essa oferta, você dá a seus clientes uma experiência guiada de sua solução usando uma demonstração interativa. O benefício de uma opção de publicação de demonstração interativa é que você pode oferecer uma experiência de avaliação sem ter que fornecer uma configuração complicada de sua solução complexa. 

## <a name="virtual-machine-offer"></a>Oferta de máquina virtual

Use o tipo de oferta de *máquina virtual* ao implantar um dispositivo de virtualização na assinatura associada ao cliente. As VMs são totalmente habilitadas para comércio, usando modelos de licenciamento pago pelo uso ou BYOL (traga sua própria licença). A Microsoft hospeda a transação comercial e cobra o cliente em seu nome. Você tem a vantagem de usar a relação de pagamento preferencial entre o cliente e a Microsoft, incluindo os Contratos Enterprise.

> [!NOTE]
> Neste momento, os compromissos monetários associados a um Enterprise Agreement podem ser usados no uso do Azure de sua VM, mas não em relação às taxas de licenciamento de software.  
> 
> [!NOTE]
> Você pode restringir a descoberta e a implantação de sua VM a um conjunto específico de clientes publicando a imagem e os preços como uma oferta privada. As ofertas privadas desbloqueiam a capacidade de criar ofertas exclusivas para seus clientes mais próximos e oferecer softwares e termos personalizados. Os termos personalizados permitem a você destacar uma variedade de cenários, incluindo negócios de campos especializados com preços e termos especializados, e também acesso antecipado a software de versão limitada. As ofertas privadas permitem que você forneça preços ou produtos específicos a um conjunto limitado de clientes criando um novo plano com esses detalhes.  
>
> Para obter mais informações, consulte [ofertas privadas no Azure Marketplace](https://azure.microsoft.com/blog/private-offers-on-azure-marketplace).  

| Requisito | Detalhes |  
|:--- |:--- | 
| Cobrança e medição | Sua VM deve dar suporte a cobrança mensal BYOL ou paga conforme o uso. |  
| VHD (disco rígido virtual) compatível com Azure | As VMs devem ser criadas em Windows ou Linux. Para obter mais informações sobre como criar um VHD, consulte: <ul> <li>[Distribuições do Linux endossadas no Azure](../virtual-machines/linux/endorsed-distros.md) (para VHDs do Linux).</li> <li>[Crie um VHD compatível com o Azure](./partner-center-portal/azure-vm-create-offer.md) (para VHDs do Windows).</li> </ul> |  

>[!Note]
>A aceitação do canal de parceiros do CSP (provedor de soluções na nuvem) já está disponível. Para obter mais informações sobre como comercializar sua oferta por meio de canais de parceiros do Microsoft CSP, consulte [provedores de soluções de nuvem](./cloud-solution-providers.md).

## <a name="next-steps"></a>Próximas etapas

Se você ainda não fez isso, saiba como [Aumentar seus negócios na nuvem com o Azure Marketplace](https://azuremarketplace.microsoft.com/sell).

Para se registrar e começar a trabalhar no Partner Center:

- [Entre no Partner Center](https://partner.microsoft.com/dashboard/account/v3/enrollment/introduction/partnership) para criar ou concluir sua oferta.
- Consulte [criar uma oferta de máquina virtual](./partner-center-portal/azure-vm-create-offer.md) para obter mais informações.
