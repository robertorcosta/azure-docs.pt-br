---
title: Login sem senha do Azure Active Directory (visualização)
description: Conheça as opções de login sem senha no Azure Active Directory usando chaves de segurança FIDO2 ou o aplicativo Microsoft Authenticator
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 03/23/2020
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: librown
ms.collection: M365-identity-device-management
ms.openlocfilehash: 402eaecbf03fd52fbb5e871fdd196da2bc9a3e1f
ms.sourcegitcommit: bd5fee5c56f2cbe74aa8569a1a5bce12a3b3efa6
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/06/2020
ms.locfileid: "80743528"
---
# <a name="passwordless-authentication-options-for-azure-active-directory"></a>Opções de autenticação sem senha para o Azure Active Directory

A autenticação multifatorial (MFA) é uma ótima maneira de proteger sua organização, mas os usuários muitas vezes ficam frustrados com a camada de segurança adicional além de ter que lembrar suas senhas. Os métodos de autenticação sem senha são mais convenientes porque a senha é removida e substituída por algo que você tem, além de algo que você é ou algo que você sabe.

|   | Algo que você tem | Algo que você é ou sabe |
| --- | --- | --- |
| Sem senha | Dispositivo, telefone ou chave de segurança do Windows 10 | Biométrico ou PIN |

Cada organização tem necessidades diferentes quando se trata de autenticação. A Microsoft oferece as seguintes três opções de autenticação sem senha que se integram ao Azure Active Directory (Azure AD):

- Windows Hello for Business
- Aplicativo Microsoft Authenticator
- Chaves de segurança FIDO2

![Autenticação: Segurança versus conveniência](./media/concept-authentication-passwordless/passwordless-convenience-security.png)

## <a name="windows-hello-for-business"></a>Windows Hello for Business

O Windows Hello for Business é ideal para trabalhadores de informação que têm seu próprio PC Windows designado. O PIN biométrico e biométrico está diretamente ligado ao PC do usuário, o que impede o acesso de qualquer pessoa que não seja o proprietário. Com integração de infra-estrutura de chave pública (PKI) e suporte integrado para logon único (SSO), o Windows Hello for Business fornece um método conveniente para acessar perfeitamente os recursos corporativos no local e na nuvem.

![Exemplo de login de usuário com o Windows Hello for Business](./media/concept-authentication-passwordless/windows-hellow-sign-in.jpeg)

As etapas a seguir mostram como o processo de login funciona com o Azure Active Directory.

![Diagrama que descreve as etapas envolvidas para o login do usuário com o Windows Hello for Business](./media/concept-authentication-passwordless/windows-hello-flow.png)

1. Um usuário entra no Windows usando gesto biométrico ou PIN. O gesto desbloqueia a chave privada do Windows Hello for Business e é enviado para o provedor de suporte de segurança de autenticação em nuvem, conhecido como *provedor de CLOUD AP*.
1. O provedor de Cloud AP solicita um nonce do Azure AD.
1. Azure AD retorna um nonce que é válido por 5 minutos.
1. O provedor de Cloud AP assina o nonce usando a chave privada do usuário e retorna o nonce assinado para o Azure AD.
1. O Azure AD valida o nonce assinado usando a chave pública registrada com segurança do usuário contra a assinatura nonce. Após validar a assinatura, o Azure AD valida o nonce assinado devolvido. Quando o nonce é validado, o Azure AD cria um token de atualização primária (PRT) com chave de sessão que é criptografada para a chave de transporte do dispositivo e a devolve ao provedor de AP da Nuvem.
1. O provedor de CLOUD AP recebe o PRT criptografado com a chave de sessão. Usando a chave de transporte privado do dispositivo, o provedor de Cloud AP descriptografa a chave de sessão e protege a chave de sessão usando o Módulo de Plataforma Confiável (TPM) do dispositivo.
1. O provedor de AP da Nuvem retorna uma resposta de autenticação bem-sucedida ao Windows. O usuário pode então acessar o Windows, bem como aplicativos em nuvem e on-premises sem a necessidade de autenticar novamente (SSO).

