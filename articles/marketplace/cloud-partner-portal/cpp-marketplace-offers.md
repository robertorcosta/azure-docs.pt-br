---
title: Ofertas dos Marketplaces do Azure e do AppSource
description: Criando e gerenciando ofertas dos Marketplaces do Azure e do AppSource
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 03/27/2019
ms.author: dsindona
ms.openlocfilehash: 7f6fd723355426a49cff032d51da0e09f13e295d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80278477"
---
# <a name="azure-and-appsource-marketplace-offers"></a>Ofertas dos Marketplaces do Azure e do AppSource

A primeira parte desta seção apresenta operações gerais usadas para criar e gerenciar as ofertas para os Marketplaces do Azure e do AppSource.  Esta parte fornece o contexto que você precisa compreender para gerenciar tipos de oferta específicos, bem como informações técnicas que são comuns a todos os tipos de oferta.  A maior parte desta seção contém instruções detalhadas sobre como criar e gerenciar tipos de oferta específicos.  

O vídeo a seguir apresenta os vários recursos e diferentes tipos de ofertadisponíveis no Azure Marketplace ou AppSource.  Ele também aborda aspectos técnicos e empresariais importantes do processo de publicação de um aplicativo ou serviço nesses marketplaces.

> [!VIDEO https://channel9.msdn.com/Events/Build/2018/BRK2513/player]

**Criando aplicativos e serviços para o Azure Marketplace e o AppSource – Build 2018**

Para obter mais informações sobre esses marketplaces, confira o [guia de publicação do Azure Marketplace e do AppSource](../marketplace-publishers-guide.md).


## <a name="common-offer-operations"></a>Operações de oferta comuns

O processo de criação de uma nova oferta difere significativamente em todos os tipos de oferta, por exemplo, entre uma [oferta de aplicativo do Azure](./azure-applications/cpp-azure-app-offer.md) e uma [oferta de serviço de consultoria](./consulting-services/cloud-partner-portal-consulting-services-publishing-offer.md).  Em contrapartida, muitas das outras operações que você executa em uma oferta no [Portal do Cloud Partner](https://cloudpartner.azure.com) são bastante padronizadas entre os tipos de oferta.  Essas operações comuns – incluindo publicar, exibir o status, atualizar e excluir – são abordadas na seção [Gerenciar ofertas](./manage-offers/cpp-manage-offers.md)


## <a name="test-drive"></a>Test drive

*O Test Drive* é um recurso do Marketplace que fornece aos clientes uma opção de demonstração para "experimentar antes de comprar" para cada oferta habilitada para isso.  O recurso test drive está limitado ao seguinte subconjunto de tipos de [ofertas: aplicações Azure,](./azure-applications/cpp-azure-app-offer.md) [Dynamics 365 Business Central,](../cloud-partner-portal-orig/cpp-business-central-offer.md) [Dynamics 365 for Customer Engagement,](./dyn365ce/cpp-customer-engagement-offer.md) [Dynamics 365 for Finance and Operations,](../cloud-partner-portal-orig/cpp-dynamics-365-operations-offer.md) [aplicações SaaS](./saas-app/cpp-saas-offer.md)e [máquinas virtuais.](./virtual-machine/cpp-virtual-machine-offer.md)  Essa funcionalidade requer que o editor crie um modelo de Test Drive personalizado para sua oferta.  Para obter mais informações, confira a seção [Test Drive](./test-drive/what-is-test-drive.md).

Você pode procurar as ofertas do Marketplace existentes com demonstrações de Test Drive aplicando o [filtro test drive](https://azuremarketplace.microsoft.com/marketplace/apps?filters=test-drive). 


## <a name="azure-marketplace-and-appsource-offer-types"></a>Tipos de oferta do Azure Marketplace e do AppSource

A tabela a seguir lista os tipos de oferta atuais compatíveis com o [Portal do Cloud Partner](https://cloudpartner.azure.com).  Para cada tipo de oferta, ele lista os marketplaces em que a oferta pode ser listada, bem como uma descrição geral da tecnologia de solução da oferta.

|                Tipo de Oferta                |  Marketplace  |   Descrição                                                           |
|                ----------                |  -----------  |   -----------                                                           |
| [Aplicação do Azure](./azure-applications/cpp-azure-app-offer.md) | Azure | A solução é composta de uma ou mais máquinas virtuais (VMs), código do Azure personalizado opcional, implantado por meio de um modelo do Azure Resource Manager.  A implantação pode ser realizada pelo cliente por meio de um modelo de solução ou gerenciada pelo publicador. Esse tipo é usado para fornecer mais flexibilidade do que o tipo de oferta de máquina virtual fornecida.  |
| [Serviço de consultoria](./consulting-services/cloud-partner-portal-consulting-services-publishing-offer.md) | ambos | Consultores da Microsoft qualificados podem listar os respectivos serviços de domínio específico no Azure Marketplace ou no AppSource.  Os conhecimentos deles auxiliam os clientes avaliar seus problemas, criando e implantando as soluções certas para atender aos seus objetivos empresariais.  |
| [Recipiente](./containers/cpp-containers-offer.md)  | Azure | A solução é uma imagem de contêiner do Docker provisionada como um serviço baseado em Kubernetes ou como Instâncias de Contêiner do Azure. |
| [Central de Negócios Dynamics 365](../cloud-partner-portal-orig/cpp-business-central-offer.md) | AppSource | Um pacote que estende este ERP (planejamento de recursos empresariais) e o sistema de gerenciamento de negócios. |
| [Dynamics 365 for Customer Engagement](./dyn365ce/cpp-customer-engagement-offer.md) | AppSource | Um pacote que amplia esse sistema de CRM (Customer Resource Management, gerenciamento de recursos de clientes), através de seus módulos de vendas, serviços, serviços de projeto e serviços de campo  |
| [Dynamics 365 for Finance and Operations](../cloud-partner-portal-orig/cpp-dynamics-365-operations-offer.md) | AppSource | Um pacote que amplia esse serviço de planejamento de recursos corporativos (ERP) que suporta finanças avançadas, operações, manufatura e gerenciamento da cadeia de suprimentos |
| [Módulo do IoT Edge](./iot-edge-module/cpp-offer-process-parts.md) | Azure | Um contêiner compatível com o Docker que é executado em um dispositivo IoT Edge.  Ele consiste em pequenos módulos computacionais que usam uma combinação de código personalizado, outros serviços do Azure e serviços de terceiros. |
| [Aplicativo do Power BI](./power-bi/cpp-power-bi-offer.md) | AppSource | Um aplicativo Power BI que embala conteúdo personalizável do Power BI, incluindo conjuntos de dados, relatórios e dashboards |
| [Aplicativo SaaS](./saas-app/cpp-saas-offer.md) | Azure | A solução é uma assinatura de software como serviço, gerenciada pelo editor, que os usuários assinam através de uma interface personalizada que usa o Azure Active Directory. |
| [Máquina virtual](./virtual-machine/cpp-virtual-machine-offer.md)  | Azure  | A solução está contida em uma única máquina virtual implantada na assinatura do cliente.  |
| &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; |   |   |

Para obter mais informações, confira o [Guia de publicação por tipo de oferta](../publisher-guide-by-offer-type.md).


## <a name="next-steps"></a>Próximas etapas

Você aprenderá sobre as operações gerais que você pode realizar em ofertas de marketplace e seus atributos técnicos e ativos comuns no artigo [Gerenciar ofertas](./manage-offers/cpp-manage-offers.md).
