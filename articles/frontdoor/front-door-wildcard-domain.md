---
title: Porta da Frente do Azure - Suporte para domínios curinga
description: Este artigo ajuda você a entender como o Azure Front Door suporta o mapeamento e o gerenciamento de domínios curinga na lista de domínios personalizados.
services: frontdoor
author: sharad4u
ms.service: frontdoor
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/10/2020
ms.author: sharadag
ms.openlocfilehash: 6d8a6d6f0b05b9b7fd0144959c82b6a2c9e659a3
ms.sourcegitcommit: d57d2be09e67d7afed4b7565f9e3effdcc4a55bf
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/22/2020
ms.locfileid: "81768309"
---
# <a name="wildcard-domains"></a>Domínios curinga

Além dos domínios e subdomínios do apex, você pode mapear um nome de domínio curinga para sua lista de hosts front-end ou domínios personalizados no perfil do Azure Front Door. Ter domínios curingas na configuração do Azure Front Door simplifica o comportamento de roteamento de tráfego para vários subdomínios para uma API, aplicativo ou site da mesma regra de roteamento. Você não precisa modificar a configuração para adicionar ou especificar cada subdomínio separadamente. Como exemplo, você pode definir `customer1.contoso.com`o `customer2.contoso.com`roteamento para , e `customerN.contoso.com` usando a `*.contoso.com`mesma regra de roteamento e adicionando o domínio curinga .

Os principais cenários que são melhorados com suporte para domínios curinga incluem:

- Você não precisa embarcar em cada subdomínio no perfil do Azure Front Door e, em seguida, habilitar o HTTPS para vincular um certificado para cada subdomínio.
- Você não é mais obrigado a alterar sua configuração de produção do Azure Front Door se um aplicativo adicionar um novo subdomínio. Anteriormente, você tinha que adicionar o subdomínio, vincular um certificado a ele, anexar uma diretiva waf (web application firewall) e, em seguida, adicionar o domínio a diferentes regras de roteamento.

> [!NOTE]
> Atualmente, os domínios curinga só são suportados via API, PowerShell e a CLI do Azure. O suporte para adicionar e gerenciar domínios curinga no portal Azure não está disponível.

## <a name="adding-wildcard-domains"></a>Adicionando domínios curinga

Você pode adicionar um domínio curinga sob a seção para hosts ou domínios front-end. Semelhante aos subdomínios, o Azure Front Door valida que há mapeamento de registro CNAME para o seu domínio curinga. Este mapeamento DNS pode ser um `*.contoso.com` mapeamento direto `contoso.azurefd.net`do registro CNAME como mapeado para . Ou você pode usar afdverificar mapeamento temporário. Por exemplo, `afdverify.contoso.com` mapeado para `afdverify.contoso.azurefd.net` validar o mapa de registro CNAME para o curinga.

> [!NOTE]
> O DNS do Azure dá suporte a registros curinga.

Você pode adicionar o máximo de subdomínios de nível único do domínio curinga em hosts front-end, até o limite dos hosts front-end. Essa funcionalidade pode ser necessária para:

- Definindo uma rota diferente para um subdomínio do que o resto dos domínios (a partir do domínio curinga).

- Ter uma política WAF diferente para um subdomínio específico. Por exemplo, `*.contoso.com` `foo.contoso.com` permite adicionar sem ter que provar novamente a propriedade do domínio. Mas não permite `foo.bar.contoso.com` porque não é um subdomínio `*.contoso.com`de nível único de . Para `foo.bar.contoso.com` adicionar sem validação `*.bar.contosonews.com` adicional de propriedade de domínio, precisa ser adicionado.

Você pode adicionar domínios curinga e seus subdomínios com certas limitações:

- Se um domínio curinga for adicionado a um perfil do Azure Front Door:
  - O domínio curinga não pode ser adicionado a nenhum outro perfil do Azure Front Door.
  - Os subdomínios de primeiro nível do domínio curinga não podem ser adicionados a outro perfil do Azure Front Door ou a um perfil da Azure Content Delivery Network.
- Se um subdomínio de um domínio curinga for adicionado a um perfil do Azure Front Door ou ao perfil Azure Content Delivery Network, o domínio curinga não poderá ser adicionado a outros perfis do Azure Front Door.
- Se dois perfis (Azure Front Door ou Azure Content Delivery Network) tiverem vários subdomínios de um domínio raiz, os domínios curingas não poderão ser adicionados a nenhum dos perfis.

## <a name="certificate-binding"></a>Vinculação de certificados

Para aceitar tráfego HTTPS em seu domínio curinga, você deve ativar HTTPS no domínio curinga. A vinculação do certificado para um domínio curinga requer um certificado curinga. Ou seja, o nome do assunto do certificado também deve ter o domínio curinga.

> [!NOTE]
> Atualmente, apenas usando sua própria opção de certificado SSL personalizado está disponível para habilitar HTTPS para domínios curinga. Os certificados gerenciados do Azure Front Door não podem ser usados para domínios curinga.

Você pode optar por usar o mesmo certificado curinga do Azure Key Vault ou dos certificados gerenciados do Azure Front Door para subdomínios.

Se um subdomínio for adicionado para um domínio curinga que já tenha um certificado associado a ele, então o HTTPS para o subdomínio não poderá ser desativado. O subdomínio usa a vinculação do certificado para o domínio curinga, a menos que um certificado gerenciado pelo Key Vault ou a Azure Front Door o sobretenha.

## <a name="waf-policies"></a>Políticas do WAF

As políticas waf podem ser anexadas a domínios curinga, semelhantes a outros domínios. Uma política WAF diferente pode ser aplicada a um subdomínio de um domínio curinga. Para os subdomínios, você deve especificar a política WAF a ser usada mesmo que seja a mesma política do domínio curinga. Os subdomínios *não* herdam automaticamente a política WAF do domínio curinga.

Se você não quiser que uma política WAF seja executada para um subdomínio, você pode criar uma diretiva WAF vazia sem conjuntos de regras gerenciados ou personalizados.

## <a name="routing-rules"></a>Regras de roteamento

Ao configurar uma regra de roteamento, você pode selecionar um domínio curinga como um host front-end. Você também pode ter diferentes comportamentos de rota para domínios e subdomínios curinga. Como descrito em [How Azure Front Door faz a correspondência de rotas,](front-door-route-matching.md)a correspondência mais específica para o domínio em diferentes regras de roteamento é escolhida em tempo de execução.

> [!IMPORTANT]
> Você deve ter padrões de caminho correspondentes através de suas regras de roteamento, ou seus clientes verão falhas. Por exemplo, você tem duas regras`*.foo.com/*` de roteamento como a Rota 1`bar.foo.com/somePath/*` (mapeada para o pool de back-end A) e a Rota 2 (mapeada para o pool b de back-end). Então, chega um `bar.foo.com/anotherPath/*`pedido para . O Azure Front Door seleciona a Rota 2 com base em uma correspondência de domínio mais específica, apenas para não encontrar padrões de caminho correspondentes entre as rotas.

## <a name="next-steps"></a>Próximas etapas

- Aprenda a [criar um perfil do Azure Front Door](quickstart-create-front-door.md).
- Aprenda a [adicionar um domínio personalizado na porta frontal do Azure](front-door-custom-domain.md).
- Saiba como [ativar https em um domínio personalizado](front-door-custom-domain-https.md).
