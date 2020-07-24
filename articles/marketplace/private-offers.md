---
title: Ofertas privadas no Microsoft Commercial Marketplace
description: Ofertas privadas no Microsoft Commercial Marketplace para Publicadores de aplicativos e serviços.
ms.subservice: partnercenter-marketplace-publisher
ms.service: marketplace
ms.topic: article
author: qianw211
ms.author: dsindona
ms.date: 07/01/2020
ms.openlocfilehash: 8fac0fa18e0332f8295ecdd287e80810b5d9c671
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/23/2020
ms.locfileid: "87087345"
---
# <a name="private-offers-in-the-microsoft-commercial-marketplace"></a>Ofertas privadas no Microsoft Commercial Marketplace

As ofertas privadas no [Microsoft Azure Marketplace](https://azuremarketplace.microsoft.com/) permitem que os editores criem planos que só são visíveis para os clientes de destino. Este artigo discute as opções e os benefícios das ofertas privadas.

## <a name="unlock-enterprise-deals-with-private-offers"></a>Desbloqueie acordos empresariais com ofertas privadas

Os clientes empresariais usam cada vez mais os mercados on-line para encontrar, experimentar e comprar soluções em nuvem. Agora, com ofertas privadas, os editores podem usar o marketplace para compartilhar de forma privada soluções personalizadas com clientes segmentados com recursos que as empresas exigem:

- *O preço negociado* permite aos editores estender descontos e preços fora da lista de ofertas disponíveis publicamente.
- *Termos e condições particulares* permitem que os editores personalizem os termos e condições para um cliente específico.
- *Configurações especializadas* permitem que os editores personalizem suas máquinas virtuais, aplicativos do Azure e aplicativos SaaS para atender às necessidades individuais de um cliente. Essa opção também permite que os editores forneçam acesso antecipado a novos recursos do produto antes de serem lançados de forma mais ampla para todos os clientes.

As ofertas privadas permitem que os editores aproveitem a escala e a disponibilidade global de um mercado público, com a flexibilidade e o controle necessários para negociar e entregar ofertas e configurações personalizadas. Juntos, esses recursos abrem as portas para uma forte adoção corporativa de mercados de nuvem. As empresas agora podem comprar e vender da maneira que esperam e exigem.

As ofertas privadas estão agora disponíveis para a Máquina Virtual, o Aplicativo Azure (implementado como modelos de solução ou aplicativos gerenciados) e as ofertas de Aplicativos SaaS.

<!--- Like public offers, private offers can be created and managed via the [Cloud Partner Portal](./cloud-partner-portal-orig/cloud-partner-portal-azure-private-skus.md). Customers can be granted or revoked access to private offers in minutes.
--->

## <a name="creating-private-offers-using-plans"></a>Criando ofertas privadas usando planos

Para *ofertas novas ou existentes com planos*, os editores podem criar facilmente novas variações privadas criando novos planos (anteriormente conhecidos como SKUs) e marcá-los como particulares.  

<!--- [Private SKUs](./cloud-partner-portal-orig/cloud-partner-portal-azure-private-skus.md) --->

Os planos privados são componentes de uma oferta e só são visíveis e compráveisdos pelos clientes de destino. Os planos privados podem reutilizar as imagens base e/ou oferecer metadados já publicados para um plano público. Essa opção permite que os editores criem várias variações privadas de uma oferta pública sem precisar publicar várias versões da mesma imagem de base e oferecer metadados. Para as ofertas de máquina virtual e aplicativo do Azure somente, quando um plano privado compartilha uma imagem base com um plano público, todas as alterações na imagem base da oferta serão propagadas em todos os planos públicos e privados usando essa imagem base.

Para *novas ofertas que incluem apenas planos privados*, os editores podem criar suas ofertas como qualquer outra oferta e, em seguida, marcar os planos como particulares. As ofertas que têm apenas planos privados não poderão ser descobertos ou acessíveis por meio do Microsoft Commercial Marketplace ou do [portal do Azure](https://azure.microsoft.com/features/azure-portal/) por clientes que não estão associados à oferta.

## <a name="targeting-customers-with-private-offers"></a>Direcionando clientes com ofertas privadas

Para ofertas privadas novas e existentes, os editores podem segmentar clientes usando identificadores de assinatura. Os Publicadores que usam uma máquina virtual ou Aplicativo Azure oferta podem restringir a disponibilidade de um plano privado para uma ID de assinatura individual do Azure ou carregar um CSV de até 20.000 IDs de assinatura do Azure. Ao usar uma oferta privada de aplicativo SaaS, os editores podem associar uma ID de locatário para restringir a disponibilidade de um plano privado, usando a abordagem de carregamento manual ou CSV.

Depois que uma oferta tiver sido certificada e publicada, os clientes poderão ser atualizados ou removidos do plano em minutos usando o recurso Sincronizar assinaturas privadas. Essa funcionalidade permite que os editores atualizem com rapidez e facilidade a lista de clientes para os quais o plano privado é apresentado sem certificar ou publicar a oferta novamente.

## <a name="deploying-private-offers"></a>Implantando ofertas privadas

As ofertas privadas só são descobertas por meio do [portal do Azure](https://azure.microsoft.com/features/azure-portal/) e não são mostradas no [Microsoft AppSource](https://appsource.microsoft.com/) ou no [Azure Marketplace](https://azuremarketplace.microsoft.com). Para saber mais sobre como publicar para as diferentes vitrines do Marketplace comercial, consulte [determinar sua opção de publicação](./determine-your-listing-type.md).

Depois de entrar no portal do Azure, os clientes podem selecionar o elemento de navegação do Marketplace para acessar suas ofertas particulares. As ofertas privadas também aparecerão nos resultados da pesquisa e poderão ser implantadas por meio de modelos de linha de comando e Azure Resource Manager como qualquer outra oferta.

![[Ofertas privadas]](./media/marketplace-publishers-guide/private-offer.png)

Ofertas particulares também aparecerão nos resultados da pesquisa. Basta procurar o selo **privado** .

>[!Note]
>Ofertas privadas não são suportadas com assinaturas estabelecidas por meio de um revendedor do programa CSP (provedor de soluções na nuvem).

<!---
## Next steps

To start using private offers, follow the steps in the [Private SKUs and Plans](./cloud-partner-portal-orig/cloud-partner-portal-azure-private-skus.md) guide.
--->
