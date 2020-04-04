---
title: Login de chave de segurança sem senha (visualização) - Azure Active Directory
description: Habilite o login da chave de segurança sem senha no Azure AD usando chaves de segurança FIDO2 (visualização)
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: how-to
ms.date: 02/12/2020
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: librown, aakapo
ms.collection: M365-identity-device-management
ms.openlocfilehash: b8574fcdff12e61f7039174ed6297d0558a66dc4
ms.sourcegitcommit: 62c5557ff3b2247dafc8bb482256fef58ab41c17
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/03/2020
ms.locfileid: "80653933"
---
# <a name="enable-passwordless-security-key-sign-in-preview"></a>Habilite o login de chave de segurança sem senha (visualização)

Para empresas que usam senhas hoje e têm um ambiente de PC compartilhado, as chaves de segurança fornecem uma maneira perfeita para os trabalhadores autenticarem sem digitar um nome de usuário ou senha. As chaves de segurança proporcionam maior produtividade para os trabalhadores e têm melhor segurança.

Este documento se concentra em habilitar a autenticação sem senha baseada em chave de segurança. No final deste artigo, você poderá fazer login em aplicativos baseados na Web com sua conta Azure AD usando uma chave de segurança FIDO2.

|     |
| --- |
| As chaves de segurança FIDO2 são um recurso de visualização pública do Azure Active Directory. Para obter mais informações sobre visualizações, consulte [Termos de Uso Suplementares para Visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)|
|     |

## <a name="requirements"></a>Requisitos

- [Autenticação Multifator do Azure](howto-mfa-getstarted.md)
- [Visualização combinada de registro de informações de segurança](concept-registration-mfa-sspr-combined.md)
- Chaves de [segurança FIDO2 compatíveis](concept-authentication-passwordless.md#fido2-security-keys)
- WebAuthN requer windows 10 versão 1809 ou superior**

Para usar as chaves de segurança para fazer login em aplicativos e serviços da Web, você deve ter um navegador que suporte o protocolo WebAuthN. Estes incluem Microsoft Edge, Chrome, Firefox e Safari.

## <a name="prepare-devices-for-preview"></a>Preparar dispositivos para visualização

O Azure AD juntou-se aos dispositivos com os que você pilota devem executar o Windows 10 versão 1809 ou superior. A melhor experiência está no Windows 10 versão 1903 ou superior.

Os dispositivos azure ad híbridos devem executar o Windows 10 Insider Build 18945 ou mais novo.

## <a name="enable-passwordless-authentication-method"></a>Habilite o método de autenticação sem senha

### <a name="enable-the-combined-registration-experience"></a>Habilite a experiência de registro combinada

Os recursos de registro para métodos de autenticação sem senha dependem da visualização combinada do registro. Siga as etapas do artigo [Habilite o registro combinado de informações de segurança (pré-visualização)](howto-registration-mfa-sspr-combined.md), para habilitar a visualização combinada do registro.

### <a name="enable-fido2-security-key-method"></a>Habilite o método-chave de segurança FIDO2

1. Entre no [portal do Azure](https://portal.azure.com).
1. Procurar os**Security** > **métodos** > de autenticação do método de autenticação do diretório ativo do >  **Azure****(Preview)**.
1. No método **FIDO2 Security Key,** escolha as seguintes opções:
   1. **Habilitar** - Sim ou Não
   1. **Destino** - Todos os usuários ou usuários selecionados
1. **Salve** a configuração.

## <a name="user-registration-and-management-of-fido2-security-keys"></a>Registro e gerenciamento de chaves de segurança FIDO2

1. Navegue [https://myprofile.microsoft.com](https://myprofile.microsoft.com)para .
1. Faça login se ainda não.
1. Clique **em Informações de Segurança**.
   1. Se o usuário já tiver pelo menos um método de autenticação multifatorial do Azure registrado, ele poderá registrar imediatamente uma chave de segurança FIDO2.
   1. Se eles não tiverem pelo menos um método de autenticação multifatorial do Azure registrado, eles devem adicionar um.
1. Adicione uma chave fido2 de segurança clicando em **Adicionar método** e escolhendo **a tecla Segurança**.
1. Escolha **dispositivo USB** ou dispositivo **NFC**.
1. Tenha sua chave pronta e escolha **Next**.
1. Uma caixa aparecerá e pedirá ao usuário para criar/inserir um PIN para sua chave de segurança e, em seguida, executar o gesto necessário para a chave, biométrica ou toque.
1. O usuário será devolvido à experiência de registro combinado e solicitado a fornecer um nome significativo para a chave para que o usuário possa identificar qual se tiver múltiplos. Clique em **Próximo**.
1. Clique **em Concluído** para concluir o processo.

## <a name="sign-in-with-passwordless-credential"></a>Faça login com credencial sem senha

No exemplo abaixo, um usuário já provisionou sua chave de segurança FIDO2. O usuário pode optar por entrar na web com sua chave de segurança FIDO2 dentro de um navegador suportado na versão 1809 do Windows 10 ou superior.

![Login de chave de segurança do Microsoft Edge](./media/howto-authentication-passwordless-security-key/fido2-windows-10-1903-edge-sign-in.png)

## <a name="troubleshooting-and-feedback"></a>Solução de problemas e feedback

Se você quiser compartilhar feedback ou encontrar problemas durante a visualização deste recurso, compartilhe através do aplicativo Windows Feedback Hub usando as seguintes etapas:

1. Inicie **o Feedback Hub** e certifique-se de que você está conectado.
1. Enviar feedback sob a seguinte categorização:
   - Categoria: Segurança e Privacidade
   - Subcategoria: FIDO
1. Para capturar logs, use a opção para **recriar meu problema**

## <a name="known-issues"></a>Problemas conhecidos

### <a name="security-key-provisioning"></a>Provisionamento de chaves de segurança

O provisionamento e o desprovisionamento das chaves de segurança do administrador não estão disponíveis na visualização pública.

### <a name="upn-changes"></a>Alterações de UPN

Estamos trabalhando no suporte a um recurso que permite que a UPN mude no Ad híbrido Azure aderido e a adesão ao Azure AD. Se o UPN de um usuário for alterado, você não poderá mais modificar as chaves de segurança FIDO2 para explicar a alteração. A resolução é redefinir o dispositivo e o usuário tem que recadastrar.

## <a name="next-steps"></a>Próximas etapas

[Chave de segurança FIDO2 Windows 10 entrar](howto-authentication-passwordless-security-key-windows.md)

[Habilite a autenticação FIDO2 para recursos locais](howto-authentication-passwordless-security-key-on-premises.md)

[Saiba mais sobre o registro de dispositivos](../devices/overview.md)

[Saiba mais sobre a Autenticação Multifator do Azure](../authentication/howto-mfa-getstarted.md)
