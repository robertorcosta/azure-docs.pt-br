---
title: Conecte os dados do CEF ao Azure Sentinel Preview| Microsoft Docs
description: Saiba como conectar dados do CEF ao Azure Sentinel.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/26/2019
ms.author: yelevin
ms.openlocfilehash: 8314614616c6b8969832d52fc684d47ba1bf0fe3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "77588341"
---
# <a name="connect-your-external-solution-using-common-event-format"></a>Conecte sua solução externa usando o Formato de Evento Comum


Quando você conecta uma solução externa que envia mensagens CEF, há três etapas para se conectar com o Azure Sentinel:

PASSO 1: [Conecte o CEF implantando o agente](connect-cef-agent.md) PASSO 2: [Execute as etapas específicas da solução](connect-cef-solution-config.md) PASSO 3: Verifique a [conectividade](connect-cef-verify.md)

Este artigo descreve como a conexão funciona, fornece pré-requisitos e dá-lhe as etapas para implantar o agente em soluções de segurança que enviam mensagens common event format (CEF) em cima do Syslog. 

> [!NOTE] 
> Os dados são armazenados na localização geográfica do espaço de trabalho no qual você está executando o Azure Sentinel.

Para fazer essa conexão, você precisa implantar um agente em uma máquina Linux dedicada (VM ou no local) para suportar a comunicação entre o aparelho e o Azure Sentinel. O diagrama a seguir descreve a configuração no caso de um VM Linux no Azure.

 ![CEF no Azure](./media/connect-cef/cef-syslog-azure.png)

Alternativamente, essa configuração existirá se você usar uma VM em outra nuvem ou uma máquina no local. 

 ![CEF no local](./media/connect-cef/cef-syslog-onprem.png)


## <a name="security-considerations"></a>Considerações sobre segurança

Certifique-se de configurar a segurança da máquina de acordo com a política de segurança da sua organização. Por exemplo, você pode configurar sua rede para se alinhar à política de segurança da rede corporativa e alterar as portas e protocolos no daemon para se alinhar às suas necessidades. Você pode usar as seguintes instruções para melhorar a configuração de segurança da sua máquina:  [Proteger vm no Azure](../virtual-machines/linux/security-policy.md), [Práticas recomendadas para segurança de rede](../security/fundamentals/network-best-practices.md).

Para usar a comunicação TLS entre a solução de segurança e a máquina Syslog, você precisará configurar o daemon Syslog (rsyslog ou syslog-ng) para se comunicar em TLS: [Criptografando o tráfego syslog com TLS -rsyslog](https://www.rsyslog.com/doc/v8-stable/tutorials/tls_cert_summary.html), [Criptografando mensagens de log com TLS –syslog-ng](https://support.oneidentity.com/technical-documents/syslog-ng-open-source-edition/3.22/administration-guide/60#TOPIC-1209298).

 
## <a name="prerequisites"></a>Pré-requisitos
Certifique-se de que a máquina Linux que você usa como proxy está executando um dos seguintes sistemas operacionais:

- 64 bits
  - CentOS 6 e 7
  - Amazon Linux 2017.09
  - Oracle Linux 6 e 7
  - Red Hat Enterprise Linux Server 6 e 7
  - Debian GNU/Linux 8 e 9
  - Ubuntu Linux 14.04 LTS, 16.04 LTS e 18.04 LTS
  - SUSE Linux Enterprise Server 12
- 32 bits
   - CentOS 6
   - Oracle Linux 6
   - Red Hat Enterprise Linux Server 6
   - Debian GNU/Linux 8 e 9
   - Ubuntu Linux 14.04 LTS and 16.04 LTS
 
 - Versões de Daemon
   - Syslog-ng: 2.1 - 3.22.1
   - Rsyslog: v8
  
 - Syslog RFCs suportados
   - Syslog RFC 3164
   - Syslog RFC 5424
 
Certifique-se de que sua máquina também atenda aos seguintes requisitos: 
- Permissões
    - Você deve ter permissões elevadas (sudo) em sua máquina. 
- Requisitos de software
    - Certifique-se de que o Python está em execução na sua máquina



## <a name="next-steps"></a>Próximas etapas
Neste documento, você aprendeu como conectar aparelhos CEF ao Azure Sentinel. Para saber mais sobre o Azure Sentinel, consulte os seguintes artigos:
- Saiba como [obter visibilidade dos seus dados e possíveis ameaças](quickstart-get-visibility.md).
- Comece a [detectar ameaças com o Azure Sentinel](tutorial-detect-threats.md).

