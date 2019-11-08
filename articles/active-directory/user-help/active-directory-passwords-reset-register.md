---
title: Registre-se para redefinir sua própria senha-Azure AD
description: Registre seus dados de autenticação para redefinição de senha de autoatendimento do Azure AD, para que você possa redefinir sua própria senha sem a ajuda do administrador.
services: active-directory
author: eross-msft
manager: daveba
ms.reviewer: sahenry
ms.assetid: ''
ms.service: active-directory
ms.subservice: user-help
ms.workload: identity
ms.topic: conceptual
ms.date: 01/11/2018
ms.author: lizross
ms.collection: M365-identity-device-management
ms.openlocfilehash: 796702b2189dcfb001f72913e4a01c0ae01462ad
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/08/2019
ms.locfileid: "73820443"
---
# <a name="register-to-reset-your-own-password"></a>Registre-se para redefinir sua própria senha

> [!IMPORTANT]
> Você está aqui porque não consegue entrar? Se for o caso, confira [Redefinir sua senha corporativa ou de estudante](active-directory-passwords-update-your-own-password.md).

Como um usuário final, você poderá redefinir sua senha ou desbloquear sua conta por conta própria se usar a SSPR (redefinição de senha de autoatendimento) do Azure AD (Azure Active Directory). Antes de usar essa funcionalidade, você precisa registrar os métodos de autenticação ou confirmar os métodos de autenticação predefinidos que o administrador preencheu.

## <a name="register-or-confirm-authentication-data-with-sspr"></a>Registrar ou confirmar dados de autenticação com SSPR

1. Abra o navegador da Web em seu dispositivo e vá para o [página de registro de redefinição de senha](https://aka.ms/ssprsetup).
2. Digite o nome de usuário e a senha fornecida pelo administrador.
3. Dependendo de como sua equipe de TI tiver configurado as coisas, uma ou mais das seguintes opções estarão disponíveis para configuração e verificação. Se o administrador tem sua permissão para usar suas informações, eles podem preencher algumas informações para você.
    * **Telefone comercial**: somente o administrador pode definir essa opção.
    * **Telefone de autenticação**: defina essa opção como outro número de telefone ao qual você tenha acesso. Um exemplo é um telefone celular que pode receber uma mensagem de texto ou uma chamada.
    * **Email de autenticação**: defina essa opção como um endereço de email alternativo que você pode acessar sem a senha que será redefinida.
    * **Perguntas de segurança**: seu administrador aprovou a lista de perguntas a serem respondidas. Você não pode usar a mesma pergunta ou resposta mais de uma vez.
4. Forneça e verifique as informações exigidas pelo seu administrador. Se houver mais de uma opção, sugerimos que você registre vários métodos. Isso dá flexibilidade quando um dos métodos não está disponível. Um exemplo é quando você está viajando e não consegue acessar seu telefone comercial.

    ![Registre os métodos de autenticação e selecione Concluir][Register]

5. Selecione **Concluir**. Agora você pode usar SSPR sempre que precisar.

Se você inserir dados no **telefone** ou **email de autenticação**, eles não ficarão visíveis no diretório global. As únicas pessoas que podem ver esses dados são você e seus administradores. Só você pode ver as respostas para suas perguntas de segurança.

Seus administradores podem exigir que você confirme os métodos de autenticação após um período de tempo para confirmar que os métodos apropriados estão registrados.

## <a name="common-problems-and-their-solutions"></a>problemas comuns e suas soluções

 Aqui estão alguns casos de erro comuns e suas soluções:

| Caso de erro| Que erro você vê?| Solução |
| --- | --- | --- |
| Uma página “Contate seu administrador” é exibida após a inserção da minha ID de usuário | Contate o administrador. <br> <br> Detectamos que a senha da sua conta de usuário não é gerenciada pela Microsoft. Portanto, não podemos redefinir sua senha automaticamente. <br> <br> Entre em contato com sua equipe de TI para obter assistência. | Você está vendo esta mensagem porque a equipe de TI gerencia sua senha no ambiente local e não permite que você redefina sua senha usando o link **Não consegue acessar sua conta**. <br> <br> Para redefinir sua senha, entre em contato com o administrador diretamente para obter ajuda. Informe que deseja redefinir a senha para que eles possam habilitar o recurso para você.|
| Recebo uma mensagem de erro "sua conta não está habilitada para redefinição de senha" depois de inserir a ID de usuário | Sua conta não está habilitada para redefinição de senha. <br> <br> A equipe de TI não configurou sua conta para usar esse serviço. <br> <br> Se desejar, podemos pode contatar um administrador na sua organização para redefinir a senha para você. | Essa mensagem aparece porque a equipe de TI não habilitou a redefinição de senha para a organização usando o link **Não é possível acessar sua conta** ou não licenciou você para usar o recurso. <br> <br> Para redefinir sua senha, selecione o link **Contate um administrador**. Um email será enviado para a equipe de TI da sua empresa. O email informa o desejo de redefinir sua senha no Office 365, para que eles possam habilitar o recurso para você. |
| Recebo uma mensagem de erro "não foi possível verificar sua conta" depois de inserir a ID de usuário | Não foi possível verificar sua conta. <br> <br> Se desejar, podemos pode contatar um administrador na sua organização para redefinir a senha para você. | A mensagem aparece porque a redefinição de senha está habilitada, mas você não se registrou para usar o serviço. Para se registrar na redefinição de senha, vá para a [página de registro de redefinição de senha](https://aka.ms/ssprsetup) depois de recuperar o acesso à sua conta. <br> <br> Para redefinir a senha, selecione o link **Contate um administrador** e envie um email à equipe de TI da empresa. |

## <a name="next-steps"></a>Próximas etapas

* [Alterar a senha usando a redefinição de senha de autoatendimento](active-directory-passwords-update-your-own-password.md)
* [Página de registro de redefinição de senha](https://aka.ms/ssprsetup)
* [Portal de redefinição de senha:](https://passwordreset.microsoftonline.com/)
* [Quando você não consegue acessar a sua conta da Microsoft](https://support.microsoft.com/help/12429/microsoft-account-sign-in-cant)

[Register]: ./media/active-directory-passwords-reset-register/register-2-methods.png "Página de registro de redefinição de senha mostrando os métodos registrados e o botão Concluir"

