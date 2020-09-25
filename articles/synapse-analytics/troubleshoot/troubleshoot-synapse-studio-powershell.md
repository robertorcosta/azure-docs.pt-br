---
title: Solucionar problemas de conectividade do Synapse Studio (versão prévia) com o PowerShell
description: Solucionar problemas de conectividade do Azure Synapse Studio usando o PowerShell
author: julieMSFT
ms.service: synapse-analytics
ms.topic: conceptual
ms.date: 04/15/2020
ms.author: jrasnick
ms.reviewer: jrasnick
ms.openlocfilehash: 7cf9f18701317482284a3f1fa4b69a2c2927f64a
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/25/2020
ms.locfileid: "91287705"
---
# <a name="diagnose-azure-synapse-studio-preview-connectivity-issues-with-powershell-script"></a>Diagnosticar problemas de conectividade do Azure Synapse Studio (versão prévia) com o script do PowerShell

O Azure Synapse Studio (versão prévia) depende de um conjunto de pontos de extremidade da API Web para funcionar corretamente. Este guia o ajudará a identificar as causas de problemas de conectividade quando você estiver:
- configurar sua rede local (como a rede por trás de um firewall corporativo) para acessar o Azure Synapse Studio.
- enfrentando problemas de conectividade usando o Azure Synapse Studio.

## <a name="prerequisite"></a>Pré-requisito

* PowerShell 5,0 ou versão superior no Windows, ou
* PowerShell Core 6,0 ou versão superior no Windows ou Linux.

## <a name="troubleshooting-steps"></a>Etapas para solucionar problemas

Clique com o botão direito do mouse no link a seguir e selecione "salvar destino como":

- [Test-AzureSynapse.ps1](https://go.microsoft.com/fwlink/?linkid=2119734)

Como alternativa, você pode abrir o link diretamente e salvar o arquivo de script aberto. Não salve o endereço do link acima, pois ele pode ser alterado no futuro.

No explorador de arquivos, clique com o botão direito do mouse no arquivo de script baixado e selecione "executar com o PowerShell".

![Executar o arquivo de script baixado com o PowerShell](media/troubleshooting-synapse-studio-powershell/run-with-powershell.png)

Quando solicitado, insira o nome do espaço de trabalho Synapse do Azure que está tendo um problema no momento ou que você deseja testar a conectividade e pressione Enter.

![Inserir nome do espaço de trabalho](media/troubleshooting-synapse-studio-powershell/enter-workspace-name.png)

A sessão de diagnóstico será iniciada. Aguarde a conclusão.

![Aguardar a conclusão do diagnóstico](media/troubleshooting-synapse-studio-powershell/wait-for-diagnosis.png)

No final, um resumo do diagnóstico será mostrado. Se o seu computador não puder se conectar a um ou mais pontos de extremidade, ele mostrará algumas sugestões na seção "Resumo".

![Examinar Resumo do diagnóstico](media/troubleshooting-synapse-studio-powershell/diagnosis-summary.png)

Além disso, um arquivo de log de diagnóstico para essa sessão será gerado na mesma pasta que o script de solução de problemas. Sua localização é mostrada na seção "Dicas gerais" ( `D:\TestAzureSynapse_2020....log` ). Você pode enviar esse arquivo para o suporte técnico, se necessário.

Se você for um administrador de rede e ajustar sua configuração de firewall para o Azure Synapse Studio, os detalhes técnicos mostrados acima da seção "Resumo" poderão ajudar.

* Todos os itens de teste (solicitações) marcados com "aprovado" significam que eles passaram por testes de conectividade, independentemente do código de status HTTP.
 Para as solicitações com falha, o motivo é mostrado em amarelo, como `NamedResolutionFailure` ou `ConnectFailure` . Esses motivos podem ajudá-lo a descobrir se há configurações incorretas no ambiente de rede.


## <a name="next-steps"></a>Próximas etapas
Se as etapas anteriores não ajudarem a resolver o problema, [crie um tíquete de suporte](../../sql-data-warehouse/sql-data-warehouse-get-started-create-support-ticket.md).
