---
title: Como abrir portas de firewall necessárias para um aplicativo de proxy de aplicativo
description: Descubra quais portas abrir para o Application Proxy do Azure AD funcionar corretamente
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.assetid: ''
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/21/2018
ms.author: mimart
ms.reviewer: asteen
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2b676508c73ff0233526f19b865bb9e4f7b80f12
ms.sourcegitcommit: b1e25a8a442656e98343463aca706f4fde629867
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/27/2020
ms.locfileid: "74275544"
---
# <a name="how-to-open-the-firewall-ports-required-for-an-application-proxy-application"></a>Como abrir as portas de firewall necessárias para um aplicativo de Application Proxy

Para ver uma lista completa das portas necessárias e a função de cada porta, consulte a seção pré-requisitos da [documentação de Application Proxy](application-proxy-add-on-premises-application.md). observe que o Application Proxy usa apenas as portas de saída.

Você também pode verificar se você tem todas as portas necessárias abertas abrindo a [ferramenta de teste de portas de conector](https://aadap-portcheck.connectorporttest.msappproxy.net/) de sua rede local. Mais marcas de seleção verde significa maior resiliência. 

## <a name="app-proxy-regions"></a>Regiões de Proxy de aplicativo

Estamos trabalhando em uma maneira para que você saiba que essas regiões precisam estar verdes para você. Por enquanto, certifique-se de que todas estão verdes. EUA Central também é necessária, independentemente de qual região você está.

Para certificar-se de que a ferramenta oferece os resultados certos, certifique-se de:

-   Abrir a ferramenta em um navegador do servidor na qual você instalou o conector.

-   Certifique-se de que quaisquer proxies ou firewalls aplicáveis a seu conector também estejam aplicados a esta página. Isso pode ser feito no Internet Explorer acessando **configurações**  - &gt; **Opções**  - &gt; da Internet **conexões**  - &gt; **configurações de LAN**. Nessa página, você vê o campo "Use um Servidor Proxy para sua LAN". Marque essa caixa e coloque o endereço do proxy no campo "Endereço".

## <a name="next-steps"></a>Próximas etapas
[Entender os conectores de Proxy de Aplicativo do AD do Azure](application-proxy-connectors.md)
