---
title: Entender os conceitos do repositório de modelos do IoT do Azure | Microsoft Docs
description: Como desenvolvedor de soluções ou profissional de ti, saiba mais sobre os conceitos básicos do repositório de modelos do IoT do Azure.
author: prashmo
ms.author: prashmo
ms.date: 07/24/2020
ms.topic: conceptual
ms.service: iot-pnp
services: iot-pnp
ms.openlocfilehash: c82858294054b50d6edae42a3d41e9fcb89ca89d
ms.sourcegitcommit: a422b86148cba668c7332e15480c5995ad72fa76
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/30/2020
ms.locfileid: "91577791"
---
# <a name="azure-iot-model-repository"></a>Repositório de modelo IoT do Azure

O repositório de modelo IoT do Azure permite que os integradores de dispositivos gerenciem e compartilhem modelos de dispositivos IoT Plug and Play. Os modelos de dispositivo são documentos JSON LD definidos usando a [linguagem de modelagem de gêmeos digital (DTDL)](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/dtdlv2.md). Os modelos armazenados no serviço de repositório de modelo podem ser compartilhados com os desenvolvedores de solução de forma privada por meio do controle de acesso ou publicamente sem a necessidade de autenticação para integrar e desenvolver a solução de nuvem de Plug and Play de IoT.

> [!NOTE]
> Os criadores de dispositivos podem optar por implementar modelos de dispositivos IoT Plug and Play diretamente em um dispositivo, usar módulos ou em um módulo IoT Edge.

Você pode acessar o repositório de modelos usando o:

