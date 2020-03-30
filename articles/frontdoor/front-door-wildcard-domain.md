---
title: Porta da Frente do Azure - Suporte para domínios curinga
description: Este artigo ajuda você a entender como o Azure Front Door suporta mapeamento e gerenciamento de domínios curinga na lista de domínios personalizados
services: frontdoor
author: sharad4u
ms.service: frontdoor
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/10/2020
ms.author: sharadag
ms.openlocfilehash: c568c9cc5c57098385cc7399459ec656cdbfc305
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79537434"
---
# <a name="wildcard-domains"></a>Domínios curinga

Além de domínios e subdomínios apex, você também pode mapear um nome de domínio curinga para sua lista de hosts frontend ou domínios personalizados do seu perfil front door. Ter domínios curinga na configuração da Porta da Frente simplifica o comportamento de roteamento de tráfego para vários subdomínios para uma API, aplicativo ou site da mesma regra de roteamento sem ter que modificar a configuração para adicionar e/ou especificar cada subdomínio separadamente. Como exemplo, você pode definir `customer1.contoso.com`o `customer2.contoso.com`roteamento para , e `customerN.contoso.com` usando a `*.contoso.com`mesma regra de roteamento adicionando um domínio curinga .

Alguns dos principais cenários que são resolvidos com suporte para domínios curinga incluem:

- Não é mais necessário embarcar em cada subdomínio na porta da frente e, em seguida, habilitar o HTTPS para vincular um certificado para cada subdomínio.
- Se um aplicativo adicionar um novo subdomínio, então você não será mais obrigado a alterar sua configuração de porta frontal de produção. Em caso contrário, antes, ele exigia adicionar o subdomínio, vinculando um certificado a ele, anexando uma política de firewall de aplicativo web (WAF), adicionando o domínio a diferentes regras de roteamento.

> [!NOTE]
> Atualmente, os domínios curinga só são suportados através da API, PowerShell e CLI. O suporte para adicionar o gerenciamento de domínios curinga via portal Azure não está disponível.

## <a name="adding-wildcard-domains"></a>Adicionando domínios curinga

Você pode embarcar em um domínio curinga na seção Hosts ou domínios do Frontend. Semelhante aos subdomínios, o Front Door valida que há um mapeamento CNAME para o seu domínio curinga também. Este mapeamento DeDN pode ser `*.contoso.com` um mapeamento `contoso.azurefd.net` CNAME direto como mapeado para ou através do mapeamento temporário afdverify como `afdverify.contoso.com` mapeado para `afdverify.contoso.azurefd.net` validar o mapa CNAME para curinga (O Azure DNS suporta registros curinga).

Você também pode adicionar tantos subdomínios de nível único do domínio curinga em hosts frontend se eles não estiverem atingindo o máximo. limite de hosts frontend. Essa funcionalidade pode ser necessária para definir uma rota diferente para um subdomínio do que o resto dos domínios (do domínio curinga) ou ter uma política WAF diferente para um subdomínio específico. Assim, `*.contoso.com` permitirá `foo.contoso.com` adicionar sem ter que `foo.bar.contoso.com` provar novamente a propriedade do `*.contoso.com`domínio, mas não como isso não é um subdomínio de nível único de . Para `foo.bar.contoso.com` adicionar sem validação `*.bar.contosonews.com` adicional de propriedade de domínio, será necessário adicionar.

### <a name="limitations"></a>Limitações

1. Se um domínio curinga for adicionado em um determinado perfil da Porta da Frente, o mesmo não poderá ser adicionado a qualquer outro perfil da Porta da Frente. 
2. Se um domínio curinga for adicionado em um determinado perfil front door, então quaisquer subdomínios desse domínio curinga não poderão ser adicionados a outra porta frontal ou a um CDN do Azure do perfil da Microsoft
3. Se um subdomínio de um domínio curinga for adicionado em um perfil da Porta da Frente ou em um CDN do Azure do perfil da Microsoft, o domínio curinga não poderá ser adicionado a nenhum outro perfil do Front Door. 
4. Se dois perfis (Front Door ou Azure CDN da Microsoft) tiverem vários subdomínios de um domínio raiz, então os domínios curinga não podem ser adicionados em nenhum dos perfis.

## <a name="certificate-binding-for-wildcard-domains-and-its-subdomains"></a>Certificado de vinculação para domínios curinga e seus subdomínios

Para aceitar tráfego HTTPS em seu domínio curinga, você deve ativar HTTPS no domínio curinga. A vinculação do certificado para domínio curinga requer um certificado curinga, ou seja, o nome do assunto do certificado também deve ter o domínio curinga.

> [!NOTE]
> Atualmente, apenas usando sua própria opção de certificado SSL personalizado está disponível para habilitar HTTPS para domínios curinga. Os certificados gerenciados do Front Door não podem ser usados para domínios curinga. 

Você pode optar por usar o mesmo certificado curinga do seu Key Vault para os subdomínios, ou, o uso de certificados gerenciados pela porta frontal para subdomínios também é suportado.
Se um subdomínio for adicionado para um domínio curinga e o domínio curinga já tiver um certificado associado, então o HTTPS para este subdomínio não poderá ser desativado. O subdomínio usará, por padrão, a vinculação do certificado do domínio curinga, a menos que seja substituído por um certificado de Key Vault diferente ou certificado gerenciado pela Porta da Frente.

## <a name="web-application-firewall-for-wildcard-domains-and-its-subdomains"></a>Firewall de aplicativos web para domínios curinga e seus subdomínios

As políticas WAF podem ser anexadas a um domínio curinga semelhante a outros domínios. Uma política WAF diferente pode ser aplicada a um subdomínio de um domínio curinga. Para os subdomínios, você deve especificar explicitamente a política WAF a ser usada e mesmo que seja a mesma política do domínio curinga. Os subdomínios **não** herdarão automaticamente a política WAF do domínio curinga.

Se você tem um cenário em que você não quer que o WAF seja executado para um subdomínio, então você pode criar uma diretiva WAF em branco sem conjuntos de regras gerenciados ou personalizados.

## <a name="routing-rules-for-wildcard-domains-and-its-subdomains"></a>Regras de roteamento para domínios curinga e seus subdomínios

Ao configurar uma regra de roteamento, você pode selecionar um domínio curinga como um host frontend. Você também pode ter diferentes comportamentos de rota para domínio curinga vs. subdomínios. Como descrito em [como o Front Door faz a correspondência de rotas,](front-door-route-matching.md)a correspondência mais específica para o domínio em diferentes regras de roteamento será escolhida em tempo de execução.

> [!WARNING]
> Se você tiver duas regras de roteamento como **a Rota 1** `*.foo.com/*` : mapeada para backend `bar.foo.com/anotherPath/*`pool A e rota **2**: `bar.foo.com/somePath/*` mapeada para Backend Pool B e se uma solicitação chegar para , então seus clientes verão falhas, pois a Porta da Frente não encontrará nenhuma correspondência em ambas as rotas. Isso porque, de acordo com nosso [algoritmo de correspondência de rotas,](front-door-route-matching.md)o Front Door selecionará a Rota 2 com base em correspondência de domínio mais específica, mas apenas para descobrir que não há padrões de caminho correspondentes. 


## <a name="next-steps"></a>Próximas etapas

- Saiba como [criar um Front Door](quickstart-create-front-door.md).
- Aprenda a [adicionar um domínio personalizado na Porta da Frente](front-door-custom-domain.md).
- Saiba como [ativar https em um domínio personalizado](front-door-custom-domain-https.md).
