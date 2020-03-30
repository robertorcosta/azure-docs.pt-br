---
title: Visão geral dos registros alias - Azure DNS
description: Neste artigo, saiba mais sobre o suporte para registros de alias no Microsoft Azure DNS.
services: dns
author: rohinkoul
ms.service: dns
ms.topic: article
ms.date: 08/09/2019
ms.author: rohink
ms.openlocfilehash: 271770935cf4cb83d4abc6e82a4f4b13ffe865b3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80295496"
---
# <a name="azure-dns-alias-records-overview"></a>Visão geral dos registros de alias do DNS do Azure

Os registros de alias do DNS do Azure são qualificações em um conjunto de registros DNS. Eles podem fazer referência a outros recursos do Azure dentro de sua zona DNS. Por exemplo, você pode criar um conjunto de registros de alias que faça referência a um endereço IP público do Azure em vez de um registro A. Seu alias registra os pontos definidos em uma instância do serviço de endereço IP público do Azure dinamicamente. Como resultado, o conjunto de registros de alias se atualiza continuamente durante a resolução de DNS.

Um conjunto de registros de alias é suportado para os seguintes tipos de registro em uma zona DNS do Azure: 

- Um
- AAAA
- CNAME

> [!NOTE]
> Se você pretende usar um registro de alias para os tipos de registros A ou AAAA para apontar para um [perfil do Gerenciador de Tráfego do Azure](../traffic-manager/quickstart-create-traffic-manager-profile.md), verifique se o perfil do Gerenciador de Tráfego tem apenas [pontos de extremidade externos](../traffic-manager/traffic-manager-endpoint-types.md#external-endpoints). Você deve fornecer o endereço IPv4 ou IPv6 para pontos de extremidade externos no Gerenciador de Tráfego. Você não pode usar nomes de domínio totalmente qualificados (FQDNs) em pontos finais. O ideal é usar endereços IP estáticos.

## <a name="capabilities"></a>Funcionalidades

- **Aponte para um recurso IP público de um conjunto de registros DNS A / AAAA.** Você pode criar um conjunto de discos A/AAAA e torná-lo um registro de alias definido para apontar para um recurso IP público (padrão ou básico). O conjunto de registros DNS muda automaticamente se o endereço IP público for alterado ou for excluído. Registros DNS pendentes que apontem para endereços IP incorretos são evitados.

   Há um limite atual de 20 conjuntos de registros de alias por recurso.

- **Aponte para um perfil do Gerenciador de Tráfego de um conjunto de registros DNS A / AAAA / CNAME.** Você pode criar um conjunto de registros CNAME ou A/AAAA e usar registros de alias para apontar para um perfil do Gerenciador de Tráfego. É especialmente útil quando você precisa direcionar o tráfego em um ápice de zona, já que os registros cname tradicionais não são suportados para um ápice de zona. Por exemplo, digamos que seu perfil do Gerenciador de Tráfego seja myprofile.trafficmanager.net e sua zona DNS empresarial seja contoso.com. Você poderá criar um conjunto de registros de alias do tipo um/AAAA para contoso.com (apex da zona) e apontar para myprofile.trafficmanager.net.
- **Aponte para um ponto final de CDN (Azure Content Delivery Network, rede de entrega de conteúdo do Azure).** Isso é útil quando você cria sites estáticos usando o armazenamento Azure e o CDN do Azure.
- **Apontar para outro conjunto de registros DNS dentro da mesma zona.** Registros de alias podem referenciar outros conjuntos de registros do mesmo tipo. Por exemplo, um conjunto de registros DNS CNAME pode ser um alias para outro conjunto de registros CNAME. Essa organização é útil se você quiser que alguns conjuntos de registros sejam aliases e alguns não-aliases.

## <a name="scenarios"></a>Cenários

Existem alguns cenários comuns para registros de alias.

### <a name="prevent-dangling-dns-records"></a>Impedir que os registros DNS pendentes

Um problema comum nos registros DNS tradicionais é o registro de registros pendentes. Por exemplo, registros de DNS que não foram atualizados para refletir alterações em endereços IP. O problema ocorre especialmente com tipos de registro A/AAAA ou CNAME.

Com um registro de zona DNS tradicional, se o CNAME ou o IP de destino não existir, o registro DNS associado a ele deverá ser atualizado manualmente. Em algumas organizações, uma atualização manual pode não acontecer a tempo devido a problemas de processo ou a separação de funções e níveis de permissão associados. Por exemplo, uma função pode ter autoridade para excluir um CNAME ou endereço IP que pertence a um aplicativo. Mas não tem autoridade suficiente para atualizar o registro DNS que aponta para esses destinos. Um atraso ao atualizar o registro DNS pode causar uma interrupção para os usuários.

Registros de alias previnem referências pendentes acoplando fortemente o ciclo de vida de um registro DNS a um recurso do Azure. Por exemplo, considere um registro DNS qualificado como um registro de alias para apontar para um endereço IP público ou um perfil do Gerenciador de Tráfego. Se você excluir esses recursos subjacentes, o registro de alias DNS se tornará um conjunto de registros vazio. Ele não faz mais referência ao recurso excluído.

### <a name="update-dns-record-set-automatically-when-application-ip-addresses-change"></a>Atualize o conjunto de registros DNS automaticamente quando os endereços IP do aplicativo mudam

Este cenário é semelhante ao anterior. Talvez um aplicativo seja movido ou a máquina virtual subjacente seja reiniciada. Um registro de alias é atualizado automaticamente quando o endereço IP é alterado para o recurso IP público subjacente. Isso evita possíveis riscos à segurança de direcionar os usuários para outro aplicativo que recebeu o endereço IP público antigo.

### <a name="host-load-balanced-applications-at-the-zone-apex"></a>Hospedar aplicativos com balanceamento de carga no ápice da zona

O protocolo DNS previne a atribuição de registros CNAME no ápice da zona. Por exemplo, se o seu domínio estiver contoso.com; você pode criar registros CNAME para somelabel.contoso.com; mas você não pode criar CNAME para contoso.com si.
Essa restrição apresenta um problema para os proprietários de aplicativos que têm aplicativos com balanceamento de carga por trás do [Gerenciador de Tráfego do Azure](../traffic-manager/traffic-manager-overview.md). Uma vez que o uso de um perfil de Gerenciador de tráfego requer a criação de um registro CNAME, não é possível apontar para o perfil do Gerenciador de tráfego a partir do ápice da região.

Este problema é resolvido usando registros de alias. Ao contrário dos registros CNAME, os registros de alias são criados no ápice da região e os proprietários de aplicativos podem usá-lo para apontar o registro do ápice da região para um perfil do Gerenciador de tráfego que tenha pontos finais externos. Os proprietários de aplicativos apontam para o mesmo perfil do Gerenciador de tráfego que é usado para qualquer outro domínio dentro de sua região de DNS.

Por exemplo, contoso.com\.e www contoso.com podem apontar para o mesmo perfil de Gerenciador de Tráfego. Para saber mais sobre como usar os registros de alias com perfis do Gerenciador de Tráfego do Azure, confira a seção Próximas etapas.

### <a name="point-zone-apex-to-azure-cdn-endpoints"></a>Ápice da zona de ponto para os pontos finais do CDN do Azure

Assim como um perfil do Gerenciador de Tráfego, você também pode usar registros de alias para apontar o ápice da zona DNS para os pontos finais do CDN do Azure. Isso é útil quando você cria sites estáticos usando o armazenamento Azure e o CDN do Azure. Em seguida, você pode acessar o site sem pré-pendente "www" para o seu nome DNS.

Por exemplo, se o `www.contoso.com, your users can access your site using contoso.com` seu site estático for nomeado sem a necessidade de preparar www para o nome DNS.

Como descrito anteriormente, os registros CNAME não são suportados no ápice da região. Assim, você não pode usar um registro CNAME para apontar contoso.com para o seu ponto final de CDN. Em vez disso, você pode usar um registro de alias para apontar o ápice da região para um ponto final de CDN diretamente.

> [!NOTE]
> Apontou um ápice de zona para pontos finais de CDN para CDN Azure da Akamai não é suportado no momento.

## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre registros de alias, consulte os seguintes artigos:

- [Tutorial: Configure um registro de alias para consultar um endereço IP público do Azure](tutorial-alias-pip.md)
- [Tutorial: Configurar um registro de alias para dar suporte a nomes de domínio com o Gerenciador de tráfego do apex](tutorial-alias-tm.md)
- [PERGUNTAS FREQUENTES SOBRE O DNS](https://docs.microsoft.com/azure/dns/dns-faq#alias-records)
