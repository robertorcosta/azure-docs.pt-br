---
title: 'Tutorial: Integração do Azure Active Directory com o Amazon Web Services (AWS) para conectar várias contas | Microsoft Docs'
description: Saiba como configurar o logon único entre o Microsoft Azure AD e várias contas do Amazon Web Services (AWS).
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 7561c20b-2325-4d97-887f-693aa383c7be
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/07/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: cb528d71b94449b282947a487e4fc79b343df778
ms.sourcegitcommit: 8e31a82c6da2ee8dafa58ea58ca4a7dd3ceb6132
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/19/2019
ms.locfileid: "74195896"
---
# <a name="tutorial-azure-active-directory-integration-with-multiple-amazon-web-services-aws-accounts"></a>Tutorial: Integração do Azure Active Directory com várias contas Amazon Web Services (AWS)

Neste tutorial, você aprenderá a integrar o Azure Active Directory (Microsoft Azure AD) a várias contas do AWS (Amazon Web Services).

A integração do AWS (Amazon Web Services) ao Azure AD oferece os seguintes benefícios:

- Você pode controlar no Microsoft Azure AD quem tem acesso ao AWS (Amazon Web Services).
- Você pode habilitar seus usuários a fazerem logon automaticamente no AWS (Amazon Web Services) (logon único) com suas contas do Microsoft Azure AD.
- Você pode gerenciar suas contas em um único local central – o portal do Azure.

