---
title: O que é o Oracle WebLogic Server no Azure?
description: Saiba como executar o Oracle WebLogic Server no Microsoft Azure.
services: virtual-machines-linux
documentationcenter: ''
author: edburns
manager: gwallace
tags: azure-resource-management
ms.assetid: ''
ms.service: virtual-machines-windows
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 05/11/2020
ms.author: edburns
ms.openlocfilehash: fe247e75040f658beb94a66176f802993268a7d8
ms.sourcegitcommit: 7fe8df79526a0067be4651ce6fa96fa9d4f21355
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/06/2020
ms.locfileid: "87851844"
---
# <a name="what-is-oracle-weblogic-server-on-azure"></a>O que é o Oracle WebLogic Server no Azure?

Esta página descreve soluções para executar o servidor WebLogic (WLS) em máquinas virtuais do Azure.  Essas soluções são desenvolvidas em conjunto pela Oracle e pela Microsoft.

O Oracle WebLogic Server é o primeiro servidor de aplicativos da plataforma Java nativo da nuvem empresarial do mundo para desenvolver e implantar aplicativos empresariais distribuídos de várias camadas. As ofertas do Azure WebLogic Server permitem que você adote a computação em nuvem.  Você obtém mais opções e flexibilidade para a migração do WebLogic, incluindo a elevação e a mudança de seus aplicativos Java EE para a nuvem do Azure.   O WLS no Azure gera um grande impacto com um pequeno esforço. As ofertas permitem que você inicie seus aplicativos de linha de negócios (LOB) rapidamente.  Cada oferta provisiona automaticamente a rede virtual, o armazenamento e os recursos do Linux.  Sem nenhum esforço, o WebLogic Server está instalado.  O WLS no Azure configura a segurança com um grupo de segurança de rede, balanceamento de carga com Azure App gateway, autenticação com Azure Active Directory e se conecta automaticamente ao banco de dados existente.

:::image type="content" source="media/oracle-weblogic/wls-on-azure.gif" alt-text="Você pode usar o portal do Azure para implantar o WebLogic Server no Azure":::

Há quatro ofertas disponíveis para atender a diferentes cenários: nó único sem um servidor de administração, nó único com um servidor de administração, cluster e cluster dinâmico.  Experimente as ofertas, elas estão disponíveis gratuitamente.

_Essas ofertas são do tipo traga sua própria licença_. Eles supõem que você já tenha as licenças apropriadas com o Oracle e que estejam devidamente licenciados para executar ofertas em Microsoft Azure.

_Se você quiser trabalhar em seus cenários de migração com a equipe de engenharia desenvolvendo essas ofertas, selecione o botão [entrar em contato comigo](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/oracle.oraclelinux-wls-cluster?tab=Overview) _ no [Azure Marketplace](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/oracle.oraclelinux-wls-cluster?tab=Overview). Gerentes de programas, arquitetos e engenheiros vão contatá-lo em breve e começar a colaboração!

## <a name="oracle-weblogic-server-single-node"></a>Nó único do Oracle WebLogic Server

Essa oferta provisiona uma única máquina virtual e instala o WLS nela. Ele não cria um domínio ou inicia o servidor de administração. Um único nó é útil para cenários com configuração de domínio altamente personalizada.

## <a name="oracle-weblogic-server-with-admin-server"></a>Servidor do Oracle WebLogic com Servidor de Administração

Essa oferta provisiona uma única máquina virtual e instala o WLS nela. Ela cria um domínio e inicia o Servidor de Administração, que permite que você gerencie o domínio.

## <a name="oracle-weblogic-server-cluster"></a>Cluster do Oracle WebLogic Server

Esta oferta cria um cluster altamente disponível de máquinas virtuais do WLS. O Servidor de Administração e todos os servidores gerenciados são iniciados por padrão, o que permite que você gerencie o domínio.

## <a name="oracle-weblogic-server-dynamic-cluster"></a>Cluster Dinâmico do Oracle WebLogic Server

Esta oferta cria um cluster dinâmico altamente disponível e escalonável de máquinas virtuais do WLS. O Servidor de Administração e todos os servidores gerenciados são iniciados por padrão, o que permite que você gerencie o domínio.

## <a name="next-steps"></a>Próximas etapas

Explore as ofertas no Azure Marketplace.

> [!div class="nextstepaction"]
> [Nó único do Oracle WebLogic Server](https://portal.azure.com/#create/oracle.20191001-arm-oraclelinux-wls20191001-arm-oraclelinux-wls)

> [!div class="nextstepaction"]
> [Servidor do Oracle WebLogic com Servidor de Administração](https://portal.azure.com/#create/oracle.20191009-arm-oraclelinux-wls-admin20191009-arm-oraclelinux-wls-admin)

> [!div class="nextstepaction"]
> [Cluster do Oracle WebLogic Server](https://portal.azure.com/#create/oracle.20191007-arm-oraclelinux-wls-cluster20191007-arm-oraclelinux-wls-cluster)

> [!div class="nextstepaction"]
> [Cluster Dinâmico do Oracle WebLogic Server](https://portal.azure.com/#create/oracle.20191021-arm-oraclelinux-wls-dynamic-cluster20191021-arm-oraclelinux-wls-dynamic-cluster)
