---
title: Debug Application Proxy application application - Azure Active Directory | Microsoft Docs
description: Depurar problemas com aplicativos proxy do Azure Active Directory (Azure AD).
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
ms.openlocfilehash: 575891d99c077299f5e7abf008c1ebb2b158373f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "74382068"
---
# <a name="debug-application-proxy-application-issues"></a>Problemas de aplicativo de depuração de proxy de aplicativos 

Este artigo ajuda você a solucionar problemas com aplicativos proxy do Azure Active Directory (Azure AD). Se você estiver usando o serviço Proxy de aplicativo para acesso remoto a um aplicativo web no local, mas estiver tendo problemas para se conectar ao aplicativo, use este fluxograma para depurar problemas de aplicativos. 

## <a name="before-you-begin"></a>Antes de começar

Ao solucionar problemas de proxy de aplicativos, recomendamos que você comece com os conectores. Primeiro, siga o fluxo de solução de problemas nos [problemas do Proxy do aplicativo Debug](application-proxy-debug-connectors.md) para garantir que os conectores proxy do aplicativo estejam configurados corretamente. Se você ainda estiver com problemas, retorne a este artigo para solucionar problemas do aplicativo.  

Para obter mais informações sobre o Proxy do aplicativo, consulte:

- [Acesso remoto a aplicativos locais através do Proxy de Aplicativos](application-proxy.md)
- [Conectores proxy de aplicativos](application-proxy-connectors.md)
- [Instalar e registrar um conector](application-proxy-add-on-premises-application.md)
- [Solucionar problemas e mensagens de erro do Application Proxy](application-proxy-troubleshoot.md)

## <a name="flowchart-for-application-issues"></a>Fluxograma para problemas de aplicativos

Este fluxograma orienta você através das etapas para depurar alguns dos problemas mais comuns com a conexão ao aplicativo. Para obter detalhes sobre cada passo, consulte a tabela que segue o fluxograma.

![Fluxograma mostrando etapas para depurar um aplicativo](media/application-proxy-debug-apps/application-proxy-apps-debugging-flowchart.png)

|  | Ação | Descrição | 
|---------|---------|---------|
|1 | Abra um navegador, acesse o aplicativo e digite suas credenciais | Tente usar suas credenciais para entrar no aplicativo e verifique se há erros relacionados ao usuário, como [este aplicativo corporativo não pode ser acessado](application-proxy-sign-in-bad-gateway-timeout-error.md). |
|2 | Verifique a atribuição do usuário para o aplicativo | Certifique-se de que sua conta de usuário tenha permissão para acessar o aplicativo de dentro da rede corporativa e, em seguida, teste o login no aplicativo seguindo as etapas em [Testar o aplicativo](application-proxy-add-on-premises-application.md#test-the-application). Se os problemas de login persistirem, consulte [Como solucionar erros de login](../reports-monitoring/concept-provisioning-logs.md?context=azure/active-directory/manage-apps/context/manage-apps-context).  |
|3 | Abra um navegador e tente acessar o aplicativo | Se um erro aparecer imediatamente, verifique se o Proxy do aplicativo está configurado corretamente. Para obter detalhes sobre mensagens de erro específicas, consulte [Problemas de proxy do aplicativo e mensagens de erro](application-proxy-troubleshoot.md).  |
|4 | Verifique a configuração do domínio personalizado ou soluciona o erro | Se a página não for exibida, certifique-se de que seu domínio personalizado esteja configurado corretamente, revisando [Working with custom domains](application-proxy-configure-custom-domain.md).<br></br>Se a página não for carregada e uma mensagem de erro aparecer, solucionar o erro, referindo-se a [problemas de proxy do aplicativo e mensagens de erro](application-proxy-troubleshoot.md). <br></br>Se demorar mais de 20 segundos para uma mensagem de erro aparecer, pode haver problema de conectividade. Vá para o [artigo Debug Application Proxy solucionando problemas.](application-proxy-debug-connectors.md)  |
|5 | Se os problemas persistirem, vá para a depuração do conector | Pode haver um problema de conectividade entre o proxy e o conector ou entre o conector e o back-end. Vá para o [artigo Debug Application Proxy solucionando problemas.](application-proxy-debug-connectors.md) |
|6 | Publique todos os recursos, verifique as ferramentas do desenvolvedor do navegador e corrija links | Certifique-se de que o caminho de publicação inclua todas as imagens, scripts e folhas de estilo necessárias para sua aplicação. Para obter [detalhes, consulte Adicionar um aplicativo no local ao Azure AD](application-proxy-add-on-premises-application.md#add-an-on-premises-app-to-azure-ad). <br></br>Use as ferramentas de desenvolvedor do navegador (ferramentas F12 no Internet Explorer ou Microsoft Edge) e verifique se os problemas de publicação descritos na [página do aplicativo não são exibidos corretamente](application-proxy-page-appearance-broken-problem.md). <br></br>As opções de revisão para resolver links [quebrados em Links na página não funcionam](application-proxy-page-links-broken-problem.md). |
|7 | Verifique se há latência da rede | Se a página for carregada lentamente, aprenda sobre maneiras de minimizar a latência da rede em [Considerações para reduzir a latência](application-proxy-network-topology.md#considerations-for-reducing-latency). | 
|8 | Veja ajuda adicional para solução de problemas | Se os problemas persistirem, encontre artigos adicionais de solução de problemas na [documentação de solução de problemas](application-proxy-troubleshoot.md)do Proxy do aplicativo . |

## <a name="next-steps"></a>Próximas etapas


* [Publicar aplicativos em redes e locais separados usando grupos de conectores](application-proxy-connector-groups.md)
* [Trabalhar com servidores proxy locais existentes](application-proxy-configure-connectors-with-proxy-servers.md)
* [Solucionar erros do conector e do Proxy de Aplicativo](application-proxy-troubleshoot.md)
* [Como fazer uma instalação silenciosa do Conector de Proxy de Aplicativo Azure AD](application-proxy-register-connector-powershell.md)
