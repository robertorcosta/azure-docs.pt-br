---
title: Use o portal Azure para configurar chaves gerenciadas pelo cliente para o serviço de importação/exportação
description: Saiba como usar o portal Azure para configurar chaves gerenciadas pelo cliente com o Azure Key Vault para o serviço de importação/exportação do Azure. As chaves gerenciadas pelo cliente permitem criar, girar, desativar e revogar controles de acesso.
services: storage
author: alkohli
ms.service: storage
ms.topic: how-to
ms.date: 03/12/2020
ms.author: alkohli
ms.subservice: common
ms.openlocfilehash: ca1327a547e8550e47ff37e4ba100fcbd2b7a79f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80282453"
---
# <a name="use-customer-managed-keys-in-azure-key-vault-for-importexport-service"></a>Use chaves gerenciadas pelo cliente no Azure Key Vault para o serviço de importação/exportação

O Azure Import/Export protege as chaves do BitLocker usadas para bloquear os drives por meio de uma chave de criptografia. Por padrão, as chaves do BitLocker são criptografadas com chaves gerenciadas pela Microsoft. Para obter controle adicional sobre as chaves de criptografia, você também pode fornecer chaves gerenciadas pelo cliente.

As chaves gerenciadas pelo cliente devem ser criadas e armazenadas em um Cofre de Chaves Do Azure. Para obter mais informações sobre o Azure Key Vault, consulte [O que é o Azure Key Vault?](../../key-vault/key-vault-overview.md)