O guia de [planejamento](https://docs.microsoft.com/windows/security/identity-protection/hello-for-business/hello-planning-guide) do Windows Hello for Business pode ser usado para ajudá-lo a tomar decisões sobre o tipo de implantação do Windows Hello for Business e as opções que você precisará considerar.

## <a name="microsoft-authenticator-app"></a>Aplicativo Microsoft Authenticator

Permita que o telefone do seu funcionário se torne um método de autenticação sem senha. Você já pode estar usando o Aplicativo Autenticador da Microsoft como uma opção conveniente de autenticação multifatorial, além de uma senha. Você também pode usar o Aplicativo Autenticador como uma opção sem senha.

![Faça login no Microsoft Edge com o aplicativo Microsoft Authenticator](./media/concept-authentication-passwordless/concept-web-sign-in-microsoft-authenticator-app.png)

O Aplicativo Autenticador transforma qualquer telefone iOS ou Android em uma credencial forte e sem senha. Os usuários podem fazer login em qualquer plataforma ou navegador recebendo uma notificação em seu telefone, combinando um número exibido na tela com o do telefone e, em seguida, usando sua biometria (toque ou rosto) ou PIN para confirmar. Consulte [O download e instale o aplicativo Microsoft Authenticator](https://docs.microsoft.com/azure/active-directory/user-help/user-help-auth-app-download-install) para obter detalhes de instalação.

A autenticação sem senha usando o Aplicativo Autenticador segue o mesmo padrão básico do Windows Hello for Business. É um pouco mais complicado, pois o usuário precisa ser identificado para que o Azure AD possa encontrar a versão do Aplicativo autenticador microsoft sendo usada:

![Diagrama que descreve as etapas envolvidas para o login do usuário com o Aplicativo Autenticador da Microsoft](./media/concept-authentication-passwordless/authenticator-app-flow.png)

1. O usuário digita seu nome de usuário.
1. O Azure AD detecta que o usuário tem uma forte credencial e inicia o fluxo de Credencial Forte.
1. Uma notificação é enviada para o aplicativo via Apple Push Notification Service (APNS) em dispositivos iOS ou via Firebase Cloud Messaging (FCM) em dispositivos Android.
1. O usuário recebe a notificação push e abre o aplicativo.
1. O aplicativo chama o Azure AD e recebe um desafio de prova de presença e nonce.
1. O usuário completa o desafio inserindo sua senha ou PIN para desbloquear chave privada.
1. O nonce é assinado com a chave privada e enviado de volta para a Azure AD.
1. O Azure AD realiza validação de chaves públicas/privadas e retorna um token.

## <a name="fido2-security-keys"></a>Chaves de segurança FIDO2

As chaves de segurança FIDO2 são um método de autenticação sem senha baseado em padrões inphishable que pode vir em qualquer fator de forma. Fast Identity Online (FIDO) é um padrão aberto para autenticação sem senha. O FIDO permite que usuários e organizações aproveitem o padrão para fazer login em seus recursos sem um nome de usuário ou senha usando uma chave de segurança externa ou uma chave de plataforma incorporada em um dispositivo.

Para visualização pública, os funcionários podem usar chaves de segurança para entrar em seu Azure AD ou Azure AD híbrido juntou dispositivos Windows 10 e obter um único sinal em seus recursos de nuvem e no local. Os usuários também podem fazer login em navegadores suportados. As chaves de segurança FIDO2 são uma ótima opção para empresas que são muito sensíveis à segurança ou têm cenários ou funcionários que não estão dispostos ou capazes de usar seu telefone como um segundo fator.

![Faça login no Microsoft Edge com uma chave de segurança](./media/concept-authentication-passwordless/concept-web-sign-in-security-key.png)

O processo a seguir é usado quando um usuário faz o sinal com uma chave de segurança FIDO2:

![Diagrama que descreve as etapas envolvidas para o login do usuário com uma chave de segurança FIDO2](./media/concept-authentication-passwordless/fido2-security-key-flow.png)

1. O usuário conecta a chave de segurança FIDO2 em seu computador.
2. O Windows detecta a chave de segurança FIDO2.
3. O Windows envia uma solicitação de autenticação.
4. Azure AD envia de volta um nonce.
5. O usuário completa seu gesto para desbloquear a chave privada armazenada no enclave seguro da chave de segurança FIDO2.
6. A chave de segurança FIDO2 assina o nonce com a chave privada.
7. A solicitação de token de token de atualização primária (PRT) com nonce assinado é enviada ao Azure AD.
8. O Azure AD verifica o nonce assinado usando a chave pública FIDO2.
9. O Azure AD retorna o PRT para permitir o acesso aos recursos locais.

Embora existam muitas chaves certificadas fido2 pela FIDO Alliance, a Microsoft requer algumas extensões opcionais da especificação FIDO2 Client-to-Authenticator Protocol (CTAP) para serem implementadas pelo fornecedor para garantir a máxima segurança e a melhor experiência.

Uma chave de segurança **DEVE** implementar os seguintes recursos e extensões do protocolo FIDO2 CTAP para ser compatível com a Microsoft:

| # | Confiança de recurso / extensão | Por que esse recurso ou extensão é necessário? |
| --- | --- | --- |
| 1 | Chave residente | Esse recurso permite que a chave de segurança seja portátil, onde sua credencial é armazenada na chave de segurança. |
| 2 | Pino do cliente | Esse recurso permite que você proteja suas credenciais com um segundo fator e se aplica a chaves de segurança que não possuem uma interface de usuário. |
| 3 | hmac-secreto | Esta extensão garante que você pode fazer login no seu dispositivo quando ele está off-line ou no modo avião. |
| 4 | Várias contas por RP | Esse recurso garante que você possa usar a mesma chave de segurança em vários serviços, como Microsoft Account e Azure Active Directory. |

Os seguintes provedores oferecem chaves de segurança FIDO2 de diferentes fatores de formulário que são conhecidos por serem compatíveis com a experiência sem senha. Nós encorajamos você a avaliar as propriedades de segurança dessas chaves entrando em contato com o fornecedor, bem como a FIDO Alliance.

| Provedor | Contato |
| --- | --- |
| Yubico | [https://www.yubico.com/support/contact/](https://www.yubico.com/support/contact/) |
| Feitian | [https://www.ftsafe.com/about/Contact_Us](https://www.ftsafe.com/about/Contact_Us) |
| HID | [https://www.hidglobal.com/contact-us](https://www.hidglobal.com/contact-us) |
| Garantia | [https://www.ensurity.com/contact](https://www.ensurity.com/contact) |
| eWBM | [https://www.ewbm.com/support](https://www.ewbm.com/support) |
| AuthenTrend | [https://authentrend.com/about-us/#pg-35-3](https://authentrend.com/about-us/#pg-35-3) |
| Gemalto (Grupo Thales) | [https://safenet.gemalto.com/multi-factor-authentication/authenticators/passwordless-authentication/](https://safenet.gemalto.com/multi-factor-authentication/authenticators/passwordless-authentication/) |
| OneSpan Inc. | [https://www.onespan.com/sites/default/files/2019-08/Digipass-SecureClick_datasheet.pdf](https://www.onespan.com/sites/default/files/2019-08/Digipass-SecureClick_datasheet.pdf) |
| IDmelon Technologies Inc. | [https://www.idmelon.com/#idmelon](https://www.idmelon.com/#idmelon) | 

> [!NOTE]
> Se você comprar e planejar usar chaves de segurança baseadas em NFC, você precisa de um leitor NFC suportado para a chave de segurança. O leitor NFC não é um requisito ou limitação do Azure. Verifique com o fornecedor a chave de segurança baseada em NFC para obter uma lista de leitores NFC suportados.

Se você é um fornecedor e deseja colocar seu dispositivo nesta [Fido2Request@Microsoft.com](mailto:Fido2Request@Microsoft.com)lista de dispositivos suportados, entre em contato .

## <a name="what-scenarios-work-with-the-preview"></a>Que cenários funcionam com a pré-visualização?

- Os administradores podem habilitar métodos de autenticação sem senha para seu inquilino
- Os administradores podem segmentar todos os usuários ou selecionar usuários/grupos dentro de seu inquilino para cada método
- Os usuários finais podem se cadastrar e gerenciar esses métodos de autenticação sem senha em seu portal de conta
- Os usuários finais podem fazer login com esses métodos de autenticação sem senha
   - Microsoft Authenticator App: Funciona em cenários onde a autenticação Azure AD é usada, inclusive em todos os navegadores, durante a configuração do Windows 10 Out Of Box (OOBE) e com aplicativos móveis integrados em qualquer sistema operacional.
   - Chaves de segurança: Trabalhe na tela de bloqueio para o Windows 10 e na Web em navegadores suportados como o Microsoft Edge (tanto o Legado quanto o novo Edge).

## <a name="choose-a-passwordless-method"></a>Escolha um método sem senha

A escolha entre essas três opções sem senha depende da segurança, plataforma e requisitos de aplicativos da sua empresa.

Aqui estão alguns fatores para você considerar ao escolher a tecnologia sem senha da Microsoft:

||**Windows Hello for Business**|**Login sem senha com o aplicativo Microsoft Authenticator**|**Chaves de segurança FIDO2**|
|:-|:-|:-|:-|
|**Pré-requisito**| Windows 10, versão 1809 ou posterior<br>Azure Active Directory| Aplicativo Microsoft Authenticator<br>Telefone (dispositivos iOS e Android rodando Android 6.0 ou superior.)|Windows 10, versão 1809 ou posterior<br>Azure Active Directory|
|**Modo**|Plataforma|Software|Hardware|
|**Sistemas e dispositivos**|PC com um Módulo de Plataforma Confiável (TPM) integrado<br>Reconhecimento de PIN e biometria |Reconhecimento de PIN e biometria no telefone|Dispositivos de segurança FIDO2 compatíveis com a Microsoft|
|**Experiência do usuário**|Faça login usando um PIN ou reconhecimento biométrico (facial, íris ou impressão digital) com dispositivos Windows.<br>A autenticação do Windows Hello está vinculada ao dispositivo; o usuário precisa tanto do dispositivo quanto de um componente de login, como um PIN ou fator biométrico para acessar recursos corporativos.|Faça login usando um celular com digitalização de impressões digitais, reconhecimento facial ou íris, ou PIN.<br>Os usuários fazem login no trabalho ou conta pessoal a partir de seu PC ou celular.|Faça login usando dispositivo de segurança FIDO2 (biometria, PIN e NFC)<br>O usuário pode acessar o dispositivo com base nos controles de organização e autenticar com base em PIN, biometria usando dispositivos como chaves de segurança USB e smartcards, chaves ou wearables habilitados para NFC.|
|**Cenários habilitados**| Experiência sem senha com dispositivo Windows.<br>Aplicável para PC de trabalho dedicado com capacidade para o login único em dispositivos e aplicativos.|Solução sem senha em qualquer lugar usando celular.<br>Aplicável para acessar trabalho ou aplicativos pessoais na web a partir de qualquer dispositivo.|Experiência sem senha para trabalhadores que usam biometria, PIN e NFC.<br>Aplicável para PCs compartilhados e quando um telefone celular não é uma opção viável (como para pessoal do help desk, quiosque público ou equipe hospitalar)|

Use a tabela a seguir para escolher qual método suportará suas necessidades e usuários.

|Persona|Cenário|Ambiente|Tecnologia sem senha|
|:-|:-|:-|:-|
|**Administração**|Acesso seguro a um dispositivo para tarefas de gerenciamento|Dispositivo com Windows 10 atribuído|Chave de segurança Windows Hello for Business e/ou FIDO2|
|**Administração**|Tarefas de gerenciamento em dispositivos não Windows| Dispositivo móvel ou não windows|Login sem senha com o aplicativo Microsoft Authenticator|
|**Operador de informações**|Trabalho de produtividade|Dispositivo com Windows 10 atribuído|Chave de segurança Windows Hello for Business e/ou FIDO2|
|**Operador de informações**|Trabalho de produtividade| Dispositivo móvel ou não windows|Login sem senha com o aplicativo Microsoft Authenticator|
|**Trabalhador da linha de frente**|Quiosques em uma fábrica, fábrica, varejo ou entrada de dados|Dispositivos compartilhados do Windows 10|Chaves de segurança FIDO2|

## <a name="next-steps"></a>Próximas etapas

[Habilite as opções sem senha da chave de segurança FIDO2 em sua organização](howto-authentication-passwordless-security-key.md)

[Habilite opções sem senha baseadas em telefone em sua organização](howto-authentication-passwordless-phone.md)

### <a name="external-links"></a>Links externos

[Aliança FIDO](https://fidoalliance.org/)

[Especificação FIDO2 CTAP](https://fidoalliance.org/specs/fido-v2.0-id-20180227/fido-client-to-authenticator-protocol-v2.0-id-20180227.html)
