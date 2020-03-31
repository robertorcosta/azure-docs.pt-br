---
title: Gerenciar acesso SSH para contas de domínio no Azure HDInsight
description: Etapas para gerenciar o acesso SSH para contas Ad do Azure no HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 02/14/2020
ms.openlocfilehash: 5529989384df75b592afa8f5e4960eb9817fb2d7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "77472512"
---
# <a name="manage-ssh-access-for-domain-accounts-in-azure-hdinsight"></a>Gerenciar acesso SSH para contas de domínio no Azure HDInsight

Em clusters seguros, por padrão, todos os usuários de domínio no [Azure AD DS](../../active-directory-domain-services/overview.md) são autorizados a [SSH](../hdinsight-hadoop-linux-use-ssh-unix.md) nos nós de cabeça e borda. Esses usuários não fazem parte do grupo sudoers e não têm acesso raiz. O usuário SSH criado durante a criação do cluster terá acesso raiz.

## <a name="manage-access"></a>Gerenciar acesso

Para modificar o acesso ao SSH `/etc/ssh/sshd_config` a usuários ou grupos específicos, atualize em cada um dos nados.

1. Use [o comando ssh](../hdinsight-hadoop-linux-use-ssh-unix.md) para se conectar ao seu cluster. Edite o comando abaixo substituindo CLUSTERNAME pelo nome do seu cluster e, em seguida, digite o comando:

    ```cmd
    ssh sshuser@CLUSTERNAME-ssh.azurehdinsight.net
    ```

1. Abra `ssh_confi`o arquivo g.

    ```bash
    sudo nano /etc/ssh/sshd_config
    ```

1. Modifique `sshd_config` o arquivo conforme desejado. Se você restringir os usuários a determinados grupos, as contas locais não podem ssh nesse nó. O seguinte é apenas um exemplo de sintaxe:

    ```bash
    AllowUsers useralias1 useralias2

    AllowGroups groupname1 groupname2
    ```

    Em seguida, salve as alterações: **Ctrl + X**, **Y**, **Enter**.

1. Reiniciar sshd.

    ```bash
    sudo systemctl restart sshd
    ```

1. Repita acima passos para cada nó.

## <a name="ssh-authentication-log"></a>Registro de autenticação SSH

O registro de autenticação `/var/log/auth.log`SSH é gravado em . Se você ver quaisquer falhas de login através do SSH para contas locais ou de domínio, você precisará passar pelo log para depurar os erros. Muitas vezes, o problema pode estar relacionado a contas de usuários específicas e geralmente é uma boa prática tentar outras contas de usuário ou SSH usando o usuário SSH padrão (conta local) e, em seguida, tentar um kinit.

## <a name="ssh-debug-log"></a>Registro de depuração SSH

Para habilitar o registro verbose, você precisará reiniciar `sshd` com a `-d` opção. Como `/usr/sbin/sshd -d` você também `sshd` pode executar em uma porta personalizada (como 2222) para que você não tenha que parar o daemon SSH principal. Você também `-v` pode usar a opção com o cliente SSH para obter mais logs (visão lateral do cliente das falhas).

## <a name="next-steps"></a>Próximas etapas

* [Gerenciar clusters HDInsight com Enterprise Security Package](./apache-domain-joined-manage.md)
* [Conectar ao HDInsight (Apache Hadoop) usando SSH](../hdinsight-hadoop-linux-use-ssh-unix.md).
