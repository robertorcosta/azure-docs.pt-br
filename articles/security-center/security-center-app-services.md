---
title: Proteja seus aplicativos e APIs do Azure App Service
description: Este artigo ajuda você a começar a proteger seus aplicativos web e APIs do Azure App Service no Azure Security Center.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: e8518710-fcf9-44a8-ae4b-8200dfcded1a
ms.service: security-center
ms.topic: conceptual
ms.date: 01/27/2019
ms.author: memildin
ms.openlocfilehash: 2d81e1a1218add504e1e35015276b6924da0e3e3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "77616479"
---
# <a name="protect-your-azure-app-service-web-apps-and-apis"></a>Proteja seus aplicativos e APIs do Azure App Service

O Azure App Service é uma plataforma totalmente gerenciada para construir e hospedar seus aplicativos e APIs da Web sem se preocupar em ter que gerenciar a infra-estrutura. Ele fornece gerenciamento, monitoramento e insights operacionais para atender aos requisitos de desempenho, segurança e conformidade de nível corporativo. Para obter mais informações, consulte [Azure App Service](https://azure.microsoft.com/services/app-service/).

Para habilitar proteção avançada contra ameaças para o seu plano de serviço de aplicativos do Azure, você deve:

* Inscreva-se no nível padrão de preços do Azure Security Center
* Habilite o plano de serviço do aplicativo conforme mostrado abaixo. O Security Center é integrado nativamente ao App Service, eliminando a necessidade de implantação e onboarding - a integração é transparente.
* Tenha um plano de Serviço de Aplicativo que esteja associado a máquinas dedicadas. Os planos suportados são: Básico, Padrão, Premium, Isolado ou Linux. O Security Center não suporta os planos Livre, Compartilhado ou de Consumo. Para obter mais informações, consulte [App Service Plans](https://azure.microsoft.com/pricing/details/app-service/plans/).

Com o plano app service ativado, o Security Center avalia os recursos cobertos pelo seu plano de Serviço de Aplicativo e gera recomendações de segurança com base em suas descobertas. O Security Center protege a instância vm na qual o Serviço de Aplicativo está sendo executado e a interface de gerenciamento. Ele também monitora solicitações e respostas enviadas para e de seus aplicativos em execução no Serviço de Aplicativo.

O Security Center aproveita a escala da nuvem e a visibilidade que o Azure tem como provedor de nuvem, para monitorar ataques comuns de aplicativos web. O Security Center pode descobrir ataques em seus aplicativos e identificar ataques emergentes - mesmo enquanto os atacantes estão em fase de reconhecimento, digitalizando para identificar vulnerabilidades em vários aplicativos hospedados no Azure. Como um serviço nativo do Azure, o Security Center também está em uma posição única para oferecer análises de segurança baseadas em host que cobrem os álos de computação subjacentes para este PaaS, permitindo que o Security Center detecte ataques contra aplicativos da Web que já foram explorados. Para obter mais detalhes, consulte [Proteção contra ameaças para o Azure App Service](threat-protection.md#app-services).


## <a name="enabling-monitoring-and-protection-of-app-service"></a>Ativando o monitoramento e a proteção do Serviço de Aplicativo

1. No portal Azure, escolha o Security Center.
2. Vá para **Configurações de & de Preços** e escolha uma assinatura.
3. Em **Nível de preços**, na linha **Serviço de aplicativos**, alterne seu plano para **Ativado**.

    [![Habilitando serviços de aplicativos em sua assinatura de nível Padrão](media/security-center-app-services/app-services-toggle.png)](media/security-center-app-services/app-services-toggle.png#lightbox)


>[!NOTE]
> O número de instâncias listadas para a quantidade de **recursos** representa o número total de instâncias de computação, em todos os planos do App Service nesta assinatura, em execução no momento em que você abriu a lâmina do nível de preços.
>
> O Azure App Service oferece uma variedade de planos. Seu plano app service define o conjunto de recursos de computação para um aplicativo web para executar. Estes são equivalentes a fazendas de servidores em hospedagem web convencional. Um ou mais aplicativos podem ser configurados para ser executado nos mesmos recursos de computação (ou no mesmo plano de Serviço de Aplicativo).
>
>Para validar a contagem, vá para 'Planos de Serviço de Aplicativo' no Portal Azure, onde você pode ver o número de instâncias de computação usadas por cada plano. 






Para desativar o monitoramento e as recomendações do seu serviço de aplicativos, repita esse processo e ative o plano do **Serviço de Aplicativo do Azure** para **Desativado**.



## <a name="see-also"></a>Confira também
Neste artigo, você aprendeu como usar os recursos de monitoramento na Central de segurança do Azure. Para saber mais sobre a Central de Segurança do Azure, leia os seguintes artigos:

* [Configurando políticas de segurança na Central de Segurança do Azure](tutorial-security-policy.md): saiba como configurar políticas de segurança na Central de Segurança do Azure.
* [Gerenciando e respondendo aos alertas de segurança na Central de Segurança do Azure](security-center-managing-and-responding-alerts.md): aprenda a gerenciar e responder aos alertas de segurança.
* [Serviços de aplicativos](security-center-virtual-machine-protection.md#app-services): Veja uma lista de seus ambientes de serviço de Aplicativos com resumos de saúde.
* [Monitorando as soluções de parceiros com a Central de Segurança do Azure](security-center-partner-solutions.md): saiba como monitorar o status de integridade de suas soluções de parceiros.
* [Blog de Segurança do Azure](https://blogs.msdn.com/b/azuresecurity/): Encontre posts no blog sobre segurança e conformidade do Azure.