Para conhecer mais detalhadamente a integração de aplicativos de SaaS ao Azure AD, consulte [o que é o acesso a aplicativos e logon único com o Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

![AWS (Amazon Web Services) na lista de resultados](./media/aws-multi-accounts-tutorial/amazonwebservice.png)

>[!NOTE]
>Observe que conectar um aplicativo da AWS a todas as suas contas da AWS não é nossa abordagem recomendada. Em vez disso, recomendamos que você use [essa](https://docs.microsoft.com/azure/active-directory/saas-apps/amazon-web-service-tutorial) abordagem para configurar várias instâncias da conta da AWS para Várias instâncias de aplicativos da AWS no Azure AD.

**Tenha em atenção que não recomendamos a utilização desta abordagem pelas seguintes razões:**

* Você tem que usar a abordagem Graph Explorer para corrigir todas as funções do aplicativo. Não recomendamos o uso da abordagem de arquivo de manifesto.

* Vimos clientes relatando que, depois de adicionar ~ 1200 funções de aplicativos para um único aplicativo da AWS, qualquer operação no aplicativo começou a gerar erros relacionados ao tamanho. Há um limite rígido de tamanho no objeto de aplicativo.

* Você precisa atualizar manualmente a função à medida que as funções forem adicionadas em qualquer uma das contas, que é uma abordagem Substituir e não Anexar infelizmente. Além disso, se suas contas estiverem crescendo, isso se tornará n x n relacionamento com contas e funções.

* Todas as contas da AWS usarão o mesmo arquivo XML de Metadados da Federação e, no momento da substituição do certificado, você terá que realizar esse exercício gigantesco para atualizar o certificado em todas as contas da AWS ao mesmo tempo

## <a name="prerequisites"></a>pré-requisitos

Para configurar a integração do Azure AD com o AWS (Amazon Web Services), você precisa dos seguintes itens:

* Uma assinatura do Azure AD. Se não tiver um ambiente do Azure AD, você pode obter uma versão de avaliação de um mês [aqui](https://azure.microsoft.com/pricing/free-trial/)
* Uma assinatura habilitada para logon único da AWS (Amazon Web Services)

> [!NOTE]
> Para testar as etapas deste tutorial, nós não recomendamos o uso de um ambiente de produção.

Para testar as etapas deste tutorial, você deve seguir estas recomendações:

- Não use o ambiente de produção, a menos que seja necessário.
- Se não tiver um ambiente de avaliação do Azure AD, você pode [obter uma versão de avaliação de um mês](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o logon único do Azure AD em um ambiente de teste.

* A AWS (Amazon Web Services) dá suporte ao SSO iniciado por **SP e IDP**

## <a name="adding-amazon-web-services-aws-from-the-gallery"></a>Adicionar o AWS (Amazon Web Services) da galeria

Para configurar a integração do AWS (Amazon Web Services) com o Azure AD, você precisa adicionar o AWS (Amazon Web Services), por meio da galeria, à sua lista de aplicativos de SaaS gerenciados.

**Para adicionar o AWS (Amazon Web Services) da galeria, execute as seguintes etapas:**

1. No **[Portal do Azure](https://portal.azure.com)** , no painel navegação à esquerda, clique no ícone **Azure Active Directory**.

    ![O botão Azure Active Directory](common/select-azuread.png)

2. Navegue até **Aplicativos Empresariais** e, em seguida, selecione a opção **Todos os Aplicativos**.

    ![A folha Aplicativos empresariais](common/enterprise-applications.png)

3. Clique no botão **Novo aplicativo** na parte superior da caixa de diálogo para adicionar o novo aplicativo.

    ![O botão Novo aplicativo](common/add-new-app.png)

4. Na caixa de pesquisa, digite **AWS (Amazon Web Services)** , selecione **AWS (Amazon Web Services)** no painel de resultados e clique no botão **Adicionar** para adicionar o aplicativo.

     ![AWS (Amazon Web Services) na lista de resultados](common/search-new-app.png)

5. Depois que o aplicativo for adicionado, vá para a página **Propriedades** e copie a **ID de Objeto**.

    ![AWS (Amazon Web Services) na lista de resultados](./media/aws-multi-accounts-tutorial/tutorial_amazonwebservices(aws)_properties.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar logon único do Azure AD

Nesta seção, você vai configurar e testar o logon único do Azure AD com o AWS (Amazon Web Services), com base em um usuário de teste chamado "Brenda Fernandes".

Para que o logon único funcione, o Azure AD precisa saber qual usuário do AWS (Amazon Web Services) é equivalente a um usuário do Azure AD. Em outras palavras, uma relação de link entre um usuário do Azure AD e o usuário relacionado no serviço AWS (Amazon Web Services) deve ser estabelecida.

No Amazon Web Services (AWS), atribua o valor do **nome de usuário** no Microsoft Azure AD como o valor do **Nome de usuário** para estabelecer a relação de vínculo.

Para configurar e testar o logon único do Azure AD com o AWS (Amazon Web Services), você precisa concluir os seguintes blocos de construção:

1. **[Configurar o logon único do Azure AD](#configure-azure-ad-single-sign-on)** – para habilitar seus usuários a usar esse recurso.
2. **[Configurar o logon único da AWS (Amazon Web Services)](#configure-amazon-web-services-aws-single-sign-on)** – para definir as configurações de Logon Único no lado do aplicativo.
3. **[Teste o logon único](#test-single-sign-on)** – para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar o logon único do Azure AD

Nesta seção, você vai habilitar o logon único do Azure AD no Portal do Azure e configurar o logon único em seu aplicativo AWS (Amazon Web Services).

**Para configurar o logon único do Azure AD com o AWS (Amazon Web Services), execute as seguintes etapas:**

1. No [Portal do Azure](https://portal.azure.com/), na página de integração de aplicativos **Amazon Web Services (AWS)** , selecione **Logon único**.

    ![Link Configurar logon único](common/select-sso.png)

2. Na caixa de diálogo **Selecionar um método de logon único**, selecione o modo **SAML/WS-Fed** para habilitar o logon único.

    ![Modo de seleção de logon único](common/select-saml-option.png)

3. Na página **Definir logon único com SAML**, clique no ícone **Editar** para abrir a caixa de diálogo **Configuração básica do SAML**.

    ![Editar a Configuração Básica de SAML](common/edit-urls.png)

4. Na seção **Configuração básica do SAML**, o usuário não precisa executar nenhuma etapa porque o aplicativo já está pré-integrado ao Azure.

    ![imagem](common/preintegrated.png)

5. O aplicativo Amazon Web Services (AWS) espera as asserções SAML em um formato específico. Configure as declarações a seguir para este aplicativo. Você pode gerenciar os valores desses atributos na seção **Atributos e reivindicações do usuário** na página de integração de aplicativos. Na página **Configurar logon único com SAML**, clique no botão **Editar** para abrir a caixa de diálogo **User Attributes & Claims**.

    ![imagem](common/edit-attribute.png)

6. Na seção **Declarações de Usuário** do diálogo **Atributos de Usuário**, configure o atributo de token SAML conforme mostrado na imagem acima e execute as seguintes etapas:

    | NOME  | Atributo de Origem  | Namespace |
    | --------------- | --------------- | --------------- |
    | RoleSessionName | user.userprincipalname | https://aws.amazon.com/SAML/Attributes |
    | Função            | user.assignedroles |  https://aws.amazon.com/SAML/Attributes |
    | SessionDuration             | "fornecer um valor entre 900 segundos (15 minutos) para segundos 43200 (12 horas)" |  https://aws.amazon.com/SAML/Attributes |

    a. Clique em **Adicionar nova reivindicação** para abrir a caixa de diálogo **Gerenciar declarações de usuários**.

    ![imagem](common/new-save-attribute.png)

    ![imagem](common/new-attribute-details.png)

    b. Na caixa de texto **Nome** , digite o nome do atributo mostrado para essa linha.

    c. Na caixa de texto **Namespace**, digite o valor de namespace mostrado para essa linha.

    d. Escolha Origem como **Atributo**.

    e. Na lista **Atributo de origem**, digite o valor do atributo mostrado para essa linha.

    f. Clique em **Ok**

    g. Clique em **Salvar**.

7. Na página **Configurar logon único com SAML**, na seção **Certificado de assinatura SAML**, clique em **Download** para fazer o download do **XML de metadados de federação** e salve-o no seu computador.

    ![O link de download do Certificado](common/metadataxml.png)

### <a name="configure-amazon-web-services-aws-single-sign-on"></a>Configurar logon único da AWS (Amazon Web Services)

1. Em uma janela de navegador diferente, entre no site de sua empresa do AWS (Amazon Web Services) como administrador.

2. Clique na **Página inicial do AWS**.

    ![Configurar página inicial de logon único][11]

3. Clique em **Gerenciamento de identidades e acesso**.

    ![Configurar identidade de logon único][12]

4. Clique em **Provedores de identidade**, e, em seguida, clique em **Criar provedor**.

    ![Configurar provedor de logon único][13]

5. Na página da caixa de diálogo **Configurar provedor** , execute as seguintes etapas:

    ![Configurar diálogo logon único][14]

    a. Como **Tipo de provedor**, selecione **SAML**.

    b. Na caixa de texto **Nome do provedor**, digite um nome de provedor (por exemplo: *WAAD*).

    c. Para carregar seu **arquivo de metadados** baixado do Portal do Azure, clique em **Escolher Arquivo**.

    d. Clique em **Próxima etapa**.

6. Na página de diálogo **Verificar informações do provedor**, clique em **Criar**.

    ![Configurar verificação de logon único][15]

7. Clique em **Funções** e, em seguida, clique em **Criar função**.

    ![Configurar funções de logon único][16]

8. Na página **Criar função**, realize as seguintes etapas:  

    ![Configurar confiança de logon único][19]

    a. Selecione **Federação do SAML 2.0** em **Selecionar tipo de entidade confiável**.

    b. Em **Escolher uma seção do provedor do SAML 2.0**, selecione o **provedor do SAML** criado anteriormente (por exemplo: *WAAD*)

    c. Selecione **Permitir acesso do Console de Gerenciamento do AWS e programação**.
  
    d. Clique em **Próximo: Permissões**.

9. Na caixa de diálogo **Anexar políticas de permissão**, anexe a política adequada conforme sua organização. Clique em **Próximo: Revisão**.  

    ![Configurar política de logon único][33]

10. Na caixa de diálogo **Examinar** , execute as seguintes etapas:

    ![Configurar revisão de logon único][34]

    a. Na caixa de texto **Nome da função**, insira o nome da função.

    b. Na caixa de texto **Descrição da função**, insira a descrição.

    c. Clique em **Criar função**.

    d. Crie quantas funções forem necessárias e mapeie-as para o Provedor de Identidade.

11. Saia da atual conta AWS e faça logon com outra conta em que você deseja configurar o logon único com o Microsoft Azure AD.

12. Execute as etapas de 2 a 10 para criar várias funções que você deseja configurar para esta conta. Se você tiver mais de duas contas, execute as mesmas etapas para todas as contas criarem funções para elas.

13. Depois que todas as funções são criadas nas contas, elas aparecem na lista **Funções** para essas contas.

    ![Instalação de funções](./media/aws-multi-accounts-tutorial/tutorial_amazonwebservices(aws)_listofroles.png)

14. É necessário capturar todas as Funções ARN e Entidades Confiáveis para todas as funções através de todas as contas, que pode ser necessário mapear manualmente com o aplicativo Microsoft Azure AD.

15. Clique nas funções para copiar os valores da **Função ARN** e das **Entidades Confiáveis**. Esses valores são necessários para todas as funções que você precisa criar no Microsoft Azure AD.

    ![Instalação de funções](./media/aws-multi-accounts-tutorial/tutorial_amazonwebservices(aws)_role_summary.png)

16. Execute a etapa acima para todas as funções em todas as contas e armazene todas elas em formato **de Função ARN, Entidades Confiáveis** em um bloco de notas.

17. Abra o [Explorador do Graph do Microsoft Azure AD](https://developer.microsoft.com/graph/graph-explorer) em outra janela.

    a. Entre no site do Explorador do Graph usando as credenciais de Administrador/Coadministrador globais para o locatário.

    b. Você precisa ter permissões suficientes para criar as funções. Clique em **modificar permissões** para obter as permissões necessárias.

    ![Caixa de diálogo do explorador do Graph](./media/aws-multi-accounts-tutorial/graph-explorer-new9.png)

    c. Selecione as permissões da lista (se você já não tiver) a seguir e clique em "Modificar Permissões" 

    ![Caixa de diálogo do explorador do Graph](./media/aws-multi-accounts-tutorial/graph-explorer-new10.png)

    d. Isso pedirá para fazer logon novamente e aceitar o consentimento. Depois de aceitar o consentimento, você estará logado no Explorador do Graph novamente.

    e. Alterar a lista suspensa de versão para **beta**. Para buscar todos as Entidades de Serviço a partir do seu locatário, use a consulta a seguir:

    `https://graph.microsoft.com/beta/servicePrincipals`

    Se você estiver usando vários diretórios, você poderá usar o padrão a seguir, que tem o seu domínio primário nele`https://graph.microsoft.com/beta/contoso.com/servicePrincipals`

    ![Caixa de diálogo do explorador do Graph](./media/aws-multi-accounts-tutorial/graph-explorer-new1.png)

    f. Da lista de Entidades de Serviço buscada, obtenha o que é necessário modificar. Você também pode usar o Ctrl+F para pesquisar o aplicativo de todos os ServicePrincipals listados. Você pode usar a consulta a seguir usando a **ID de objeto** que você copiou da página Propriedades do Microsoft Azure AD e usar a consulta a seguir para acessar a respectiva Entidade de Serviço.

    `https://graph.microsoft.com/beta/servicePrincipals/<objectID>`.

    ![Caixa de diálogo do explorador do Graph](./media/aws-multi-accounts-tutorial/graph-explorer-new2.png)

    g. Extraia a propriedade appRoles do objeto da entidade de serviço.

    ![Caixa de diálogo do explorador do Graph](./media/aws-multi-accounts-tutorial/graph-explorer-new3.png)

    h. Agora, é necessário gerar novas funções para aplicativo. 

    i. Abaixo, um exemplo do objeto appRoles. Crie um objeto semelhante para adicionar as funções que você deseja para o aplicativo.

    ```
    {
    "appRoles": [
        {
            "allowedMemberTypes": [
                "User"
            ],
            "description": "msiam_access",
            "displayName": "msiam_access",
            "id": "7dfd756e-8c27-4472-b2b7-38c17fc5de5e",
            "isEnabled": true,
            "origin": "Application",
            "value": null
        },
        {
            "allowedMemberTypes": [
                "User"
            ],
            "description": "Admin,WAAD",
            "displayName": "Admin,WAAD",
            "id": "4aacf5a4-f38b-4861-b909-bae023e88dde",
            "isEnabled": true,
            "origin": "ServicePrincipal",
            "value": "arn:aws:iam::12345:role/Admin,arn:aws:iam::12345:saml-provider/WAAD"
        },
        {
            "allowedMemberTypes": [
                "User"
            ],
            "description": "Auditors,WAAD",
            "displayName": "Auditors,WAAD",
            "id": "bcad6926-67ec-445a-80f8-578032504c09",
            "isEnabled": true,
            "origin": "ServicePrincipal",
            "value": "arn:aws:iam::12345:role/Auditors,arn:aws:iam::12345:saml-provider/WAAD"
        }    ]
    }
    ```

    > [!Note]
    > Você pode apenas adicionar as novas funções depois do **msiam_access** para a operação de patch. Além disso, você pode adicionar quantas regras quiser de acordo com a necessidade da sua Organização. O Microsoft Azure AD enviará o **valor** dessas funções conforme o valor de solicitação na resposta SAML.

    j. Volte ao Explorador Graph e altere o método de **GET** para **PATCH**. Atualize o objeto da Entidade de Serviço para ter as funções desejadas, atualizando a propriedade appRoles semelhante àquela exibida acima no exemplo. Clique em **Executar Consulta** para executar o operação de patch. Uma mensagem confirma a criação da função do seu aplicativo Amazon Web Services.

    ![Caixa de diálogo do explorador do Graph](./media/aws-multi-accounts-tutorial/graph-explorer-new11.png)

18. Após a atualização da Entidade de Serviço com mais funções, será possível atribuir Usuários/Grupos às respectivas funções. Isso pode ser feito, acessando o portal e navegando até o aplicativo Amazon Web Services. Clique na guia **Usuários e Grupos** na parte superior.

19. Recomendamos que você crie novos grupos para cada função AWS para poder atribuir essa função específica a esse grupo. Observe que este é um mapeamento de um grupo para uma função. Em seguida, você pode adicionar os membros que pertencem ao grupo.

20. Uma vez que os Grupos são criados, selecione o grupo e atribua ao aplicativo.

    ![Configurar logon único Add](./media/aws-multi-accounts-tutorial/graph-explorer-new5.png)

    > [!Note]
    > Grupos aninhados não têm suporte durante a atribuição de grupos.

21. Para atribuir a função ao grupo, selecione a função e clique no botão **Atribuir** na parte inferior da página.

    ![Configurar logon único Add](./media/aws-multi-accounts-tutorial/graph-explorer-new6.png)

    > [!Note]
    > Observe que você precisa atualizar a sua sessão no Portal do Azure para ver as novas funções.

### <a name="test-single-sign-on"></a>Testar logon único

Nesta seção, você testará sua configuração de logon único do Azure AD usando o Painel de Acesso.

Ao clicar no bloco AWS (Amazon Web Services) no Painel de Acesso, você deve entrar na página do aplicativo AWS (Amazon Web Services) com a opção para selecionar a função.

![Configurar logon único Add](./media/aws-multi-accounts-tutorial/tutorial_amazonwebservices(aws)_test_screen.png)

Você também pode verificar a resposta SAML para ver as funções sendo passadas como declarações.

![Configurar logon único Add](./media/aws-multi-accounts-tutorial/tutorial_amazonwebservices(aws)_test_saml.png)

Para saber mais sobre o Painel de Acesso, consulte [Introdução ao Painel de Acesso](../active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>Recursos adicionais

* [Como configurar o provisionamento usando APIs do MS Graph](https://docs.microsoft.com/azure/active-directory/manage-apps/application-provisioning-configure-api)
* [Lista de tutoriais sobre como integrar aplicativos SaaS com o Active Directory do Azure](tutorial-list.md)
* [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[11]: ./media/aws-multi-accounts-tutorial/ic795031.png
[12]: ./media/aws-multi-accounts-tutorial/ic795032.png
[13]: ./media/aws-multi-accounts-tutorial/ic795033.png
[14]: ./media/aws-multi-accounts-tutorial/ic795034.png
[15]: ./media/aws-multi-accounts-tutorial/ic795035.png
[16]: ./media/aws-multi-accounts-tutorial/ic795022.png
[17]: ./media/aws-multi-accounts-tutorial/ic795023.png
[18]: ./media/aws-multi-accounts-tutorial/ic795024.png
[19]: ./media/aws-multi-accounts-tutorial/ic795025.png
[32]: ./media/aws-multi-accounts-tutorial/ic7950251.png
[33]: ./media/aws-multi-accounts-tutorial/ic7950252.png
[35]: ./media/aws-multi-accounts-tutorial/tutorial_amazonwebservices_provisioning.png
[34]: ./media/aws-multi-accounts-tutorial/ic7950253.png
[36]: ./media/aws-multi-accounts-tutorial/tutorial_amazonwebservices_securitycredentials.png
[37]: ./media/aws-multi-accounts-tutorial/tutorial_amazonwebservices_securitycredentials_continue.png
[38]: ./media/aws-multi-accounts-tutorial/tutorial_amazonwebservices_createnewaccesskey.png
[39]: ./media/aws-multi-accounts-tutorial/tutorial_amazonwebservices_provisioning_automatic.png
[40]: ./media/aws-multi-accounts-tutorial/tutorial_amazonwebservices_provisioning_testconnection.png
[41]: ./media/aws-multi-accounts-tutorial/
