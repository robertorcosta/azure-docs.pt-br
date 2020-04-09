---
title: 'Proxy do aplicativo Azure AD: histórico de lançamento de versão | Microsoft Docs'
description: Este artigo lista todas as versões do Azure AD Application Proxy e descreve novos recursos e problemas fixos
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
editor: ''
ms.assetid: ''
ms.service: active-directory
ms.devlang: na
ms.topic: reference
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/07/2020
ms.subservice: app-mgmt
ms.author: mimart
ms.collection: M365-identity-device-management
ms.openlocfilehash: f027fbce66a73306165a0ad35d1ba3faa7a5c0bc
ms.sourcegitcommit: 7d8158fcdcc25107dfda98a355bf4ee6343c0f5c
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/09/2020
ms.locfileid: "80983884"
---
# <a name="azure-ad-application-proxy-version-release-history"></a>Proxy do aplicativo Azure AD: histórico de lançamento de versão
Este artigo lista as versões e recursos do Proxy de aplicativo do Azure Active Directory (Azure AD) que foram lançados. A equipe do Azure AD atualiza regularmente o Proxy do aplicativo com novos recursos e funcionalidades. Os conectores proxy do aplicativo são atualizados automaticamente quando uma nova versão é lançada. 

Recomendamos garantir que as atualizações automáticas estejam habilitadas para seus conectores para garantir que você tenha os recursos mais recentes e correções de bugs. A Microsoft oferece suporte direto para a versão mais recente do conector e uma versão anterior.

Aqui está uma lista de recursos relacionados:

Recurso |  Detalhes
--------- | --------- |
Como ativar o Proxy de aplicativos | Pré-requisitos para habilitar o Proxy de aplicativos e instalar e registrar um conector são descritos neste [tutorial](application-proxy-add-on-premises-application.md).
Noções básicas sobre conectores de Proxy de Aplicativo Azure AD | Saiba mais sobre o [gerenciamento do conector](application-proxy-connectors.md) e como os conectores [atualizam automaticamente](application-proxy-connectors.md#automatic-updates).
Download do conector proxy do aplicativo Azure AD |  [Baixe o conector mais recente](https://download.msappproxy.net/subscription/d3c8b69d-6bf7-42be-a529-3fe9c2e70c90/connector/download).

## <a name="1515260"></a>1.5.1526.0

### <a name="release-status"></a>Status de liberação

07 de Abril de 2020: Liberado para download

### <a name="new-features-and-improvements"></a>Novos recursos e aprimoramentos
-   Os conectores só usam O TLS 1.2 para todas as conexões. Consulte [os pré-requisitos do Connector](application-proxy-add-on-premises-application.md#before-you-begin) para obter mais detalhes.
- Sinalização melhorada entre os serviços Connector e Azure. Isso inclui o suporte a sessões confiáveis para comunicação WCF entre os serviços Connector e Azure e melhorias de cache DNS para comunicações WebSocket.
- Suporte para configurar um proxy entre o Conector e o aplicativo back-end. Para obter mais informações, [consulte Trabalhar com servidores proxy existentes no local](application-proxy-configure-connectors-with-proxy-servers.md).

### <a name="fixed-issues"></a>Problemas corrigidos
- Removido recuando para a porta 8080 para comunicações dos serviços Connector para Azure.
- Adicionados traços de depuração para comunicações webSocket. 
- Resolveu preservar o atributo SameSite ao definir cookies de aplicativos back-end.

## <a name="156120"></a>1.5.612.0

### <a name="release-status"></a>Status de liberação

20 de setembro de 2018: Liberado para download

### <a name="new-features-and-improvements"></a>Novos recursos e aprimoramentos

- Adicionado suporte ao WebSocket para o aplicativo QlikSense. Para saber mais sobre como integrar o QlikSense com o Proxy do aplicativo, consulte este [passo a passo](application-proxy-qlik.md). 
- Melhorou o assistente de instalação para facilitar a configuração de um proxy de saída. 
- Defina o TLS 1.2 como o protocolo padrão para conectores. 
- Adicionada um novo Contrato de Licença de Usuário Final (EULA).  

### <a name="fixed-issues"></a>Problemas corrigidos

- Corrigimos um bug que causou alguns vazamentos de memória no conector.
- Atualizei a versão do Azure Service Bus, que inclui uma correção de bugs para problemas de tempo de tempo.

## <a name="154020"></a>1.5.402.0

### <a name="release-status"></a>Status de liberação

19 de janeiro de 2018: Liberado para download

### <a name="fixed-issues"></a>Problemas corrigidos

- Adicionado suporte para domínios personalizados que precisam de tradução de domínio no cookie.

## <a name="151320"></a>1.5.132.0

### <a name="release-status"></a>Status de liberação 

25 de maio de 2017: Liberado para download 

### <a name="new-features-and-improvements"></a>Novos recursos e aprimoramentos 

Melhor controle sobre os limites de conexão de saída dos conectores. 

## <a name="15360"></a>1.5.36.0

### <a name="release-status"></a>Status de liberação

15 de abril de 2017: Liberado para download

### <a name="new-features-and-improvements"></a>Novos recursos e aprimoramentos

- Onboarding e gerenciamento simplificados com menos portas necessárias. O Proxy do aplicativo agora requer a abertura de apenas duas portas de saída padrão: 443 e 80. O Proxy do aplicativo continua a usar apenas conexões de saída, então você ainda não precisa de componentes em um DMZ. Para obter detalhes, consulte nossa [documentação de configuração](application-proxy-add-on-premises-application.md).  
- Se apoiado por seu proxy externo ou firewall, agora você pode abrir sua rede por DNS em vez de alcance IP. Os serviços de proxy de aplicativos exigem conexões apenas para *.msappproxy.net e *.servicebus.windows.net.


## <a name="earlier-versions"></a>Versões anteriores

Se você estiver usando uma versão do conector Proxy de aplicativo antes do 1.5.36.0, atualize para a versão mais recente para garantir que você tenha os recursos totalmente suportados mais recentes.

## <a name="next-steps"></a>Próximas etapas
- Saiba mais sobre [o acesso remoto a aplicativos locais através do Proxy de aplicativo AZure AD](application-proxy.md).
- Para começar a usar o Proxy de aplicativo, consulte [Tutorial: Adicione um aplicativo local para acesso remoto através do Proxy do aplicativo](application-proxy-add-on-premises-application.md).
