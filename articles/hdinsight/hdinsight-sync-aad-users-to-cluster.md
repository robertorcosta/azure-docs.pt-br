---
title: Sincronizar Azure Active Directory usuários com o cluster HDInsight
description: Sincronizar usuários autenticados do Azure Active Directory para um cluster HDInsight.
ms.service: hdinsight
author: ashishthaps
ms.author: ashishth
ms.reviewer: jasonh
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 09/24/2018
ms.openlocfilehash: b6252e99e69f849e2e988819f38dcccc5a7a73e0
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/04/2019
ms.locfileid: "73498149"
---
# <a name="synchronize-azure-active-directory-users-to-an-hdinsight-cluster"></a>Sincronizar usuários do Azure Active Directory para um cluster HDInsight

[Os clusters HDInsight com ESP (Enterprise Security Package - Pacote de Segurança Corporativa)](hdinsight-domain-joined-introduction.md) podem usar autenticação forte com usuários do Azure AD (Azure Active Directory), bem como usar *políticas de controle de acesso baseado em função* (RBAC). Como você adicionou usuários e grupos no Azure AD, você pode sincronizar os usuários que precisam de acesso ao seu cluster.

## <a name="prerequisites"></a>Pré-requisitos

Se você ainda não fez isso, [crie um cluster do HDInsight com o Enterprise Security Package](hdinsight-domain-joined-configure.md).

## <a name="add-new-azure-ad-users"></a>Adicionar novos usuários do Azure AD

Para exibir seus hosts, abra a interface do usuário do Ambari Web. Cada nó será atualizado com novas configurações de atualização automática.

