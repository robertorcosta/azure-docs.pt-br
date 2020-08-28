---
title: Gerenciar backups com o controle de acesso baseado em função
description: Use o Controle de Acesso baseado em função para gerenciar o acesso a operações de gerenciamento de backups em um cofre dos Serviços de Recuperação.
ms.reviewer: utraghuv
ms.topic: conceptual
ms.date: 06/24/2019
ms.openlocfilehash: 1a4bb617511f5d5ccf882ccb11e7d88eb7a848ea
ms.sourcegitcommit: 419cf179f9597936378ed5098ef77437dbf16295
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/27/2020
ms.locfileid: "89017797"
---
# <a name="use-role-based-access-control-to-manage-azure-backup-recovery-points"></a>Use o Controle de Acesso baseado em função para gerenciar pontos de recuperação de Backup do Azure

O Azure RBAC (controle de acesso baseado em função) permite o gerenciamento de acesso refinado para o Azure. Com o RBAC, você pode separar as tarefas dentro de sua equipe e conceder somente a quantidade de acesso que os usuários precisam para realizar seus trabalhos.

> [!IMPORTANT]
> As funções fornecidas pelo backup do Azure são limitadas a ações que podem ser executadas em portal do Azure ou por meio da API REST ou dos cmdlets do PowerShell ou do cofre dos serviços de recuperação. Ações executadas na interface do usuário do cliente do agente de backup do Azure ou na interface do usuário do System Center Data Protection Manager ou da interface do usuário do Servidor de Backup do Azure estão fora do controle dessas funções.

O backup do Azure fornece três funções internas para controlar as operações de gerenciamento de backup. Saiba mais sobre as [funções internas do Azure](../role-based-access-control/built-in-roles.md)

