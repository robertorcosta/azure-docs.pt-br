---
title: Criptografia de dados para o Azure Database para MySQL usando o portal do Azure
description: Saiba como configurar e gerenciar a criptografia de dados para o banco de dado do Azure para MySQL usando o portal do Azure.
author: kummanish
ms.author: manishku
ms.service: mysql
ms.topic: conceptual
ms.date: 01/13/2020
ms.openlocfilehash: ba1ad7c533ea1dc9e88c8337373e607db653ac24
ms.sourcegitcommit: 57669c5ae1abdb6bac3b1e816ea822e3dbf5b3e1
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/06/2020
ms.locfileid: "77048541"
---
# <a name="data-encryption-for-azure-database-for-mysql-by-using-the-azure-portal"></a>Criptografia de dados para o Azure Database para MySQL usando o portal do Azure

Saiba como usar o portal do Azure para configurar e gerenciar a criptografia de dados para o banco de dado do Azure para MySQL.

## <a name="prerequisites-for-azure-cli"></a>Pré-requisitos para CLI do Azure

* É necessário ter uma assinatura do Azure e ser um administrador nessa assinatura.
* No Azure Key Vault, crie um cofre de chaves e uma chave para usar para uma chave gerenciada pelo cliente.
* O cofre de chaves deve ter as seguintes propriedades para usar como uma chave gerenciada pelo cliente:
  * [Exclusão reversível](../key-vault/key-vault-ovw-soft-delete.md)

    ```azurecli-interactive
    az resource update --id $(az keyvault show --name \ <key_vault_name> -test -o tsv | awk '{print $1}') --set \ properties.enableSoftDelete=true
    ```

  * [Limpar protegido](../key-vault/key-vault-ovw-soft-delete.md#purge-protection)

    ```azurecli-interactive
    az keyvault update --name <key_vault_name> --resource-group <resource_group_name>  --enable-purge-protection true
    ```

* A chave deve ter os seguintes atributos para usar como uma chave gerenciada pelo cliente:
  * Sem data de validade
  * Não desabilitado
  * Capaz de executar operações Get, wrap Key, não encapsular chave

## <a name="set-the-right-permissions-for-key-operations"></a>Definir as permissões corretas para operações de chave

1. Em Key Vault, selecione **políticas de acesso** > **Adicionar política de acesso**.

   ![Captura de tela de Key Vault, com políticas de acesso e adicionar política de acesso realçado](media/concepts-data-access-and-security-data-encryption/show-access-policy-overview.png)

2. Selecione **permissões de chave**e selecione **obter**, **encapsular**, **desencapsular**e a **entidade de segurança**, que é o nome do servidor MySQL. Se a entidade de segurança do servidor não puder ser encontrada na lista de entidades de segurança existentes, você precisará registrá-la. Você será solicitado a registrar a entidade de segurança do servidor ao tentar configurar a criptografia de dados pela primeira vez e ela falhará.

   ![Visão geral da política de acesso](media/concepts-data-access-and-security-data-encryption/access-policy-wrap-unwrap.png)

3. Selecione **Salvar**.

## <a name="set-data-encryption-for-azure-database-for-mysql"></a>Definir a criptografia de dados para o Azure Database para MySQL

1. No banco de dados do Azure para MySQL, selecione **criptografia de dado** para configurar a chave gerenciada pelo cliente.

   ![Captura de tela do banco de dados do Azure para MySQL, com Data Encryption realçado](media/concepts-data-access-and-security-data-encryption/data-encryption-overview.png)

2. Você pode selecionar um cofre de chaves e um par de chaves, ou inserir um identificador de chave.

   ![Captura de tela do banco de dados do Azure para MySQL, com opções de criptografia de dados destacadas](media/concepts-data-access-and-security-data-encryption/setting-data-encryption.png)

3. Selecione **Salvar**.

4. Para garantir que todos os arquivos (incluindo arquivos temporários) sejam totalmente criptografados, reinicie o servidor.

## <a name="restore-or-create-a-replica-of-the-server"></a>Restaurar ou criar uma réplica do servidor

Depois que o banco de dados do Azure para MySQL é criptografado com a chave gerenciada de um cliente armazenada no Key Vault, qualquer cópia recém-criada do servidor também é criptografada. Você pode fazer essa nova cópia por meio de uma operação local ou de restauração geográfica, ou por meio de uma operação de réplica (local/entre regiões). Portanto, para um servidor MySQL criptografado, você pode usar as etapas a seguir para criar um servidor restaurado criptografado.

1. No servidor, selecione **visão geral** > **restaurar**.

   ![Captura de tela do banco de dados do Azure para MySQL, com visão geral e restauração realçadas](media/concepts-data-access-and-security-data-encryption/show-restore.png)

   Ou para um servidor habilitado para replicação, no cabeçalho **configurações** , selecione **replicação**.

   ![Captura de tela do banco de dados do Azure para MySQL, com replicação realçada](media/concepts-data-access-and-security-data-encryption/mysql-replica.png)

2. Depois que a operação de restauração for concluída, o novo servidor criado será criptografado com a chave do servidor primário. No entanto, os recursos e as opções no servidor estão desabilitados e o servidor está inacessível. Isso impede qualquer manipulação de dados, porque a identidade do novo servidor ainda não recebeu permissão para acessar o cofre de chaves.

   ![Captura de tela do banco de dados do Azure para MySQL, com status inacessível realçado](media/concepts-data-access-and-security-data-encryption/show-restore-data-encryption.png)

3. Para tornar o servidor acessível, revalide a chave no servidor restaurado. Selecione **criptografia de dados** > **chave de revalidação**.

   > [!NOTE]
   > A primeira tentativa de revalidação falhará, pois a entidade de serviço do novo servidor precisa receber acesso ao cofre de chaves. Para gerar a entidade de serviço, selecione **chave de revalidação**, que mostrará um erro, mas gerará a entidade de serviço. Depois disso, consulte [estas etapas](#set-the-right-permissions-for-key-operations) anteriormente neste artigo.

   ![Captura de tela do banco de dados do Azure para MySQL, com a etapa de revalidação realçada](media/concepts-data-access-and-security-data-encryption/show-revalidate-data-encryption.png)

   Você precisará conceder ao cofre de chaves acesso ao novo servidor.

4. Depois de registrar a entidade de serviço, revalide a chave novamente e o servidor retoma sua funcionalidade normal.

   ![Captura de tela do banco de dados do Azure para MySQL, mostrando a funcionalidade restaurada](media/concepts-data-access-and-security-data-encryption/restore-successful.png)

## <a name="next-steps"></a>{1&gt;{2&gt;Próximas etapas&lt;2}&lt;1}

 Para saber mais sobre a criptografia de dados, consulte [banco de dados do Azure para MySQL Data Encryption com chave gerenciada pelo cliente](concepts-data-encryption-mysql.md).
