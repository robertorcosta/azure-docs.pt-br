---
title: Configurar registro de contêiner interno para o Red Hat OpenShift 4 do Azure
description: Configurar registro de contêiner interno para o Red Hat OpenShift 4 do Azure
author: jiangma
ms.author: jiangma
ms.service: container-service
ms.topic: conceptual
ms.date: 10/15/2020
ms.openlocfilehash: 917da58c7f5ac2294fc30cd385a4834fde3f5f0b
ms.sourcegitcommit: 17b36b13857f573639d19d2afb6f2aca74ae56c1
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/10/2020
ms.locfileid: "94414285"
---
# <a name="configure-built-in-container-registry-for-azure-red-hat-openshift-4"></a>Configurar registro de contêiner interno para o Red Hat OpenShift 4 do Azure

Neste artigo, você configurará o registro de imagem de contêiner interno para um cluster do Azure Red Hat OpenShift (toa) 4. Você aprenderá a:

> [!div class="checklist"]
> * Configurar o Azure AD
> * Configurar o OpenID Connect
> * Acessar o registro de imagem de contêiner interno

## <a name="prerequisites"></a>Pré-requisitos

* Crie um cluster seguindo as etapas em [criar um cluster do Azure Red Hat OpenShift 4](/azure/openshift/tutorial-create-cluster). Certifique-se de criar o cluster com o `--pull-secret` argumento para `az aro create` .  Isso será necessário se você quiser configurar o Azure AD para entrar, conforme exigido neste artigo.
* Conecte-se ao cluster seguindo as etapas em [conectar-se a um cluster do Azure Red Hat OpenShift 4](/azure/openshift/tutorial-connect-cluster).
   * Certifique-se de seguir as etapas em "instalar a CLI do OpenShift", pois usaremos o `oc` comando mais adiante neste artigo.
   * Anote a URL do console de cluster, que se parece com `https://console-openshift-console.apps.<random>.<region>.aroapp.io/` . Os valores para `<random>` e `<region>` serão usados posteriormente neste artigo.
   * Observe as `kubeadmin` credenciais. Eles serão usados posteriormente neste artigo.

## <a name="set-up-azure-active-directory"></a>Configurar Azure Active Directory

O Azure Active Directory (Azure AD) implementa o OpenID Connect (OIDC). O OIDC permite que você use o Azure AD para entrar no cluster toa. Siga as etapas abaixo para configurar o Azure AD.

1. Configure um locatário do Azure AD seguindo as etapas em [início rápido: configurar um locatário](/azure/active-directory/develop/quickstart-create-new-tenant). Sua conta do Azure já pode ter um locatário. Nesse caso, você pode ignorar a criação de uma se tiver privilégios suficientes no locatário para seguir as etapas. Anote sua **ID de locatário**. Esse valor será usado mais tarde.
2. Crie pelo menos um usuário do Azure AD seguindo as etapas em [Adicionar ou excluir usuários usando Azure Active Directory](/azure/active-directory/fundamentals/add-users-azure-active-directory). Você pode usar essas contas ou suas próprias para testar o aplicativo. Anote os endereços de email e as senhas dessas contas para entrar.
3. Crie um novo registro de aplicativo em seu locatário do Azure AD seguindo as etapas em [início rápido: registrar um aplicativo com a plataforma de identidade da Microsoft](/azure/active-directory/develop/quickstart-register-app). 
   1. Lembre-se da observação nos pré-requisitos sobre os valores de `<random>` e `<region>` . Use esses valores para criar um URI de acordo com a fórmula a seguir:

      `https://oauth-openshift.apps.<random>.<region>.aroapp.io/oauth2callback/openid`
   1. Quando você chegar à etapa lidando com o campo **URI de redirecionamento** , insira o URI da etapa anterior.
   1. Selecione **Registrar**.
   1. Na página **visão geral** do aplicativo, observe o valor mostrado para **ID do aplicativo (cliente)** , como mostrado aqui.
      :::image type="content" source="media/built-in-container-registry/azure-ad-app-overview-client-id.png" alt-text="Página de visão geral do aplicativo do Azure AD.":::