- Portal [do repositório de modelo IOT do Azure](https://aka.ms/iotmodelrepo)
- [API REST do repositório de modelo IoT do Azure](https://docs.microsoft.com/rest/api/iothub/digitaltwinmodelrepositoryservice/getmodelasync/getmodelasync)
- [Comandos do repositório de modelo de IoT CLI do Azure](https://docs.microsoft.com/cli/azure/ext/azure-iot/iot/pnp?view=azure-cli-latest&preserve-view=true)

## <a name="public-models"></a>Modelos públicos

Os modelos de entrelaçamento digital pública armazenados no repositório de modelo estão disponíveis para todos consumirem e integrarem em seu aplicativo sem nenhuma autenticação. Além disso, os modelos públicos possibilitam que um sistema de eco aberto para criadores de dispositivos e desenvolvedores de soluções compartilhem e reutilizem seus modelos de dispositivos IoT Plug and Play.

Consulte a seção [publicar um modelo](#publish-a-model) nos **modelos da empresa** para obter instruções sobre como publicar um modelo no repositório de modelos para torná-lo público.

Para exibir um modelo público usando o portal do repositório de modelos:

1. Vá para [portal do repositório de modelo IOT do Azure](https://aka.ms/iotmodelrepo).

1. Selecione em **Exibir modelos públicos**.

    ![Exibir modelos públicos](./media/concepts-model-repository/public-models.png)

Para exibir um modelo público programaticamente usando a API REST, consulte obter a documentação da API REST do [modelo](https://docs.microsoft.com/rest/api/iothub/digitaltwinmodelrepositoryservice/getmodelasync/getmodelasync) .

```csharp
var httpClient = new HttpClient();
httpClient.BaseAddress = new Uri("https://repo.azureiotrepository.com");

var modelId = "dtmi:com:mxchip:model;1";
var response = await httpClient.GetAsync($"/models/{modelId}?api-version=2020-09-30").ConfigureAwait(false);
```

Para exibir um modelo público usando a CLI, consulte o comando CLI do Azure [obter modelo](https://docs.microsoft.com/cli/azure/ext/azure-iot/iot/pnp/model?view=azure-cli-latest#ext-azure-iot-az-iot-pnp-model-show&preserve-view=true) .

## <a name="company-models"></a>Modelos da empresa

O repositório de modelo da empresa é um locatário no repositório de modelos do IoT do Azure para sua organização para criar e gerenciar modelos de entrelaçamento digital criados por usuários em sua empresa ou organização. Os modelos da empresa estão disponíveis somente para usuários autenticados de sua empresa ou organização. Um administrador de locatários do repositório de modelos pode atribuir permissões e controlar o acesso de outros usuários na empresa ou organização aos modelos no repositório de modelos da empresa.

### <a name="set-up-your-company-model-repository"></a>Configurar o repositório de modelos da empresa

Use sua *conta de Azure Active Directory corporativa ou de estudante (Azure AD)* para acessar o repositório de modelos. Se sua organização já tiver um locatário do Azure AD, você poderá usar contas de usuário e entidades de serviço desse locatário do Azure AD.

Para saber como configurar um locatário do Azure AD e como criar um usuário ou uma entidade de serviço em um locatário do Azure AD, consulte a seção [informações adicionais](#additional-information) .

- Se você for o primeiro usuário da sua organização para acessar o repositório de modelos ou entrar no portal, você terá a função de **administrador de locatários** . Essa função permite que você atribua funções a outros usuários no locatário do repositório da sua organização.

- Você pode receber outras funções por um **administrador de locatários** como **ler modelos** ou **criar modelos**.

### <a name="understand-access-management"></a>Entender o gerenciamento de acesso

A tabela a seguir resume os recursos com suporte no repositório de modelo da empresa e suas permissões associadas:

| Funcionalidade  | Permissão| Descrição|
|-------------|-----------|------------|
|Ler modelos|Ler modelos|Por padrão, todos os usuários no locatário da empresa podem exibir seus modelos de empresa. Além disso, o usuário também pode exibir os modelos privados compartilhados com eles por outras empresas.|
|Gerenciar Acesso|Gerenciar Acesso|Gerencie a atribuição de função de usuário (adicionar ou remover) para outros usuários na organização.|
|Criar modelos|Criar modelos|Crie modelos no repositório de modelo da empresa.|
|Modelos de publicação|Modelos de publicação|Publique modelos para torná-los públicos para qualquer pessoa que exiba o modelo.|

A tabela a seguir resume as funções com suporte e seus recursos no repositório de modelo que podem ser usados para o gerenciamento de acesso.

|Função|Funcionalidade|
|----|----------|
|TenantAdministrator|Gerenciar acesso, ler modelos|
|Criador|Criar modelos, ler modelos|
|Publisher|Modelos de publicação, modelos de leitura|

#### <a name="passing-a-security-token-when-accessing-company-models-with-a-rest-api"></a>Passando um token de segurança ao acessar modelos da empresa com uma API REST

Ao chamar as APIs REST para gerenciar os modelos da empresa que são particulares ou compartilhados, você deve fornecer um token de autorização para o usuário ou entidade de serviço no formato JWT. Consulte a seção [informações adicionais](#additional-information) para saber como obter um token JWT para um usuário ou entidade de serviço.

O token JWT deve ser passado no cabeçalho HTTP de autorização na API ao direcionar modelos de empresa ou modelos compartilhados. O token JWT não é necessário ao direcionar modelos públicos.

```csharp
// sample token
var authorizationToken = "eyJhbGciOiJIUzI1NiIsInR5cCTI6IkpXVCJ9.eyJzdWIiOiIxMjM0NTY3ODkwIiwibmFtZSI6IkpvaG4gRG9lIiwiaWF0IjoxNTE2MjM5MDIyfQ.SflKxwRJSMeKKF2QT4fwpMeJf36POk6yJV_adQssw5c";
httpClient.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Bearer", authorizationToken);
```

### <a name="view-company-or-shared-models"></a>Exibir modelos de empresa ou compartilhados

Você deve ser um membro da função *leitor* do locatário do repositório ou o modelo deve ser compartilhado com você para ler um modelo. Você pode ver uma lista de modelos não publicados que foram compartilhados com você e uma lista de modelos publicados que foram compartilhados com você. Por padrão, os usuários podem ler os modelos de sua empresa, os modelos que foram compartilhados com eles por outras empresas e todos os modelos públicos.

Para exibir uma empresa ou um modelo compartilhado usando o portal:

1. Entre no portal de [repositório do modelo IOT do Azure](https://aka.ms/iotmodelrepo).

1. Expandir **modelos da empresa** – não **publicado** no painel esquerdo

    ![Exibir modelos da empresa](./media/concepts-model-repository/view-company-models.png)

1. Expandir **modelos compartilhados – não publicado** no painel esquerdo

    ![Exibir modelos compartilhados](./media/concepts-model-repository/view-shared-models.png)

Para exibir uma empresa ou um modelo compartilhado usando a API REST, consulte a documentação obter API REST do [modelo](https://docs.microsoft.com/rest/api/iothub/digitaltwinmodelrepositoryservice/getmodelasync/getmodelasync) . Consulte [passando um token de segurança ao acessar modelos da empresa com uma API REST](#passing-a-security-token-when-accessing-company-models-with-a-rest-api) para obter informações sobre como transmitir um cabeçalho de autorização JWT na solicitação HTTP.

```csharp
var modelId = "dtmi:com:mxchip:model;1";
var response = await httpClient.GetAsync($"/models/{modelId}?api-version=2020-09-30").ConfigureAwait(false);
```

Para exibir um modelo da empresa ou um modelo compartilhado usando a CLI, consulte o comando CLI do Azure [obter modelo](https://docs.microsoft.com/cli/azure/ext/azure-iot/iot/pnp/model?view=azure-cli-latest#ext-azure-iot-az-iot-pnp-model-show&preserve-view=true) .

### <a name="manage-roles"></a>Gerenciar funções

O administrador de locatários pode atribuir funções a usuários no locatário do repositório para que eles possam criar modelos privados para a empresa ou organização, publicar modelos ou gerenciar funções para outros usuários.

Para adicionar um usuário a uma função de locatário do repositório de modelos usando o portal:

1. Entre no portal de [repositório do modelo IOT do Azure](https://aka.ms/iotmodelrepo).

1. Selecione **Gerenciamento de acesso** no painel esquerdo e, em seguida, selecione **+ Adicionar**. No painel **adicionar permissão** , digite o endereço de trabalho do usuário que você deseja adicionar à função:

    ![Adicionar endereço de trabalho](./media/concepts-model-repository/add-user.png)

1. Escolha a função à qual você deseja adicionar o usuário na lista suspensa **função** . Em seguida, selecione **salvar**:

    ![Escolher função](./media/concepts-model-repository/choose-role.png)

### <a name="upload-a-model"></a>Carregar um modelo

Você deve ser um membro da função de **criador** do locatário do repositório para carregar um modelo para o repositório de modelo da empresa.

Esses modelos não são publicados e só podem ser acessados por usuários em sua organização por padrão. Você também pode compartilhar um ou mais modelos não publicados com usuários externos.

Os modelos carregados são imutáveis.

As IDs de modelo para esses modelos devem ser globalmente exclusivas em todos os locatários de repositório para todos os modelos carregados.

Para carregar um modelo usando o portal:

1. Entre no portal de [repositório do modelo IOT do Azure](https://aka.ms/iotmodelrepo).

1. Expanda **modelos da empresa** no painel esquerdo e selecione **criar modelo**. Em seguida, selecione **importar JSON**.

    ![Criar modelo](./media/concepts-model-repository/create-model.png)

1. Selecione o arquivo que você deseja carregar. Se o portal validar com êxito seu modelo, selecione **salvar**.

Para carregar um modelo usando a API REST, consulte [criar uma](https://docs.microsoft.com/rest/api/iothub/digitaltwinmodelrepositoryservice/createorupdateasync/createorupdateasync) API de modelo. Consulte [passando um token de segurança ao acessar modelos da empresa com uma API REST](#passing-a-security-token-when-accessing-company-models-with-a-rest-api) para obter informações sobre como transmitir um cabeçalho de autorização JWT na solicitação HTTP.

```csharp
var httpContent = new StringContent(jsonLdModel, Encoding.UTF8, "application/json");
var modelId = "dtmi:com:mxchip:model;1";
var response = await httpClient.PutAsync($"/models/{modelId}?api-version=2020-09-30", httpContent).ConfigureAwait(false);
```

Para carregar um modelo usando a CLI, consulte o CLI do Azure [criar um comando de modelo](https://docs.microsoft.com/cli/azure/ext/azure-iot/iot/pnp/model?view=azure-cli-latest#ext-azure-iot-az-iot-pnp-model-create&preserve-view=true) .

### <a name="publish-a-model"></a>Publicar um modelo

Para publicar um modelo, os seguintes requisitos devem ser atendidos:

1. Sua organização precisa ser membro do [Microsoft Partner Network](https://docs.microsoft.com/partner-center/) para publicar um modelo. Para criar uma conta do Partner Center, consulte [criar uma conta do Partner Center](https://docs.microsoft.com/partner-center/mpn-create-a-partner-center-account). Depois que sua conta for aprovada, você poderá publicar seus modelos. Para obter mais informações, consulte as [perguntas frequentes sobre o Partner Center](https://support.microsoft.com/help/4340639/partner-center-account-faqs).

2. O usuário deve ser membro da função de *Editor* do locatário do repositório.

Os modelos criados e publicados pelos usuários em sua organização são visíveis como *modelos publicados*. Esses modelos são públicos e podem ser encontrados por qualquer pessoa em **modelos públicos**.

Para publicar um modelo usando o portal:

1. Entre no portal de [repositório do modelo IOT do Azure](https://aka.ms/iotmodelrepo).

2. Expanda **modelos da empresa** no painel esquerdo e selecione o modelo que você deseja publicar. Em seguida, selecione **publicar**.

    ![Publicar modelo](./media/concepts-model-repository/publish-model.png)

> [!NOTE]
> Se você receber uma notificação dizendo que você não tem uma ID de parceiro da Microsoft (MPN), siga as etapas de registro na notificação. Para obter mais informações, consulte os requisitos no início desta seção.

Para publicar um modelo usando a API REST, consulte a documentação [publicar uma](https://docs.microsoft.com/rest/api/iothub/digitaltwinmodelrepositoryservice/createorupdateasync/createorupdateasync) API REST do modelo. Forneça o parâmetro de cadeia de caracteres de consulta `update-metadata=true` para publicar um modelo usando a API REST. Consulte [passando um token de segurança ao acessar modelos da empresa com uma API REST](#passing-a-security-token-when-accessing-company-models-with-a-rest-api) para obter informações sobre como transmitir um cabeçalho de autorização JWT na solicitação HTTP.

Para publicar um modelo usando a CLI, consulte o CLI do Azure [publicar um comando de modelo](https://docs.microsoft.com/cli/azure/ext/azure-iot/iot/pnp/model?view=azure-cli-latest#ext-azure-iot-az-iot-pnp-model-publish&preserve-view=true) .

> [!NOTE]
> Os modelos devem ser publicados no repositório de modelos antes de executar os testes de certificação. Para saber mais, confira [como certificar dispositivos IoT plug and Play](howto-certify-device.md).

### <a name="share-a-model"></a>Compartilhar um modelo

Você pode compartilhar modelos da empresa que você criou com usuários de organizações externas. Dessa forma, você pode permitir que colaboradores exibam e desenvolvam soluções com seus modelos de empresa privada.

Por exemplo, um fabricante de dispositivo pode querer manter modelos privados para a empresa ou organização. Eles podem ter clientes que exigem que seus recursos de dispositivo permaneçam confidenciais.

O compartilhamento de modelos entre empresas ou organizações permite o acesso seguro a modelos que não são públicos.

Para compartilhar um modelo da empresa usando o portal:

- Se você for o criador de um modelo, o **compartilhamento** e o **compartilhado com** botões estarão ativos quando você exibir o modelo na seção **modelos da empresa** .

    ![Compartilhar modelo](./media/concepts-model-repository/share-model.png)

- Para compartilhar o modelo com um usuário externo, selecione **compartilhar**. No painel **compartilhar modelo** , insira o endereço de email do usuário externo e selecione **salvar**.

- Para ver os usuários com os quais você compartilhou o modelo, selecione **compartilhado com**.

- Para interromper o compartilhamento do modelo com um usuário específico, selecione o usuário na lista de usuários no painel **compartilhado com** . Em seguida, selecione **remover** e confirme sua escolha quando solicitado.

    ![Parar compartilhamento](./media/concepts-model-repository/stop-sharing.png)

## <a name="additional-information"></a>Informações adicionais

Você pode encontrar os seguintes tópicos úteis ao trabalhar com o Azure AD:

- Para criar um novo locatário do Azure AD, consulte [criar um novo locatário no Azure ad](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-access-create-new-tenant). A maioria das organizações já terá locatários do Azure AD.

- Para adicionar usuários ou usuários convidados a um locatário do Azure AD, consulte [Adicionar ou excluir usuários usando o Azure ad](https://docs.microsoft.com/azure/active-directory/fundamentals/add-users-azure-active-directory).

- Para adicionar uma entidade de serviço a um locatário do Azure AD, consulte [como usar o portal para criar um aplicativo do Azure AD e uma entidade de serviço que pode acessar recursos](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal).

- Para saber como obter um token JWT do AD do Azure para usar ao chamar APIs REST, consulte [adquirir um token do Azure ad para autorizar solicitações de um aplicativo cliente](https://docs.microsoft.com/azure/storage/common/storage-auth-aad-app).

## <a name="next-steps"></a>Próximas etapas

A próxima etapa sugerida é examinar a [arquitetura de plug and Play de IOT](concepts-architecture.md).
