---
title: Definir Azure Resource Manager senha em seu dispositivo do Azure Stack Edge
description: Descreve como se conectar ao Azure Resource Manager em execução no Azure Stack Edge usando o Azure PowerShell.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: article
ms.date: 02/24/2020
ms.author: alkohli
ms.openlocfilehash: d56f12c746383576a32e2c0ade542bc8aedff22d
ms.sourcegitcommit: 656c0c38cf550327a9ee10cc936029378bc7b5a2
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/28/2020
ms.locfileid: "89083266"
---
# <a name="set-azure-resource-manager-password"></a>Definir Azure Resource Manager senha

[!INCLUDE [applies-to-skus](../../includes/azure-stack-edge-applies-to-all-sku.md)]

Este artigo descreve como definir sua senha de Azure Resource Manager. Você precisa definir essa senha quando estiver se conectando às APIs locais do dispositivo por meio do Azure Resource Manager.

O procedimento para definir a senha pode ser diferente dependendo se você usa o portal do Azure ou os cmdlets do PowerShell. Cada um desses procedimentos é descrito nas seções a seguir.


## <a name="reset-password-via-the-azure-portal"></a>Redefinir a senha por meio do portal do Azure

1. Na portal do Azure, vá para o recurso de borda do Azure Stack que você criou para gerenciar seu dispositivo. Acesse **Computação de borda > Introdução**.

2. No painel direito, na barra de comandos, selecione **Redefinir senha do ARM de borda**. 

    ![Redefinir senha de usuário do EdgeARM 1](media/azure-stack-edge-j-series-set-azure-resource-manager-password/set-edgearm-password-1.png)

3. Na folha **Redefinir senha do usuário do EdgeArm** , forneça uma senha para se conectar às APIs locais do dispositivo por meio do Azure Resource Manager. Confirme a senha e selecione **Redefinir**.

    ![Redefinir senha de usuário do EdgeARM 2](media/azure-stack-edge-j-series-set-azure-resource-manager-password/set-edgearm-password-2.png)



## <a name="reset-password-via-powershell"></a>Redefinir a senha por meio do PowerShell

1. No portal do Azure, vá para o Azure Stack recurso de borda que você criou para gerenciar seu dispositivo. Anote os parâmetros a seguir na página **visão geral** .

    - Nome do recurso do Azure Stack Edge
    - ID da assinatura

2. Vá para **configurações > Propriedades**. Anote os parâmetros a seguir na página **Propriedades** .

    - Resource group
    - Chave de criptografia CIK: selecione Exibir e, em seguida, copie a **chave de criptografia**.

    ![Obter chave de criptografia CIK](media/azure-stack-edge-j-series-set-azure-resource-manager-password/get-cik-portal.png)
 
3. Identifique uma senha que será usada para se conectar ao Azure Resource Manager.

4. Inicie o Cloud Shell. Selecione no ícone no canto superior direito:

    ![Iniciar o Cloud Shell](media/azure-stack-edge-j-series-set-azure-resource-manager-password/start-cloud-shell.png) 

    Depois que o Cloud Shell for iniciado, talvez seja necessário alternar para o PowerShell.

    ![Cloud Shell](media/azure-stack-edge-j-series-set-azure-resource-manager-password/cloud-shell.png)   


5. Definir contexto. Tipo:

    `Set-AzContext -SubscriptionId <Subscription ID>`

    Aqui está uma amostra de saída:

    
    ```azurepowershell
    PS Azure:\> Set-AzContext -SubscriptionId 8eb87630-972c-4c36-a270-f330e6c063df
    
        Name        Account   SubscriptionName   Environment  TenantId
        ----       -------    ----------------   -----------  --------
        DataBox_Edge_Test (8eb87630-972c-4c36-a… MSI@50342 DataBox_Edge_Tes AzureCloud           72f988bf-86f1-41af-91ab-2d7…
    
        PS Azure:/
    ```
    
