---
title: Modo de acesso, energia e conectividade do azure Data Box Gateway
description: Descreve como gerenciar o acesso, a potência e o modo de conectividade para o dispositivo de Azure Data Box Gateway que ajuda a transferir dados para o Azure
services: databox
author: alkohli
ms.service: databox
ms.subservice: gateway
ms.topic: article
ms.date: 06/03/2019
ms.author: alkohli
ms.openlocfilehash: e4d85bd460c39964c9f42ac946e3522f5f129c1c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79474434"
---
# <a name="manage-access-power-and-connectivity-mode-for-your-azure-data-box-gateway"></a>Gerencie o modo de acesso, energia e conectividade para o gateway da caixa de dados do Azure

Este artigo descreve como gerenciar o modo de acesso, energia e conectividade para o Azure Data Box Gateway. Essas operações são executadas por meio da interface do usuário da web local ou o portal do Azure.

Neste artigo, você aprenderá como:

> [!div class="checklist"]
> * Gerenciar o acesso de dispositivo
> * Gerenciar o modo de conectividade
> * Gerenciar potência

## <a name="manage-device-access"></a>Gerenciar o acesso de dispositivo

O acesso ao seu dispositivo Data Box Gateway é controlado pelo uso de uma senha do dispositivo. Você pode alterar a senha através da ui local. Você também pode redefinir a senha do dispositivo no portal Azure.

### <a name="change-device-password"></a>Alterar a senha de dispositivo

Siga estas etapas na ui local para alterar a senha do dispositivo.

1. Na interface do usuário de web local, vá para **manutenção > alteração de senha**.
2. Digite a senha atual e, em seguida, a nova senha. A senha fornecida deve ter entre 8 e 16 caracteres. A senha deve ter 3 dos seguintes caracteres: maiúscula, minúscula, numérica e caracteres especiais. Confirme a nova senha.

    ![Alterar senha](media/data-box-gateway-manage-access-power-connectivity-mode/change-password-1.png)

3. Clique em **Alterar senha**.
 
### <a name="reset-device-password"></a>Redefinir a senha do dispositivo

O fluxo de trabalho de redefinição não exige que o usuário recupere a senha antiga e é útil quando a senha é perdida. Esse fluxo de trabalho é executado no portal do Azure.

1. No portal do Azure, acesse **visão geral > Redefinir senha de administrador**.

    ![Redefinir senha](media/data-box-gateway-manage-access-power-connectivity-mode/reset-password-1.png)

 
2. Digite a nova senha e confirme-a. A senha fornecida deve ter entre 8 e 16 caracteres. A senha deve ter 3 dos seguintes caracteres: maiúscula, minúscula, numérica e caracteres especiais. Clique em **redefinir**.

    ![Redefinir senha](media/data-box-gateway-manage-access-power-connectivity-mode/reset-password-2.png)

## <a name="manage-resource-access"></a>Gerenciar o acesso de recursos

Para criar o recurso Data Box Edge/Data Box Gateway, IoT Hub e Azure Storage, você precisa de permissões como contribuinte ou superior em um nível de grupo de recursos. Você também precisa que os provedores de recursos correspondentes sejam registrados. Para quaisquer operações que envolvam chave de ativação e credenciais, também são necessárias permissões para a API do Azure Active Directory Graph. Estes são descritos nas seções a seguir.

### <a name="manage-microsoft-graph-api-permissions"></a>Gerencie as permissões de API do Microsoft Graph

Ao gerar a chave de ativação para o dispositivo Data Box Edge ou realizar quaisquer operações que exijam credenciais, você precisa de permissões para a API do Microsoft Graph. As operações que precisam de credenciais podem ser:

-  Criando uma parte com uma conta de armazenamento associada.
-  Criando um usuário que possa acessar as ações no dispositivo.

Você deve `User` ter um acesso no inquilino do Active `Read all directory objects`Directory como você precisa para ser capaz de . Você não pode ser um usuário convidado, pois `Read all directory objects`eles não têm permissões para . Se você for um convidado, as operações como geração de uma chave de ativação, criação de um compartilhamento em seu dispositivo do Data Box Edge, a criação de um usuário falhará.

