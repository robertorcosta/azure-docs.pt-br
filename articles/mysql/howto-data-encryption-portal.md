---
title: Criptografia de dados - Portal Azure - Banco de dados Azure para MySQL
description: Saiba como configurar e gerenciar a criptografia de dados para o seu Banco de Dados Azure para MySQL usando o portal Azure.
author: kummanish
ms.author: manishku
ms.service: mysql
ms.topic: conceptual
ms.date: 01/13/2020
ms.openlocfilehash: acf3e6273f98d98d5da55cfb5b044677116c44dc
ms.sourcegitcommit: b0ff9c9d760a0426fd1226b909ab943e13ade330
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/01/2020
ms.locfileid: "80520801"
---
# <a name="data-encryption-for-azure-database-for-mysql-by-using-the-azure-portal"></a>Criptografia de dados para banco de dados Azure para MySQL usando o portal Azure

Aprenda a usar o portal Azure para configurar e gerenciar a criptografia de dados para o seu Banco de Dados Azure para MySQL.

## <a name="prerequisites-for-azure-cli"></a>Pré-requisitos para Azure CLI

* É necessário ter uma assinatura do Azure e ser um administrador nessa assinatura.
* No Azure Key Vault, crie um cofre de chaves e uma chave para usar para uma chave gerenciada pelo cliente.
* O cofre-chave deve ter as seguintes propriedades para usar como uma chave gerenciada pelo cliente:
  * [Exclusão suave](../key-vault/key-vault-ovw-soft-delete.md)

    ```azurecli-interactive
    az resource update --id $(az keyvault show --name \ <key_vault_name> -o tsv | awk '{print $1}') --set \ properties.enableSoftDelete=true
    ```

  * [Expurgo protegido](../key-vault/key-vault-ovw-soft-delete.md#purge-protection)

    ```azurecli-interactive
    az keyvault update --name <key_vault_name> --resource-group <resource_group_name>  --enable-purge-protection true
    ```

* A chave deve ter os seguintes atributos para usar como uma chave gerenciada pelo cliente:
  * Sem data de validade
  * Não desabilitado
  * Capaz de realizar operações get, codificar chave, decodificar chave

## <a name="set-the-right-permissions-for-key-operations"></a>Defina as permissões certas para operações-chave

1. No Key Vault, selecione **políticas** > de acesso**adicionar política de acesso**.

   ![Captura de tela do Key Vault, com políticas de acesso e política de acesso destacada](media/concepts-data-access-and-security-data-encryption/show-access-policy-overview.png)

2. Selecione **As permissões de tecla**e selecione **Get**, **Wrap**, **Desembrulhe**e o **Principal**, que é o nome do servidor MySQL. Se o principal do servidor não puder ser encontrado na lista de diretores existentes, você precisa registrá-lo. Você é solicitado a registrar o principal do servidor quando você tenta configurar a criptografia de dados pela primeira vez, e ela falha.

   ![Visão geral da política de acesso](media/concepts-data-access-and-security-data-encryption/access-policy-wrap-unwrap.png)

3. Clique em **Salvar**.

## <a name="set-data-encryption-for-azure-database-for-mysql"></a>Defina a criptografia de dados para o Banco de Dados Do Azure para MySQL

1. No Banco de Dados Do Azure para MySQL, selecione **criptografia de dados** para configurar a chave gerenciada pelo cliente.

   ![Captura de tela do Banco de Dados Do Azure para MySQL, com criptografia de dados destacada](media/concepts-data-access-and-security-data-encryption/data-encryption-overview.png)

2. Você pode selecionar um cofre de chaves e um par de chaves ou inserir um identificador de chave.

   ![Captura de tela do Banco de Dados Do Azure para MySQL, com opções de criptografia de dados destacadas](media/concepts-data-access-and-security-data-encryption/setting-data-encryption.png)

3. Clique em **Salvar**.

4. Para garantir que todos os arquivos (incluindo arquivos temporários) estejam totalmente criptografados, reinicie o servidor.

## <a name="using-data-encryption-for-restore-or-replica-servers"></a>Usando criptografia de dados para restaurar ou replicar servidores

Depois que o Banco de Dados Do Azure para MySQL é criptografado com a chave gerenciada do cliente armazenada no Key Vault, qualquer cópia recém-criada do servidor também é criptografada. Você pode fazer essa nova cópia através de uma operação local ou de georestauração, ou através de uma operação de réplica (local/região transversal). Assim, para um servidor MySQL criptografado, você pode usar as seguintes etapas para criar um servidor restaurado criptografado.

1. No servidor, selecione > **'''''Restauração** **de visão geral'.**

   ![Captura de tela do Banco de Dados Azure para MySQL, com visão geral e restauração destacadas](media/concepts-data-access-and-security-data-encryption/show-restore.png)

   Ou para um servidor habilitado para replicação, sob o título **Configurações,** selecione **Replicação**.

   ![Captura de tela do Banco de Dados Azure para MySQL, com replicação destacada](media/concepts-data-access-and-security-data-encryption/mysql-replica.png)

2. Depois que a operação de restauração estiver concluída, o novo servidor criado será criptografado com a chave do servidor principal. No entanto, os recursos e opções no servidor estão desativados, e o servidor está inacessível. Isso evita qualquer manipulação de dados, porque a identidade do novo servidor ainda não recebeu permissão para acessar o cofre de chaves.

   ![Captura de tela do Banco de Dados Azure para MySQL, com status inacessível destacado](media/concepts-data-access-and-security-data-encryption/show-restore-data-encryption.png)

3. Para tornar o servidor acessível, revalide a chave no servidor restaurado. Selecione **a tecla** > **Revalidar**a criptografia de dados .

   > [!NOTE]
   > A primeira tentativa de revalidação falhará, porque o diretor de serviço do novo servidor precisa ter acesso ao cofre de chaves. Para gerar o principal de serviço, selecione **Revalidar a tecla**, que mostrará um erro, mas gera o principal de serviço. A partir daí, consulte [estas etapas](#set-the-right-permissions-for-key-operations) anteriores neste artigo.

   ![Captura de tela do Banco de Dados Azure para MySQL, com etapa de revalidação destacada](media/concepts-data-access-and-security-data-encryption/show-revalidate-data-encryption.png)

   Você terá que dar ao cofre chave acesso ao novo servidor.

4. Depois de registrar o principal de serviço, revalide a chave novamente e o servidor retoma sua funcionalidade normal.

   ![Captura de tela do Banco de Dados Azure para MySQL, mostrando funcionalidade restaurada](media/concepts-data-access-and-security-data-encryption/restore-successful.png)

## <a name="next-steps"></a>Próximas etapas

 Para saber mais sobre criptografia de dados, consulte [O Banco de Dados Do Azure para criptografia de dados MySQL com a chave gerenciada pelo cliente](concepts-data-encryption-mysql.md).
