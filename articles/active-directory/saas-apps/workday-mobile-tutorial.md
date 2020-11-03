---
title: 'Tutorial: Integração do SSO (logon único) do Azure Active Directory ao Aplicativo Móvel do Workday | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e o Aplicativo Móvel do Workday.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 08/31/2020
ms.author: jeedes
ms.openlocfilehash: 256da169761da486d8ac064a2f58a59be43bb5df
ms.sourcegitcommit: 8c7f47cc301ca07e7901d95b5fb81f08e6577550
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/27/2020
ms.locfileid: "92754141"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-workday-mobile-application"></a>Tutorial: Integração do SSO (logon único) do Azure Active Directory ao Aplicativo Móvel do Workday

Neste tutorial, você aprenderá a integrar o Azure AD (Azure Active Directory), o Acesso Condicional e o Intune ao Aplicativo Móvel do Workday. Ao integrar os Aplicativos Móveis do Workday à Microsoft, você poderá:

* Verificar se os dispositivos estão em conformidade com suas políticas antes de entrar.
* Adicionar controles ao Aplicativo do Workday para verificar se os usuários estão acessando dados corporativos com segurança. 
* Controlar no Azure AD quem tem acesso ao Workday.
* Permitir que os usuários sejam conectados automaticamente ao Workday com suas contas do Azure AD.
* Gerenciar suas contas em um local central: o portal do Azure.

## <a name="prerequisites"></a>Pré-requisitos

Para começar, você precisará dos seguintes itens:

