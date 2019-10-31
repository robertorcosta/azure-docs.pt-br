---
title: Personalizar a interface do usuário de um Percurso do Usuário com políticas personalizadas | Microsoft Docs
description: Saiba mais sobre as políticas personalizadas do Azure Active Directory B2C.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 04/25/2017
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 7e4714de9868dbd540e2e662b22a22da6df6514b
ms.sourcegitcommit: 0b1a4101d575e28af0f0d161852b57d82c9b2a7e
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/30/2019
ms.locfileid: "73147539"
---
# <a name="customize-the-ui-of-a-user-journey-with-custom-policies"></a>Personalizar a interface do usuário de um Percurso do Usuário com políticas personalizadas

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

> [!NOTE]
> Este artigo é uma descrição avançada de como a personalização da interface do usuário funciona e como habilitar com as políticas personalizadas do Azure AD B2C, usando a Estrutura de Experiência de Identidade.


Uma experiência perfeita para o usuário é a chave para qualquer solução para negócios para consumidor. Uma experiência do usuário perfeita é uma experiência, seja no dispositivo ou no navegador, onde um percurso do usuário através do serviço é indistinguível do serviço de atendimento ao consumidor que ele está usando.

## <a name="understand-the-cors-way-for-ui-customization"></a>Introdução ao método CORS para personalização da IU

O Azure AD B2C permite personalizar a aparência da experiência do usuário (UX) nas várias páginas atendidas e exibidas pelo Azure AD B2C usando as políticas personalizadas.

