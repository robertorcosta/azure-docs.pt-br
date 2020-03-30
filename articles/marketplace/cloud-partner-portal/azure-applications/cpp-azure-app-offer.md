---
title: Oferta de aplicativo Azure | Mercado Azure
description: Visão geral do processo de publicação de uma oferta de Aplicativo Azure no Azure Marketplace.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 02/06/2019
ms.author: dsindona
ms.openlocfilehash: ed086ffdc49e21b819c0ee05b38ad882b4e269d7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80285309"
---
# <a name="azure-application-offer"></a>Oferta de aplicativo do Azure

|    |    |
|-----------------------------------------------------------------|------------------------------------------|
| <div class="body"> Esta seção explica como publicar uma nova oferta de aplicativo Azure no [Azure Marketplace](https://azuremarketplace.microsoft.com).  Cada aplicativo Azure contém um modelo do Azure Resource Manager que define todos os ativos técnicos usados pelo aplicativo e que normalmente inclui uma ou mais máquinas virtuais e outros serviços de suporte com base no Azure ou na Web. Todas as ofertas de aplicativo do Azure precisam habilitar a segurança de acesso por meio do [Azure Active Directory](https://docs.microsoft.com/azure/active-directory/).  </div> | ![Ícone de aplicativos do Azure](./media/azureapp-icon1.png)  |

## <a name="publishing-overview"></a>Visão geral da publicação

O vídeo a seguir, [Criando modelos de solução e aplicativos gerenciados para o Azure Marketplace](https://channel9.msdn.com/Events/Build/2018/BRK3603), é uma introdução: quais tipos de oferta estão disponíveis, quais ativos técnicos são necessários, como criar um modelo do Azure Resource Manager, desenvolvimento e teste da interface do usuário do aplicativo, como publicar a oferta do aplicativo e, por fim, o processo de revisão do aplicativo.

>[!VIDEO https://channel9.msdn.com/Events/Build/2018/BRK3603/player]


## <a name="types-of-azure-applications"></a>Tipos de aplicativos do Azure

Há dois tipos de aplicativos do Azure: aplicativos gerenciados e modelos de solução. 

- Os modelos de solução são uma das principais formas de publicar uma solução no Marketplace. Esse tipo de oferta é usado quando sua solução requer implantação adicional e automação de configuração de mais de uma única VM (máquina virtual). Você pode automatizar o fornecimento de mais de uma VM usando um modelo de solução. Essa automação inclui o provisionamento de recursos de rede e de armazenamento para fornecer soluções complexas de IaaS. Para obter uma visão geral dos requisitos de modelo de solução e o modelo de cobrança, confira [Aplicativos Azure: modelos de solução](https://docs.microsoft.com/azure/marketplace/marketplace-solution-templates).

- Aplicativos gerenciados são semelhantes a modelos de solução do mercado, com uma diferença importante. Em um aplicativo gerenciado, os recursos são implantados em um grupo de recursos gerenciado pelo distribuidor do aplicativo. O grupo de recursos está presente na assinatura do consumidor, mas uma identidade no locatário do fornecedor tem acesso ao grupo de recursos. Como fornecedor, você deve especificar o custo do suporte contínuo da solução. Use os aplicativos gerenciados do Azure para criar e entregar facilmente aplicativos totalmente gerenciados e prontos para uso aos clientes.

Além do Azure Marketplace, você também pode oferecer aplicativos gerenciados em um catálogo de serviços. O catálogo de serviços é um catálogo interno das soluções aprovadas para os usuários em uma organização. Você pode usar o catálogo para atender aos padrões organizacionais ao oferecer soluções para grupos em uma organização. Os funcionários usam o catálogo para encontrar com facilidade aplicativos recomendados e aprovados pelos departamentos de TI.

>[!Note]
>O opt-in do canal parceiro Cloud Solution Providers (CSP) já está disponível.  Consulte [os Provedores de Soluções em Nuvem](../../cloud-solution-providers.md) para obter mais informações sobre o marketing de sua oferta através dos canais parceiros microsoft CSP.

Para obter mais informações sobre as vantagens e os tipos de aplicativos gerenciados, confira a [Visão geral dos aplicativos gerenciados do Azure](https://docs.microsoft.com/azure/managed-applications/overview).


## <a name="publishing-process-workflow"></a>Publicar o fluxo de trabalho do processo

O diagrama a seguir mostra o processo de alto nível para a publicação uma oferta de aplicativo Azure.

![Fluxo de trabalho para publicação de oferta](./media/new-offer-process.png)

As etapas de alto nível para publicar uma oferta de aplicativo Azure são:

1. Atender aos [pré-requisitos](./cpp-prerequisites.md) – (não mostrado) Verifique se você atende aos requisitos comerciais e técnicos para a publicação de um aplicativo do Azure no Azure Marketplace. 

1. [Crie a oferta](./cpp-create-offer.md) - Forneça informações detalhadas sobre a oferta. Essas informações incluem: a descrição da oferta, materiais de marketing, informações de suporte e especificações de ativos.

1. [Coletar ativos técnicos e comerciais existentes ou criá-los](./cpp-create-technical-assets.md) – crie ativos comerciais (documentos legais e materiais de marketing) e ativos técnicos para a solução associada.

1. [Criar o SKU](./cpp-skus-tab.md) – crie os SKUs associados à oferta. Uma SKU exclusiva é necessária para cada imagem que você estiver planejando publicar.

1. Certificar e [publicar a oferta](./cpp-publish-offer.md) – depois que a oferta e os recursos técnicos forem concluídos, você poderá enviar a oferta. Esse envio inicia o processo de publicação. Durante esse processo, a solução é testada, validada, certificada e, em seguida, "entra em operação" no Azure Marketplace.

## <a name="next-steps"></a>Próximas etapas

Antes de considerar essas etapas, você precisa cumprir os [requisitos técnicos e comerciais](./cpp-prerequisites.md) para publicar um aplicativo gerenciado no Microsoft Azure Marketplace.