* Integrar o Workday ao Azure AD
* Tutorial: [Integração do SSO (logon único) do Azure Active Directory ao Workday](https://docs.microsoft.com/azure/active-directory/saas-apps/workday-tutorial)

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará as Políticas de Acesso Condicional da Microsoft e o Intune com os Aplicativos Móveis do Workday.

* Agora, o aplicativo federado do Workday pode ser configurado com o Azure AD para habilitar o SSO. Para obter mais detalhes sobre como configurar, siga [este](workday-tutorial.md) link.

> [!NOTE] 
> O Workday não é compatível com as Políticas de Proteção de Aplicativo do Intune. Você precisa usar o Gerenciamento de Dispositivo Móvel para utilizar o Acesso Condicional.


## <a name="ensure-users-have-access-to-the-workday-mobile-app"></a>Verifique se os usuários têm acesso ao Aplicativo Móvel do Workday:

Configure o Workday para permitir o acesso às ofertas de Aplicativo Móvel dele. Você precisará configurar as seguintes políticas para dispositivos móveis:

Você pode configurá-las seguindo estas instruções:

1. Acesse o relatório Políticas de Segurança de Domínio para Área Funcional.
2. Selecione uma política de segurança.
    * Uso Móvel – Android
    * Uso Móvel – iPad
    * Uso Móvel – iPhone
3. Clique em Editar Permissões.
4. Marque a caixa de seleção Exibir ou Modificar para conceder aos grupos de segurança acesso aos itens protegíveis de tarefa ou relatório.
5. Marque a caixa de seleção Obter ou Colocar para conceder aos grupos de segurança acesso à integração e ações protegíveis de tarefa ou relatório.

Ative as alterações de política de segurança pendentes executando a tarefa **Ativar as Alterações de Política de Segurança Pendentes**.

## <a name="open-workday-login-page-in-mobile-browser"></a>Abra a página de logon do Workday no navegador móvel:

Para aplicar o Acesso Condicional ao aplicativo móvel do Workday, é necessário que o aplicativo seja aberto em um navegador externo. Isso pode ser feito marcando a caixa **Habilitar SSO do Navegador Móvel para Aplicativos Nativos** em **Editar Configuração do Locatário – Segurança.** Isso exigirá que um navegador aprovado pelo Intune esteja instalado no dispositivo para iOS e no Perfil de Trabalho para Android

![Logon do Navegador Móvel](./media/workday-tutorial/mobile-browser.png)

## <a name="setup-conditional-access-policy"></a>Configurar política de acesso condicional:

Essa política só afetará o logon em um dispositivo iOS ou Android. Se você quiser estendê-la para todas as plataformas, basta selecionar **Qualquer Dispositivo.** Essa política exigirá que o dispositivo esteja em conformidade com a política e verificará isso por meio do Microsoft Intune. Devido ao Android ter Perfis de Trabalho, isso deve impedir que os usuários façam logon no Workday (Web ou aplicativo), a menos que estejam fazendo logon por meio do Perfil de Trabalho deles e tenham instalado o aplicativo por meio do Portal da Empresa do Intune. Há uma etapa adicional para o iOS verificar se a mesma situação será aplicada. Veja algumas capturas de tela da configuração de Acesso Condicional.

**O Workday é compatível com os seguintes Controles de Acesso:**
* Exigir autenticação multifator
* Exigir que o dispositivo seja marcado como em conformidade

**O Aplicativo Workday não é compatível com o seguinte:**
* Exigir um aplicativo cliente aprovado
* Exigir uma política de proteção do aplicativo (versão prévia)

Para configurar o **Workday** como **Dispositivo Gerenciado** , execute as seguintes etapas:

![Configurar política de acesso condicional](./media/workday-tutorial/managed-devices-only.png)

1. Clique em **Página Inicial > Microsoft Intune > Acesso condicional – Políticas > Somente Dispositivos Gerenciados** 

1. Na página **Somente Dispositivos Gerenciados** , forneça o valor do campo **Nome** como `Managed Devices Only` e clique em **Aplicativos de nuvem ou ações**.

1. Execute as etapas a seguir em **Aplicativos de nuvem ou ações**.

    a. Alterne **Selecione a que essa política se aplica** como **Aplicativos de nuvem**.

    b. Em Incluir, clique em **Selecionar aplicativos**.

    c. Escolha **Workday** na lista selecionada.

    d. Clique em **Concluído**.

1. Ative **Habilitar política**.

1. Clique em **Save** (Salvar).

Para **Conceder** acesso, execute as seguintes etapas:

![Configurar política de acesso condicional do Workday](./media/workday-tutorial/managed-devices-only-2.png)

1. Clique em **Página Inicial > Microsoft Intune > Acesso condicional – Políticas > Somente Dispositivos Gerenciados** 

1. Na página **Somente Dispositivos Gerenciados** , forneça o valor do campo **Nome** como `Managed Devices Only` e clique em **Controles de acesso > Conceder**.

1. Execute as etapas a seguir na página **Conceder**.

    a. Selecione os controles a serem impostos como **Conceder acesso**.

    b. Marque a caixa **Exigir que o dispositivo seja marcado como em conformidade**.

    c. Selecione **Exigir um dos controles selecionados**.

    d. Clique em **Selecionar**.

1. Ative **Habilitar política**.

1. Clique em **Salvar**

## <a name="set-up-device-compliance-policy"></a>Configurar Política de Conformidade do Dispositivo:

para que os dispositivos iOS só possam fazer logon por meio de um Aplicativo Workday gerenciado pelo MDM, você precisa bloquear o aplicativo da App Store adicionando **com.workday.workdayapp** à lista de aplicativos restritos. Com isso, apenas os dispositivos que tiverem o aplicativo Workday instalado por meio do portal da empresa poderão acessar o Workday. Para o navegador, eles só poderão acessar o Workday se o dispositivo for gerenciado pelo Intune e estiverem usando um navegador gerenciado.

![Configurar Política de Conformidade de Dispositivos do Workday](./media/workday-tutorial/ios-policy.png)

## <a name="set-up-microsoft-intune-app-configuration-policies"></a>Configurar Políticas de Configuração de Aplicativos do Microsoft Intune:

| Cenário | Pares chave-valor |
|----------------------------------------------------------------------------------------   |-----------|
| Preencha automaticamente os campos Locatário e Endereço Web para:<br>● Workday no Android, quando você habilitar os perfis do Android for Work.<br>● Workday no iPad e iPhone.     | Use estes valores para configurar seu Locatário: <br>● Chave de configuração = UserGroupCode<br>● Tipo de valor = String <br>● Valor de Configuração = nome do seu locatário. Por exemplo: gms<br>Use estes valores para configurar seu Endereço Web:<br>● Chave de Configuração = AppServiceHost<br>● Tipo de valor = String<br>● Valor de Configuração = a URL base para seu locatário. Exemplo: https://www.myworkday.com                              |   |
| Desabilite estas ações para o Workday no iPad e no iPhone:<br>● Recortar, Copiar e Colar<br>● Imprimir                       | Defina o valor (booliano) como False nestas chaves para desabilitar a funcionalidade:<br>● AllowCutCopyPaste<br>● AllowPrint  |
| Desabilite capturas de tela para o Workday no Android. |Defina o valor (booliano) como False na chave AllowScreenshots para desabilitar a funcionalidade.|
| Desabilite as atualizações sugeridas para seus usuários.|Defina o valor (booliano) como False na chave AllowSuggestedUpdates para desabilitar a funcionalidade.|
|Personalize a URL da loja de aplicativos para direcionar os usuários móveis à loja de aplicativos de sua escolha.|Use estes valores para alterar a URL da loja de aplicativos:<br>● Chave de Configuração = AppUpdateURL<br>● Tipo de valor = String<br> ● Valor de Configuração = URL da loja de aplicativos|
|       |


## <a name="ios-configuration-policies"></a>Políticas de configuração do iOS:

1. Acesse https://portal.azure.com/ e faça logon
2. Pesquise **Intune** ou clique no widget na lista.
3. Acesse **Aplicativos Cliente -> Aplicativos -> Políticas de Configuração de Aplicativos -> + Adicionar -> Dispositivos Gerenciados**
4. Insira um nome.
5. Em **Plataforma** , escolha **iOS/iPadOS**
6. Em **Aplicativo Associado** , escolha o aplicativo Workday para iOS que você adicionou
7. Clique em **Definições de Configuração** e, em **Formato de definições de configuração** , escolha **Inserir Dados XML**
8. Veja um arquivo XML de exemplo. Adicione as configurações que você deseja aplicar. Substitua **STRING_VALUE** pela cadeia de caracteres que você deseja usar. Substitua `<true />` ou `<false />` por `<true />` ou `<false />`. Se você não adicionar uma configuração, ela funcionará como se estivesse definida como True.

    ```
    <dict>
    <key>UserGroupCode</key>
    <string>STRING_VALUE</string>
    <key>AppServiceHost</key>
    <string>STRING_VALUE</string>
    <key>AllowCutCopyPaste</key>
    <true /> or <false />
    <key>AllowPrint</key>
    <true /> or <false />
    <key>AllowSuggestedUpdates</key>
    <true /> or <false />
    <key>AppUpdateURL</key>
    <string>STRING_VALUE</string>
    </dict>

    ```
9. Clique em Adicionar
10. Atualize a página e clique na política recém-criada.
11. Clique em Atribuições e escolha a quem você deseja que o aplicativo se aplique.
12. Clique em Salvar.

## <a name="android-configuration-policies"></a>Políticas de Configuração do Android:

1. Acesse `https://portal.azure.com/` e faça logon.
2. Pesquise **Intune** ou clique no widget na lista.
3. Acesse **Aplicativos Cliente -> Aplicativos -> Políticas de Configuração de Aplicativos -> + Adicionar -> Dispositivos Gerenciados**
5. Insira um nome. 
6. Em **Plataforma** , escolha **Android**
7. Em **Aplicativo Associado** , escolha o aplicativo Workday para Android que você adicionou
8. Clique em **Definições de Configuração** e, em **Formato de definições de configuração** , escolha **Inserir Dados JSON**