Para essa finalidade, o Azure AD B2C executa o código no navegador do cliente e usa a abordagem moderna e padrão do [Compartilhamento de recursos entre origens (CORS)](https://www.w3.org/TR/cors/) para carregar conteúdo personalizado de uma URL particular, que você especifica em uma política personalizada para apontar para os modelos de HTML5/CSS. O CORS é um mecanismo que permite o uso recursos restritos, como fontes, em uma página da web solicitada em outro domínio fora do domínio do qual o recurso foi originado.

Em comparação com a maneira tradicional antiga, onde as páginas de modelo pertencentes à solução onde você forneceu textos e imagens limitados, onde o controle limitado de layout e aparência oferecidos prejudicam uma experiência perfeita, o método CORS dá suporte às HTML5 e CSS e permitem que você:

- Hospede o conteúdo e a solução injeta os controles usando o script do cliente.
- Tenha controle total sobre cada pixel de layout e aparência.

Você pode fornecer quantas páginas de conteúdo quiser ao criar arquivos de HTML5/CSS conforme a conveniência.

> [!NOTE]
> Por motivos de segurança, bloqueamos a personalização do uso do JavaScript no momento. 

Em cada um de seus modelos de HTML5/CSS, você deve fornecer um elemento *âncora*, que corresponde ao elemento `<div id="api">` necessário no HTML ou na página de conteúdo, como ilustram daqui em diante. O Azure AD B2C requer que todas as páginas de conteúdo tenham essa divisão específica.

```
<!DOCTYPE html>
<html>
  <head>
    <title>Your page content’s tile!</title>
  </head>
  <body>
    <div id="api"></div>
  </body>
</html>
```

O conteúdo relacionado ao Azure AD B2C para a página é injetado nessa divisão, enquanto o restante da página fica sob o seu controle. O código JavaScript do Azure AD B2C extrai o conteúdo e injeta HTML nesse elemento div específico. O Azure AD B2C injeta os seguintes controles, conforme apropriado: controle do seletor de conta, controles de logon, controles multifator (atualmente baseados em telefone) e controles de coleção de atributos. O Azure AD B2C garante que todos os controles HTML5 estejam acessíveis e em conformidade, que todos os controles possam ser totalmente estilizados e que a versão daquele controle não seja regredida.

O conteúdo mesclado, por fim, é exibido como o documento dinâmico para o consumidor.

Para garantir que tudo funcione conforme o esperado, é necessário que você:

- Certifique-se de que seu conteúdo esteja em conformidade com HTML5 e acessível
- Verifique se que o servidor de conteúdo está habilitado para CORS.
- Forneça conteúdo por HTTPS.
- Use URLs absolutas como `https://yourdomain/content` para todos os links e conteúdo CSS.

> [!TIP]
> Para verificar se o site em que você está hospedando o conteúdo possui CORS habilitado e testar solicitações CORS, use o site https://test-cors.org/. Graças a esse site, você pode enviar a solicitação CORS para um servidor remoto (para testar se há suporte para CORS), ou enviar a solicitação CORS para um servidor de teste (para explorar alguns recursos do CORS).

> [!TIP]
> O site https://enable-cors.org/ também apresenta mais recursos úteis no CORS.

Graças a essa abordagem baseada em CORS, os usuários finais têm experiências consistentes entre seu aplicativo e as páginas atendidas pelo Azure AD B2C.

## <a name="create-a-storage-account"></a>Crie uma conta de armazenamento

Como pré-requisito, você precisa criar uma conta de armazenamento. Você precisa de uma assinatura do Azure para criar uma conta de Armazenamento de Blobs do Azure. Você pode se inscrever para uma avaliação gratuita no [site do Azure](https://azure.microsoft.com/pricing/free-trial/).

1. Abra uma sessão de navegação e navegue até o [portal do Azure](https://portal.azure.com).
2. Entre com suas credenciais administrativas.
3. Clique em **Criar um recurso** > **Armazenamento** > **Conta de armazenamento**.  Um painel **Criar conta de armazenamento** abre.
4. No **nome**, forneça um nome para a conta de armazenamento, por exemplo, *contoso369b2c*. Esse valor é referenciado mais tarde como *storageAccountName*.
5. Escolha as seleções apropriadas para o tipo de preço, o grupo de recursos e a assinatura. Verifique se a opção **Fixar no Quadro Inicial** está marcada. Clique em **Criar**.
6. Retorne para o Quadro Inicial e clique na conta de armazenamento que você criou.
7. Na seção **serviços**, clique em **Blobs**. Um **painel do serviço Blob** abre.
8. Clique em **+ Contêiner**.
9. No **nome**, forneça um nome para o contêiner, por exemplo, *b2c*. Esse valor é posteriormente referenciado como *containerName*.
9. Selecione **Blob** como o **Tipo de acesso**. Clique em **Criar**.
10. O contêiner que você criou aparece na lista do **painel do serviço Blob**.
11. Feche o painel **Blobs**.
12. No **painel da conta de armazenamento**, clique o ícone **Chave**. Um **painel Chaves de acesso** abre.  
13. Anote o valor de **key1**. Esse valor é referenciado mais tarde como *key1*.

## <a name="downloading-the-helper-tool"></a>Como baixar a ferramenta auxiliar

1.  Baixe a ferramenta auxiliar do [GitHub](https://github.com/azureadquickstarts/b2c-azureblobstorage-client/archive/master.zip).
2.  Salve o arquivo *B2C-AzureBlobStorage-Client-master.zip* no seu computador local.
3.  Extraia o conteúdo do arquivo B2C-AzureBlobStorage-Client-master.zip no seu disco local, por exemplo, na pasta **UI-Customization-Pack**, que cria uma pasta *B2C-AzureBlobStorage-Client-master* abaixo.
4.  Abra a pasta e extraia o conteúdo do arquivo *B2CAzureStorageClient.zip* nela.

## <a name="upload-the-ui-customization-pack-sample-files"></a>Carregue os arquivos de exemplo do UI-Customization-Pack

1.  Usando o Windows Explorer, navegue até a pasta *B2C-AzureBlobStorage-Client-master* localizado na pasta *UI-Customization-Pack* criada na seção anterior.
2.  Execute o arquivo *B2CAzureStorageClient.exe*. Este programa carrega todos os arquivos no diretório que você especifica na sua conta de armazenamento e habilita o acesso CORS para esses arquivos.
3.  Quando solicitado, especifique: um.  O nome da sua conta de armazenamento *storageAccountName*, por exemplo, *contoso369b2c*.
    b.  A chave de acesso primária de seu armazenamento de BLOBs do Azure, *key1*, por exemplo, *contoso369b2c*.
    c.  O nome do seu contêiner de armazenamento de blobs de armazenamento, *containerName*, por exemplo, *b2c*.
    d.  O caminho dos arquivos de exemplo *Pacote Starter*, por exemplo *..\B2CTemplates\wingtiptoys*.

Se você seguiu as etapas anteriores, os arquivos HTML5 e CSS do *UI-Customization-Pack* para a empresa fictícia **wingtiptoys** agora estão apontando para sua conta de armazenamento.  Para verificar se o conteúdo foi carregado corretamente, abra o painel de contêineres relacionados no Portal do Azure. Como alternativa, você pode verificar se o conteúdo foi carregado corretamente, para isso, acesse a página de um navegador. Para obter mais informações, confira [Azure Active Directory B2C: uma ferramenta auxiliar usada para demonstrar o recurso de personalização da interface de usuário (IU) da página](active-directory-b2c-reference-ui-customization-helper-tool.md).

## <a name="ensure-the-storage-account-has-cors-enabled"></a>Certifique-se de que a conta de armazenamento tenha o CORS habilitado

O CORS (compartilhamento de recurso entre origens) deve estar habilitado no ponto de extremidade do Azure AD B2C para carregar o conteúdo. Isso ocorre porque o conteúdo está hospedado em um domínio diferente do domínio do qual o Azure AD B2C atenderá à página.

Para verificar se o armazenamento que está hospedando o conteúdo tem CORS habilitado, prossiga com as etapas a seguir:

1. Abra uma sessão de navegação e navegue até a página *unified.html* usando a URL completa do seu local na sua conta de armazenamento `https://<storageAccountName>.blob.core.windows.net/<containerName>/unified.html`. Por exemplo, https://contoso369b2c.blob.core.windows.net/b2c/unified.html.
2. Navegue até https://test-cors.org. Este site permite que você verifique se a página que você está usando tem CORS habilitado.  
   <!--
   ![test-cors.org](../../media/active-directory-b2c-customize-ui-of-a-user-journey/test-cors.png)
   -->

3. Na **URL remota**, insira a URL completa para o seu conteúdo unified.html e clique em **Enviar solicitação**.
4. Verifique se a saída na seção **Resultados** contém *XHR status: 200*, o que indica que o CORS está habilitado.
   <!--
   ![CORS enabled](../../media/active-directory-b2c-customize-ui-of-a-user-journey/cors-enabled.png)
   -->
   Agora, a conta de armazenamento deve conter um contêiner de blobs denominado *b2c* na ilustração, que contém os seguintes modelos wingtiptoys do *Starter Pack*.

<!--
![Correctly configured storage account](../../articles/active-directory-b2c/media/active-directory-b2c-reference-customize-ui-custom/storage-account-final.png)
-->

A tabela a seguir descreve a finalidade das páginas HTML5 precedentes.

| Modelo do HTML5 | Descrição |
|----------------|-------------|
| *phonefactor.html* | Esta página pode ser usada como modelo para uma página de autenticação multifator. |
| *resetpassword.html* | Esta página pode ser usada como modelo para uma página de esquecimento de senha. |
| *selfasserted.html* | Esta página pode ser utilizada como um modelo para uma página de inscrição para conta de redes sociais, página de inscrição de conta local ou uma página de entrada na conta local. |
| *unified.html* | Esta página pode ser usada como modelo para uma página de inscrição ou entrada unificada. |
| *updateprofile.html* | Esta página pode ser usada como modelo para uma página de atualização de perfil. |

## <a name="add-a-link-to-your-html5css-templates-to-your-user-journey"></a>Adicione um link aos modelos de HTML5/CSS para o seu percurso do usuário

Você pode adicionar um link para os modelos de HTML5/CSS ao seu percurso do usuário ao editar diretamente uma política personalizada.

Os modelos de HTML5/CSS personalizados usados no seu percurso do usuário devem ser especificados em uma lista de definições de conteúdo que podem ser usados nos percursos do usuário. Para essa finalidade, um elemento XML opcional de *\<ContentDefinitions >* deve ser declarado na seção *\<> do BUILDINGBLOCKS* do arquivo XML da política personalizada.

A tabela a seguir descreve o conjunto de IDs de definição de conteúdo reconhecido pelo mecanismo de experiência de identidade de Azure AD B2C e o tipo de páginas relacionadas a eles.

| ID de definição de conteúdo | Descrição |
|-----------------------|-------------|
| *api.error* | **Página de erro**. Essa página é exibida quando uma exceção ou um erro é encontrado. |
| *api.idpselections* | **Página de seleção de provedor de identidade**. Esta página contém uma lista de provedores de identidade que o usuário pode escolher durante a inscrição. Esses provedores são fornecedores de identidade empresarial, provedores de identidade social, como Facebook e Google+, ou contas locais (baseadas em endereço de email ou nome de usuário). |
| *api.idpselections.signup* | **Seleção de provedor de identidade para inscrição**. Esta página contém uma lista de provedores de identidade que o usuário pode escolher durante a inscrição. Esses provedores são fornecedores de identidade empresarial, provedores de identidade social, como Facebook e Google+, ou contas locais (baseadas em endereço de email ou nome de usuário). |
| *api.localaccountpasswordreset* | **Página de esquecimento de senha**. Esta página contém um formulário que o usuário precisa preencher para iniciar sua redefinição de senha.  |
| *api.localaccountsignin* | **Página de entrada da conta local**. Esta página contém um formulário de inscrição que o usuário deve preencher ao entrar usando uma conta local baseada em endereço de email ou nome de usuário. O formulário pode conter uma caixa de entrada de texto e caixa de entrada de senha. |
| *api.localaccountsignup* | **Página de inscrição da conta local**. Esta página contém um formulário de inscrição que o usuário deve preencher ao inscrever-se usando uma conta local baseada em endereço de email ou nome de usuário. O formulário pode conter diferentes controles de entrada como caixa de entrada de texto, caixa de entrada de senha, botão de opção, caixas de lista suspensa de seleção única e caixas de seleção múltipla. |
| *api.phonefactor* | **Página de autenticação multifator**. Nesta página, os usuários pode verificar seus números de telefone (usando mensagem de texto ou por voz) durante a inscrição ou entrada. |
| *api.selfasserted* | **Página de inscrição de conta social**. Esta página contém um formulário de inscrição que deve ser preenchida pelo usuário quando o mesmo se inscreve usando uma conta existente de um provedor de identidade social, como o Facebook ou Google+. Essa página é semelhante à página de inscrição para conta de redes sociais anterior, com exceção dos campos de entrada da senha. |
| *api.selfasserted.profileupdate* | **Página de atualização de perfil**. Esta página contém um formulário que o usuário pode usar para atualizar seu perfil. Essa página é semelhante à página de inscrição para conta de redes sociais anterior, com exceção dos campos de entrada da senha. |
| *api.signuporsignin* | **Página de inscrição ou entrada unificada**.  Esta página controla tanto a inscrição quanto a entrada dos usuários, que podem usar provedores de identidade empresarial, provedores de identidade social como Facebook ou Google+ ou contas locais.

## <a name="next-steps"></a>Próximos passos
[Referência: Aprenda como as políticas personalizadas trabalham com o Identity Experience Framework no B2C](active-directory-b2c-reference-custom-policies-understanding-contents.md)