Este artigo mostra como usar chaves gerenciadas pelo cliente com o serviço Importação/Exportação no [portal Azure](https://portal.azure.com/).

## <a name="prerequisites"></a>Pré-requisitos

Antes de começar, verifique se:

1. Você criou um trabalho de importação ou exportação de acordo com as instruções em:

    - [Crie um trabalho de importação para blobs](storage-import-export-data-to-blobs.md).
    - [Crie um trabalho de importação para arquivos](storage-import-export-data-to-files.md).
    - [Crie um trabalho de exportação para blobs](storage-import-export-data-from-blobs.md)

2. Você tem um Azure Key Vault existente com uma chave que você pode usar para proteger sua chave BitLocker. Para aprender como criar um cofre de chaves usando o portal Azure, consulte [Quickstart: Configure e recupere um segredo do Azure Key Vault usando o portal Azure](../../key-vault/quick-create-portal.md).

    - **O soft delete** e **o Não expurgo** são definidos no cofre de chaves existente. Essas propriedades não são habilitadas por padrão. Para habilitar essas propriedades, consulte as seções intituladas **Habilitando a exclusão suave** e habilitando a **proteção contra expurgo** em um dos seguintes artigos:

        - [Como usar soft-delete com PowerShell](../../key-vault/key-vault-soft-delete-powershell.md).
        - [Como usar soft-delete com CLI](../../key-vault/key-vault-soft-delete-cli.md).
    - O cofre-chave existente deve ter uma chave RSA de tamanho 2048 ou mais. Para obter mais informações sobre as chaves, consulte **as chaves do Key Vault** em [chaves, segredos e certificados sobre o Azure Key Vault](../../key-vault/about-keys-secrets-and-certificates.md#key-vault-keys).
    - O cofre-chave deve estar na mesma região que a conta de armazenamento de seus dados.  
    - Se você não tiver um Azure Key Vault existente, você também pode criá-lo inline conforme descrito na seção a seguir.

## <a name="enable-keys"></a>Habilitar chaves

A configuração da chave gerenciada pelo cliente para o serviço importação/exportação é opcional. Por padrão, o serviço Importação/Exportação usa uma chave gerenciada pela Microsoft para proteger sua chave BitLocker. Para habilitar as chaves gerenciadas pelo cliente no portal Azure, siga estas etapas:

1. Vá para a lâmina **visão geral** para o seu trabalho de importação.
2. No painel direito, selecione **Escolha como suas teclas do BitLocker são criptografadas**.

    ![Escolha a opção de criptografia](./media/storage-import-export-encryption-key-portal/encryption-key-1.png)

3. Na **lâmina Criptografia,** você pode visualizar e copiar a tecla BitLocker do dispositivo. Em **Tipo de Criptografia,** você pode escolher como deseja proteger sua chave BitLocker. Por padrão, uma chave gerenciada pela Microsoft é usada.

    ![Exibir a tecla BitLocker](./media/storage-import-export-encryption-key-portal/encryption-key-2.png)

4. Você tem a opção de especificar uma chave gerenciada pelo cliente. Depois de selecionar a chave gerenciada pelo cliente, **selecione o cofre de chaves e uma chave**.

    ![Selecione a chave gerenciada pelo cliente](./media/storage-import-export-encryption-key-portal/encryption-key-3.png)

5. Na **tecla Select do Azure Key Vault** blade, a assinatura é preenchida automaticamente. Para **o cofre chave,** você pode selecionar um cofre de chaves existente na lista de isto.

    ![Selecione ou crie o Azure Key Vault](./media/storage-import-export-encryption-key-portal/encryption-key-4.png)

6. Você também pode selecionar **Criar novo** para criar um novo cofre de chaves. Na **lâmina do cofre de chaves Criar,** digite o grupo de recursos e o nome do cofre de chaves. Aceite todos os outros padrões. Selecione **Revisão + Criar**.

    ![Criar novo Cofre chave Azure](./media/storage-import-export-encryption-key-portal/encryption-key-5.png)

7. Revise as informações associadas ao cofre de chaves e selecione **Criar**. Espere alguns minutos para que a criação do cofre principal seja concluída.

    ![Criar Azure Key Vault](./media/storage-import-export-encryption-key-portal/encryption-key-6.png)

8. Na **tecla Select do Azure Key Vault,** você pode selecionar uma chave no cofre de chaves existente.

9. Se você criou um novo cofre de chaves, selecione **Criar novo** para criar uma chave. O tamanho da chave RSA pode ser 2048 ou maior.

    ![Crie nova chave no Azure Key Vault](./media/storage-import-export-encryption-key-portal/encryption-key-7.png)

    Se a proteção de exclusão e purga suave não estiver ativada quando você criar o cofre de chaves, o cofre de chaves será atualizado para ter proteção de exclusão e purga suave ativada.

10. Forneça o nome da sua chave, aceite os outros padrões e selecione **Criar**.

    ![Criar nova chave](./media/storage-import-export-encryption-key-portal/encryption-key-8.png)

11. Selecione a **versão** e, em seguida, escolha **Selecionar**. Você é notificado de que uma chave é criada em seu cofre de chaves.

    ![Nova chave criada no cofre de chaves](./media/storage-import-export-encryption-key-portal/encryption-key-9.png)

Na **lâmina Criptografia,** você pode ver o cofre de chaves e a chave selecionada para a chave gerenciada pelo cliente.

## <a name="disable-keys"></a>Desativar chaves

Você só pode desativar as chaves gerenciadas pela Microsoft e passar para chaves gerenciadas pelo cliente em qualquer fase do trabalho de importação/exportação. No entanto, não é possível desativar a chave gerenciada pelo cliente depois de criá-la.

## <a name="troubleshoot-customer-managed-key-errors"></a>Solucionar problemas gerenciados pelo cliente

Se você receber quaisquer erros relacionados à chave gerenciada pelo cliente, use a tabela a seguir para solucionar problemas:

| Código do erro     |Detalhes     | Recuperáveis?    |
|----------------|------------|-----------------|
| CmkErrorAccessRevogado | Aplicou uma chave gerenciada pelo cliente, mas o acesso à chave é atualmente revogado. Para obter mais informações, consulte como [ativar o acesso à chave](https://docs.microsoft.com/rest/api/keyvault/vaults/updateaccesspolicy).                                                      | Sim, veja se: <ol><li>O cofre-chave ainda tem o MSI na política de acesso.</li><li>A política de acesso fornece permissões para obter, embrulhar, desembrulhar.</li><li>Se o cofre de chaves estiver em um vNet atrás do firewall, verifique se **o Allow Microsoft Trusted Services** está ativado.</li></ol>                                                                                            |
| CmkErrorDisabled      | Aplicou uma chave gerenciada pelo cliente, mas a chave está desativada. Para obter mais informações, consulte como [ativar a chave](https://docs.microsoft.com/rest/api/keyvault/vaults/createorupdate).                                                                             | Sim, ativando a versão-chave     |
| CmkErrorNãoencontrado      | Aplicou uma chave gerenciada pelo cliente, mas não consigo encontrar a chave. <br>Se a chave for excluída e eliminada após o período de retenção, você não poderá recuperar a chave. Se você fez backup da chave, você pode restaurar a chave para resolver esse problema. | Não, a chave foi excluída e também foi eliminada após o período de retenção. <br>Sim, só se o cliente tiver a chave apoiada e restaurá-la.  |
| CmkErrorVaultNãoencontrado | Aplicou uma chave gerenciada pelo cliente, mas não consegue encontrar o cofre de chaves associado à chave.<br>Se você excluiu o cofre da chave, não poderá recuperar a chave gerenciada pelo cliente.  Se você migrou o cofre de chaves para um inquilino diferente, consulte [Alterar um ID de inquilino do cofre chave após um movimento de assinatura](https://docs.microsoft.com/azure/key-vault/key-vault-subscription-move-fix). |   Não, se o cliente apagou o cofre da chave.<br> Sim, se o cofre-chave sofreu uma migração de inquilinos, então faça um de: <ol><li>mover de volta o cofre chave para o antigo inquilino.</li><li>definir Identidade = Nenhum e, em seguida, voltar para Identidade = SistemaAtribuído, isso exclui e recria a identidade</li></ol><br>Nota: O caso de migração do inquilino é baseado em compreensão limitada, necessidade de testar e confirmar o comportamento real, podendo ser revisto posteriormente. |

## <a name="next-steps"></a>Próximas etapas

- [O que é o Azure Key Vault?](https://docs.microsoft.com/azure/key-vault/key-vault-overview)
