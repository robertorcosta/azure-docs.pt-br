---
title: O que há de novo no agente de servidores habilitados para Arc do Azure (versão prévia)
description: Este artigo tem notas de versão para o agente de servidores habilitados para Arc do Azure (versão prévia). Para muitos dos problemas resumidos, há links para detalhes adicionais.
ms.topic: conceptual
ms.date: 08/31/2020
ms.openlocfilehash: 0f5060189d6f9cac620b49f414e0e27bed3e356e
ms.sourcegitcommit: d68c72e120bdd610bb6304dad503d3ea89a1f0f7
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/01/2020
ms.locfileid: "89236595"
---
# <a name="whats-new-with-azure-arc-enabled-servers-preview-agent"></a>O que há de novo no agente de servidores habilitados para Arc do Azure (versão prévia)

O agente de computador conectado de servidores habilitados para Arc (visualização) do Azure recebe melhorias em uma base contínua. Para se manter atualizado com os desenvolvimentos mais recentes, este artigo fornece informações sobre:

- As versões mais recentes
- Problemas conhecidos
- Correções de bug

## <a name="august-2020"></a>Agosto de 2020

Versão: 0,11

- Suporte para Ubuntu 20, 4.

- Melhorias de confiabilidade para implantações de extensão.

### <a name="known-issues"></a>Problemas conhecidos

Se você estiver usando uma versão mais antiga do agente do Linux e o tiver configurado para usar um servidor proxy, será necessário reconfigurar a configuração do servidor proxy após a atualização. Para fazer isso, execute `sudo azcmagent_proxy add http://proxyserver.local:83`.

## <a name="next-steps"></a>Próximas etapas

Antes de avaliar ou habilitar os servidores habilitados para Arc (versão prévia) em vários computadores híbridos, examine a [visão geral do agente de computador conectado](agent-overview.md) para entender os requisitos, detalhes técnicos sobre o agente e os métodos de implantação.