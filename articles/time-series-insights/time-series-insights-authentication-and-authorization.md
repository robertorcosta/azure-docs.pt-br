---
title: Autenticação e autorização de API – Azure Time Series Insights | Microsoft Docs
description: Este artigo descreve como configurar a autenticação e autorização para um aplicativo personalizado que chama a API do Azure Time Series Insights.
ms.service: time-series-insights
services: time-series-insights
author: deepakpalled
ms.author: dpalled
manager: cshankar
ms.reviewer: v-mamcge, jasonh, kfile
ms.devlang: csharp
ms.workload: big-data
ms.topic: conceptual
ms.date: 04/14/2020
ms.custom: seodec18, has-adal-ref
ms.openlocfilehash: bf959a7ac8c1038c4306a45ba4519374c5d85f29
ms.sourcegitcommit: 50ef5c2798da04cf746181fbfa3253fca366feaa
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/30/2020
ms.locfileid: "82612275"
---
# <a name="authentication-and-authorization-for-azure-time-series-insights-api"></a>Autenticação e autorização para API do Azure Time Series Insights

Este documento descreve como registrar um aplicativo no Azure Active Directory usando a nova folha do Azure Active Directory. Os aplicativos registrados no Azure Active Directory permitem que os usuários se autentiquem sejam autorizados a usar a API do Azure Time Series Insights associada a um ambiente do Time Series Insights.

