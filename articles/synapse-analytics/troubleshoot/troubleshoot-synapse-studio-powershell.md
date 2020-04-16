---
title: Solução de problemas Azure Synapse Studio (pré-visualização) conectividade PowerShell
description: Solução de problemas conectividade do Azure Synapse Studio usando o PowerShell
author: julieMSFT
ms.service: synapse-analytics
ms.topic: conceptual
ms.date: 04/15/2020
ms.author: jrasnick
ms.reviewer: jrasnick
ms.openlocfilehash: bbc985407a6cb56f4f1b539f514ab092b5f7d0de
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/16/2020
ms.locfileid: "81431469"
---
# <a name="diagnose-azure-synapse-studio-preview-connectivity-issues-with-powershell-script"></a>Diagnosticar problemas de conectividade do Azure Synapse Studio (pré-visualização) com o script PowerShell

O Azure Synapse Studio (visualização) depende de um conjunto de pontos finais da API da Web para funcionar corretamente. Este guia irá ajudá-lo a identificar causas de problemas de conectividade quando você:
- configurando sua rede local (como rede atrás de um firewall corporativo) para acessar o Azure Synapse Studio.
- enfrentando problemas de conectividade usando o Azure Synapse Studio.

## <a name="prerequisite"></a>Pré-requisito

* PowerShell 5.0 ou versão superior no Windows, ou
* PowerShell Core 6.0 ou versão superior no Windows ou Linux.

## <a name="troubleshooting-steps"></a>Etapas para solucionar problemas

Clique com o botão direito do mouse no seguinte link e clique em "Salvar destino como":

- [Teste-AzureSynapse.ps1](https://go.microsoft.com/fwlink/?linkid=2119734)

Alternativamente, você pode abrir o link diretamente e salvar o arquivo de script aberto. Não salve o endereço do link acima, pois ele pode mudar no futuro.

No explorador de arquivos, clique com o botão direito do mouse no arquivo de script baixado e clique em "Executar com powershell".

![Executar arquivo de script baixado com PowerShell](media/troubleshooting-synapse-studio-powershell/run-with-powershell.png)

Quando solicitado, digite o nome do espaço de trabalho Azure Synapse que está atualmente enfrentando um problema ou que você deseja testar para conectividade e pressione enter.

![Digite o nome do espaço de trabalho](media/troubleshooting-synapse-studio-powershell/enter-workspace-name.png)

A sessão de diagnóstico será iniciada. Aguarde a conclusão.

![Aguarde o diagnóstico para ser concluído](media/troubleshooting-synapse-studio-powershell/wait-for-diagnosis.png)

No final, um resumo do diagnóstico será mostrado. Se o seu PC não puder se conectar a um ou mais pontos finais, ele mostrará algumas sugestões na seção "Resumo".

![Reveja o resumo do diagnóstico](media/troubleshooting-synapse-studio-powershell/diagnosis-summary.png)

Além disso, um arquivo de registro de diagnóstico para esta sessão será gerado na mesma pasta que o script de solução de problemas. Sua localização é mostrada na`D:\TestAzureSynapse_2020....log`seção "Dicas gerais" (). Você pode enviar este arquivo para suporte técnico, se necessário.

Se você é um administrador de rede e ajusta sua configuração de firewall para o Azure Synapse Studio, os detalhes técnicos mostrados acima da seção "Resumo" podem ajudar.

* Todos os itens de teste (solicitações) marcados com "Passed" significam que passaram por testes de conectividade, independentemente do código de status HTTP.
 Para os pedidos falhos, a razão `NamedResolutionFailure` é `ConnectFailure`mostrada em amarelo, como ou . Essas razões podem ajudá-lo a descobrir se há configurações erradas com o ambiente da rede.


## <a name="next-steps"></a>Próximas etapas
Se as etapas anteriores não ajudarem a resolver o problema [Crie um ticket de suporte](../../sql-data-warehouse/sql-data-warehouse-get-started-create-support-ticket.md).
