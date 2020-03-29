---
title: Validação controlada do Azure AD híbrido - Azure AD
description: Aprenda a fazer uma validação controlada do Azure AD híbrido antes de habilitá-lo em toda a organização de uma só vez
services: active-directory
ms.service: active-directory
ms.subservice: devices
ms.topic: conceptual
ms.date: 06/28/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sandeo
ms.collection: M365-identity-device-management
ms.openlocfilehash: f43db805ccbb7d4e546c51bbe39350f4bbba2efb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80049992"
---
# <a name="controlled-validation-of-hybrid-azure-ad-join"></a>Validação controlada de ingresso no Azure AD híbrido

Quando todos os pré-requisitos estiverem em vigor, os dispositivos Windows se registrarão automaticamente como dispositivos no seu inquilino Azure AD. O estado dessas identidades de dispositivos no Azure AD é referido como adesão a Azure AD híbrido. Mais informações sobre os conceitos abordados neste artigo podem ser encontradas nos artigos [Introdução ao gerenciamento de dispositivos no Azure Active Directory](overview.md) e [Planeje sua implementação híbrida do Azure Active Directory](hybrid-azuread-join-plan.md).

As organizações podem querer fazer uma validação controlada da adesão híbrida do Azure AD antes de habilitá-lo em toda a sua organização de uma só vez. Este artigo explicará como realizar uma validação controlada da adesão híbrida do Azure AD.

## <a name="controlled-validation-of-hybrid-azure-ad-join-on-windows-current-devices"></a>Validação controlada do Azure AD híbrido se une a dispositivos atuais do Windows

Para os dispositivos que executam o sistema operacional da área de trabalho do Windows, a versão com suporte é a Atualização de Aniversário do Windows 10 (versão 1607) ou posterior. Como melhor prática, atualize para a última versão do Windows 10.

Para fazer uma validação controlada do Azure AD híbrido se juntar aos dispositivos atuais do Windows, você precisa:

