---
title: Alias records overview - Azure DNS
description: In this article, learn about support for alias records in Microsoft Azure DNS.
services: dns
author: asudbring
ms.service: dns
ms.topic: article
ms.date: 08/09/2019
ms.author: allensu
ms.openlocfilehash: da396d3e3db4acd1a9843977b8b3e3d51c33f021
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/20/2019
ms.locfileid: "74212336"
---
# <a name="azure-dns-alias-records-overview"></a>Visão geral dos registros de alias do DNS do Azure

Os registros de alias do DNS do Azure são qualificações em um conjunto de registros DNS. Eles podem fazer referência a outros recursos do Azure dentro de sua zona DNS. Por exemplo, você pode criar um conjunto de registros de alias que faça referência a um endereço IP público do Azure em vez de um registro A. Seu alias registra os pontos definidos em uma instância do serviço de endereço IP público do Azure dinamicamente. Como resultado, o conjunto de registros de alias se atualiza continuamente durante a resolução de DNS.

Um conjunto de registros de alias é suportado para os seguintes tipos de registro em uma zona DNS do Azure: 

- A
- AAAA
- CNAME

> [!NOTE]
> Se você pretende usar um registro de alias para os tipos de registros A ou AAAA para apontar para um [perfil do Gerenciador de Tráfego do Azure](../traffic-manager/quickstart-create-traffic-manager-profile.md), verifique se o perfil do Gerenciador de Tráfego tem apenas [pontos de extremidade externos](../traffic-manager/traffic-manager-endpoint-types.md#external-endpoints). Você deve fornecer o endereço IPv4 ou IPv6 para pontos de extremidade externos no Gerenciador de Tráfego. You can't use fully-qualified domain names (FQDNs) in endpoints. O ideal é usar endereços IP estáticos.

## <a name="capabilities"></a>Capacidades

- **Aponte para um recurso IP público de um conjunto de registros DNS A / AAAA.** You can create an A/AAAA record set and make it an alias record set to point to a public IP resource (standard or basic). The DNS record set changes automatically if the public IP address changes or is deleted. Registros DNS pendentes que apontem para endereços IP incorretos são evitados.

   There is a current limit of 20 alias records sets per resource.

- **Aponte para um perfil do Gerenciador de Tráfego de um conjunto de registros DNS A / AAAA / CNAME.** Você pode criar um conjunto de registros CNAME ou A/AAAA e usar registros de alias para apontar para um perfil do Gerenciador de Tráfego. It's especially useful when you need to route traffic at a zone apex, as traditional CNAME records aren't supported for a zone apex. Por exemplo, digamos que seu perfil do Gerenciador de Tráfego seja myprofile.trafficmanager.net e sua zona DNS empresarial seja contoso.com. Você poderá criar um conjunto de registros de alias do tipo um/AAAA para contoso.com (apex da zona) e apontar para myprofile.trafficmanager.net.
- **Point to an Azure Content Delivery Network (CDN) endpoint**. This is useful when you create static websites using Azure storage and Azure CDN.
- **Apontar para outro conjunto de registros DNS dentro da mesma zona.** Registros de alias podem referenciar outros conjuntos de registros do mesmo tipo. Por exemplo, um conjunto de registros DNS CNAME pode ser um alias para outro conjunto de registros CNAME. Essa organização é útil se você quiser que alguns conjuntos de registros sejam aliases e alguns não-aliases.

## <a name="scenarios"></a>Cenários

Existem alguns cenários comuns para registros de alias.

### <a name="prevent-dangling-dns-records"></a>Impedir que os registros DNS pendentes

Um problema comum nos registros DNS tradicionais é o registro de registros pendentes. For example, DNS records that haven't been updated to reflect changes to IP addresses. O problema ocorre especialmente com tipos de registro A/AAAA ou CNAME.

Com um registro de zona DNS tradicional, se o CNAME ou o IP de destino não existir, o registro DNS associado a ele deverá ser atualizado manualmente. In some organizations, a manual update might not happen in time because of process issues or the separation of roles and associated permission levels. Por exemplo, uma função pode ter autoridade para excluir um CNAME ou endereço IP que pertence a um aplicativo. Mas não tem autoridade suficiente para atualizar o registro DNS que aponta para esses destinos. Um atraso ao atualizar o registro DNS pode causar uma interrupção para os usuários.

Registros de alias previnem referências pendentes acoplando fortemente o ciclo de vida de um registro DNS a um recurso do Azure. Por exemplo, considere um registro DNS qualificado como um registro de alias para apontar para um endereço IP público ou um perfil do Gerenciador de Tráfego. If you delete those underlying resources, the DNS alias record becomes an empty record set. It no longer references the deleted resource.

### <a name="update-dns-record-set-automatically-when-application-ip-addresses-change"></a>Atualize o conjunto de registros DNS automaticamente quando os endereços IP do aplicativo mudam

Este cenário é semelhante ao anterior. Talvez um aplicativo seja movido ou a máquina virtual subjacente seja reiniciada. Um registro de alias é atualizado automaticamente quando o endereço IP é alterado para o recurso IP público subjacente. Isso evita possíveis riscos à segurança de direcionar os usuários para outro aplicativo que recebeu o endereço IP público antigo.

### <a name="host-load-balanced-applications-at-the-zone-apex"></a>Hospedar aplicativos com balanceamento de carga no ápice da zona

O protocolo DNS previne a atribuição de registros CNAME no ápice da zona. For example if your domain is contoso.com; you can create CNAME records for somelabel.contoso.com; but you can't create CNAME for contoso.com itself.
Essa restrição apresenta um problema para os proprietários de aplicativos que têm aplicativos com balanceamento de carga por trás do [Gerenciador de Tráfego do Azure](../traffic-manager/traffic-manager-overview.md). Since using a Traffic Manager profile requires creation of a CNAME record, it isn't possible to point at the Traffic Manager profile from the zone apex.

This problem is solved using alias records. Unlike CNAME records, alias records are created at the zone apex and application owners can use it to point their zone apex record to a Traffic Manager profile that has external endpoints. Application owners point to the same Traffic Manager profile that's used for any other domain within their DNS zone.

For example, contoso.com and www\.contoso.com can point to the same Traffic Manager profile. Para saber mais sobre como usar os registros de alias com perfis do Gerenciador de Tráfego do Azure, confira a seção Próximas etapas.

### <a name="point-zone-apex-to-azure-cdn-endpoints"></a>Point zone apex to Azure CDN endpoints

Just like a Traffic Manager profile, you can also use alias records to point your DNS zone apex to Azure CDN endpoints. This is useful when you create static websites using Azure storage and Azure CDN. You can then access the website without prepending "www" to your DNS name.

For example, if your static website is named www.contoso.com, your users can access your site using contoso.com without the need to prepend www to the DNS name.

As described previously, CNAME records aren't supported at the zone apex. So, you can’t use a CNAME record to point contoso.com to your CDN endpoint. Instead, you can use an alias record to point the zone apex to a CDN endpoint directly.

> [!NOTE]
> Pointing a zone apex to CDN endpoints for Azure CDN from Akamai is currently not supported.

## <a name="next-steps"></a>Próximos passos

Para saber mais sobre registros de alias, consulte os seguintes artigos:

- [Tutorial: configure um registro de alias para se referir a um endereço IP público do Azure](tutorial-alias-pip.md)
- [Tutorial: Configurar um registro de alias para dar suporte a nomes de domínio com o Gerenciador de tráfego do apex](tutorial-alias-tm.md)
- [PERGUNTAS FREQUENTES SOBRE O DNS](https://docs.microsoft.com/azure/dns/dns-faq#alias-records)
