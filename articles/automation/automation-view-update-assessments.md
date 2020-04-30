---
title: Exibir a automação do Azure Gerenciamento de Atualizações avaliações de atualização
description: Este artigo descreve como exibir as avaliações de atualização para implantações de atualização.
services: automation
ms.subservice: update-management
ms.date: 01/21/2020
ms.topic: conceptual
ms.openlocfilehash: 75762afc9ae69da81e89ce320f454d9764f82914
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/28/2020
ms.locfileid: "81617397"
---
# <a name="view-azure-automation-update-management-update-assessments"></a>Exibir a automação do Azure Gerenciamento de Atualizações avaliações de atualização

Em sua conta de automação do Azure, selecione **Gerenciamento de atualizações** para exibir o status de seus computadores.

Essa exibição fornece informações sobre os computadores, atualizações ausentes, implantações de atualização e implementações de atualização agendada. Na coluna **conformidade** , você pode ver a última vez em que o computador foi avaliado. Na coluna **prontidão do agente de atualização** , você pode ver a integridade do agente de atualização. Se houver um problema, selecione o link para acessar a documentação de solução de problemas que pode ajudá-lo a corrigir o problema.

Para executar uma pesquisa de logs que retorna informações sobre a máquina, atualização ou implantação, selecione o item correspondente na lista. O painel pesquisa de log é aberto com uma consulta para o item selecionado.

![Exibição padrão do Gerenciamento de Atualizações](media/automation-update-management/update-management-view.png)

## <a name="view-missing-updates"></a>Exibir atualizações ausentes

Selecione **Atualizações ausentes** para exibir a lista de atualizações que estão faltando nos computadores. Cada atualização é listada e pode ser selecionada. As informações sobre o número de computadores que exigem a atualização, os detalhes do sistema operacional e um link para obter mais informações são mostradas. O painel pesquisa de logs também mostra mais detalhes sobre as atualizações.

![Atualizações ausentes](./media/automation-view-update-assessments/automation-view-update-assessments-missing-updates.png)

## <a name="update-classifications"></a>Classificações de origem

As tabelas a seguir listam as classificações de atualização com suporte no Gerenciamento de Atualizações, com uma definição para cada classificação.

### <a name="windows"></a>Windows

|classificação  |Descrição  |
|---------|---------|
|Atualizações críticas     | Atualizações para problemas específicos que abordam bugs críticos não relacionados à segurança.        |
|Atualizações de segurança     | Atualizações para problemas específicos de produtos e relacionados à segurança.        |
|Pacotes cumulativos de atualização     | Conjuntos de hotfixes que são empacotados juntos para facilitar a implantação.        |
|Feature packs     | Novos recursos do produto que são distribuídos fora de uma versão do produto.        |
|Service packs     | Conjuntos de hotfixes que são aplicados a um aplicativo.        |
|Atualizações de Definições     | Atualizações para vírus ou outros arquivos de definição.        |
|Ferramentas     | Utilitários ou recursos que ajudam a concluir uma ou mais tarefas.        |
|Atualizações     | Atualizações de aplicativos ou arquivos que estão instalados no momento.        |

### <a name="linux"></a><a name="linux-2"></a>Linux

|classificação  |Descrição  |
|---------|---------|
|Atualizações críticas ou de segurança     | Atualizações para um problema específico ou um problema relacionado à segurança específico do produto.         |
|Outras atualizações     | Todas as outras atualizações que não são críticas por natureza ou que não são atualizações de segurança.        |

Para o Linux, Gerenciamento de Atualizações pode distinguir entre atualizações críticas e atualizações de segurança na nuvem enquanto exibe dados de avaliação. (Essa granularidade é possível devido ao enriquecimento de dados na nuvem.) Para aplicação de patch, Gerenciamento de Atualizações depende dos dados de classificação disponíveis no computador. Ao contrário de outras distribuições, o CentOS não tem essas informações disponíveis nas versões RTM do produto. Se você tiver máquinas CentOS configuradas para retornar dados de segurança para o comando a seguir, Gerenciamento de Atualizações poderá aplicar patch com base nas classificações:

```bash
sudo yum -q --security check-update
```

Atualmente, não há um método com suporte para habilitar a disponibilidade de dados de classificação nativa no CentOS. Neste momento, apenas o suporte de melhor esforço é fornecido aos clientes que habilitaram essa funcionalidade por conta própria.

Para classificar atualizações no Red Hat Enterprise versão 6, você precisa instalar o plug-in yum-Security. No Red Hat Enterprise Linux 7, o plug-in já faz parte do próprio yum, não há necessidade de instalar nada. Para obter mais informações, consulte o seguinte [artigo de conhecimento](https://access.redhat.com/solutions/10021)do Red Hat.

## <a name="next-steps"></a>Próximas etapas

Depois de exibir as avaliações de atualização, você pode agendar uma implantação de atualização seguindo as etapas em [gerenciar atualizações e patches para suas VMs do Azure](automation-tutorial-update-management.md).
