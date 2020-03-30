---
title: Configure as configurações do proxy do serviço de mobilidade para a recuperação de desastres do Azure para o Azure | Microsoft Docs
description: Fornece detalhes sobre como configurar o serviço de mobilidade quando os clientes usam um proxy em seu ambiente de origem.
services: site-recovery
author: sideeksh
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 03/18/2020
ms.author: sideeksh
ms.openlocfilehash: 3d33b5a89a718a41e5c547551f6e7eb4f7033a63
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79503121"
---
# <a name="configure-mobility-service-proxy-settings-for-azure-to-azure-disaster-recovery"></a>Configure as configurações do proxy do serviço de mobilidade para a recuperação de desastres do Azure para o Azure

Este artigo fornece orientações sobre a personalização das configurações de rede na máquina virtual (VM) alvo quando você está replicando e recuperando VMs do Azure de uma região para outra, usando [o Azure Site Recovery](site-recovery-overview.md).

O objetivo deste documento é fornecer etapas para configurar configurações proxy para o Azure Site Recovery Mobility Service no cenário de recuperação de desastres do Azure para o Azure. 

Proxies são gateways de rede que permitem/desautorizar conexões de rede a pontos finais. Normalmente, um proxy é uma máquina fora da máquina cliente que tenta acessar pontos finais de rede. Uma lista de bypass permite que o cliente faça conexões diretamente para os pontos finais sem passar pelo proxy. Um nome de usuário e senha podem ser definidos opcionalmente para um proxy por admins de rede para que apenas clientes autenticados possam usar proxy. 

## <a name="before-you-start"></a>Antes de começar

Saiba como o Site Recovery fornece recuperação de desastre para [esse cenário](azure-to-azure-architecture.md).
Entenda a [orientação de rede](azure-to-azure-about-networking.md) quando estiver replicando e recuperando VMs do Azure de uma região para outra, usando [o Azure Site Recovery](site-recovery-overview.md).
Certifique-se de que seu proxy seja configurado adequadamente com base nas necessidades de sua organização.

## <a name="configure-the-mobility-service"></a>Configure o Serviço de Mobilidade

O Serviço de Mobilidade suporta apenas proxies não autenticados. Ele fornece duas maneiras de inserir detalhes proxy para comunicação com pontos finais de recuperação de site. 

### <a name="method-1-auto-detection"></a>Método 1: Detecção automática

O Mobility Service detecta automaticamente as configurações de proxy das configurações do ambiente ou das configurações do IE (Somente windows) durante a replicação ativada. 

- Windows OS: Durante a replicação de habilitação, o Mobility Service detecta as configurações de proxy configuradas no Internet Explorer para usuário do sistema local. Para configurar o proxy para conta do Sistema Local, um administrador pode usar psexec para iniciar um prompt de comando e, em seguida, o Internet Explorer. 
- Sistema operacional Windows: As configurações de proxy são configuradas como variáveis de ambiente http_proxy e no_proxy. 
- Sistema operacional Linux: As configurações de proxy são configuradas em /etc/perfil ou /etc/ambiente como variáveis de ambiente http_proxy, no_proxy. 
- As configurações de proxy detectadas automaticamente são salvas no arquivo proxy do Mobility Service ProxyInfo.conf 
- Localização padrão do ProxyInfo.conf 
    - Windows: C:\ProgramData\Microsoft Azure Site Recovery\Config\ProxyInfo.conf 
    - Linux: /usr/local/InMage/config/ProxyInfo.conf


### <a name="method-2-provide-custom-application-proxy-settings"></a>Método 2: Forneça configurações personalizadas de proxy de aplicativos

Neste caso, o cliente fornece configurações personalizadas de proxy de aplicativos no arquivo de configuração do Serviço de Mobilidade ProxyInfo.conf. Esse método permite que os clientes forneçam proxy apenas para o Mobility Service ou um proxy diferente para o Azure Site Recovery Mobility Service do que um proxy (ou nenhum proxy) para o resto dos aplicativos na máquina.

## <a name="proxy-template"></a>Modelo proxy
ProxyInfo.conf contém o seguinte modelohttp://1.2.3.4 [proxy] Endereço= Port=5678 BypassList=hypervrecoverymanager.windowsazure.com,login.microsoftonline.com,blob.core.windows.net. O BypassList não suporta curingas como '*.windows.net', mas dar windows.net é bom o suficiente para contornar. 

## <a name="next-steps"></a>Próximas etapas:
- Leia [as orientações de rede](site-recovery-azure-to-azure-networking-guidance.md) para replicar VMs do Azure.
- Implante a recuperação de desastres [replicando as VMs do Azure](site-recovery-azure-to-azure.md).