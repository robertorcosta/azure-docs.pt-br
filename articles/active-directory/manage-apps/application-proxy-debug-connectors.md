---
title: Conectores proxy de debug application - Azure Active Directory | Microsoft Docs
description: Depurar problemas com conectores proxy do Azure Active Directory (Azure AD).
services: active-directory
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: conceptual
ms.date: 05/21/2019
ms.author: mimart
ms.reviewer: japere
ms.openlocfilehash: c041578932bd33eb0a2d3afc18a35c2c0458dc8b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "72311857"
---
# <a name="debug-application-proxy-connector-issues"></a>Problemas no conector de depuração de proxy de aplicativos 

Este artigo ajuda você a solucionar problemas com conectores proxy do Azure Active Directory (Azure AD). Se você estiver usando o serviço Proxy de aplicativo para acesso remoto a um aplicativo web no local, mas estiver tendo problemas para se conectar ao aplicativo, use este fluxograma para depurar problemas de conector. 

## <a name="before-you-begin"></a>Antes de começar

Este artigo pressupõe que você instalou o conector Proxy do aplicativo e está tendo um problema. Ao solucionar problemas de proxy de aplicativos, recomendamos que você comece com esse fluxo de solução de problemas para determinar se os conectores proxy do aplicativo estão configurados corretamente. Se você ainda estiver tendo problemas para se conectar ao aplicativo, siga o fluxo de solução de problemas em [problemas do aplicativo Debug Application Proxy](application-proxy-debug-apps.md).  


Para obter mais informações sobre o Proxy do aplicativo e usando seus conectores, consulte:

- [Acesso remoto a aplicativos locais através do Proxy de Aplicativos](application-proxy.md)
- [Conectores proxy de aplicativos](application-proxy-connectors.md)
- [Instalar e registrar um conector](application-proxy-add-on-premises-application.md)
- [Solucionar problemas e mensagens de erro do Application Proxy](application-proxy-troubleshoot.md)

## <a name="flowchart-for-connector-issues"></a>Fluxograma para problemas de conector

Este fluxograma orienta você através das etapas para depurar alguns dos problemas mais comuns do conector. Para obter detalhes sobre cada passo, consulte a tabela que segue o fluxograma.

![Fluxograma mostrando etapas para depurar um conector](media/application-proxy-debug-connectors/application-proxy-connector-debugging-flowchart.png)

|  | Ação | Descrição | 
|---------|---------|---------|
|1 | Encontre o grupo de conectores atribuído ao aplicativo | Você provavelmente tem um conector instalado em vários servidores, nesse caso os conectores devem ser [atribuídos a grupos de conectores](application-proxy-connector-groups.md#assign-applications-to-your-connector-groups). Para saber mais sobre os grupos de conectores, confira [Publicar aplicativos em redes e locais separados usando grupos de conector](application-proxy-connector-groups.md). |
|2 | Instale o conector e designe um grupo | Se você não tiver um conector instalado, consulte [Instalar e registrar um conector](application-proxy-add-on-premises-application.md#install-and-register-a-connector).<br></br> Se você estiver tendo problemas para instalar o conector, consulte [Problema de instalação do Conector](application-proxy-connector-installation-problem.md).<br></br> Se o conector não estiver atribuído a um grupo, consulte [Atribuir o conector a um grupo](application-proxy-connector-groups.md#create-connector-groups).<br></br>Se o aplicativo não for atribuído a um grupo de conectores, consulte [Atribuir o aplicativo a um grupo de conectores](application-proxy-connector-groups.md#assign-applications-to-your-connector-groups).|
|3 | Execute um teste de porta no servidor conector | No servidor de conectores, execute um teste de porta usando [telnet](https://docs.microsoft.com/windows-server/administration/windows-commands/telnet) ou outra ferramenta de teste de porta para verificar se as portas 443 e 80 estão abertas.|
|4 | Configure os domínios e portas | [Certifique-se de que seus domínios e portas estão configurados corretamente](application-proxy-add-on-premises-application.md#prepare-your-on-premises-environment) Para que o conector funcione corretamente, existem certas portas que devem estar abertas e URLs que seu servidor deve ser capaz de acessar. |
|5 | Verifique se um proxy back-end está em uso | Verifique se os conectores estão usando servidores proxy back-end ou contornando-os. Para obter detalhes, consulte [Problemas de proxy de solução de problemas e problemas de conectividade de serviço](application-proxy-configure-connectors-with-proxy-servers.md#troubleshoot-connector-proxy-problems-and-service-connectivity-issues). |
|6 | Atualize o conector e o updater para usar o proxy back-end | Se um proxy back-end estiver em uso, você deverá ter certeza de que o conector está usando o mesmo proxy. Para obter detalhes sobre a solução de problemas e a configuração de conectores para trabalhar com servidores proxy, consulte [Trabalhar com servidores proxy existentes no local](application-proxy-configure-connectors-with-proxy-servers.md). |
|7 | Carregue a URL interna do aplicativo no servidor de conectores | No servidor conector, carregue a URL interna do aplicativo. |
|8 | Verifique a conectividade interna da rede | Há um problema de conectividade em sua rede interna que esse fluxo de depuração é incapaz de diagnosticar. O aplicativo deve ser acessível internamente para que os conectores funcionem. Você pode habilitar e visualizar registros de eventos do conector conforme descrito nos [conectores proxy do aplicativo](application-proxy-connectors.md#under-the-hood). |
|9 | Alongar o valor de tempo na parte traseira | Nas **configurações adicionais** para o aplicativo, altere a configuração **de tempo de saída do aplicativo Backend** para **Long**. Consulte [Adicionar um aplicativo on-premises ao Azure AD](application-proxy-add-on-premises-application.md#add-an-on-premises-app-to-azure-ad). |
|10 | Se os problemas persistirem, direcione problemas específicos de fluxo, revise os fluxos de depuração do aplicativo e do SSO | Use o [aplicativo Debug Application Proxy problemas](application-proxy-debug-apps.md) de solução de problemas. |

## <a name="next-steps"></a>Próximas etapas


* [Publicar aplicativos em redes e locais separados usando grupos de conectores](application-proxy-connector-groups.md)
* [Trabalhar com servidores proxy locais existentes](application-proxy-configure-connectors-with-proxy-servers.md)
* [Solucionar erros do conector e do Proxy de Aplicativo](application-proxy-troubleshoot.md)
* [Como fazer uma instalação silenciosa do Conector de Proxy de Aplicativo Azure AD](application-proxy-register-connector-powershell.md)
