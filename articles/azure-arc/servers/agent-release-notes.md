---
title: O que há de novo no agente de servidores habilitados para Arc do Azure
description: Este artigo tem notas de versão para o agente de servidores habilitados para o Azure Arc. Para muitos dos problemas resumidos, há links para detalhes adicionais.
ms.topic: conceptual
ms.date: 09/16/2020
ms.openlocfilehash: 1918d03b5bbfaaa64b7d74c18fad4eb9a86800a0
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/22/2020
ms.locfileid: "90908155"
---
# <a name="whats-new-with-azure-arc-enabled-servers-agent"></a>O que há de novo no agente de servidores habilitados para Arc do Azure

O agente de máquina conectado de servidores habilitados para Arc do Azure recebe melhorias em uma base contínua. Para se manter atualizado com os desenvolvimentos mais recentes, este artigo fornece informações sobre:

- As versões mais recentes
- Problemas conhecidos
- Correções de bug

## <a name="september-2020"></a>Setembro de 2020

Versão: 1,0 (disponibilidade geral)

### <a name="plan-for-change"></a>Plano para alteração

- O suporte para agentes de visualização (todas as versões anteriores a 1,0) será removido em uma atualização de serviço futura.
- O suporte ao ponto de extremidade de fallback foi removido `.azure-automation.net` . Se você tiver um proxy, precisará permitir o ponto de extremidade `*.his.arc.azure.com` .
- Se o agente do computador conectado estiver instalado em uma máquina virtual hospedada no Azure, as extensões de VM não poderão ser instaladas ou modificadas a partir do recurso de servidores habilitados para Arc. Isso é para evitar que operações de extensão conflitantes sejam executadas do recurso **Microsoft. Compute** e **Microsoft. HybridCompute** da máquina virtual. Use o recurso **Microsoft. Compute** para o computador para todas as operações de extensão.
- O nome do processo de configuração de convidado foi alterado, de *GCD* para *gcad* no Linux e de *gcservice* para *gcarcservice* no Windows.

### <a name="new-feature"></a>New recurso

- `azcmagent logs`Opção adicionada para coletar informações de suporte.
- `azcmagent license`Opção adicionada para exibir o EULA.
- `azcmagent show --json`Opção adicionada para saída de estado do agente no formato facilmente analisável.
- Foi adicionado um sinalizador na `azcmagent show` saída para indicar se o servidor está em uma máquina virtual hospedada no Azure.
- `azcmagent disconnect --force-local-only`Opção adicionada para permitir a redefinição do estado do agente local quando o serviço do Azure não pode ser acessado.
- `azcmagent connect --cloud`Opção adicionada para dar suporte a nuvens adicionais. Nesta versão, somente o serviço do Azure é suportado pelo Service no momento da versão do agente.
- O agente foi localizado em idiomas com suporte do Azure.

### <a name="fixed"></a>Fixo

- Melhorias na verificação de conectividade.
- Problema corrigido com as configurações do servidor proxy sendo perdidas ao atualizar o agente no Linux.
- Problemas resolvidos ao tentar instalar o agente no servidor que executa o Windows Server 2012 R2.
- Melhorias na confiabilidade da instalação da extensão

## <a name="august-2020"></a>Agosto de 2020

Versão: 0,11

- Esta versão anunciou anteriormente o suporte para Ubuntu 20, 4. Como algumas extensões de VM do Azure não dão suporte ao Ubuntu 20, 4, o suporte para esta versão do Ubuntu está sendo removido.

- Melhorias de confiabilidade para implantações de extensão.

### <a name="known-issues"></a>Problemas conhecidos

Se você estiver usando uma versão mais antiga do agente do Linux e o tiver configurado para usar um servidor proxy, será necessário reconfigurar a configuração do servidor proxy após a atualização. Para fazer isso, execute `sudo azcmagent_proxy add http://proxyserver.local:83`.

## <a name="next-steps"></a>Próximas etapas

Antes de avaliar ou habilitar servidores habilitados para Arc em vários computadores híbridos, examine [Visão geral do agente do Computador Conectado](agent-overview.md) para entender os requisitos, os detalhes técnicos sobre o agente e os métodos de implantação.