4. Crie um novo segredo do cliente. 
   1. No registro de aplicativo recém-criado, selecione **certificados & segredos** no painel de navegação esquerdo.
   1. Selecione **Novo segredo do cliente**.
   1. Forneça **uma descrição** e selecione **Adicionar**.
   1. Economize o valor do **segredo do cliente** gerado. Esse valor será usado posteriormente neste artigo.

### <a name="add-openid-connect-identity-provider"></a>Adicionar provedor de identidade do OpenID Connect

A toa fornece um registro de contêiner interno.  Para acessá-lo, configure um provedor de identidade (IDP) usando o Azure AD OIDC seguindo estas etapas.

1. Entre no console Web do OpenShift do seu navegador como `kubeadmin` .  Esse é o mesmo procedimento usado ao executar as etapas no [tutorial: conectar-se a um cluster do Azure Red Hat OpenShift 4](/azure/openshift/tutorial-connect-cluster).
1. No painel de navegação esquerdo, selecione **Administração**  >  **configurações de cluster**.
1. No meio da página, selecione **configuração global**.
1. Localize o **OAuth** na coluna **recurso de configuração** da tabela e selecione-o.
1. Em **provedores de identidade** , selecione **Adicionar** e escolha **OpenID Connect**.
1. Defina o **nome** como **OpenID**.  Esse valor pode já estar preenchido.
1. Defina a **ID do cliente** e o segredo do **cliente** como os valores da etapa anterior.
1. Siga esta etapa para localizar o valor da **URL do emissor**.
   1. Substitua **\<tenant-id>** por um salvo durante a configuração da seção **Azure Active Directory** na URL `https://login.microsoftonline.com/<tenant-id>/v2.0/.well-known/openid-configuration` .
   1. Abra a URL no mesmo navegador que você estava usando para interagir com o portal do Azure.
   1. Copie o valor do **emissor** da propriedade no corpo JSON retornado e cole-o na caixa de texto rotulada **URL do emissor**.  O valor do **emissor** será semelhante a `https://login.microsoftonline.com/44p4o433-2q55-474q-on88-4on94469o74n/v2.0` .
1. Vá para a parte inferior da página e selecione **Adicionar**.
   :::image type="content" source="media/built-in-container-registry/openid-connect-identity-provider.png" alt-text="OpenID Connect em OpenShift.":::
1. Saia do console Web do OpenShift selecionando o botão **Kube: admin** no canto superior direito da janela do navegador e escolhendo **fazer logoff**.

### <a name="access-the-built-in-container-image-registry"></a>Acessar o registro de imagem de contêiner interno

As instruções a seguir habilitam o acesso ao registro interno.

#### <a name="define-the-azure-ad-user-to-be-an-administrator"></a>Definir o usuário do Azure AD como administrador

1. Entre no console Web do OpenShift do seu navegador usando as credenciais de um usuário do Azure AD.

   1. Use um recurso de janela de navegador InPrivate, Incognito ou outros equivalentes para entrar no console.
   1. A janela terá uma aparência diferente depois de ter habilitado OIDC.
   :::image type="content" source="media/built-in-container-registry/oidc-enabled-login-window.png" alt-text="Janela de entrada habilitada para OpenID Connect.":::
   1. Selecionar **OpenID**

   > [!NOTE]
   > Anote o nome de usuário e a senha que você usa para entrar aqui. Esse nome de usuário e senha funcionarão como administrador para outras ações neste e em outros artigos.
