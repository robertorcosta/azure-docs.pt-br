---
title: Visão geral da política do Azure Web Application Firewall (WAF)
description: Este artigo é uma visão geral das políticas globais, por site e por URI do Web Application Firewall.
services: web-application-firewall
ms.topic: article
author: winthrop28
ms.service: web-application-firewall
ms.date: 02/01/2020
ms.author: victorh
ms.openlocfilehash: fff4fb5707c07098fd7fac261a36909224365cdf
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80060262"
---
# <a name="azure-web-application-firewall-waf-policy-overview"></a>Visão geral da política do Azure Web Application Firewall (WAF)

As Políticas de Firewall de Aplicativos da Web contêm todas as configurações e configurações do WAF. Isso inclui exclusões, regras personalizadas, regras gerenciadas, e assim por diante. Essas políticas são então associadas a um gateway de aplicativo (global), a um ouvinte (por site) ou a uma regra baseada em caminho (por URI) para que elas entrem em vigor.

> [!NOTE]
> As políticas waf (Azure Web Application Firewall) por site e por URI estão em Visualização Pública.
> 
> Essa versão prévia pública é fornecida sem um SLA e não deve ser usada para cargas de trabalho de produção. Talvez alguns recursos não tenham suporte, tenham recursos restritos ou não estejam disponíveis em todos os locais do Azure. Para obter mais informações, consulte [Termos de Uso Suplementares para Visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Não há limite no número de políticas que você pode criar. Quando você cria uma política, ela deve ser associada a um gateway de aplicativo para fazer efeito. Ele pode ser associado a qualquer combinação de gateways de aplicativos, ouvintes e regras baseadas em caminhos.

## <a name="global-waf-policy"></a>Política global do WAF

Quando você associa uma política WAF globalmente, todos os sites por trás do WAF do Gateway de aplicativo são protegidos com as mesmas regras gerenciadas, regras personalizadas, exclusões e quaisquer outras configurações configuradas.

Se você quiser que uma única política se aplique a todos os sites, você pode associar a política ao gateway de aplicativo. Para obter mais informações, consulte [Criar políticas de firewall de aplicativos da Web para criar](create-waf-policy-ag.md) e aplicar uma política WAF usando o portal Azure. 

## <a name="per-site-waf-policy"></a>Política de WAF por site

Com as políticas de WAF por site, você pode proteger vários sites com diferentes necessidades de segurança por trás de um único WAF usando políticas por site. Por exemplo, se houver cinco sites atrás do SEU WAF, você pode ter cinco políticas WAF separadas (uma para cada ouvinte) para personalizar as exclusões, regras personalizadas, conjuntos de regras gerenciadas e todas as outras configurações de WAF para cada site.

Digamos que seu gateway de aplicativo tenha uma política global aplicada a ele. Em seguida, você aplica uma política diferente a um ouvinte no gateway de aplicativo. A política do ouvinte agora faz efeito apenas para aquele ouvinte. A política global do gateway de aplicativo ainda se aplica a todos os outros ouvintes e regras baseadas em caminhos que não têm uma política específica atribuída a eles.

## <a name="per-uri-policy"></a>Política per-URI

Para obter ainda mais personalização até o nível URI, você pode associar uma política WAF a uma regra baseada em caminhos. Se houver certas páginas dentro de um único site que exijam políticas diferentes, você pode fazer alterações na diretiva WAF que afetam apenas um determinado URI. Isso pode se aplicar a uma página de pagamento ou login, ou a quaisquer outras URIs que precisem de uma política WAF ainda mais específica do que os outros sites por trás do seu WAF.

Como acontece com as políticas de WAF por site, políticas mais específicas se sobrepõe às menos específicas. Isso significa que uma política por URI em um mapa de caminho de URL substitui qualquer política de WAF por site ou global acima dela.

## <a name="example"></a>Exemplo

Digamos que você tenha três sites: contoso.com, fabrikam.com e adatum.com todos atrás do mesmo gateway de aplicativo. Você quer um WAF aplicado aos três sites, mas precisa de segurança adicional com adatum.com porque é onde os clientes visitam, navegam e compram produtos.

Você pode aplicar uma política global ao WAF, com algumas configurações básicas, exclusões ou regras personalizadas, se necessário, para impedir que alguns falsos positivos bloqueiem o tráfego. Neste caso, não há necessidade de ter regras globais de injeção de SQL funcionando porque fabrikam.com e contoso.com são páginas estáticas sem backend SQL. Então você pode desativar essas regras na política global.

Esta política global é adequada para contoso.com e fabrikam.com, mas você precisa ter mais cuidado com adatum.com onde as informações e pagamentos são tratados. Você pode aplicar uma política por site ao ouvinte adatum e deixar as regras do SQL em execução. Também suponha que há um cookie bloqueando algum tráfego, para que você possa criar uma exclusão para que o cookie pare o falso positivo. 

A adatum.com/payments URI é onde você precisa ter cuidado. Então aplique outra política sobre esse URI e deixe todas as regras habilitadas, e também remova todas as exclusões.

Neste exemplo, você tem uma política global que se aplica a dois sites. Você tem uma diretiva por site que se aplica a um site e, em seguida, uma política por URI que se aplica a uma regra específica baseada em caminho. Consulte (insira link aqui quando ele existir) como criar políticas por site e por URI para o PowerShell correspondente para este exemplo.

## <a name="existing-waf-configurations"></a>Configurações existentes do WAF

Todas as configurações de WAF do Novo Firewall do Aplicativo web (regras personalizadas, configurações gerenciadas de conjuntos de regras, exclusões e assim por diante.) existem em uma política WAF. Se você tiver um WAF existente, essas configurações ainda podem existir na configuração do WAF. Para obter mais informações sobre a mudança para a nova política waf, [migre waf config para uma política WAF](https://docs.microsoft.com/azure/web-application-firewall/ag/migrate-policy). 


## <a name="next-steps"></a>Próximas etapas

Crie políticas por site e por URI usando o Azure PowerShell.
