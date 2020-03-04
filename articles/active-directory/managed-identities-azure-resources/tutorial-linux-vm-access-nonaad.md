---
title: Tutorial`:` Usar uma identidade gerenciada para acessar o Azure Key Vault – Linux – Azure AD
description: Um tutorial que o conduz pelo processo de usar uma identidade gerenciada atribuída pelo sistema da VM do Linux para acessar o Azure Resource Manager.
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
editor: daveba
ms.service: active-directory
ms.subservice: msi
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 11/20/2017
ms.author: markvi
ms.collection: M365-identity-device-management
ms.openlocfilehash: cdccabf701d4603b8c78f7e23ec1890171603273
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/20/2019
ms.locfileid: "74232179"
---
# <a name="tutorial-use-a-linux-vm-system-assigned-managed-identity-to-access-azure-key-vault"></a>Tutorial: Usar uma identidade gerenciada atribuída pelo sistema da VM do Linux para acessar o Azure Key Vault 

[!INCLUDE [preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

Este tutorial mostra como usar uma identidade gerenciada atribuída pelo sistema para uma VM (máquina virtual) do Linux para acessar o Azure Key Vault. Atuando como bootstrap, o Key Vault possibilita que o aplicativo cliente use o segredo para acessar recursos não protegidos pelo Azure AD (Active Directory). As identidades gerenciadas para recursos do Azure são gerenciadas automaticamente pelo Azure e permitem a você autenticar os serviços que dão suporte à autenticação do Azure AD sem necessidade de inserir as credenciais em seu código. 

Você aprenderá como:

> [!div class="checklist"]
> * Conceda o acesso da máquina virtual a um segredo armazenado em um Key Vault 
> * Obtenha um token de acesso usando a identidade da VM e use-o para recuperar o segredo do Key Vault 
 
## <a name="prerequisites"></a>Prerequisites

[!INCLUDE [msi-tut-prereqs](../../../includes/active-directory-msi-tut-prereqs.md)]

## <a name="grant-your-vm-access-to-a-secret-stored-in-a-key-vault"></a>Conceder o acesso a um segredo armazenado em um Key Vault para sua VM  

Usando as identidades serviço gerenciadas para recursos do Azure, seu código pode obter tokens de acesso para autenticar para recursos que oferecem suporte à autenticação do Azure Active Directory. No entanto, nem todos os serviços do Azure dão suporte à autenticação do Azure AD. Para usar identidades gerenciadas para recursos do Azure com esses serviços, armazene as credenciais de serviço no Azure Key Vault e use as identidades gerenciada para recursos do Azure para acessar o Key Vault para recuperar as credenciais. 

Primeiro, precisamos criar um cofre de chaves e conceder à nossa identidade gerenciada atribuída pelo sistema da VM acesso ao Key Vault.   

1. Na parte superior da barra de navegação esquerda, selecione**Criar um recurso** > **Segurança + Identidade** > **Key Vault**.  
2. Forneça um **Nome** para o novo Key Vault. 
3. Localize o Key Vault no mesmo grupo de recursos e assinatura da VM que você criou anteriormente. 
4. Selecione **Políticas de acesso** e clique em **Adicionar nova**. 
5. Em Configurar usando modelo, selecione **Gerenciamento de Segredo**. 
6. Escolha **Selecionar Entidade de Segurança** e no campo de pesquisa insira o nome da VM que você criou anteriormente.  Selecione a VM na lista de resultados e clique em **Selecionar**. 
7. Clique em **OK** para terminar de adicionar a nova política de acesso e **OK** para concluir a seleção de política de acesso. 
8. Clique em **Criar** para terminar de criar o Key Vault. 

    ![Texto Alt da imagem](./media/msi-tutorial-windows-vm-access-nonaad/msi-blade.png)

Em seguida, adicione um segredo ao Key Vault, para que posteriormente você possa recuperar o segredo usando código em execução em sua VM: 

1. Selecione **Todos os Recursos** e localize e selecione o Key Vault criado. 
2. Selecione **Segredos** e clique em **Adicionar**. 
3. Selecione **Manual** em **Opções de upload**. 
4. Insira um nome e um valor para o segredo.  O valor pode ser qualquer coisa que você desejar. 
5. Deixe a data de ativação e a data de validade em branco e deixe **Habilitado** como **Sim**. 
6. Clique em **Criar** para criar o segredo. 
 
## <a name="get-an-access-token-using-the-vms-identity-and-use-it-to-retrieve-the-secret-from-the-key-vault"></a>Obtenha um token de acesso usando a identidade da VM e use-o para recuperar o segredo do Key Vault  

Para concluir essas etapas, você precisará do cliente SSH.  Se você estiver usando o Windows, poderá usar o cliente SSH no [Subsistema do Windows para Linux](https://msdn.microsoft.com/commandline/wsl/about). Se precisar de ajuda para configurar as chaves do cliente SSH, confira [Como usar chaves SSH com o Windows no Azure](../../virtual-machines/linux/ssh-from-windows.md), ou [Como criar e usar um par de chaves SSH pública e privada para VMs Linux no Azure](../../virtual-machines/linux/mac-create-ssh-keys.md).
 
1. No portal, navegue até a VM do Linux e em **Visão geral**, clique em **Conectar**. 
2. **Conecte-se** à VM com um cliente SSH de sua escolha. 
3. Na janela de terminal, usando CURL, faça uma solicitação para as identidades gerenciadas locais para o ponto de extremidade de recursos do Azure para obter um token de acesso para o Azure Key Vault.  
 
    A solicitação CURL para o token de acesso está abaixo.  
    
    ```bash
    curl 'http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&resource=https%3A%2F%2Fvault.azure.net' -H Metadata:true  
    ```
    A resposta inclui o token de acesso de que você precisa para acessar o Azure Resource Manager. 
    
    Resposta:  
    
    ```bash
    {"access_token":"eyJ0eXAi...",
    "refresh_token":"",
    "expires_in":"3599",
    "expires_on":"1504130527",
    "not_before":"1504126627",
    "resource":"https://vault.azure.net",
    "token_type":"Bearer"} 
    ```
    
    Você pode usar esse token de acesso para autenticar o Azure Key Vault.  A próxima solicitação CURL mostra como ler um segredo do Key Vault usando CURL e a API REST do Key Vault.  Você precisará da URL de seu Key Vault, que está na seção **Essentials** da página **Visão geral** do Key Vault.  O token de acesso que você obteve na chamada anterior também será necessário. 
        
    ```bash
    curl https://<YOUR-KEY-VAULT-URL>/secrets/<secret-name>?api-version=2016-10-01 -H "Authorization: Bearer <ACCESS TOKEN>" 
    ```
    
    A resposta terá a seguinte aparência: 
    
    ```bash
    {"value":"p@ssw0rd!","id":"https://mytestkeyvault.vault.azure.net/secrets/MyTestSecret/7c2204c6093c4d859bc5b9eff8f29050","attributes":{"enabled":true,"created":1505088747,"updated":1505088747,"recoveryLevel":"Purgeable"}} 
    ```
    
Depois de recuperar o segredo do Key Vault, você pode usá-lo para autenticar um serviço que requer um nome e uma senha.

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você aprendeu a usar uma identidade gerenciada atribuída pelo sistema da VM do Linux para acessar o Azure Key Vault.  Para saber mais sobre o Azure Key Vault, confira:

> [!div class="nextstepaction"]
>[Cofre da Chave do Azure](/azure/key-vault/key-vault-overview)




