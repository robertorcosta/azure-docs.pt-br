---
title: Guia de publicação por tipo de oferta-Microsoft Commercial Marketplace
description: Este artigo descreve os tipos de oferta que estão disponíveis no Microsoft Commercial Marketplace.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
author: keferna
ms.author: keferna
ms.date: 10/06/2020
ms.openlocfilehash: 54775d7e9923505c6e68a47feed6726b9cb84c47
ms.sourcegitcommit: 857859267e0820d0c555f5438dc415fc861d9a6b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93129925"
---
# <a name="publishing-guide-by-offer-type"></a>Guia de publicação por tipo de oferta

Este artigo descreve os tipos de oferta que estão disponíveis no mercado comercial. O *tipo de oferta* define a estrutura da oferta, que inclui os metadados, os artefatos e outros conteúdos apresentados no mercado comercial.

Depois [de decidir sobre uma opção de publicação](determine-your-listing-type.md), você deve escolher um tipo de oferta antes de começar a criar sua oferta no Partner Center. O tipo de oferta corresponderá ao tipo de solução, aplicativo, ou oferta de serviço que você deseja publicar, bem como seu alinhamento com produtos e serviços Microsoft.

Você pode configurar um único tipo de oferta de maneiras diferentes para habilitar diferentes opções de publicação, a opção de listagem, o provisionamento ou o preço. A opção de publicação e a configuração do tipo de oferta também são alinhados aos requisitos técnicos e de qualificação da oferta.

Certifique-se de examinar a loja online e oferecer requisitos de qualificação de tipo e os requisitos de publicação técnica antes de criar sua oferta.

## <a name="list-of-offer-types"></a>Lista de tipos de oferta

A tabela a seguir mostra os tipos de oferta do Marketplace comercial no Partner Center.

| **Tipo de oferta**    | **Descrição**  |
| :------------------- | :-------------------|
| [Aplicativo Azure: **aplicativo gerenciado**](marketplace-managed-apps.md) | Use o tipo de oferta de aplicativo Aplicativo Azure: Managed quando as seguintes condições forem necessárias: <br> <ul> <li>Você pode implantar uma solução baseada em assinatura para o cliente usando uma VM ou uma solução inteira com base em IaaS. </li> <li>Você ou seu cliente exigem que a solução seja gerenciada por um parceiro. </li> <ul> |
| [Aplicativo Azure: **modelo de solução**](marketplace-solution-templates.md) | Use o tipo de oferta de modelo de solução Aplicativo Azure: quando sua solução exigir implantação adicional e automação de configuração além de uma única VM. Os modelos de solução podem empregar vários tipos diferentes de recursos do Azure, incluindo, mas não se limitando a VMs.  |
  | [**Contêiner do Azure**](marketplace-containers.md) | Use o tipo de oferta de contêiner do Azure quando sua solução for uma imagem de contêiner do Docker provisionado como um serviço de contêiner do Azure baseado em kubernetes. |
| [**Máquina virtual do Azure**](marketplace-virtual-machines.md) | Use o tipo de oferta de Máquina Virtual quando implantar um dispositivo virtual na assinatura associada ao cliente. |
| [**Serviço de consultoria**](consulting-services.md) | Os serviços de consultoria ajudam a conectar clientes com serviços para dar suporte e estender o uso dos serviços Azure, Dynamics 365 ou Power Suite.|
| [**Dynamics 365**](appsource-offer-publishing-guide.md) | Você pode publicar ofertas de AppSource que criam ou estendem o Dynamics 365 Business central, o compromisso com o cliente do Dynamics 365, os aplicativos de energia e os aplicativos de finanças e operações.|
| [**Módulo do IoT Edge**](iot-edge-module.md) | Os módulos do Azure IoT Edge são as menores unidades de computação gerenciadas pelo IoT Edge e podem conter serviços da Microsoft (como o Azure Stream Analytics), serviços de terceiros ou o código específico da sua própria solução. |
| [**Serviço gerenciado**](partner-center-portal/create-new-managed-service-offer.md) | Você pode criar ofertas de serviço gerenciado e gerenciar assinaturas delegadas pelo cliente ou grupos de recursos por meio [do Azure Lighthouse](../lighthouse/overview.md).|
| [**Power bi aplicativo** <br/> **Microsoft 365**](appsource-offer-publishing-guide.md) | Você pode publicar ofertas de AppSource que se baseiam ou estendem Power BI e Microsoft 365.|
| [**Software como serviço**](plan-saas-offer.md) | Use o tipo de oferta SaaS (software como serviço) para permitir que o cliente compre sua solução técnica baseada em SaaS como uma assinatura. |


Para obter informações sobre os requisitos de logon único listando opções e tipos de oferta, consulte [**requisitos do Azure ad**](enable-appsource-marketplace-using-azure-ad.md).

## <a name="next-steps"></a>Próximas etapas

- Examine os requisitos de qualificação no artigo correspondente para seu tipo de oferta para finalizar a seleção e a configuração de sua oferta.
- Examine os padrões de publicação de cada loja online para obter exemplos de como sua solução é mapeada para um tipo de oferta e configuração.