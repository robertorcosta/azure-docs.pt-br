---
title: Adicionar entrada com a conta da Microsoft a um aplicativo Web Python da plataforma de identidade da Microsoft | Azure
description: Saiba como implementar a opção Entrar com a conta da Microsoft em um aplicativo Web do Python usando o OAuth2
services: active-directory
author: abhidnya13
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: quickstart
ms.workload: identity
ms.date: 09/25/2019
ms.author: abpati
ms.custom: aaddev
ms.openlocfilehash: 0affae56ef6998efe4bb370287ff3688f83f3878
ms.sourcegitcommit: 2d7910337e66bbf4bd8ad47390c625f13551510b
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/08/2020
ms.locfileid: "80873949"
---
# <a name="quickstart-add-sign-in-with-microsoft-to-a-python-web-app"></a>Início Rápido: Adicionar a opção Entrar com a conta da Microsoft a um aplicativo Web do Python

Neste início rápido, você aprenderá a integrar um aplicativo Web do Python à plataforma de identidade da Microsoft. Seu aplicativo conectará um usuário, obterá um token de acesso para chamar a API do Microsoft Graph e fará uma solicitação para a API do Microsoft Graph.

Após concluir este guia, seu aplicativo aceitará conexões de contas Microsoft pessoais (incluindo outlook.com, live.com e outras) e contas corporativas ou de estudante de qualquer empresa ou organização que utilize o Azure Active Directory. (Confira [Como o exemplo funciona](#how-the-sample-works) para ver uma ilustração.)


## <a name="prerequisites"></a>Pré-requisitos

Para executar esta amostra, você precisará do seguinte:

- [Python 2.7 e posterior](https://www.python.org/downloads/release/python-2713) ou [Python 3 e posterior](https://www.python.org/downloads/release/python-364/)
- [Flask](http://flask.pocoo.org/), [Flask-Session](https://pythonhosted.org/Flask-Session/), [solicitações](https://requests.kennethreitz.org/en/master/)
- [MSAL Python](https://github.com/AzureAD/microsoft-authentication-library-for-python)

> [!div renderon="docs"]
>
> ## <a name="register-and-download-your-quickstart-app"></a>Registrar e baixar o aplicativo de início rápido
>
> Você tem duas opções para iniciar o aplicativo de início rápido: expresso (opção 1) ou manual (opção 2)
>
> ### <a name="option-1-register-and-auto-configure-your-app-and-then-download-your-code-sample"></a>Opção 1: Registrar e configurar o aplicativo automaticamente e, em seguida, baixar seu exemplo de código
>
> 1. Acesse o [portal do Azure – Registros de aplicativo](https://portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/applicationsListBlade/quickStartType/PythonQuickstartPage/sourceType/docs).
> 1. Insira um nome para seu aplicativo e selecione **Registrar**.
> 1. Siga as instruções para baixar e configurar automaticamente o novo aplicativo.
>
> ### <a name="option-2-register-and-manually-configure-your-application-and-code-sample"></a>Opção 2: Registrar e configurar manualmente o aplicativo e o exemplo de código
>
> #### <a name="step-1-register-your-application"></a>Etapa 1: Registre seu aplicativo
>
> Para registrar seu aplicativo e adicionar as informações de registro do aplicativo à solução manualmente, siga estas etapas:
>
> 1. Entre no [portal do Azure](https://portal.azure.com) usando uma conta corporativa ou de estudante ou uma conta pessoal da Microsoft.
> 1. Se sua conta fornecer acesso a mais de um locatário, selecione sua conta no canto superior direito e defina sua sessão do portal para o locatário desejado do Azure AD.
> 1. Navegue até a página [Registros de aplicativo](https://go.microsoft.com/fwlink/?linkid=2083908) da plataforma de identidade da Microsoft para desenvolvedores.
> 1. Selecione **Novo registro**.
> 1. Quando a página **Registrar um aplicativo** for exibida, insira as informações de registro do aplicativo:
>      - Na seção **Nome**, insira um nome de aplicativo relevante que será exibido aos usuários do aplicativo, por exemplo, `python-webapp`.
>      - Em **Tipos de conta com suporte**, selecione **Contas em qualquer diretório organizacional e contas pessoais da Microsoft**.
>      - Selecione **Registrar**.
>      - Na página **Visão geral** do aplicativo, anote o valor de **ID do aplicativo (cliente)** para uso posterior.
> 1. Selecione **Autenticação** no menu e, em seguida, adicione as seguintes informações:
>    - Adicione a configuração **Web** da plataforma. Adicione `http://localhost:5000/getAToken` como **URIs de Redirecionamento**.
>    - Clique em **Salvar**.
> 1. No menu à esquerda, escolha **Certificados e segredos** e clique em **Novo segredo do cliente** na seção **Segredos do Cliente**:
>
>      - Digite uma descrição de chave (do segredo do aplicativo da instância).
>      - Selecione uma duração de chave igual a **Em 1 ano**.
>      - Quando você clicar em **Adicionar**, o valor da chave será exibido.
>      - Copie o valor da chave. Você precisará dela mais tarde.
> 1. Selecione a seção **Permissões da API**
>
>      - Clique no botão **Adicionar uma permissão** e, em seguida,
>      - Verifique se a guia **APIs da Microsoft** está selecionada
>      - Na seção *APIs da Microsoft frequentemente utilizadas*, clique em **Microsoft Graph**
>      - Na seção **Permissões delegadas**, certifique-se de que as permissões corretas estejam marcadas: **User.ReadBasic.All**. Use a caixa de pesquisa, se necessário.
>      - Selecione o botão **Adicionar permissões**
>
> [!div class="sxs-lookup" renderon="portal"]
>
> #### <a name="step-1-configure-your-application-in-azure-portal"></a>Etapa 1: Configurar seu aplicativo no portal do Azure
>
> Para o exemplo de código deste início rápido funcionar, você precisará:
>
> 1. Adicione uma URL de resposta como `http://localhost:5000/getAToken`.
> 1. Criar um segredo do cliente.
> 1. Adicione a permissão delegada User.ReadBasic.All da API do Microsoft Graph.
>
> > [!div renderon="portal" id="makechanges" class="nextstepaction"]
> > [Fazer essas alterações para mim]()
> > [!div id="appconfigured" class="alert alert-info"]
> > ![Já configurado](media/quickstart-v2-aspnet-webapp/green-check.png) Seu aplicativo já está configurado com esse atributo

#### <a name="step-2-download-your-project"></a>Etapa 2: Baixar o seu projeto
> [!div renderon="docs"]
> [Baixe o exemplo de código](https://github.com/Azure-Samples/ms-identity-python-webapp/archive/master.zip)

> [!div class="sxs-lookup" renderon="portal"]
> Baixe o projeto e extraia o arquivo zip para uma pasta local mais próxima da pasta raiz, por exemplo, **C:\Azure-Samples**
> [!div renderon="portal" id="autoupdate" class="nextstepaction"]
> [Baixe o exemplo de código](https://github.com/Azure-Samples/ms-identity-python-webapp/archive/master.zip)

> [!div class="sxs-lookup" renderon="portal"]
> > [!NOTE]
> > `Enter_the_Supported_Account_Info_Here`

> [!div renderon="docs"]
> #### <a name="step-3-configure-the-application"></a>Etapa 3: Configurar o aplicativo
>
> 1. Extraia o arquivo zip para uma pasta local mais próxima da pasta raiz, por exemplo, **C:\Azure-Samples**
> 1. Se você usar um ambiente de desenvolvimento integrado, abra a amostra em seu IDE favorito (opcional).
> 1. Abra o arquivo **app_config.py**, que pode ser encontrado na pasta raiz, e substitua-o pelo seguinte snippet de código:
>
> ```python
> CLIENT_ID = "Enter_the_Application_Id_here"
> CLIENT_SECRET = "Enter_the_Client_Secret_Here"
> AUTHORITY = "https://login.microsoftonline.com/Enter_the_Tenant_Name_Here"
> ```
> Em que:
>
> - `Enter_the_Application_Id_here`: é a ID do aplicativo que você registrou.
> - `Enter_the_Client_Secret_Here` – é o **Segredo do Cliente** criado em **Certificados e Segredos** para o aplicativo registrado.
> - `Enter_the_Tenant_Name_Here` – é o valor da **ID do Diretório (locatário)** do aplicativo que você registrou.

> [!div class="sxs-lookup" renderon="portal"]
> #### <a name="step-3-run-the-code-sample"></a>Etapa 3: Executar o exemplo de código

> [!div renderon="docs"]
> #### <a name="step-4-run-the-code-sample"></a>Etapa 4: Executar o exemplo de código

1. Você precisará instalar a biblioteca MSAL Python, a estrutura Flask, o Flask-Sessions para gerenciamento de sessão do servidor e solicitações usando o PIP da seguinte maneira:

    ```Shell
    pip install -r requirements.txt
    ```

2. Execute app.py no shell ou na linha de comando:

    ```Shell
    python app.py
    ```
   > [!IMPORTANT]
   > Este aplicativo de início rápido usa um segredo do cliente para se identificar como cliente confidencial. Como o segredo do cliente é adicionado como texto sem formatação a seus arquivos de projeto, por motivos de segurança, é recomendável que você use um certificado, em vez de um segredo do cliente, antes de considerar o aplicativo como aplicativo de produção. Para saber mais sobre como usar um certificado, confira [estas instruções](https://docs.microsoft.com/azure/active-directory/develop/active-directory-certificate-credentials).

## <a name="more-information"></a>Mais informações

### <a name="how-the-sample-works"></a>Como o exemplo funciona
![Mostra como o aplicativo de exemplo gerado por este início rápido funciona](media/quickstart-v2-python-webapp/python-quickstart.svg)

### <a name="getting-msal"></a>Como obter o MSAL
A MSAL é a biblioteca usada para conectar usuários e solicitar tokens usados para acessar uma API protegida pela plataforma de identidade da Microsoft.
Você pode adicionar a MSAL Python ao seu aplicativo usando o PIP.

```Shell
pip install msal
```

### <a name="msal-initialization"></a>Inicialização da MSAL
Adicione a referência à MSAL Python adicionando o seguinte código ao início do arquivo no qual você usará a MSAL:

```Python
import msal
```

## <a name="next-steps"></a>Próximas etapas

Saiba mais sobre aplicativos Web que conectam usuários e, em seguida, chamam APIs Web:

> [!div class="nextstepaction"]
> [Cenário: aplicativos Web que conectam usuários](scenario-web-app-sign-user-overview.md)

[!INCLUDE [Help and support](../../../includes/active-directory-develop-help-support-include.md)]
