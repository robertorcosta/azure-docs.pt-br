---
title: Exibir avaliações de atualização da Automação do Azure
description: Este artigo informa como exibir as avaliações de atualização para implantações de Gerenciamento de Atualizações.
services: automation
ms.subservice: update-management
ms.date: 01/21/2020
ms.topic: conceptual
ms.openlocfilehash: 321146364897d46a403bdfd6789fcb219179d88c
ms.sourcegitcommit: 0b80a5802343ea769a91f91a8cdbdf1b67a932d3
ms.contentlocale: pt-BR
ms.lasthandoff: 05/25/2020
ms.locfileid: "83830592"
---
# <a name="view-update-assessments"></a>Exibir as avaliações de atualização

Na sua conta de Automação do Azure, escolha **Gerenciamento de Atualizações** para ver o status dos computadores.

Essa exibição fornece informações sobre os computadores, atualizações ausentes, implantações de atualização e implementações de atualização agendada. Na coluna **CONFORMIDADE**, é possível ver a última vez em que a máquina foi avaliada. Na coluna **PREPARAÇÃO DO AGENTE DE ATUALIZAÇÃO**, veja a integridade do agente de atualização. Se houver um problema, escolha o link para ir para a documentação de solução problemas que pode ajudar a corrigir isso.

Para executar uma pesquisa de logs sobre as informações do computador, da atualização ou da implantação, escolha o item na lista. O painel Pesquisa de Logs abre com uma consulta para o item selecionado.

![Exibição padrão do Gerenciamento de Atualizações](media/automation-update-management/update-management-view.png)

## <a name="view-missing-updates"></a>Exibir atualizações ausentes

Selecione **Atualizações ausentes** para exibir a lista de atualizações que estão faltando nos computadores. Cada atualização é listada e pode ser selecionada. Informações sobre o número de computadores que exigem a atualização, detalhes do sistema operacional e um link para obter mais informações são mostrados. O painel Pesquisa de Logs mostra mais detalhes sobre as atualizações.

![Atualizações Ausentes](./media/automation-view-update-assessments/automation-view-update-assessments-missing-updates.png)

## <a name="work-with-update-classifications"></a>Trabalhar com classificações de atualização

As tabelas a seguir listam as classificações de atualização compatíveis no Gerenciamento de Atualizações, com uma definição de cada classificação.

### <a name="windows"></a>Windows

|classificação  |Descrição  |
|---------|---------|
|Atualizações críticas     | Atualizações para problemas específicos que abordam bugs críticos não relacionados à segurança.        |
|Atualizações de segurança     | Atualizações para problemas específicos de produtos e relacionados à segurança.        |
|Pacotes cumulativos de atualização     | Conjuntos de hotfixes que são empacotados para facilitar a implantação.        |
|Feature packs     | Novos recursos do produto que são distribuídos fora de uma versão do produto.        |
|Service packs     | Conjuntos de hotfixes que são aplicados a um aplicativo.        |
|Atualizações de definição     | Atualizações para vírus ou outros arquivos de definição.        |
|Ferramentas     | Utilitários ou recursos que ajudam a concluir uma ou mais tarefas.        |
|Atualizações     | Atualizações de aplicativos ou arquivos que estão instalados no momento.        |

### <a name="linux"></a><a name="linux-2"></a>Linux

|classificação  |Descrição  |
|---------|---------|
|Atualizações críticas ou de segurança     | Atualizações para um problema específico ou um problema relacionado à segurança específico do produto.         |
|Outras atualizações     | Todas as outras atualizações que não são críticas nem de segurança.        |

Para o Linux, o Gerenciamento de Atualizações pode distinguir entre e atualizações críticas e de segurança na nuvem ao exibir dados de avaliação. (Essa granularidade é possível devido ao enriquecimento de dados na nuvem.) Para aplicação de patch, o Gerenciamento de Atualizações se baseia em dados de classificação disponíveis no computador. Ao contrário de outras distribuições, o CentOS não tem essas informações disponíveis nas versões RTM do produto. Se você tiver computadores CentOS configurados para retornar dados de segurança para o seguinte comando, o Gerenciamento de Atualizações poderá aplicar patch com base em classificações:

```bash
sudo yum -q --security check-update
```

Atualmente, não há nenhum método compatível para habilitar a disponibilidade de dados nativos de classificação em CentOS. Neste momento, somente o suporte de melhor esforço é fornecido a clientes que habilitaram essa funcionalidade por conta própria.

Para classificar atualizações no Red Hat Enterprise versão 6, é preciso instalar o plug-in yum-security. No Red Hat Enterprise Linux 7, o plug-in já faz parte do próprio yum, não é preciso instalar nada. Para obter mais informações, veja o [artigo de conhecimento](https://access.redhat.com/solutions/10021) da Red Hat a seguir.

## <a name="next-steps"></a>Próximas etapas

Para obter informações gerais, veja [Gerenciar atualizações e patches para suas VMs do Azure](automation-tutorial-update-management.md).