Para obter mais informações sobre como fornecer acesso aos usuários à API do Microsoft Graph, consulte a [referência de permissões do Microsoft Graph](https://docs.microsoft.com/graph/permissions-reference).

### <a name="register-resource-providers"></a>Registre provedores de recursos

Para prover um recurso no Azure (no modelo Azure Resource Manager), você precisa de um provedor de recursos que suporte a criação desse recurso. Por exemplo, para provisionar uma máquina virtual, você deve ter um provedor de recursos 'Microsoft.Compute' disponível na assinatura.
 
Provedores de recursos são registrados no nível da assinatura. Por padrão, qualquer nova assinatura do Azure é previamente registrada com uma lista de provedores de recursos comumente usados. O provedor de recursos do 'Microsoft.DataBoxEdge' não está incluído nesta lista.

Você não precisa conceder permissões de acesso ao nível de assinatura para que os usuários possam criar recursos como 'Microsoft.DataBoxEdge' em seus grupos de recursos que eles têm direitos de proprietário, desde que os provedores de recursos para esses recursos já estejam Registrado.

Antes de tentar criar qualquer recurso, certifique-se de que o provedor de recursos esteja registrado na assinatura. Se o provedor de recursos não estiver registrado, você precisará ter certeza de que o usuário que cria o novo recurso tem direitos suficientes para registrar o provedor de recursos necessário no nível de assinatura. Se você não fez isso também, então você verá o seguinte erro:

*O \<nome de assinatura de assinatura> não tem permissões para registrar o provedor de recursos: Microsoft.DataBoxEdge.*


Para obter uma lista de provedores de recursos registrados na assinatura atual, execute o seguinte comando:

```PowerShell
Get-AzResourceProvider -ListAvailable |where {$_.Registrationstate -eq "Registered"}
```

Para o dispositivo `Microsoft.DataBoxEdge` Data Box Edge, deve ser registrado. Para `Microsoft.DataBoxEdge`registrar, o admin de assinatura deve executar o seguinte comando:

```PowerShell
Register-AzResourceProvider -ProviderNamespace Microsoft.DataBoxEdge
```

Para obter mais informações sobre como registrar um provedor de recursos, consulte [Resolver erros para registro de provedor de recursos](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-register-provider-errors).

## <a name="manage-connectivity-mode"></a>Gerenciar o modo de conectividade

Além do modo normal padrão, o dispositivo também pode ser executado no modo parcialmente desconectado ou desconectado. Cada um desses modos é descrita como abaixo:

- **Parcialmente desconectado** - Nesse modo, o dispositivo não pode carregar dados para os compartilhamentos, mas pode ser gerenciado pelo portal do Azure.

    Esse modo é normalmente usado quando em uma rede de satélite medida e o objetivo é minimizar o consumo de largura de banda da rede. Consumo de rede mínima ainda poderão ocorrer para monitoramento de operações de dispositivo.

- **Disconnected** - Neste modo, o dispositivo é totalmente desconectado da nuvem e os uploads e downloads na nuvem são desativados. O dispositivo só pode ser gerenciado pela interface da web local.

    Esse modo é normalmente usado quando você deseja colocar o dispositivo offline.

Para alterar o modo de dispositivo, siga estas etapas:

1. Na interface do usuário da web local do seu dispositivo, acesse **Configuração> Configurações da nuvem**.
2. Desabilitar a **upload e download de nuvem**.
3. Para executar o dispositivo no modo desconectado parcialmente, habilite **gerenciamento do portal do Azure**.

    ![Modo de conectividade](media/data-box-gateway-manage-access-power-connectivity-mode/connectivity-mode-1.png)
 
4. Para executar o dispositivo no modo desconectado, desabilite **gerenciamento do portal do Azure**. Agora, o dispositivo só pode ser gerenciado por meio da interface do usuário da web local.

    ![Modo de conectividade](media/data-box-gateway-manage-access-power-connectivity-mode/connectivity-mode-2.png)

## <a name="manage-power"></a>Gerenciar potência

Você pode desligar ou reiniciar seu dispositivo virtual usando a interface do usuário da Web local. Nós recomendamos que antes de reiniciar, você coloque os compartilhamentos offline no host e, em seguida, no dispositivo. Essa ação minimiza a possibilidade de corrupção de dados.

1. Na interface do usuário de web local, vá para **manutenção > configurações de energia**.
2. Clique em **desligamento** ou **reiniciar** dependendo do que você pretende fazer.

    ![Configurações de energia](media/data-box-gateway-manage-access-power-connectivity-mode/shut-down-restart-1.png)

3. Quando solicitado a confirmar, clique em **Sim** para continuar.

> [!NOTE]
> Se você desligar o dispositivo virtual, você precisará iniciar o dispositivo por meio do gerenciamento de hipervisor.