* [Colaborador de backup](../role-based-access-control/built-in-roles.md#backup-contributor) -essa função tem todas as permissões para criar e gerenciar o backup, exceto excluir o cofre dos serviços de recuperação e conceder acesso a outras pessoas. Imagine essa função como administrador do gerenciamento de backups, que pode executar todas as operações de gerenciamento de backups.
* [Operador de Backup](../role-based-access-control/built-in-roles.md#backup-operator): essa função tem permissões para fazer tudo que um colaborador faz, exceto remover backups e gerenciar políticas de backup. Essa função é equivalente à de colaborador, com exceção de que não é possível executar operações destrutivas, como interromper backups com exclusão de dados ou remover registro de recursos locais.
* [Leitor de Backup](../role-based-access-control/built-in-roles.md#backup-reader): essa função tem permissões para exibir todas as operações de gerenciamento de backups. Imagine essa função como uma pessoa de monitoramento.

Se você estiver procurando definir suas próprias funções para obter ainda mais controle, consulte como [criar funções personalizadas](../role-based-access-control/custom-roles.md) no RBAC do Azure.

## <a name="mapping-backup-built-in-roles-to-backup-management-actions"></a>Mapeando funções internas de backup para ações de gerenciamento de backups

A tabela a seguir captura as ações de gerenciamento de backup e a função mínima do Azure correspondente necessária para executar essa operação.

| Operação de gerenciamento | Função mínima do Azure necessária | Escopo exigido |
| --- | --- | --- |
| Criar cofre de Serviços de Recuperação | Colaborador de Backup | Grupo de recursos contendo o cofre |
| Habilitar backup de VMs do Azure | Operador de Backup | Grupo de recursos contendo o cofre |
| | Colaborador de Máquina Virtual | Recurso de VM |
| Backup sob demanda de VM | Operador de Backup | Cofre dos Serviços de Recuperação |
| Restaurar VM | Operador de Backup | Cofre dos Serviços de Recuperação |
| | Colaborador | Grupo de recursos no qual a VM será implantada |
| | Colaborador de Máquina Virtual | VM de origem que foi submetida a backup |
| Restaurar backup de VM de discos não gerenciados | Operador de Backup | Cofre dos Serviços de Recuperação |
| | Colaborador de Máquina Virtual | VM de origem que foi submetida a backup |
| | Colaborador da Conta de Armazenamento | Recurso de conta de armazenamento no qual os discos serão restaurados |
| Restaurar discos gerenciados do backup da VM | Operador de Backup | Cofre dos Serviços de Recuperação |
| | Colaborador de Máquina Virtual | VM de origem que foi submetida a backup |
| | Colaborador da Conta de Armazenamento | Conta de armazenamento temporária selecionada como parte da restauração para manter dados do cofre antes de convertê-los em discos gerenciados |
| | Colaborador | Grupo de recursos para o qual o disco gerenciado, ou discos, será restaurado |
| Restaurar arquivos individuais do backup da VM | Operador de Backup | Cofre dos Serviços de Recuperação |
| | Colaborador de Máquina Virtual | VM de origem que foi submetida a backup |
| Criar política de backup para backup da VM do Azure | Colaborador de Backup | Cofre dos Serviços de Recuperação |
| Modificar a política de backup da VM do Azure | Colaborador de Backup | Cofre dos Serviços de Recuperação |
| Excluir a política de backup da VM do Azure | Colaborador de Backup | Cofre dos Serviços de Recuperação |
| Interromper o backup (com retenção de dados ou exclusão de dados) no backup da VM | Colaborador de Backup | Cofre dos Serviços de Recuperação |
| Registrar-se no Windows Server/cliente/SCDPM local ou no Servidor de Backup do Azure | Operador de Backup | Cofre dos Serviços de Recuperação |
| Excluir o Windows Server/cliente/SCDPM local registrado ou o Servidor de Backup do Azure | Colaborador de Backup | Cofre dos Serviços de Recuperação |

> [!IMPORTANT]
> Se você especificar o Colaborador de VM em um escopo de recurso da VM e clicar em Backup como parte das configurações da VM, ele abrirá a tela 'Habilitar Backup', mesmo que o backup da VM já tenha sido realizado, pois a chamada para verificar o status do backup funciona apenas no nível de assinatura. Para evitar isso, vá para o cofre e abra a exibição de item de backup da VM ou especifique a função de Colaborador de VM em um nível de assinatura.

## <a name="minimum-role-requirements-for-the-azure-file-share-backup"></a>Requisitos mínimos de função para o backup do compartilhamento de arquivos do Azure

A tabela a seguir captura as ações de gerenciamento de backup e a função correspondente necessária para executar a operação de compartilhamento de arquivos do Azure.

| Operação de gerenciamento | Função necessária | Recursos |
| --- | --- | --- |
| Habilitar o backup de compartilhamentos de arquivos do Azure | Colaborador de Backup |Cofre dos Serviços de Recuperação |
| |Conta de armazenamento | Recurso de conta de armazenamento do colaborador |
| Backup sob demanda de VM | Operador de Backup | Cofre dos Serviços de Recuperação |
| Restaurar compartilhamento de arquivos | Operador de Backup | Cofre dos Serviços de Recuperação |
| | Colaborador da Conta de Armazenamento | Recursos da conta de armazenamento em que os compartilhamentos de arquivos de origem e de destino são presentes |
| Restaurar arquivos individuais | Operador de Backup | Cofre dos Serviços de Recuperação |
| |Colaborador da Conta de Armazenamento|Recursos da conta de armazenamento em que os compartilhamentos de arquivos de origem e de destino são presentes |
| Parar a proteção |Colaborador de Backup | Cofre dos Serviços de Recuperação |
| Cancelar o registro da conta de armazenamento do cofre |Colaborador de Backup | Cofre dos Serviços de Recuperação |
| |Colaborador da Conta de Armazenamento | Recurso da conta de armazenamento|

## <a name="next-steps"></a>Próximas etapas

* [Controle de acesso baseado em função do Azure (RBAC do Azure)](../role-based-access-control/role-assignments-portal.md): introdução ao rbac no portal do Azure.
* Saiba como gerenciar o acesso com:
  * [PowerShell](../role-based-access-control/role-assignments-powershell.md)
  * [CLI do Azure](../role-based-access-control/role-assignments-cli.md)
  * [REST API](../role-based-access-control/role-assignments-rest.md)
* [Solução de problemas de Controle de Acesso Baseado em Função](../role-based-access-control/troubleshooting.md): obtenha sugestões para corrigir problemas comuns.
