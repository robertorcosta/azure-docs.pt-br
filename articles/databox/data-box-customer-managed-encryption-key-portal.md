---
title: Use o portal do Azure para configurar chaves gerenciadas pelo cliente para Azure Data Box
description: Saiba como usar o portal do Azure para configurar chaves gerenciadas pelo cliente com Azure Key Vault para Azure Data Box. As chaves gerenciadas pelo cliente permitem criar, girar, desabilitar e revogar controles de acesso.
services: databox
author: alkohli
ms.service: databox
ms.topic: how-to
ms.date: 05/07/2020
ms.author: alkohli
ms.subservice: pod
ms.openlocfilehash: 6f543b3f5c2bb7d4949c431580771c4b0d965e4d
ms.sourcegitcommit: a8ee9717531050115916dfe427f84bd531a92341
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/12/2020
ms.locfileid: "83125270"
---
# <a name="use-customer-managed-keys-in-azure-key-vault-for-azure-data-box"></a>Usar chaves gerenciadas pelo cliente no Azure Key Vault para Azure Data Box

Azure Data Box protege a chave de desbloqueio do dispositivo (também conhecida como senha do dispositivo) que é usada para bloquear o dispositivo por meio de uma chave de criptografia. Por padrão, a chave de desbloqueio de dispositivo para um pedido de Data Box é criptografada com uma chave gerenciada da Microsoft. Para obter mais controle sobre a chave de desbloqueio de dispositivo, você também pode fornecer uma chave gerenciada pelo cliente. 

As chaves gerenciadas pelo cliente devem ser criadas e armazenadas em um Azure Key Vault. Para obter mais informações sobre Azure Key Vault, consulte [o que é Azure Key Vault?](../key-vault/general/overview.md).