5.  Se você tiver módulos PS antigos, precisará instalá-los.

    `Remove-Module  Az.DataBoxEdge -force`

    Aqui está um exemplo de saída. Neste exemplo, não havia nenhum módulo antigo a ser instalado.

    
    ```azurepowershell
        PS Azure:\> Remove-Module  Az.DataBoxEdge -force
        Remove-Module : No modules were removed. Verify that the specification of modules to remove is correct and those modules exist in the runspace.
        At line:1 char:1
        + Remove-Module  Az.DataBoxEdge -force
        + ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
        + CategoryInfo          : ResourceUnavailable: (:) [Remove-Module], InvalidOperationException
        + FullyQualifiedErrorId : Modules_NoModulesRemoved,Microsoft.PowerShell.Commands.RemoveModuleCommand
    
        PS Azure:\
    ```

6. O próximo conjunto de comandos baixará e executará um script para instalar os módulos do PowerShell.
    
    ```azurepowershell
        cd ~/clouddrive
        wget https://aka.ms/dbe-cmdlet-beta -O Az.DataBoxEdge.zip
        unzip ./Az.DataBoxEdge.zip
        Import-Module ~/clouddrive/Az.DataBoxEdge/Az.DataBoxEdge.psd1 -Force
    ```

7. No próximo conjunto de comandos, você precisará fornecer o nome do recurso, o nome do grupo de recursos, a chave de criptografia e a senha que você identificou na etapa anterior.

    ```azurepowershell
    $devicename = "<Azure Stack Edge resource name>"
    $resourceGroup = "<Resource group name>"
    $cik = "<Encryption key>"
    $password = "<Password>"
    ```
    Os parâmetros de chave de criptografia e senha devem ser passados como cadeias de caracteres seguras. Use os cmdlets a seguir para converter a senha e a chave de criptografia para proteger as cadeias de caracteres.

    ```azurepowershell
    $pass = ConvertTo-SecureString $password -AsPlainText -Force
    $key = ConvertTo-SecureString $cik -AsPlainText -Force
    ```
    Use as cadeias de caracteres seguras geradas acima como parâmetros no cmdlet Set-AzDataBoxEdgeUser para redefinir a senha. Use o mesmo grupo de recursos que você usou ao criar o Azure Stack borda/Gateway do Data Box recurso.

    ```azurepowershell
    Set-AzDataBoxEdgeUser -ResourceGroupName $resourceGroup -DeviceName $devicename -Name EdgeARMUser  -Password $pass -EncryptionKey $key
    ```
    Aqui está a saída de exemplo.
    
    ```azurepowershell
    PS /home/aseuser/clouddrive> $devicename = "myaseresource"
    PS /home/aseuser/clouddrive> $resourceGroup = "myaserg"
    PS /home/aseuser/clouddrive> $cik = "54a7450fd7b3c506e10efea4e0c88a9390f37e299fbf43e01fb5dfe483ac036b6d0f85a6246e1926e297f98c0ff84c20a57348c689eff179ce31571fc787ac0a"
    PS /home/aseuser/clouddrive> $password = "Password2"
    PS /home/aseuser/clouddrive> $pass = ConvertTo-SecureString $password -AsPlainText -Force
    PS /home/aseuser/clouddrive> $key = ConvertTo-SecureString $cik -AsPlainText -Force
    PS /home/aseuser/clouddrive> Set-AzDataBoxEdgeUser -ResourceGroupName $resourceGroup -DeviceName $devicename -Name EdgeARMUser  -Password $pass -EncryptionKey $key
    
        User name   Type ResourceGroupName DeviceName
    ---------   ---- ----------------- ----------
        EdgeARMUser ARM  myaserg        myaseresource
    
        PS /home/aseuser/clouddrive>
    ```
Use a nova senha para se conectar ao Azure Resource Manager.

## <a name="next-steps"></a>Próximas etapas

[Conectar-se ao Azure Resource Manager](azure-stack-edge-j-series-connect-resource-manager.md)