1. Entre com a CLI do OpenShift usando as etapas a seguir.  Para discussão, esse processo é conhecido como `oc login` .
   1. Na parte superior direita do console Web, expanda o menu de contexto do usuário conectado e selecione o **comando copiar logon**.
   1. Entre em uma nova janela de guia com o mesmo usuário, se necessário.
   1. Selecione o **token de exibição**.
   1. Copie o valor listado abaixo de **fazer logon com esse token** na área de transferência e execute-o em um shell, como mostrado aqui.

       ```bash
       oc login --token=XOdASlzeT7BHT0JZW6Fd4dl5EwHpeBlN27TAdWHseob --server=https://api.aqlm62xm.rnfghf.aroapp.io:6443
       Logged into "https://api.aqlm62xm.rnfghf.aroapp.io:6443" as "kube:admin" using the token provided.

       You have access to 57 projects, the list has been suppressed. You can list all projects with 'oc projects'

       Using project "default".
       ```

1. Execute `oc whoami` no console do e anote a saída como **\<aad-user>** .  Usaremos esse valor posteriormente neste artigo.
1. Saia do console Web do OpenShift. Selecione o botão no canto superior direito da janela do navegador rotulado como o **\<aad-user>** e escolha **fazer logoff**.


#### <a name="grant-the-azure-ad-user-the-necessary-roles-for-registry-interaction"></a>Conceder ao usuário do Azure AD as funções necessárias para a interação do registro

1. Entre no console Web do OpenShift no seu navegador usando as `kubeadmin` credenciais.
1. Entre na CLI do OpenShift com o token para `kubeadmin` , seguindo as etapas `oc login` acima, mas faça isso depois de entrar no console Web com `kubeadmin` .
1. Execute os comandos a seguir para habilitar o acesso ao registro interno para o usuário do **AAD**.

   ```bash
   # Switch to project "openshift-image-registry"
   oc project openshift-image-registry
   ```

   A saída deve ser semelhante à seguinte.

   ```bash
   Now using project "openshift-image-registry" on server "https://api.x8xl3f4y.eastus.aroapp.io:6443".
   ```

   ```bash
   # Expose the registry using "DefaultRoute"
   oc patch configs.imageregistry.operator.openshift.io/cluster --patch '{"spec":{"defaultRoute":true}}' --type=merge
   ```

   A saída deve ser semelhante à seguinte.

   ```bash
   config.imageregistry.operator.openshift.io/cluster patched
   ```

   ```bash
   # Add roles to "aad-user" for pulling and pushing images
   # Note: replace "<aad-user>" with the one you wrote down before
   oc policy add-role-to-user registry-viewer <aad-user>
   ```

   A saída deve ser semelhante à seguinte.

   ```bash
   clusterrole.rbac.authorization.k8s.io/registry-viewer added: "kaaIjx75vFWovvKF7c02M0ya5qzwcSJ074RZBfXUc34"
   ```

   ```bash
   oc policy add-role-to-user registry-editor <aad-user>
   ```

   A saída deve ser semelhante à seguinte.

   ```bash
   clusterrole.rbac.authorization.k8s.io/registry-editor added: "kaaIjx75vFWovvKF7c02M0ya5qzwcSJ074RZBfXUc34"
   ```

#### <a name="obtain-the-container-registry-url"></a>Obter a URL do registro de contêiner

Use o `oc get route` comando conforme mostrado ao lado de obter a URL do registro de contêiner.

```bash
# Note: the value of "Container Registry URL" in the output is the fully qualified registry name.
HOST=$(oc get route default-route --template='{{ .spec.host }}')
echo "Container Registry URL: $HOST"
```

   > [!NOTE]
   > Observe a saída do console da **URL do registro de contêiner**. Ele será usado como o nome totalmente qualificado do registro para este guia e os subsequentes.

## <a name="next-steps"></a>Próximas etapas

Use o registro de imagem de contêiner interno implantando um aplicativo no OpenShift.  Para aplicativos Java, siga o guia de instruções, [implante um aplicativo Java com o Liberty/WebSphere Liberty aberto em um cluster do Azure Red Hat OpenShift 4](howto-deploy-java-liberty-app.md).