Este artigo mostra como usar chaves gerenciadas pelo cliente com Azure Data Box no [portal do Azure](https://portal.azure.com/). Este artigo se aplica a dispositivos Azure Data Box e Azure Data Box Heavy dispositivos.

## <a name="prerequisites"></a>Pré-requisitos

Antes de começar, verifique se:

1. Você criou uma ordem de Azure Data Box de acordo com as instruções em [tutorial: order Azure data Box](data-box-deploy-ordered.md).

2. Você tem um Azure Key Vault existente com uma chave que pode ser usada para proteger a chave de desbloqueio do dispositivo. Para saber como criar um cofre de chaves usando o portal do Azure, consulte [início rápido: definir e recuperar um segredo de Azure Key Vault usando o portal do Azure](../key-vault/secrets/quick-create-portal.md).

    - A **exclusão reversível** e **não a limpeza** são definidas no cofre de chaves existente. Essas propriedades não são habilitadas por padrão. Para habilitar essas propriedades, consulte as seções intituladas **habilitando a exclusão reversível** e **habilitando a proteção de limpeza** em um dos seguintes artigos:

        - [Como usar a exclusão reversível com o PowerShell](../key-vault/general/soft-delete-powershell.md).
        - [Como usar a exclusão reversível com a CLI](../key-vault/general/soft-delete-cli.md).
    - O cofre de chaves existente deve ter uma chave RSA de tamanho de 2048 ou mais. Para obter mais informações sobre chaves, consulte [sobre chaves de Azure Key Vault](../key-vault/keys/about-keys.md).
    - O cofre de chaves deve estar na mesma região que as contas de armazenamento usadas para seus dados. Várias contas de armazenamento podem ser vinculadas com o recurso de Azure Data Box.
    - Se você não tiver um cofre de chaves existente, também poderá criá-lo em linha, conforme descrito na seção a seguir.

## <a name="enable-keys"></a>Habilitar chaves

Configurar a chave gerenciada pelo cliente para seu Azure Data Box é opcional. Por padrão, Data Box usa uma chave gerenciada da Microsoft para proteger sua chave do BitLocker. Para habilitar uma chave gerenciada pelo cliente no portal do Azure, siga estas etapas:

1. Vá para a folha de **visão geral** de sua ordem de data box.

    ![Folha de visão geral da ordem de Data Box](./media/data-box-customer-managed-encryption-key-portal/customer-managed-key-1.png)

2. Vá para **configurações > criptografia**. Em **tipo de criptografia**, você pode escolher como deseja proteger a chave de desbloqueio do dispositivo. Por padrão, uma chave gerenciada da Microsoft é usada para proteger a senha de desbloqueio do dispositivo. 

    ![Escolher opção de criptografia](./media/data-box-customer-managed-encryption-key-portal/customer-managed-key-2.png)

3. Selecione o tipo de criptografia como **chave gerenciada pelo cliente**. Depois de selecionar a chave gerenciada pelo cliente, **Selecione um cofre de chaves e uma chave**.

    ![Selecionar chave gerenciada pelo cliente](./media/data-box-customer-managed-encryption-key-portal/customer-managed-key-3.png)

5. Na folha **selecionar chave de Azure Key Vault** , a assinatura é preenchida automaticamente. Para o **Key Vault**, você pode selecionar um cofre de chaves existente na lista suspensa.

    ![Criar novo Azure Key Vault](./media/data-box-customer-managed-encryption-key-portal/customer-managed-key-31.png)

    Você também pode selecionar **criar novo** para criar um novo cofre de chaves. Na **folha criar cofre de chaves**, insira o grupo de recursos e o nome do cofre de chaves. Verifique se a **exclusão reversível** e a **proteção de limpeza** estão habilitadas. Aceite todos os outros padrões. Selecione **Examinar + criar**.

    ![Criar novo Azure Key Vault](./media/data-box-customer-managed-encryption-key-portal/customer-managed-key-4.png)

7. Examine as informações associadas ao cofre de chaves e selecione **criar**. Aguarde alguns minutos para que a criação do cofre de chaves seja concluída.

    ![Criar Azure Key Vault](./media/data-box-customer-managed-encryption-key-portal/customer-managed-key-5.png)

8. Na **Azure Key Vault selecionar chave de**, você pode selecionar uma chave no cofre de chaves existente.

    ![Criar nova chave no Azure Key Vault](./media/data-box-customer-managed-encryption-key-portal/customer-managed-key-6.png)

9. Se você quiser criar uma nova chave, selecione **criar nova** para criar uma chave. O tamanho da chave RSA pode ser 2048 ou maior.

    ![Criar nova chave no Azure Key Vault](./media/data-box-customer-managed-encryption-key-portal/customer-managed-key-61.png)

10. Forneça o nome da sua chave, aceite os outros padrões e selecione **criar**. 

    ![Criar nova chave](./media/data-box-customer-managed-encryption-key-portal/customer-managed-key-7.png)


11. Você será notificado de que uma chave é criada no cofre de chaves. Selecione a **versão** e escolha **selecionar**.

    ![Nova chave criada no cofre de chaves](./media/data-box-customer-managed-encryption-key-portal/customer-managed-key-8.png)

12. No painel **tipo de criptografia** , você pode ver o cofre de chaves e a chave selecionada para a chave gerenciada pelo cliente.

    ![Chave e Key Vault para chave gerenciada pelo cliente](./media/data-box-customer-managed-encryption-key-portal/customer-managed-key-9.png)

13. Salve a chave. 

    ![Salvar chave gerenciada pelo cliente](./media/data-box-customer-managed-encryption-key-portal/customer-managed-key-10.png)

    A URL da chave é exibida em **tipo de criptografia**.

    ![URL de chave gerenciada pelo cliente](./media/data-box-customer-managed-encryption-key-portal/customer-managed-key-11.png)

> [!IMPORTANT]
> Você pode desabilitar a chave gerenciada da Microsoft e mover para a chave gerenciada pelo cliente em qualquer estágio da ordem de Data Box. No entanto, depois de criar a chave gerenciada pelo cliente, você não pode alternar de volta para a chave gerenciada pela Microsoft.

## <a name="troubleshoot-errors"></a>Solucionar problemas de erros

Se você receber erros relacionados à chave gerenciada pelo cliente, use a tabela a seguir para solucionar problemas.

| Código de erro| Mensagem de erro| Detalhes|
|-------------|--------------|---------|
| SsemUserErrorEncryptionKeyDisabled| Não foi possível obter a chave de acesso porque a chave gerenciada pelo cliente está desabilitada.| Sim, habilitando a versão da chave.|
| SsemUserErrorEncryptionKeyExpired| Não foi possível buscar a chave de acesso, pois a chave gerenciada pelo cliente expirou.| Sim, habilitando a versão da chave.|
| SsemUserErrorKeyDetailsNotFound| Não foi possível buscar a chave de acesso porque a chave gerenciada pelo cliente não pôde ser encontrada.| Se você excluiu o cofre de chaves, não é possível recuperar a chave gerenciada pelo cliente.  Se você migrou o cofre de chaves para um locatário diferente, consulte [alterar uma ID de locatário do cofre de chaves após a movimentação de uma assinatura](https://docs.microsoft.com/azure/key-vault/key-vault-subscription-move-fix). Se você excluiu o cofre de chaves:<ol><li>Sim, se estiver na duração da proteção de limpeza, usando as etapas em [recuperar um cofre de chaves](https://docs.microsoft.com/azure/key-vault/general/soft-delete-powershell#recovering-a-key-vault).</li><li>Não, se estiver além da duração da proteção de limpeza.</li></ol><br>Caso contrário, se o cofre de chaves sofreu uma migração de locatário, sim, ele poderá ser recuperado usando uma das etapas a seguir: <ol><li>Reverter o cofre de chaves de volta para o locatário antigo.</li><li>Defina `Identity = None` e, em seguida, defina o valor de volta para `Identity = SystemAssigned` . Isso exclui e recria a identidade depois que a nova identidade é criada. Habilite `Get` `Wrap` `Unwrap` as permissões, e para a nova identidade na política de acesso do Key Vault.</li></ol> |
| SsemUserErrorKeyVaultBadRequestException| Não foi possível buscar a chave de acesso, pois a chave gerenciada pelo cliente foi revogada.| Sim, verifique se: <ol><li>O cofre de chaves ainda tem o MSI na política de acesso.</li><li>A política de acesso fornece permissões para obter, encapsular, desencapsular.</li><li>Se o Key Vault estiver em uma vNet por trás do firewall, verifique se **permitir que os serviços confiáveis da Microsoft** estejam habilitados.</li></ol>|
| SsemUserErrorKeyVaultDetailsNotFound| Não foi possível buscar a chave de acesso porque o cofre de chaves associado à chave gerenciada pelo cliente não foi encontrado. | Se você excluiu o cofre de chaves, não é possível recuperar a chave gerenciada pelo cliente.  Se você migrou o cofre de chaves para um locatário diferente, consulte [alterar uma ID de locatário do cofre de chaves após a movimentação de uma assinatura](https://docs.microsoft.com/azure/key-vault/key-vault-subscription-move-fix). Se você excluiu o cofre de chaves:<ol><li>Sim, se estiver na duração da proteção de limpeza, usando as etapas em [recuperar um cofre de chaves](https://docs.microsoft.com/azure/key-vault/general/soft-delete-powershell#recovering-a-key-vault).</li><li>Não, se estiver além da duração da proteção de limpeza.</li></ol><br>Caso contrário, se o cofre de chaves sofreu uma migração de locatário, sim, ele poderá ser recuperado usando uma das etapas a seguir: <ol><li>Reverter o cofre de chaves de volta para o locatário antigo.</li><li>Defina `Identity = None` e, em seguida, defina o valor de volta para `Identity = SystemAssigned` . Isso exclui e recria a identidade depois que a nova identidade é criada. Habilite `Get` `Wrap` `Unwrap` as permissões, e para a nova identidade na política de acesso do Key Vault.</li></ol> |
| SsemUserErrorSystemAssignedIdentityAbsent  | Não foi possível buscar a chave de acesso porque a chave gerenciada pelo cliente não pôde ser encontrada.| Sim, verifique se: <ol><li>O cofre de chaves ainda tem o MSI na política de acesso.</li><li>A identidade é do tipo atribuído pelo sistema.</li><li>Habilite as permissões obter, encapsular e desencapsular para a identidade na política de acesso do cofre de chaves.</li></ol>|
| Erro genérico  | Não foi possível buscar a chave de acesso.| Esse é um erro genérico. Entre em contato com Suporte da Microsoft para solucionar o erro e determinar as próximas etapas.|


## <a name="next-steps"></a>Próximas etapas

- [O que é Azure Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-overview)?