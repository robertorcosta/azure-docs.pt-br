---
title: Aplicativos do Oracle WebLogic Server do Azure | Microsoft Docs
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
ms.openlocfilehash: 9ba4e3a66ae8ef71b39aa26fd54048381237c2fa
ms.sourcegitcommit: fdec8e8bdbddcce5b7a0c4ffc6842154220c8b90
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/19/2020
ms.locfileid: "83664044"
---
# <a name="oracle-weblogic-server-azure-applications"></a>Aplicativos Azure do Oracle WebLogic Server

## <a name="oracle-weblogic-server-is-a-scalable-enterprise-ready-java-ee-application-server"></a>O Oracle WebLogic Server é um servidor de aplicativos Java EE escalonável e pronto para a empresa.

O Oracle WebLogic Server é o primeiro servidor de aplicativos da plataforma Java nativo da nuvem empresarial do mundo para desenvolver e implantar aplicativos empresariais distribuídos de várias camadas. As ofertas do Azure WebLogic Server permitem que você adote a computação em nuvem fornecendo mais opções e flexibilidade para a migração do WebLogic, incluindo a elevação e a mudança de seus aplicativos Java EE para a Nuvem do Azure com o menor esforço e o maior impacto. As ofertas permitem que você inicie seus aplicativos de negócios rapidamente provisionando de modo automático a rede virtual, o armazenamento e os recursos do Linux, instalando o WebLogic Server, configurando a segurança com um grupo de segurança de rede, balanceamento de carga com o Gateway de Aplicativo Azure, autenticação com o Azure Active Directory e facilitando a conectividade do banco de dados.

Há quatro ofertas disponíveis para atender a diferentes cenários: nó único sem um servidor de administração, nó único com um servidor de administração, cluster e cluster dinâmico.  Você deve ficar à vontade para experimentá-los, as ofertas estão disponíveis gratuitamente.

_Essas ofertas são do tipo traga sua própria licença_. Elas supõem que você já adquiriu as licenças apropriadas da Oracle e que estão devidamente licenciadas para executar ofertas no Microsoft Azure.

_Se você quiser trabalhar fortemente em seus cenários de migração com a equipe de engenharia desenvolvendo essas ofertas, basta clicar no botão [ENTRE EM CONTATO COMIGO](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/oracle.oraclelinux-wls-cluster?tab=Overview)_ no [Azure Marketplace](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/oracle.oraclelinux-wls-cluster?tab=Overview). Gerentes de programas, arquitetos e engenheiros entrarão em contato com você em breve e iniciarão a colaboração.

### <a name="oracle-weblogic-server-single-node"></a>Nó único do Oracle WebLogic Server

Essa oferta provisiona uma máquina virtual e instala o Oracle WebLogic Server nela. Ela não cria um domínio nem inicia o Servidor de Administração. Isso é útil para cenários com configuração de domínio altamente personalizada.

### <a name="oracle-weblogic-server-with-admin-server"></a>Servidor do Oracle WebLogic com Servidor de Administração

Essa oferta provisiona uma máquina virtual e instala o Oracle WebLogic Server nela. Ela cria um domínio e inicia o Servidor de Administração, que permite que você gerencie o domínio.

### <a name="oracle-weblogic-server-cluster"></a>Cluster do Oracle WebLogic Server

Esta oferta cria um cluster altamente disponível de máquinas virtuais do Oracle WebLogic Server. O Servidor de Administração e todos os servidores gerenciados são iniciados por padrão, o que permite que você gerencie o domínio.

### <a name="oracle-weblogic-server-dynamic-cluster"></a>Cluster Dinâmico do Oracle WebLogic Server

Esta oferta cria um cluster dinâmico altamente disponível e escalonável de máquinas virtuais do Oracle WebLogic Server. O Servidor de Administração e todos os servidores gerenciados são iniciados por padrão, o que permite que você gerencie o domínio.

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
