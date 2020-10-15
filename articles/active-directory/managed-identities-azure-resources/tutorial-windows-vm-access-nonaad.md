---
title: Tutorial`:` Usar uma identidade gerenciada para acessar o Azure Key Vault – Windows – Azure AD
description: Um tutorial que o conduz pelo processo de usar uma identidade gerenciada atribuída pelo sistema da VM do Windows para acessar o Azure Key Vault.
services: active-directory
documentationcenter: ''
author: barclayn
manager: daveba
editor: daveba
ms.service: active-directory
ms.subservice: msi
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 01/10/2020
ms.author: barclayn
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2890eb2211ac0a105363742a0e900e52a577ed27
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "89255818"
---
# <a name="tutorial-use-a-windows-vm-system-assigned-managed-identity-to-access-azure-key-vault"></a>Tutorial: Usar uma identidade gerenciada atribuída pelo sistema da VM do Windows para acessar o Azure Key Vault 

[!INCLUDE [preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

Este tutorial mostra como usar uma identidade gerenciada atribuída pelo sistema para uma VM (máquina virtual) do Windows para acessar o Azure Key Vault. Atuando como bootstrap, o Key Vault possibilita que o aplicativo cliente use o segredo para acessar recursos não protegidos pelo Azure AD (Active Directory). As Identidades de Serviço Gerenciadas são gerenciadas automaticamente pelo Azure e permitem a você autenticar os serviços que dão suporte à autenticação do Azure AD sem necessidade de inserir as credenciais em seu código. 

Você aprenderá como:


> [!div class="checklist"]
> * Conceda o acesso da máquina virtual a um segredo armazenado em um Key Vault 
> * Obter um token de acesso usando a identidade da máquina virtual e usá-la para recuperar o segredo do Key Vault 

## <a name="prerequisites"></a>Pré-requisitos

[!INCLUDE [msi-tut-prereqs](../../../includes/active-directory-msi-tut-prereqs.md)]


## <a name="enable"></a>Habilitar

[!INCLUDE [msi-tut-enable](../../../includes/active-directory-msi-tut-enable.md)]



## <a name="grant-access"></a>Conceder acesso  
 
Esta seção mostra como conceder acesso a um segredo armazenado em um Key Vault para sua VM. Usando a identidades gerenciadas para recursos do Azure, seu código pode obter tokens de acesso para autenticar para recursos que oferecem suporte à autenticação do Azure AD.  No entanto, nem todos os serviços do Azure dão suporte à autenticação do Azure AD. Para usar identidades gerenciadas para recursos do Azure com esses serviços, armazene as credenciais de serviço no Azure Key Vault e use a identidade gerenciada da VM para acessar o Key Vault para recuperar as credenciais. 

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
 
## <a name="access-data"></a>Acessar dados  

Esta seção mostra como obter um token de acesso usando a identidade da VM e usá-lo para recuperar o segredo do Key Vault. Se você não tiver o PowerShell 4.3.1 ou superior instalado, você precisará [baixar e instalar a versão mais recente](/powershell/azure/).

Primeiro, usamos a identidade gerenciada atribuída pelo sistema da VM para obter um token de acesso para autenticar o para o Key Vault:
 
1. No portal, navegue até **Máquinas Virtuais** e vá para a máquina virtual do Windows e em **Visão geral**, clique em **Conectar**.
2. Insira o seu **Nome de usuário** e **Senha** que você adicionou quando criou a **VM do Windows**.  
3. Agora que você criou uma **Conexão de Área de Trabalho Remota** com a máquina virtual, abra o PowerShell na sessão remota.  
4. No PowerShell, invoque a solicitação Web no locatário para obter o token para o host local na porta específica para a máquina virtual.  

    A solicitação do PowerShell:
    
    ```powershell
    $response = Invoke-WebRequest -Uri 'http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&resource=https%3A%2F%2Fvault.azure.net' -Method GET -Headers @{Metadata="true"} 
    ```
    
    Em seguida, extraia a resposta completa, que é armazenada como uma cadeia de caracteres JSON formatada no objeto $response.  
    
    ```powershell
    $content = $response.Content | ConvertFrom-Json 
    ```
    
    Em seguida, extraia o token de acesso da resposta.  
    
    ```powershell
    $KeyVaultToken = $content.access_token 
    ```
    
    Por fim, use o comando de Invoke-WebRequest do PowerShell para recuperar o segredo que você criou anteriormente no Key Vault, passando o token de acesso no cabeçalho de Autorização.  Você precisará da URL de seu Key Vault, que está na seção **Essentials** da página **Visão geral** do Key Vault.  
    
    ```powershell
    (Invoke-WebRequest -Uri https://<your-key-vault-URL>/secrets/<secret-name>?api-version=2016-10-01 -Method GET -Headers @{Authorization="Bearer $KeyVaultToken"}).content 
    ```
    
    A resposta terá a seguinte aparência: 
    
    ```powershell
    {"value":"p@ssw0rd!","id":"https://mytestkeyvault.vault.azure.net/secrets/MyTestSecret/7c2204c6093c4d859bc5b9eff8f29050","attributes":{"enabled":true,"created":1505088747,"updated":1505088747,"recoveryLevel":"Purgeable"}} 
    ```
    
Depois de recuperar o segredo do Key Vault, você pode usá-lo para autenticar um serviço que requer um nome e uma senha. 


## <a name="disable"></a>Desabilitar

[!INCLUDE [msi-tut-disable](../../../includes/active-directory-msi-tut-disable.md)]



## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você aprendeu a usar uma identidade gerenciada atribuída pelo sistema da VM do Windows para acessar o Azure Key Vault.  Para saber mais sobre o Azure Key Vault, confira:

> [!div class="nextstepaction"]
>[Cofre da Chave do Azure](../../key-vault/general/overview.md)