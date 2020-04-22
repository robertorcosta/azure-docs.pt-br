---
title: Ofertas privadas | Mercado Azure
description: Ofertas particulares no Azure Marketplace para editores de aplicativos e serviços.
author: qianw211
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/19/2020
ms.author: dsindona
ms.openlocfilehash: efda9fb66a65f1f2cd40de8bf32a8a3fa99aea7f
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/21/2020
ms.locfileid: "81681424"
---
# <a name="private-offers"></a>Ofertas privadas

As ofertas particulares no [Microsoft Azure Marketplace](https://azuremarketplace.microsoft.com/) permitem que os editores criem SKUs visíveis apenas para clientes segmentados.

## <a name="unlock-enterprise-deals-with-private-offers"></a>Desbloqueie negócios corporativos com ofertas privadas

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

SKUs particulares e planos são componentes de uma oferta e só são visíveis e comprados pelos clientes segmentados. SKUs e planos particulares podem reutilizar as imagens de base e/ou oferecer metadados já publicados para um SKU ou plano público. Essa opção permite que os editores criem várias variações privadas de uma oferta pública sem precisar publicar várias versões da mesma imagem de base e oferecer metadados. Para o aplicativo Virtual Machine e Azure oferece apenas, quando um SKU privado compartilha uma imagem base com um SKU público, qualquer alteração na imagem base da oferta se propagará em todas as SKUs públicas e privadas usando essa imagem base.

Para *novas ofertas que incluam apenas SKUs ou planos privados*, os editores podem criar ofertas como qualquer outra oferta e marcar os SKUs ou os planos como privados. As ofertas que possuem apenas SKUs privadas ou planos não serão descobertas ou acessíveis através do mercado comercial da Microsoft ou do [portal Azure](https://azure.microsoft.com/features/azure-portal/) por clientes que não estão associados à oferta.

## <a name="targeting-customers-with-private-offers"></a>Direcionamento de clientes com ofertas privadas

Para ofertas privadas novas e existentes, os editores podem segmentar clientes usando identificadores de assinatura. Os editores que usam uma oferta de Máquina Virtual ou Aplicativo Azure AD podem restringir a disponibilidade de um SKU particular a um ID individual de assinatura do Azure ou carregar um CSV de até 20.000 IDs de assinatura do Azure. Ao usar uma oferta privada do SaaS App, os editores podem associar um ID de inquilino para restringir a disponibilidade de um plano privado, usando a abordagem manual ou de upload do CSV.

Depois que uma oferta é certificada e publicada, os clientes podem ser atualizados ou removidos do SKU ou do Plan em questão de minutos, usando o recurso Sincronizar assinaturas privadas. Esse recurso permite que os editores atualizem de forma rápida e fácil a lista de clientes aos quais o SKU ou plano privado é apresentado sem certificar ou publicar a oferta novamente.

## <a name="deploying-private-offers"></a>Implantação de ofertas privadas

Ofertas privadas só são descobertas através do [portal Azure](https://azure.microsoft.com/features/azure-portal/) e não são mostradas no [Microsoft AppSource](https://appsource.microsoft.com/) ou [no Azure Marketplace](https://azuremarketplace.microsoft.com). Para saber mais sobre a publicação nas diferentes vitrines do mercado comercial, consulte [Determine sua opção de publicação](./determine-your-listing-type.md).

Uma vez logado no portal do Microsoft Azure, os clientes podem selecionar o elemento de navegação do Marketplace para acessar suas ofertas privadas. Ofertas privadas também aparecerão nos resultados de pesquisa e podem ser implantadas através da linha de comando e modelos do Azure Resource Manager como qualquer outra oferta.

![[Ofertas privadas]](./media/marketplace-publishers-guide/private-offer.png)

Ofertas particulares também aparecerão nos resultados da pesquisa. Basta procurar o distintivo "Soldado".

>[!Note]
>As ofertas privadas não são suportadas com assinaturas estabelecidas através de um revendedor do programa Cloud Solution Provider (CSP).

<!---
## Next steps

To start using private offers, follow the steps in the [Private SKUs and Plans](./cloud-partner-portal-orig/cloud-partner-portal-azure-private-skus.md) guide.

--->