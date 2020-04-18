---
title: Veja as avaliações de atualização de gerenciamento de atualização de atualização de automação do Azure
description: Este artigo descreve como visualizar avaliações de atualização para implantações de atualização.
services: automation
ms.subservice: update-management
ms.date: 01/21/2020
ms.topic: conceptual
ms.openlocfilehash: 75762afc9ae69da81e89ce320f454d9764f82914
ms.sourcegitcommit: eefb0f30426a138366a9d405dacdb61330df65e7
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/17/2020
ms.locfileid: "81617397"
---
# <a name="view-azure-automation-update-management-update-assessments"></a>Veja as avaliações de atualização de gerenciamento de atualização de atualização de automação do Azure

Em sua conta azure Automação, selecione **Atualizar gerenciamento** para visualizar o status de suas máquinas.

Essa exibição fornece informações sobre os computadores, atualizações ausentes, implantações de atualização e implementações de atualização agendada. Na coluna **COMPLIANCE,** você pode ver a última vez que a máquina foi avaliada. Na coluna **PRONTIDÃO DO AGENTE DE ATUALIZAÇÃO,** você pode ver a saúde do agente de atualização. Se houver um problema, selecione o link para ir para a documentação de solução de problemas que pode ajudá-lo a corrigir o problema.

Para executar uma pesquisa de log que retorne informações sobre a máquina, atualização ou implantação, selecione o item correspondente na lista. O painel Pesquisa de log é aberto com uma consulta para o item selecionado.

![Exibição padrão do Gerenciamento de Atualizações](media/automation-update-management/update-management-view.png)

## <a name="view-missing-updates"></a>Exibir atualizações ausentes

Selecione **Atualizações ausentes** para exibir a lista de atualizações que estão faltando nos computadores. Cada atualização é listada e pode ser selecionada. Informações sobre o número de máquinas que requerem a atualização, detalhes do sistema operacional e um link para mais informações são mostradas. O painel Log Search também mostra mais detalhes sobre as atualizações.

![Atualizações ausentes](./media/automation-view-update-assessments/automation-view-update-assessments-missing-updates.png)

## <a name="update-classifications"></a>Classificações de origem

As tabelas a seguir listam as classificações de atualização suportadas no Gerenciamento de Atualizações, com uma definição para cada classificação.

### <a name="windows"></a>Windows

|classificação  |Descrição  |
|---------|---------|
|Atualizações críticas     | Atualizações para problemas específicos que abordam bugs críticos e não relacionados à segurança.        |
|Atualizações de segurança     | Atualizações para problemas específicos de segurança específicos do produto.        |
|Pacotes cumulativos de atualização     | Conjuntos de hotfixes que são embalados juntos para fácil implantação.        |
|Feature packs     | Novos recursos do produto que são distribuídos fora de uma versão do produto.        |
|Service packs     | Conjuntos de hotfixes que são aplicados a um aplicativo.        |
|Atualizações de definição     | Atualizações para vírus ou outros arquivos de definição.        |
|Ferramentas     | Utilitários ou recursos que ajudam a completar uma ou mais tarefas.        |
|Atualizações     | Atualizações para aplicativos ou arquivos que estão instalados atualmente.        |

### <a name="linux"></a><a name="linux-2"></a>Linux

|classificação  |Descrição  |
|---------|---------|
|Atualizações críticas ou de segurança     | Atualizações para um problema específico ou um problema relacionado à segurança específico do produto.         |
|Outras atualizações     | Todas as outras atualizações que não são críticas na natureza ou que não são atualizações de segurança.        |

Para o Linux, o Update Management pode distinguir entre atualizações críticas e atualizações de segurança na nuvem enquanto exibe dados de avaliação. (Essa granularidade é possível devido ao enriquecimento de dados na nuvem.) Para patches, o Update Management conta com dados de classificação disponíveis na máquina. Ao contrário de outras distribuições, o CentOS não tem essas informações disponíveis nas versões RTM do produto. Se você tiver máquinas CentOS configuradas para retornar dados de segurança para o seguinte comando, o Update Management pode corrigir com base em classificações:

```bash
sudo yum -q --security check-update
```

Atualmente, não há nenhum método suportado para permitir a disponibilidade nativa de dados de classificação no CentOS. Neste momento, apenas o suporte de melhor esforço é fornecido aos clientes que habilitaram essa funcionalidade por conta própria.

Para classificar as atualizações na versão 6 do Red Hat Enterprise, você precisa instalar o plugin yum-security. No Red Hat Enterprise Linux 7, o plugin já faz parte do próprio yum, não há necessidade de instalar nada. Para obter mais informações, consulte o [seguinte artigo de conhecimento](https://access.redhat.com/solutions/10021)da Red Hat .

## <a name="next-steps"></a>Próximas etapas

Depois de visualizar quaisquer avaliações de atualização, você pode agendar uma implantação de atualização seguindo as etapas em [Gerenciar atualizações e patches para suas VMs do Azure](automation-tutorial-update-management.md).
