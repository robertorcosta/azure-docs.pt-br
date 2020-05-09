---
title: Ofertas privadas no Microsoft Commercial Marketplace
description: Ofertas privadas no Microsoft Commercial Marketplace para Publicadores de aplicativos e serviços.
author: qianw211
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/19/2020
ms.author: dsindona
ms.openlocfilehash: e449f65ca65a679aa6c6823938fa561b84e38368
ms.sourcegitcommit: f57297af0ea729ab76081c98da2243d6b1f6fa63
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/06/2020
ms.locfileid: "82872385"
---
# <a name="private-offers-in-the-microsoft-commercial-marketplace"></a>Ofertas privadas no Microsoft Commercial Marketplace

As ofertas privadas no [Microsoft Azure Marketplace](https://azuremarketplace.microsoft.com/) permitem que os editores criem planos que só são visíveis para os clientes de destino. Neste artigo, você aprenderá sobre as opções e os benefícios das ofertas privadas.

## <a name="unlock-enterprise-deals-with-private-offers"></a>Desbloqueie acordos empresariais com ofertas privadas

Os clientes empresariais usam cada vez mais os mercados on-line para encontrar, experimentar e comprar soluções em nuvem. Agora, com ofertas privadas, os editores podem usar o marketplace para compartilhar de forma privada soluções personalizadas com clientes segmentados com recursos que as empresas exigem:

- *O preço negociado* permite aos editores estender descontos e preços fora da lista de ofertas disponíveis publicamente.
- *Termos e condições particulares* permitem que os editores personalizem os termos e condições para um cliente específico.
- *Configurações especializadas* permitem que os editores personalizem suas máquinas virtuais, aplicativos do Azure e aplicativos SaaS para atender às necessidades individuais de um cliente. Essa opção também permite que os editores forneçam acesso antecipado a novos recursos do produto antes de serem lançados de forma mais ampla para todos os clientes.

As ofertas privadas permitem que os editores aproveitem a escala e a disponibilidade global de um mercado público, com a flexibilidade e o controle necessários para negociar e entregar ofertas e configurações personalizadas. Juntos, esses recursos abrem as portas para uma forte adoção corporativa de mercados de nuvem. As empresas agora podem comprar e vender da maneira que esperam e exigem.

As ofertas privadas estão agora disponíveis para a Máquina Virtual, o Aplicativo Azure (implementado como modelos de solução ou aplicativos gerenciados) e as ofertas de Aplicativos SaaS. 

<!--- Like public offers, private offers can be created and managed via the [Cloud Partner Portal](./cloud-partner-portal-orig/cloud-partner-portal-azure-private-skus.md).  Customers can be granted or revoked access to private offers in minutes.
--->

## <a name="creating-private-offers-using-skus-and-plans"></a>Criando ofertas privadas usando SKUs e planos

Para *ofertas novas ou existentes com SKUs ou planos públicos*, os editores podem criar facilmente novas variações privadas, criando novos SKUs ou planos e marcando-os como privados.  

<!--- [Private SKUs](./cloud-partner-portal-orig/cloud-partner-portal-azure-private-skus.md) ---> 

SKUs particulares e planos são componentes de uma oferta e só são visíveis e comprados pelos clientes segmentados. SKUs e planos particulares podem reutilizar as imagens de base e/ou oferecer metadados já publicados para um SKU ou plano público. Essa opção permite que os editores criem várias variações privadas de uma oferta pública sem precisar publicar várias versões da mesma imagem de base e oferecer metadados. Para as ofertas de máquina virtual e aplicativo do Azure somente, quando uma SKU privada compartilha uma imagem base com um SKU público, todas as alterações na imagem base da oferta serão propagadas em todas as SKUs públicas e privadas usando essa imagem base.

Para *novas ofertas que incluam apenas SKUs ou planos privados*, os editores podem criar ofertas como qualquer outra oferta e marcar os SKUs ou os planos como privados. As ofertas que têm apenas os SKUs ou os planos privados não poderão ser descobertos ou acessíveis por meio do Microsoft Commercial Marketplace ou do [portal do Azure](https://azure.microsoft.com/features/azure-portal/) por clientes que não estão associados à oferta.

## <a name="targeting-customers-with-private-offers"></a>Direcionando clientes com ofertas privadas

Para ofertas privadas novas e existentes, os editores podem segmentar clientes usando identificadores de assinatura. Os editores que usam uma oferta de Máquina Virtual ou Aplicativo Azure AD podem restringir a disponibilidade de um SKU particular a um ID individual de assinatura do Azure ou carregar um CSV de até 20.000 IDs de assinatura do Azure. Ao usar uma oferta privada de aplicativo SaaS, os editores podem associar uma ID de locatário para restringir a disponibilidade de um plano privado, usando a abordagem de carregamento manual ou CSV.

Depois que uma oferta é certificada e publicada, os clientes podem ser atualizados ou removidos do SKU ou do Plan em questão de minutos, usando o recurso Sincronizar assinaturas privadas. Esse recurso permite que os editores atualizem com rapidez e facilidade a lista de clientes para os quais a SKU ou o plano privado é apresentado sem certificar ou publicar a oferta novamente.

## <a name="deploying-private-offers"></a>Implantando ofertas privadas

As ofertas privadas só são descobertas por meio do [portal do Azure](https://azure.microsoft.com/features/azure-portal/) e não são mostradas no [Microsoft AppSource](https://appsource.microsoft.com/) ou no [Azure Marketplace](https://azuremarketplace.microsoft.com). Para saber mais sobre como publicar para as diferentes vitrines do Marketplace comercial, consulte [determinar sua opção de publicação](./determine-your-listing-type.md).

Uma vez logado no portal do Microsoft Azure, os clientes podem selecionar o elemento de navegação do Marketplace para acessar suas ofertas privadas. As ofertas privadas também aparecerão nos resultados da pesquisa e poderão ser implantadas por meio de modelos de linha de comando e Azure Resource Manager como qualquer outra oferta.

![[Ofertas privadas]](./media/marketplace-publishers-guide/private-offer.png)

Ofertas particulares também aparecerão nos resultados da pesquisa. Basta procurar o selo "privado".

>[!Note]
>Ofertas privadas não são suportadas com assinaturas estabelecidas por meio de um revendedor do CSP (programa de provedor de soluções na nuvem).

<!---
## Next steps

To start using private offers, follow the steps in the [Private SKUs and Plans](./cloud-partner-portal-orig/cloud-partner-portal-azure-private-skus.md) guide.

--->