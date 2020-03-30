---
author: roygara
ms.service: storage
ms.topic: include
ms.date: 12/11/2018
ms.author: rogarana
ms.openlocfilehash: aeb15fbb8da44a203789e06a359cb664998602ab
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "77123190"
---
O agente de Sincronização de arquivos do Azure é atualizado regularmente para adicionar novos recursos e resolver problemas. Recomendamos que você configure o Microsoft Update para obter atualizações para o agente de Sincronização de arquivos do Azure à medida que elas ficarem disponíveis.

#### <a name="major-vs-minor-agent-versions"></a>Versões do agente principal versus secundário
* As versões do agente principal geralmente contêm novos recursos e têm um número crescente como a primeira parte do número da versão. Por exemplo: \*2.\*.\*\*
* As versões do agente secundário também são chamadas de "patches" e são lançadas com mais frequência do que as versões do principal. Geralmente contêm correções de bug e pequenas melhorias, mas sem novos recursos. Por exemplo: \*\*.3.\*\*

#### <a name="upgrade-paths"></a>Caminhos de atualização
Há quatro maneiras aprovadas e testadas para instalar as atualizações do agente de Sincronização de Arquivos do Azure. 
1. **(Preferencial) Configure o Microsoft Update para baixar e instalar automaticamente as atualizações do agente.**  
    É sempre recomendável executar todas as atualizações de Sincronização de arquivos do Azure para garantir que você tenha acesso às últimas correções para o Server Agent. O Microsoft Update simplifica esse processo, baixando e instalando atualizações automaticamente para você.
2. **Use AfsUpdater.exe para baixar e instalar atualizações de agente.**  
    O AfsUpdater.exe está localizado no diretório de instalação do agente. Clique duas vezes no executável para baixar e instalar atualizações de agente. 
3. **Patch um agente azure File Sync existente usando um arquivo de patch do Microsoft Update ou um executável .msp. O mais recente pacote de atualização do Azure File Sync pode ser baixado no [Microsoft Update Catalog](https://www.catalog.update.microsoft.com/Search.aspx?q=Azure%20File%20Sync).**  
    Executar um executável .msp atualizará a instalação de Sincronização de arquivos do Azure com o mesmo método usado automaticamente pelo Microsoft Update no caminho de atualização anterior. A aplicação de um patch do Microsoft Update realizará uma atualização no local de uma instalação e Sincronização de arquivos do Azure.
4. **Baixe o mais novo instalador de agente Azure File Sync do [Microsoft Download Center](https://go.microsoft.com/fwlink/?linkid=858257).**  
    Para fazer upgrade de uma instalação existente do agente de Sincronização de Arquivos do Azure, desinstale a versão mais antiga e então instale a última versão do instalador baixado. O registro do servidor, os grupos de sincronização e outras configurações são mantidos pelo instalador de Sincronização de arquivos do Azure.

#### <a name="automatic-agent-lifecycle-management"></a>Gerenciamento automático do ciclo de vida do agente
Com a versão 6 do agente, a equipe de sincronização de arquivos introduziu um recurso de atualização automática do agente. Você pode selecionar qualquer um dos dois modos e especificar uma janela de manutenção na qual a atualização deve ser tentada no servidor. Este recurso foi projetado para ajudá-lo com o gerenciamento do ciclo de vida do agente, fornecendo um guardrail impedindo que seu agente expire ou permitindo uma configuração sem incômodos.
1. A **configuração padrão** tentará impedir que o agente expire. Dentro de 21 dias da data de validade postada de um agente, o agente tentará se auto-atualizar. Ele iniciará uma tentativa de upgrade uma vez por semana dentro de 21 dias antes do vencimento e na janela de manutenção selecionada. **Essa opção não elimina a necessidade de tomar patches regulares do Microsoft Update.**
1. Opcionalmente, você pode selecionar que o agente irá atualizar-se automaticamente assim que uma nova versão do agente estiver disponível (atualmente não aplicável a servidores agrupados). Essa atualização ocorrerá durante a janela de manutenção selecionada e permitirá que seu servidor se beneficie de novos recursos e melhorias assim que eles estiverem geralmente disponíveis. Esta é a configuração recomendada e sem preocupações que fornecerá versões principais do agente, bem como patches de atualização regulares para o seu servidor. Todos os agentes liberados são de qualidade GA. Se você selecionar essa opção, a Microsoft enviará a versão mais recente do agente para você. Os servidores agrupados são excluídos. Uma vez que o vôo esteja completo, o agente também estará disponível no [Microsoft Download Center](https://go.microsoft.com/fwlink/?linkid=858257) aka.ms/AFS/agent.

 ##### <a name="changing-the-auto-upgrade-setting"></a>Alterando a configuração de upgrade automático

As instruções a seguir descrevem como alterar as configurações depois de concluir o instalador, se você precisar fazer alterações.

Abra um console PowerShell e navegue até o diretório onde você instalou o agente de sincronização e importe os cmdlets do servidor. Por padrão, isso seria algo parecido com isso:
```powershell
cd 'C:\Program Files\Azure\StorageSyncAgent'
Import-Module -Name .\StorageSync.Management.ServerCmdlets.dll
```

Você pode `Get-StorageSyncAgentAutoUpdatePolicy` correr para verificar a configuração atual da diretiva e determinar se deseja alterá-la.

Para alterar a configuração de diretiva atual para a faixa de atualização atrasada, você pode usar:
```powershell
Set-StorageSyncAgentAutoUpdatePolicy -PolicyMode UpdateBeforeExpiration
```

Para alterar a configuração de diretiva atual para a faixa de atualização imediata, você pode usar:
```powershell
Set-StorageSyncAgentAutoUpdatePolicy -PolicyMode InstallLatest
```

#### <a name="agent-lifecycle-and-change-management-guarantees"></a>Garantia de gerenciamento de alterações e ciclo de vida do agente
O Azure File Sync é um serviço em nuvem, que introduz continuamente novos recursos e melhorias. Isso significa que uma versão específica do agente de Sincronização de arquivos do Azure somente poderá ter suporte por um tempo limitado. Para facilitar sua implantação, as seguintes regras garantem que você tenha tempo e notificação suficientes para acomodar atualizações/atualizações de agentes em seu processo de gerenciamento de alterações:

- As versões do agente principal terão suporte por pelo menos seis meses, a partir da data da versão inicial.
- Garantimos que há uma sobreposição de pelo menos três meses entre o suporte das versões do agente principal. 
- Avisos para servidores registrados que utilizam um agente que expirará em breve serão emitidos pelo menos três meses antes da expiração. É possível verificar se um servidor registrado está usando uma versão mais antiga do agente na seção de servidores registrados de um Serviço de Sincronização de Armazenamento.
- O tempo de vida de uma versão do agente secundário está associado à versão principal associada. Por exemplo, quando a versão do agente 3.0 for lançada, as versões do agente 2.\* serão todas definidas para expirar conjuntamente.

> [!Note]
> A instalação de uma versão do agente com um aviso de expiração exibirá um aviso, porém com êxito. A tentativa de instalar ou conectar uma versão do agente expirada não tem suporte e será bloqueada.