1. Limpe a entrada do Ponto de Conexão de Serviço (SCP) do Active Directory (AD) se existir
1. Configure a configuração de registro do lado do cliente para SCP em seus computadores unidos pelo domínio usando um GPO (Group Policy Object, objeto de diretiva de grupo)
1. Se você estiver usando AD FS, você também deve configurar a configuração de registro do lado do cliente para SCP em seu servidor AD FS usando um GPO  
1. Você também pode precisar [personalizar opções de sincronização](../hybrid/how-to-connect-post-installation.md#additional-tasks-available-in-azure-ad-connect) no Azure AD Connect para permitir a sincronização do dispositivo. 


### <a name="clear-the-scp-from-ad"></a>Limpar o SCP do AD

Use o Active Directory Services Interfaces Editor (ADSI Edit) para modificar os objetos SCP em AD.

1. Inicie o aplicativo de desktop **ADSI Edit** de uma estação de trabalho administrativa ou um controlador de domínio como administrador corporativo.
1. Conecte-se ao contexto de nomeação de **configuração** do seu domínio.
1. Navegue até **CN=Configuração,DC=contoso,DC=com** > **CN=Serviços** > **CN=Configuração de registro de dispositivo**
1. Clique com o botão direito do mouse no objeto folha **CN=62a0ff2e-97b9-4513-943f-0d221bd30080** e selecione **Propriedades**
   1. Selecione **palavras-chave** na janela **Do Editor de atributos** e clique **em Editar**
   1. Selecione os valores **de azureADId** e **azureADName** (um de cada vez) e clique em **Remover**
1. Fechar **edição ADSI**


### <a name="configure-client-side-registry-setting-for-scp"></a>Configurar a configuração do registro do lado do cliente para SCP

Use o exemplo a seguir para criar um GPO (Group Policy Object, objeto de diretiva de grupo) para implantar uma configuração de registro configurando uma entrada SCP no registro de seus dispositivos.

1. Abra um console de gerenciamento de políticas de grupo e crie um novo objeto de diretiva de grupo em seu domínio.
   1. Forneça um nome ao seu GPO recém-criado (por exemplo, ClientSideSCP).
1. Edite o GPO e localize o seguinte caminho:**Registro de configurações** > **Windows Settings** >  **de configuração** > do computador do**Windows**
1. Clique com o botão direito do mouse no Registro e selecione **Novo** > **Item de Registro**
   1. Na guia **Geral,** configure o seguinte
      1. Ação: **Atualização**
      1. Colmeia: **HKEY_LOCAL_MACHINE**
      1. Caminho-chave: **SOFTWARE\Microsoft\Windows\CurrentVersion\CDJ\AAD**
      1. Nome do valor: **TenantId**
      1. Tipo de valor: **REG_SZ**
      1. Dados de valor: O **ID** GUID ou Diretório da sua instância Ad do Azure (esse valor pode ser encontrado no > **ID**do Diretório de > **Propriedades**ativas do diretório ativo do **Azure)** > **Azure Active Directory**
   1. Clique em **OK**.
1. Clique com o botão direito do mouse no Registro e selecione **Novo** > **Item de Registro**
   1. Na guia **Geral,** configure o seguinte
      1. Ação: **Atualização**
      1. Colmeia: **HKEY_LOCAL_MACHINE**
      1. Caminho-chave: **SOFTWARE\Microsoft\Windows\CurrentVersion\CDJ\AAD**
      1. Nome do valor: **Nome do inquilino**
      1. Tipo de valor: **REG_SZ**
      1. Dados de valor: Seu **nome de domínio** verificado se você estiver usando ambiente federado, como AD FS. Seu **nome de domínio** verificado ou seu `contoso.onmicrosoft.com` nome de domínio onmicrosoft.com, por exemplo, se você estiver usando ambiente gerenciado
   1. Clique em **OK**.
1. Feche o editor para o gpo recém-criado
1. Vincule o GPO recém-criado ao OU desejado que contém computadores unidos por domínio que pertencem à sua população de implantação controlada

### <a name="configure-ad-fs-settings"></a>Configure as configurações do AD FS

Se você estiver usando OD FS, primeiro você precisa configurar o SCP do lado do cliente usando as instruções mencionadas acima, vinculando o GPO aos seus servidores AD FS. O objeto SCP define a fonte de autoridade para objetos de dispositivo. Pode ser no local ou No Local ou Azure AD. Quando o SCP do lado do cliente é configurado para AD FS, a fonte para objetos do dispositivo é estabelecida como Azure AD.

> [!NOTE]
> Se você não conseguiu configurar o SCP do lado do cliente em seus servidores AD FS, a fonte de identidades do dispositivo será considerada como no local. Em seguida, o ADFS iniciará a exclusão de objetos de dispositivo do diretório local após o período estipulado definido no atributo "MaximumInactiveDays" do Registro de Dispositivos ADFS. Objetos de registro de dispositivos ADFS podem ser encontrados usando o [cmdlet Get-AdfsDeviceRegistration](/powershell/module/adfs/get-adfsdeviceregistration?view=win10-ps).

## <a name="controlled-validation-of-hybrid-azure-ad-join-on-windows-down-level-devices"></a>Validação controlada do Azure AD híbrido se une a dispositivos de nível baixo do Windows

Para registrar dispositivos de nível inferior do Windows, as organizações devem instalar o [Microsoft Workplace Join para computadores não Windows 10](https://www.microsoft.com/download/details.aspx?id=53554) disponível no Centro de Download da Microsoft.

É possível implantar o pacote usando um sistema de distribuição de software como o  [Microsoft Endpoint Configuration Manager](/configmgr/). O pacote dá suporte às opções de instalação silenciosa padrão com o parâmetro quiet. O atual branch do Configuration Manager oferece benefícios adicionais em relação às versões anteriores, como a capacidade de rastrear registros concluídos.

O instalador cria uma tarefa agendada no sistema que é executada no contexto do usuário. A tarefa é disparada quando o usuário entra no Windows. A tarefa ingressa silenciosamente o dispositivo com Azure AD com as credenciais do usuário após a autenticação no Azure AD.

Para controlar o registro do dispositivo, você deve implantar o pacote do Instalador do Windows no grupo selecionado de dispositivos de nível baixo do Windows.

> [!NOTE]
> Se um SCP não estiver configurado em AD, então você deve seguir a mesma abordagem descrita para configurar a [configuração de registro do lado do cliente para SCP](#configure-client-side-registry-setting-for-scp)) em seus computadores unidos pelo domínio usando um GPO (Group Policy Object, objeto de política de grupo).


Depois de verificar se tudo funciona como esperado, você pode registrar automaticamente o resto dos dispositivos atuais e de nível de baixa do Windows com o Azure AD [configurando o SCP usando o Azure AD Connect](hybrid-azuread-join-managed-domains.md#configure-hybrid-azure-ad-join).

## <a name="next-steps"></a>Próximas etapas

[Planejar a sua implementação do ingresso do Azure Active Directory híbrido](hybrid-azuread-join-plan.md)