> [!IMPORTANT]
> O Azure Time Series Insights dá suporte às duas bibliotecas de autenticação a seguir:
> * A [MSAL (Biblioteca de Autenticação da Microsoft)](https://docs.microsoft.com/azure/active-directory/develop/msal-overview) mais recente
> * A [ADAL (Biblioteca de Autenticação do Azure Active Directory)](https://docs.microsoft.com/azure/active-directory/develop/active-directory-authentication-libraries)

## <a name="service-principal"></a>Entidade de serviço

As seções a seguir descrevem como configurar um aplicativo para acessar a API do Time Series Insights em nome do aplicativo. O aplicativo pode, então, consultar ou publicar dados de referência no ambiente do Time Series Insights usando as próprias credenciais por meio do Azure Active Directory.

## <a name="summary-and-best-practices"></a>Resumo e melhores práticas

O fluxo de registro de aplicativo do Azure Active Directory envolve três etapas principais.

1. [Registrar um aplicativo](#azure-active-directory-app-registration) no Azure Active Directory.
1. Autorizar o aplicativo a ter [acesso a dados no ambiente do Time Series Insights](#granting-data-access).
1. Usar a **ID do aplicativo** e o **Segredo do cliente** para obter um token de `https://api.timeseries.azure.com/` em seu [aplicativo cliente](#client-app-initialization). O token, em seguida, pode ser usado para chamar a API de análises de séries temporais.

De acordo com a **etapa 3**, separar as credenciais do aplicativo e as do usuário permite:

* Atribuir permissões à identidade do aplicativo diferentes das suas próprias permissões. Normalmente, essas permissões são restritas a apenas o que o aplicativo exige. Por exemplo, você pode permitir que o aplicativo leia dados apenas de um ambiente específico do Time Series Insights.
* Isolar a segurança do aplicativo das credenciais de autenticação do usuário de criação usando um **Segredo do cliente** ou um certificado de segurança. Como resultado, as credenciais do aplicativo não dependem das credenciais de um usuário específico. Se a função do usuário for alterada, o aplicativo não precisará necessariamente de novas credenciais nem de configuração adicional. Se o usuário alterar sua senha, o acesso ao aplicativo não exigirá novas credenciais nem chaves.
* Executar um script autônomo usando um **Segredo do cliente** ou um certificado de segurança em vez das credenciais de um usuário específico (exigindo que ele esteja presente).
* Usar um certificado de segurança em vez de uma senha para proteger o acesso à API do Azure Time Series Insights.

> [!IMPORTANT]
> Siga o princípio de **Separação de escopos** (descrita para este cenário acima) ao configurar sua política de segurança do Azure Time Series Insights.

> [!NOTE]
> * Este artigo se concentra em um aplicativo de locatário único que se destina a ser executado em uma só organização.
> * Normalmente, você usa aplicativos com um locatário para os aplicativos da linha de negócios executados em sua organização.

## <a name="detailed-setup"></a>Configuração detalhada

### <a name="azure-active-directory-app-registration"></a>Registro de aplicativo do Azure Active Directory

[!INCLUDE [Azure Active Directory app registration](../../includes/time-series-insights-aad-registration.md)]

### <a name="granting-data-access"></a>Concedendo acesso a dados

1. Para o ambiente do Azure Time Series Insights, selecione **Políticas de Acesso a Dados** e clique em **Adicionar**.

   [![Adicionar nova política de acesso a dados ao ambiente do Azure Time Series Insights](media/authentication-and-authorization/time-series-insights-data-access-policies-add.png)](media/authentication-and-authorization/time-series-insights-data-access-policies-add.png#lightbox)

1. Na caixa de diálogo **Selecionar Usuário**, cole o **Nome do Aplicativo** ou a **ID do Aplicativo** na seção registro do aplicativo do Azure Active Directory.

   [![Localizar um aplicativo na caixa de diálogo Selecionar usuário](media/authentication-and-authorization/time-series-insights-data-access-policies-select-user.png)](media/authentication-and-authorization/time-series-insights-data-access-policies-select-user.png#lightbox)

1. Selecione a função. Selecione **Leitor** para consultar dados ou **Colaborador** para consultar dados e alterar dados de referência. Selecione **OK**.

   [![Escolher Leitor ou Colaborador na caixa de diálogo Selecionar função](media/authentication-and-authorization/time-series-insights-data-access-policies-select-role.png)](media/authentication-and-authorization/time-series-insights-data-access-policies-select-role.png#lightbox)

1. Salve a política selecionando **OK**.

   > [!TIP]
   > Para opções de acesso a dados avançadas, leia [Concedendo acesso a dados](./time-series-insights-data-access.md).

### <a name="client-app-initialization"></a>Inicialização do aplicativo cliente

* Os desenvolvedores podem usar a [MSAL (Biblioteca de Autenticação da Microsoft)](https://docs.microsoft.com/azure/active-directory/develop/msal-overview) ou a [ADAL (Biblioteca de Autenticação do Azure Active Directory)](https://docs.microsoft.com/azure/active-directory/develop/active-directory-authentication-libraries) para autenticação no Azure Time Series Insights.

* Por exemplo, para a autenticação usando a ADAL:

   1. Use a **ID do Aplicativo** e o **Segredo do Cliente** (chave do aplicativo) da seção de registro do aplicativo do Azure Active Directory para adquirir o token em nome do aplicativo.

   1. Em C#, o código a seguir pode adquirir o token em nome do aplicativo. Para obter um exemplo completo, confira [Consultar dados usando C#](time-series-insights-query-data-csharp.md).

        [!code-csharp[csharpquery-example](~/samples-tsi/csharp-tsi-ga-sample/Program.cs?range=170-199)]

   1. O token, em seguida, pode ser passado ao `Authorization` cabeçalho quando o aplicativo chama a API de análises de séries temporais.

* Como alternativa, os desenvolvedores podem optar por autenticar usando a MSAL. Leia sobre a [migração para a MSAL](https://docs.microsoft.com/azure/active-directory/develop/msal-net-migration) e confira nosso artigo [Gerenciar dados de referência de GA de um ambiente do Azure Time Series Insights usando o C#](time-series-insights-manage-reference-data-csharp.md) para saber mais.

## <a name="common-headers-and-parameters"></a>Cabeçalhos e parâmetros comuns

Esta seção descreve os cabeçalhos de solicitação HTTP e os parâmetros comuns usados para fazer consultas nas APIs de Versão Prévia e GA do Time Series Insights. Os requisitos específicos da API são abordados com mais detalhes na [Documentação de referência da API REST do Time Series Insights](https://docs.microsoft.com/rest/api/time-series-insights/).

> [!TIP]
> Leia a [Referência da API REST do Azure](https://docs.microsoft.com/rest/api/azure/) para saber mais sobre como consumir APIs REST, fazer solicitações HTTP e manipular respostas HTTP.

### <a name="authentication"></a>Autenticação

Para executar consultas autenticadas nas [APIs REST do Time Series Insights](https://docs.microsoft.com/rest/api/time-series-insights/), um token de portador do OAuth 2.0 válido precisa ser passado no [Cabeçalho de autorização](/rest/api/apimanagement/2019-12-01/authorizationserver/createorupdate) usando um cliente REST de sua escolha (post, JavaScript, C# ).

> [!TIP]
> Leia o [exemplo de visualização do SDK de cliente](https://tsiclientsample.azurewebsites.net/) do Azure Time Series Insights hospedado para saber como autenticar programaticamente com as APIs do Time Series Insights usando o [SDK do cliente JavaScript](https://github.com/microsoft/tsiclient/blob/master/docs/API.md) com gráficos e grafos.

### <a name="http-headers"></a>Cabeçalhos HTTP

Os cabeçalhos de solicitação necessários são descritos abaixo.

| Cabeçalho de solicitação necessário | Descrição |
| --- | --- |
| Autorização | Para autenticar com o Time Series Insights, um token de portador OAuth 2.0 válido precisa ser passado no cabeçalho de **Autorização**. |

> [!IMPORTANT]
> O token deve ser emitido exatamente para o recurso `https://api.timeseries.azure.com/` (também conhecido como o "público" do token).
> * Sendo assim, sua [AuthURL](https://www.getpostman.com/) do **Postman** será: `https://login.microsoftonline.com/microsoft.onmicrosoft.com/oauth2/authorize?resource=https://api.timeseries.azure.com/`
> * `https://api.timeseries.azure.com/` é válido, mas `https://api.timeseries.azure.com` não é.

Os cabeçalhos de solicitação opcionais são descritos abaixo.

| Cabeçalho de solicitação opcional | Descrição |
| --- | --- |
| Content-type | Apenas `application/json` tem suporte. |
| x-ms-client-request-id | Uma ID de solicitação do cliente. O serviço registra esse valor. Permite que o serviço rastreie a operação entre serviços. |
| x-ms-client-session-id | Uma ID de sessão do cliente. O serviço registra esse valor. Permite que o serviço rastreie um grupo de operações relacionadas entre serviços. |
| x-ms-client-application-name | Nome do aplicativo que gerou a solicitação. O serviço registra esse valor. |

Os cabeçalhos de resposta opcionais, mas recomendados, são descritos abaixo.

| Cabeçalho de resposta | Descrição |
| --- | --- |
| Content-type | Apenas `application/json` tem suporte. |
| x-ms-request-id | ID da solicitação gerada pelo servidor. Pode ser usada para contatar a Microsoft para investigar uma solicitação. |
| x-ms-property-not-found-behavior | Cabeçalho de resposta opcional da API de GA. Os valores possíveis são `ThrowError` (padrão) ou `UseNull`. |

### <a name="http-parameters"></a>Parâmetros HTTP

> [!TIP]
> Saiba mais sobre as informações de consulta necessárias e opcionais na [documentação de referência](https://docs.microsoft.com/rest/api/time-series-insights/).

Os parâmetros necessários da cadeia de consulta de URL dependem da versão da API.

| Versão | Valores possíveis de versão da API |
| --- |  --- |
| Disponibilidade geral | `api-version=2016-12-12`|
| Visualização | `api-version=2018-11-01-preview` |
| Visualização | `api-version=2018-08-15-preview` |

Os parâmetros opcionais de cadeia de consulta de URL incluem a definição de um tempo limite para os tempos de execução da solicitação HTTP.

| Parâmetro de consulta opcional | Descrição | Versão |
| --- |  --- | --- |
| `timeout=<timeout>` | Tempo limite do lado do servidor para a execução da solicitação HTTP. Aplicável somente às APIs [Obter Eventos de Ambiente](https://docs.microsoft.com/rest/api/time-series-insights/ga-query-api#get-environment-events-api) e [Obter Agregações de Ambiente](https://docs.microsoft.com/rest/api/time-series-insights/ga-query-api#get-environment-aggregates-api). O valor do tempo limite deve estar no formato de duração ISO 8601, por exemplo, `"PT20S"` e deve estar no intervalo `1-30 s`. O valor padrão é `30 s`. | GA |
| `storeType=<storeType>` | Para ambientes de Versão prévia com armazenamento warm habilitado, a consulta pode ser executada no `WarmStore` ou no `ColdStore`. Esse parâmetro na consulta define em qual armazenamento a consulta deve ser executada. Se ele não estiver definido, a consulta será executada no armazenamento cold. Para consultar o armazenamento warm, **storeType** precisa ser definido como `WarmStore`. Se ele não estiver definido, a consulta será executada no armazenamento cold. | Visualização |

## <a name="next-steps"></a>Próximas etapas

- Para ver um código de exemplo que chama a API de GA do Time Series Insights, confira [Consultar dados usando C#](./time-series-insights-query-data-csharp.md).

- Para ver códigos de exemplo da API de Versão Prévia do Time Series Insights, confira [Consultar dados de versão prévia usando C#](./time-series-insights-update-query-data-csharp.md).

- Para obter informações de referência da API, leia a documentação de [Referência da API de Consulta](https://docs.microsoft.com/rest/api/time-series-insights/ga-query-api).

- Saiba como [criar uma entidade de serviço](../active-directory/develop/howto-create-service-principal-portal.md).
