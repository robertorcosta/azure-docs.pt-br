---
title: Como gerenciar dispositivos usando o portal do Azure | Microsoft Docs
description: Saiba como usar o portal do Azure para gerenciar dispositivos.
services: active-directory
ms.service: active-directory
ms.subservice: devices
ms.topic: conceptual
ms.date: 06/04/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: jairoc
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6c8e94a1b15ad8cd55019f9351c82f58130e472d
ms.sourcegitcommit: 4c831e768bb43e232de9738b363063590faa0472
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/23/2019
ms.locfileid: "74420611"
---
# <a name="manage-device-identities-using-the-azure-portal"></a>Manage device identities using the Azure portal

With device identity management in Azure Active Directory (Azure AD), you can ensure that your users are accessing your resources from devices that meet your standards for security and compliance.

Este artigo:

- Assumes that you are familiar with the [introduction to device identity management in Azure Active Directory](overview.md)
- Provides you with information about managing your device identities using the Azure AD portal

## <a name="manage-device-identities"></a>Gerenciar identidades do dispositivo

The Azure AD portal provides you with a central place to manage your device identities. Você pode acessar este local usando um [link direto](https://portal.azure.com/#blade/Microsoft_AAD_IAM/DevicesMenuBlade/Devices) ou seguindo estas etapas manuais:

1. Entre no [portal do Azure](https://portal.azure.com) como administrador.

2. Search for and select **Azure Active Directory** or select it from the **Home** page.

3. Na seção **Gerenciar**, clique em **Dispositivos**.

   ![Definir configurações do dispositivo](./media/device-management-azure-portal/74.png)

A página de **Dispositivos** permite que você:

- Configurar suas configurações de dispositivo
- Localizar dispositivos
- Perform device identity management tasks
- Review device-related audit logs  
  
## <a name="configure-device-settings"></a>Definir configurações do dispositivo

To manage your device identities using the Azure AD portal, your devices need to be either [registered or joined](overview.md) to Azure AD. Como administrador, você pode ajustar o processo de registro e ingresso de dispositivos definindo as configurações do dispositivo.

![Definir configurações do dispositivo](./media/device-management-azure-portal/22.png)

A página de configurações do dispositivo permite que você configure:

![Gerenciar um dispositivo do Intune](./media/device-management-azure-portal/21.png)

- **Users may join devices to Azure AD** - This setting enables you to select the users who can register their devices as Azure AD joined devices. O padrão é **Todos**.

> [!NOTE]
> **Users may join devices to Azure AD** setting is only applicable to Azure AD join on Windows 10.

- **Outros administradores locais nos dispositivos associados ao Azure AD** – você pode selecionar os usuários que têm direitos de administrador local em um dispositivo. Os usuários adicionados aqui são adicionados à função *Administradores do dispositivo* no Azure AD. Os administradores globais no Azure AD e os proprietários do dispositivo recebem direitos de administrador local por padrão. Essa opção é uma funcionalidade Premium Edition disponível por meio de produtos como o Azure AD Premium ou o EMS (Enterprise Mobility Suite).
- **Users may register their devices with Azure AD** - You need to configure this setting to allow Windows 10 personal, iOS, Android, and macOs devices to be registered with Azure AD. If you select **None**, devices are not allowed to register with Azure AD. O registro com o Microsoft Intune ou o MDM (Gerenciamento de Dispositivo Móvel) para o Office 365 exige registro. Se você tiver configurado qualquer um desses serviços, a opção **TODOS** estará selecionada e **NENHUM** não estará disponível.
- **Require Multi-Factor Auth to join devices** - You can choose whether users are required to provide an additional authentication factor to join their device to Azure AD. O padrão é **Não**. É recomendável exigir a autenticação multifator ao registrar um dispositivo. Antes de habilitar a autenticação multifator para este serviço, você deve garantir que a autenticação multifator esteja configurada para os usuários que registram seus dispositivos. Para saber mais sobre os diferentes serviços de autenticação multifator do Azure, consulte [Introdução à autenticação multifator do Azure](../authentication/concept-mfa-whichversion.md). 

> [!NOTE]
> **Require Multi-Factor Auth to join devices** setting applies to devices that are either Azure AD joined or Azure AD registered. This setting does not apply to hybrid Azure AD joined devices.

- **Maximum number of devices** - This setting enables you to select the maximum number of Azure AD joined or Azure AD registered devices that a user can have in Azure AD. Se um usuário atingir esta cota, ele não poderá adicionar mais dispositivos até que um ou mais dos seus dispositivos existentes sejam removidos. O valor padrão é **20**.

> [!NOTE]
> **Maximum number of devices** setting applies to devices that are either Azure AD joined or Azure AD registered. This setting does not apply to hybrid Azure AD joined devices.

- **Os usuários podem sincronizar configurações e dados de aplicativo em dispositivos** – por padrão, essa configuração é definida como **NENHUM**. Selecionar usuários específicos ou grupos ou TODOS permite que as configurações do usuário e os dados de aplicativo sejam sincronizados em seus dispositivos Windows 10. Saiba mais sobre como a sincronização funciona no Windows 10.
Essa opção é uma funcionalidade premium disponível por meio de produtos como o Azure AD Premium ou o EMS (Enterprise Mobility Suite).

## <a name="locate-devices"></a>Localizar dispositivos

Você tem duas opções para localizar dispositivos registrados e associados:

- **Todos os dispositivos** na seção **Gerenciar** da página **Dispositivos**  

   ![Todos os dispositivos](./media/device-management-azure-portal/41.png)

- **Dispositivos** na seção **Gerenciar** de uma página **Usuário**

   ![Todos os dispositivos](./media/device-management-azure-portal/43.png)

Com as duas opções, você pode obter uma exibição que:

- Enables you to search for devices using the display name or device ID as filter.
- Fornece visão geral detalhada de dispositivos registrados e associados
- Permite que você execute tarefas comuns de gerenciamento de dispositivo

![Todos os dispositivos](./media/device-management-azure-portal/51.png)

>[!TIP]
>
>* If you see a device that is "Hybrid Azure AD joined" with a state "Pending" under the REGISTERED column, it indicates that the device has been synchronized from Azure AD connect and is waiting to complete registration from the client. Read more on how to [plan your Hybrid Azure AD join implementation](hybrid-azuread-join-plan.md). Additional information can be found in the article, [Devices frequently asked questions](faq.md).
>
>   ![Pending devices](./media/device-management-azure-portal/75.png)
>
>* Para alguns dispositivos iOS, os nomes de dispositivo que contém apóstrofos podem usar caracteres diferentes que se parecem com apóstrofos. So searching for such devices is a little tricky - if you are not seeing search results correctly, ensure that the search string contains matching apostrophe character.

## <a name="device-identity-management-tasks"></a>Device identity management tasks

As a global administrator or cloud device administrator, you can manage the registered or joined devices. Os administradores de Serviço do Intune podem:

- Atualizar dispositivos – exemplos são operações diárias, como habilitar/desabilitar dispositivos
- Excluir dispositivos – quando um dispositivo está desativado e deve ser excluído do Azure AD

This section provides you with information about common device identity management tasks.

### <a name="manage-an-intune-device"></a>Gerenciar um dispositivo do Intune

Se você for um administrador do Intune, será possível gerenciar dispositivos marcados como **Microsoft Intune**. If the device is not enrolled with Microsoft Intune the "Manage" option will be greyed out.

![Gerenciar um dispositivo do Intune](./media/device-management-azure-portal/31.png)

### <a name="enable--disable-an-azure-ad-device"></a>Habilitar/desabilitar um dispositivo do Azure Active Directory

Para ativar / desativar um dispositivo, você tem duas opções:

- O menu de tarefas ("...") na página **Todos os dispositivos**

   ![Gerenciar um dispositivo do Intune](./media/device-management-azure-portal/71.png)

- A barra de ferramentas na página **Dispositivos**

   ![Gerenciar um dispositivo do Intune](./media/device-management-azure-portal/32.png)

**Comentários:**

- You need to be a global administrator or cloud device administrator in Azure AD to enable / disable a device. 
- Disabling a device prevents a device from successfully authenticating with Azure AD, thereby preventing the device from accessing your Azure AD resources that are guarded by device CA or using your WH4B credentials.
- Disabling the device will revoke both the Primary Refresh Token (PRT) and any Refresh Tokens (RT) on the device.

### <a name="delete-an-azure-ad-device"></a>Excluir um dispositivo do Azure Active Directory

Para excluir um dispositivo, você tem duas opções:

- O menu de tarefas ("...") na página **Todos os dispositivos**

   ![Gerenciar um dispositivo do Intune](./media/device-management-azure-portal/72.png)

- A barra de ferramentas na página **Dispositivos**

   ![Excluir um dispositivo](./media/device-management-azure-portal/34.png)

**Comentários:**

- Você precisa ser um administrador global ou um administrador do Intune no Azure AD para excluir um dispositivo.
- Excluindo um dispositivo:
   - Impede que um dispositivo acesse seus recursos do Azure Active Directory.
   - Remove todos os detalhes que estão relacionados ao dispositivo, por exemplo, chaves do BitLocker para dispositivos Windows.  
   - Representa uma atividade não recuperável e não é recomendável a menos que necessário.

If a device is managed by another management authority (for example, Microsoft Intune), make sure that the device has been wiped / retired before deleting the device in Azure AD. Review how to [manage stale devices](device-management-azure-portal.md) before deleting any devices.

### <a name="view-or-copy-device-id"></a>Exibir ou copiar a ID do dispositivo

Você pode usar uma ID de dispositivo para verificar os detalhes de ID de dispositivo no dispositivo ou usar o PowerShell durante a solução de problemas. Para acessar a opção de cópia, clique no dispositivo.

![Exibir uma ID do dispositivo](./media/device-management-azure-portal/35.png)
  
### <a name="view-or-copy-bitlocker-keys"></a>Exibir ou copiar as chaves do BitLocker

É possível exibir e copiar as chaves do BitLocker para ajudar os usuários a recuperar sua unidade criptografada. Essas chaves só estão disponíveis para dispositivos Windows que são criptografados e têm suas chaves armazenadas no Azure AD. Você pode copiar essas chaves ao acessar detalhes do dispositivo.

![Exibir chaves do BitLocker](./media/device-management-azure-portal/36.png)

Para exibir ou copiar as chaves do BitLocker, você precisa ser o proprietário do dispositivo ou um usuário que tenha pelo menos uma das seguintes funções atribuídas:

- Administrador de Dispositivo de Nuvem
- Administrador global
- Administrador de assistência técnica
- Administrador de serviços do Intune
- Administrador de segurança
- Leitor de segurança

> [!NOTE]
> Os dispositivos do Windows 10 ingressados no Azure AD híbrido não têm um proprietário. Portanto, se você está procurando por um dispositivo pelo proprietário e não o encontrou, pesquise pela ID do dispositivo.

## <a name="audit-logs"></a>Logs de auditoria

As atividades de dispositivo estão disponíveis por meio dos logs de atividade. These logs include activities triggered by the device registration service and by users:

- Criação de dispositivo e adição de usuários/proprietários no dispositivo
- Alterações nas configurações do dispositivo
- Operações de dispositivo como excluir ou atualizar um dispositivo

O ponto de entrada para os dados de auditoria é **Logs de auditoria**, na seção **Atividade** da página **Dispositivos**.

![Logs de auditoria](./media/device-management-azure-portal/61.png)

Um log de auditoria tem um modo de exibição de lista padrão que mostra:

- A data e a hora da ocorrência
- Os destinos
- O iniciador/ator (quem) de uma atividade
- A atividade (o quê)

![Logs de auditoria](./media/device-management-azure-portal/63.png)

Você pode personalizar o modo de exibição de lista clicando em **Colunas** na barra de ferramentas.

![Logs de auditoria](./media/device-management-azure-portal/64.png)

Para restringir os dados relatados a um nível que funciona para você, filtre os dados de auditoria usando os seguintes campos:

- Categoria
- Tipo de recurso de atividade
- Atividade
- Intervalo de datas
- Escolha o destino
- Iniciado por (ator)

Além dos filtros, você pode pesquisar itens específicos.

![Logs de auditoria](./media/device-management-azure-portal/65.png)

## <a name="next-steps"></a>Próximos passos

[How to manage stale devices in Azure AD](manage-stale-devices.md)
