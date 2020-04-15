---
title: Autenticação e autorização da API - Azure Time Series Insights | Microsoft Docs
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
ms.custom: seodec18
ms.openlocfilehash: beefad41a270233336bb9134268c98341e81a7cd
ms.sourcegitcommit: ea006cd8e62888271b2601d5ed4ec78fb40e8427
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/14/2020
ms.locfileid: "81380801"
---
# <a name="authentication-and-authorization-for-azure-time-series-insights-api"></a>Autenticação e autorização para API do Azure Time Series Insights

Este documento descreve como registrar um aplicativo no Azure Active Directory usando a nova lâmina do Azure Active Directory. Os aplicativos registrados no Azure Active Directory permitem que os usuários autentiquem e sejam autorizados a usar a API Azure Time Series Insight associada a um ambiente time series insights.

> [!IMPORTANT]
> O Azure Time Series Insights suporta as seguintes bibliotecas de autenticação:
> * A mais recente [Biblioteca de Autenticação da Microsoft (MSAL)](https://docs.microsoft.com/azure/active-directory/develop/msal-overview)
> * A [Biblioteca de Autenticação de Diretório Ativo do Azure (ADAL)](https://docs.microsoft.com/azure/active-directory/develop/active-directory-authentication-libraries)

## <a name="service-principal"></a>Entidade de serviço

As seções a seguir descrevem como configurar um aplicativo para acessar a API de Insights da Série do Tempo em nome de um aplicativo. O aplicativo pode então consultar ou publicar dados de referência no ambiente Time Series Insights usando suas próprias credenciais de aplicativo através do Azure Active Directory.

## <a name="summary-and-best-practices"></a>Resumo e melhores práticas

O fluxo de registro do aplicativo Azure Active Directory envolve três etapas principais.

1. [Registre um aplicativo](#azure-active-directory-app-registration) no Azure Active Directory.
1. Autorize o aplicativo a ter [acesso aos dados ao ambiente Time Series Insights](#granting-data-access).
1. Use o **ID de aplicativo** e o `https://api.timeseries.azure.com/` Client **Secret** para adquirir um token no aplicativo do seu [cliente](#client-app-initialization). O token, em seguida, pode ser usado para chamar a API de análises de séries temporais.

De **acordo com a etapa 3,** separar as credenciais do seu aplicativo e das suas credenciais de usuário permite:

* Atribua permissões à identidade do aplicativo que são distintas de suas próprias permissões. Normalmente, essas permissões são restritas a apenas o que o aplicativo exige. Por exemplo, você pode permitir que o aplicativo leia dados apenas de um ambiente específico do Time Series Insights.
* Isole a segurança do aplicativo das credenciais de autenticação do usuário criador usando um **Client Secret** ou certificado de segurança. Como resultado, as credenciais do aplicativo não dependem das credenciais de um usuário específico. Se a função do usuário mudar, o aplicativo não necessariamente requer novas credenciais ou configuração adicional. Se o usuário alterar sua senha, todo o acesso ao aplicativo não requer novas credenciais ou chaves.
* Execute um script autônomo usando um **Client Secret** ou certificado de segurança em vez de credenciais de um usuário específico (exigindo que eles estejam presentes).
* Use um certificado de segurança em vez de uma senha para garantir o acesso à sua API azure Time Series Insights.

> [!IMPORTANT]
> Siga o princípio da **Separação de Preocupações** (descrito para este cenário acima) ao configurar sua política de segurança do Azure Time Series Insights.

> [!NOTE]
> * O artigo se concentra em um aplicativo de inquilino único onde o aplicativo é destinado a ser executado em apenas uma organização.
> * Você normalmente usará aplicativos de inquilino único para aplicativos de linha de negócios que são executados em sua organização.

## <a name="detailed-setup"></a>Configuração detalhada

### <a name="azure-active-directory-app-registration"></a>Registro do aplicativo Azure Active Directory

[!INCLUDE [Azure Active Directory app registration](../../includes/time-series-insights-aad-registration.md)]

### <a name="granting-data-access"></a>Concessão de acesso a dados

1. Para o ambiente Time Series Insights, selecione **Políticas de acesso a dados** e selecione **Adicionar**.

   [![Adicionar nova política de acesso de dados para o ambiente de análises de séries temporais](media/authentication-and-authorization/time-series-insights-data-access-policies-add.png)](media/authentication-and-authorization/time-series-insights-data-access-policies-add.png#lightbox)

1. Na caixa de diálogo **Selecionar usuário,** cole o Nome do **aplicativo** ou o **ID** do aplicativo da seção de registro do aplicativo Azure Active Directory.

   [![Localizar um aplicativo na caixa de diálogo Selecionar usuário](media/authentication-and-authorization/time-series-insights-data-access-policies-select-user.png)](media/authentication-and-authorization/time-series-insights-data-access-policies-select-user.png#lightbox)

1. Selecione o papel. Selecione **'Leitor'** para consultar dados ou **Contribuinte** para consultar dados e alterar dados de referência. Selecione **OK**.

   [![Escolha leitor ou contribuinte na caixa de diálogo Selecionar função do usuário](media/authentication-and-authorization/time-series-insights-data-access-policies-select-role.png)](media/authentication-and-authorization/time-series-insights-data-access-policies-select-role.png#lightbox)

1. Salve a diretiva selecionando **OK**.

   > [!TIP]
   > Para opções avançadas de acesso a dados, leia [a concessão de acesso aos dados](./time-series-insights-data-access.md).

### <a name="client-app-initialization"></a>Inicialização de aplicativos clientes

* Os desenvolvedores podem usar a [Microsoft Authentication Library (MSAL)](https://docs.microsoft.com/azure/active-directory/develop/msal-overview) ou [a Azure Active Directory Authentication Library (ADAL)](https://docs.microsoft.com/azure/active-directory/develop/active-directory-authentication-libraries) para autenticar com o Azure Time Series Insights.

* Por exemplo, para autenticar usando ADAL:

   1. Use o **ID do aplicativo** e **o customer secret** (chave de aplicativo) da seção de registro do aplicativo Azure Active Directory para adquirir o token em nome do aplicativo.

   1. Em C#, o código a seguir pode adquirir o token em nome do aplicativo. Para obter uma amostra completa, leia [os dados da consulta usando C#](time-series-insights-query-data-csharp.md).

        [!code-csharp[csharpquery-example](~/samples-tsi/csharp-tsi-ga-sample/Program.cs?range=170-199)]

   1. O token, em seguida, pode ser passado ao `Authorization` cabeçalho quando o aplicativo chama a API de análises de séries temporais.

* Alternativamente, os desenvolvedores podem optar por autenticar usando o MSAL. Leia sobre [a migração para o MSAL](https://docs.microsoft.com/azure/active-directory/develop/msal-net-migration) e consulte [nossos dados de referência do Manage GA para um ambiente azure Time Series Insights usando o](time-series-insights-manage-reference-data-csharp.md) artigo C# para saber mais. 

## <a name="common-headers-and-parameters"></a>Cabeçalhos e parâmetros comuns

Esta seção descreve cabeçalhos e parâmetros comuns de solicitação HTTP usados para fazer consultas contra as APIs GA e Preview Do Time Series Insights GA e Preview. Os requisitos específicos da API são cobertos com mais detalhes na [documentação de referência da API Do Time Series Insights REST](https://docs.microsoft.com/rest/api/time-series-insights/).

> [!TIP]
> Leia a [API Azure REST](https://docs.microsoft.com/rest/api/azure/) para saber mais sobre como consumir APIs REST, fazer solicitações HTTP e lidar com respostas HTTP.

### <a name="authentication"></a>Autenticação

Para realizar consultas autenticadas contra as [APIs REST do Time Series Insights](https://docs.microsoft.com/rest/api/time-series-insights/), um token carregador OAuth 2.0 válido deve ser passado no [cabeçalho autorização](/rest/api/apimanagement/2019-12-01/authorizationserver/createorupdate) usando um cliente REST de sua escolha (Carteiro, JavaScript, C#). 

> [!TIP]
> Leia a visualização de amostra [sdk](https://tsiclientsample.azurewebsites.net/) do cliente Azure Time Series Insights para aprender a autenticar com as APIs do Time Series Insights de forma programática usando o [Cliente JavaScript SDK,](https://github.com/microsoft/tsiclient/blob/master/docs/API.md) juntamente com gráficos e gráficos.

### <a name="http-headers"></a>Cabeçalhos HTTP

Os cabeçalhos de solicitação necessários são descritos abaixo.

| Cabeçalho de solicitação necessário | Descrição |
| --- | --- |
| Autorização | Para autenticar com o Time Series Insights, um token válido do Portador OAuth 2.0 deve ser passado no cabeçalho **Autorização.** | 

> [!IMPORTANT]
> O token deve ser emitido `https://api.timeseries.azure.com/` exatamente para o recurso (também conhecido como "público" do token).
> * Seu [Carteiro](https://www.getpostman.com/) **AuthURL** será, portanto:`https://login.microsoftonline.com/microsoft.onmicrosoft.com/oauth2/authorize?resource=https://api.timeseries.azure.com/`
> * `https://api.timeseries.azure.com/`é válido, mas `https://api.timeseries.azure.com` não é.

Os cabeçalhos de solicitação opcionais são descritos abaixo.

| Cabeçalho de solicitação opcional | Descrição |
| --- | --- |
| Content-type | só `application/json` é suportado. |
| x-ms-client-request-id | Um cliente solicita um id. O serviço registra esse valor. Permite que o serviço rastreie a operação através dos serviços. |
| x-ms-client-session-id | Uma id de sessão de cliente. O serviço registra esse valor. Permite que o serviço rastreie um grupo de operações relacionadas entre os serviços. |
| x-ms-cliente-nome do aplicativo | Nome do aplicativo que gerou essa solicitação. O serviço registra esse valor. |

Os cabeçalhos de resposta opcionais, mas recomendados, são descritos abaixo.

| Cabeçalho de resposta | Descrição |
| --- | --- |
| Content-type | Apenas `application/json` tem suporte. |
| x-ms-request-id | ID de solicitação gerado pelo servidor. Pode ser usado para entrar em contato com a Microsoft para investigar uma solicitação. |
| x-ms-propriedade-não-encontrado-comportamento | Cabeçalho de resposta opcional ga API. Os valores possíveis `ThrowError` `UseNull`são (padrão) ou . |

### <a name="http-parameters"></a>Parâmetros HTTP

> [!TIP]
> Encontre mais informações sobre informações de consulta necessárias e opcionais na [documentação de referência](https://docs.microsoft.com/rest/api/time-series-insights/).

Os parâmetros de seqüência de consulta de URL necessários dependem da versão da API.

| Versão | Possíveis valores de versão da API |
| --- |  --- |
| Disponibilidade geral | `api-version=2016-12-12`|
| Visualização | `api-version=2018-11-01-preview` |
| Visualização | `api-version=2018-08-15-preview` |

Os parâmetros opcionais da seqüência de consultas de URL incluem a definição de um tempo para os tempos de execução da solicitação HTTP.

| Parâmetro de consulta opcional | Descrição | Versão |
| --- |  --- | --- |
| `timeout=<timeout>` | Tempo de intervalo do lado do servidor para execução de solicitação HTTP. Aplicável apenas às APIs [Get Environment Events](https://docs.microsoft.com/rest/api/time-series-insights/ga-query-api#get-environment-events-api) e Get Environment [Aggregates.](https://docs.microsoft.com/rest/api/time-series-insights/ga-query-api#get-environment-aggregates-api) O valor de tempo deve estar no formato de `"PT20S"` duração ISO `1-30 s`8601, por exemplo, e deve estar na faixa . O valor padrão é `30 s`. | GA |
| `storeType=<storeType>` | Para ambientes de visualização com armazenamento aquecido ativado, a `WarmStore` `ColdStore`consulta pode ser executada no ou . Este parâmetro na consulta define em qual armazenar a consulta deve ser executada. Se não for definida, a consulta será executada no cold store. Para consultar a loja quente, **storeType** `WarmStore`precisa ser definido para . Se não for definida, a consulta será executada contra o frigorífico. | Visualização |

## <a name="next-steps"></a>Próximas etapas

- Para obter o código de exemplo que chama a API ga time series insights, leia [os dados de consulta usando C#](./time-series-insights-query-data-csharp.md).

- Para visualizar amostras de código de API da série tempo, leia [os dados de visualização de consulta usando C#](./time-series-insights-update-query-data-csharp.md).

- Para obter informações de referência da API, leia a documentação [de referência da API de consulta.](https://docs.microsoft.com/rest/api/time-series-insights/ga-query-api)

- Aprenda a [criar um diretor de serviço.](../active-directory/develop/howto-create-service-principal-portal.md)