1. No [Portal do Azure](https://portal.azure.com), navegue até o diretório do Azure AD associado ao seu cluster do ESP.

2. Selecione **Todos os usuários** no menu à esquerda, selecione **Novo usuário**.

    ![portal do Azure todos os usuários e grupos](./media/hdinsight-sync-aad-users-to-cluster/users-and-groups-new.png)

3. Preencha o formulário de novo usuário. Selecione grupos que você criou para a atribuição de permissões com base em cluster. Neste exemplo, crie um grupo chamado "HiveUsers", aos quais você pode atribuir novos usuários. As [instruções de exemplo](hdinsight-domain-joined-configure.md) para criar um cluster ESP incluem a inclusão de dois grupos, `HiveUsers` e `AAD DC Administrators`.

    ![Painel do usuário do portal do Azure selecionar grupos](./media/hdinsight-sync-aad-users-to-cluster/hdinsight-new-user-form.png)

4. Selecione **Criar**.

## <a name="use-the-apache-ambari-rest-api-to-synchronize-users"></a>Use a API REST do Apache Ambari para sincronizar os usuários

Os grupos de usuários especificados durante o processo de criação de cluster estão sincronizados no momento. A sincronização de usuário ocorre automaticamente uma vez a cada hora. Para sincronizar os usuários imediatamente, ou para sincronizar um grupo diferente de grupos especificados durante a criação do cluster, use a API REST do Ambari.

O método a seguir usa o POST com a API REST do Ambari. Para obter mais informações, consulte [Gerenciar clusters do HDInsight usando a API REST do Apache Ambari](hdinsight-hadoop-manage-ambari-rest-api.md).

1. [Conectar ao seu cluster com SSH](hdinsight-hadoop-linux-use-ssh-unix.md). No painel de visão geral do seu cluster no portal do Azure, selecione o botão **Secure Shell (SSH)** .

    ![Ícone do Secure Shell do HDInsight (SSH)](./media/hdinsight-sync-aad-users-to-cluster/hdinsight-secure-shell.png)

2. Copie o comando `ssh` exibido e cole-o em seu cliente SSH. Insira a senha de usuário do SSH quando solicitado.

3. Depois de autenticar, insira o seguinte comando:

    ```bash
    curl -u admin:<YOUR PASSWORD> -sS -H "X-Requested-By: ambari" \
    -X POST -d '{"Event": {"specs": [{"principal_type": "groups", "sync_type": "existing"}]}}' \
    "https://<YOUR CLUSTER NAME>.azurehdinsight.net/api/v1/ldap_sync_events"
    ```
    
    A resposta deve se parecer com esta:

    ```json
    {
      "resources" : [
        {
          "href" : "http://hn0-hadoop.<YOUR DOMAIN>.com:8080/api/v1/ldap_sync_events/1",
          "Event" : {
            "id" : 1
          }
        }
      ]
    }
    ```

4. Para ver o status da sincronização, execute um novo comando `curl`:

    ```bash
    curl -u admin:<YOUR PASSWORD> https://<YOUR CLUSTER NAME>.azurehdinsight.net/api/v1/ldap_sync_events/1
    ```
    
    A resposta deve se parecer com esta:
    
    ```json
    {
      "href" : "http://hn0-hadoop.YOURDOMAIN.com:8080/api/v1/ldap_sync_events/1",
      "Event" : {
        "id" : 1,
        "specs" : [
          {
            "sync_type" : "existing",
            "principal_type" : "groups"
          }
        ],
        "status" : "COMPLETE",
        "status_detail" : "Completed LDAP sync.",
        "summary" : {
          "groups" : {
            "created" : 0,
            "removed" : 0,
            "updated" : 0
          },
          "memberships" : {
            "created" : 1,
            "removed" : 0
          },
          "users" : {
            "created" : 1,
            "removed" : 0,
            "skipped" : 0,
            "updated" : 0
          }
        },
        "sync_time" : {
          "end" : 1497994072182,
          "start" : 1497994071100
        }
      }
    }
    ```

5. Esse resultado mostra que o status é **CONCLUÍDO**, um novo usuário foi criado e o usuário foi atribuído a uma associação. Neste exemplo, o usuário é atribuído ao grupo LDAP sincronizado "HiveUsers", já que o usuário foi adicionado ao mesmo grupo no Azure AD.

> [!NOTE]  
> O método anterior sincroniza somente os grupos do Azure AD especificados na propriedade **Acessar grupo de usuários** das configurações de domínio durante a criação do cluster. Para saber mais, veja [Criar um cluster HDInsight](domain-joined/apache-domain-joined-configure.md).

## <a name="verify-the-newly-added-azure-ad-user"></a>Verifique se o usuário recém-adicionado do Azure AD

Abra a [Interface do Usuário do Apache Ambari](hdinsight-hadoop-manage-ambari.md) para verificar se o novo usuário do Azure AD foi adicionado. Acesse a interface do usuário do Ambari Web navegando até **`https://<YOUR CLUSTER NAME>.azurehdinsight.net`** . Insira o nome de usuário de administrador do cluster.

1. No painel do Ambari, selecione **Gerenciar Ambari** abaixo do menu **admin**.

    ![Painel do Apache Ambari gerenciar Ambari](./media/hdinsight-sync-aad-users-to-cluster/manage-apache-ambari.png)

2. Selecione **usuários** abaixo do grupo de menu **Gerenciamento de grupo de usuário +** no lado esquerdo da página.

    ![Menu usuários e grupos do HDInsight](./media/hdinsight-sync-aad-users-to-cluster/hdinsight-users-menu-item.png)

3. O novo usuário deve ser listado na tabela de usuários. O tipo é definido como `LDAP` em vez de `Local`.

    ![Visão geral da página de usuários do HDInsight AAD](./media/hdinsight-sync-aad-users-to-cluster/hdinsight-users-page.png)

## <a name="log-in-to-ambari-as-the-new-user"></a>Faça logon Ambari como o novo usuário

Quando o novo usuário (ou qualquer outro usuário de domínio) faz logon no Ambari, usa suas credenciais de nome e o domínio de usuário completo do Azure AD.  Ambari exibe um alias do usuário, que é o nome de exibição do usuário no Azure AD. O novo usuário de exemplo tem o nome de usuário `hiveuser3@contoso.com`. No Ambari, esse novo usuário é exibido como `hiveuser3`, mas o usuário faz logon em Ambari como `hiveuser3@contoso.com`.

## <a name="see-also"></a>Confira também

* [Configurar políticas do Apache Hive no HDInsight com ESP](hdinsight-domain-joined-run-hive.md)
* [Gerenciar clusters do HDInsight com ESP](hdinsight-domain-joined-manage.md)
* [Autorizar usuários para o Apache Ambari](hdinsight-authorize-users-to-ambari.md)
