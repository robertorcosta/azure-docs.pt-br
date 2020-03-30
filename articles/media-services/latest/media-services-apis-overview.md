---
title: Desenvolver com APIs v3
titleSuffix: Azure Media Services
description: Conheça as regras que se aplicam a entidades e APIs ao desenvolver com os Serviços de Mídia v3.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 10/21/2019
ms.author: juliako
ms.custom: seodec18
ms.openlocfilehash: eacdfe8211c97e75b6609f5e11b681f84ae55846
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79472077"
---
# <a name="develop-with-media-services-v3-apis"></a>Desenvolver com Media Services v3 APIs

Como desenvolvedor, você pode usar a [API REST](https://docs.microsoft.com/rest/api/media/) dos Serviços de Mídia ou bibliotecas de clientes que permitem interagir com a API REST para criar, gerenciar e manter fluxos de trabalho de mídia personalizados com facilidade. A API [v3 do Media Services](https://aka.ms/ams-v3-rest-sdk) é baseada na especificação OpenAPI (anteriormente conhecida como Swagger).

Este artigo discute regras que se aplicam a entidades e APIs quando você se desenvolve com os Serviços de Mídia v3.

## <a name="accessing-the-azure-media-services-api"></a>Acessando a API de serviços de mídia do Azure

Para estar autorizado a acessar os recursos e a API dos Serviços de Mídia, primeiro você deve ser autenticado. Os Serviços de Mídia suportam a autenticação [baseada no Azure Active Directory (Azure AD).](../../active-directory/fundamentals/active-directory-whatis.md) Duas opções comuns de autenticação são:
 
* **Autenticação principal do**serviço : Usado para autenticar um serviço (por exemplo: aplicativos web, aplicativos de função, aplicativos lógicos, API e microsserviços). Os aplicativos que geralmente usam esse método de autenticação são aplicativos que executam serviços daemon, serviços de camada intermediária ou trabalhos agendados. Por exemplo, para aplicativos web deve haver sempre um intermediário que se conecta aos Serviços de Mídia com um Diretor de Serviço.
* **Autenticação do usuário**: Usado para autenticar uma pessoa que está usando o aplicativo para interagir com os recursos do Media Services. O aplicativo interativo deve primeiro solicitar ao usuário as credenciais do usuário. Um exemplo é um aplicativo de console de gerenciamento usado por usuários autorizados para monitorar trabalhos de codificação ou uma transmissão ao vivo.

A API de Serviços de Mídia exige que o usuário ou aplicativo que faz as solicitações da API REST tenha acesso ao recurso de conta do Media Services e use uma função **contribuinte** ou **proprietário.** A API pode ser acessada com a função **Leitor,** mas apenas as operações **Get** ou **List** estarão disponíveis.Para obter mais informações, consulte [o controle de acesso baseado em função para contas de Serviços de Mídia](rbac-overview.md).

Em vez de criar um principal de serviço, considere usar identidades gerenciadas para recursos do Azure para acessar a API de Serviços de Mídia através do Azure Resource Manager. Para saber mais sobre identidades gerenciadas para os recursos do Azure, consulte [O que é identidades gerenciadas para os recursos do Azure](../../active-directory/managed-identities-azure-resources/overview.md).

### <a name="azure-ad-service-principal"></a>Diretor de serviços azure AD

Se você está criando um aplicativo azure AD e diretor de serviço, o aplicativo tem que estar em seu próprio inquilino. Depois de criar o aplicativo, dê ao aplicativo **O Contribuinte** ou **Proprietário** tem acesso à conta dos Serviços de Mídia.

Se você não tiver certeza se tem permissões para criar um aplicativo AD do Azure, consulte [as permissões necessárias](../../active-directory/develop/howto-create-service-principal-portal.md#required-permissions).

Na figura a seguir, os números representam o fluxo das solicitações em ordem cronológica:

![Autenticação de aplicativo de nível intermediário com AAD de uma API web](./media/use-aad-auth-to-access-ams-api/media-services-principal-service-aad-app1.png)

1. Um aplicativo intermediário solicita um token de acesso Azure AD que tenha os seguintes parâmetros:  

   * Ponto de extremidade do locatário do Azure AD.
   * URI de recurso dos Serviços de Mídia.
   * URI de recurso dos Serviços de Mídia REST.
   * Valores do aplicativo Azure AD: o ID do cliente e o cliente secreto.

   Para obter todos os valores necessários, consulte [a API Access Azure Media Services com o Azure CLI](access-api-cli-how-to.md).

2. O token de acesso do Azure AD é enviado à camada intermediária.
4. A camada intermediária envia a solicitação à API REST da Mídia do Azure com o token do Azure AD.
5. A camada intermediária obtém novamente os dados dos Serviços de Mídia.

### <a name="samples"></a>Exemplos

Veja as seguintes amostras que mostram como se conectar com o diretor de serviço saque azure AD:

* [Conecte-se com REST](media-rest-apis-with-postman.md)  
* [Conectar-se com Java](configure-connect-java-howto.md)
* [Conectar-se com .NET](configure-connect-dotnet-howto.md)
* [Conectar-se com Node.js](configure-connect-nodejs-howto.md)
* [Conectar-se com Python](configure-connect-python-howto.md)

## <a name="naming-conventions"></a>Convenções de nomenclatura

Nomes de recursos do Serviços de Mídia do Azure v3 (por exemplo, ativos, trabalhos, transformações) estão sujeitos a restrições de nomenclatura do Azure Resource Manager. De acordo com o Azure Resource Manager, os nomes dos recursos são sempre exclusivos. Desse modo, é possível usar qualquer cadeia de caracteres de identificador exclusivo (por exemplo, GUIDs) para os nomes dos recursos.

Os nomes de recursos do Media Services não podem incluir: '<', '>', '%', '&', ':', '&#92;', '?', '/', '*', '+', o caractere de citação única ou qualquer caractere de controle. Todos os outros caracteres são permitidos. O comprimento máximo de um nome de recurso é de 260 caracteres.

Para obter mais informações sobre a nomeação do Azure Resource Manager, consulte [requisitos de nomeação](https://github.com/Azure/azure-resource-manager-rpc/blob/master/v1.0/resource-api-reference.md#arguments-for-crud-on-resource) e [convenções de nomeação](/azure/cloud-adoption-framework/ready/azure-best-practices/naming-and-tagging).

### <a name="names-of-filesblobs-within-an-asset"></a>Nomes de arquivos/blobs dentro de um ativo

Os nomes de arquivos/blobs dentro de um ativo devem seguir os requisitos de [nome blob](https://docs.microsoft.com/rest/api/storageservices/Naming-and-Referencing-Containers--Blobs--and-Metadata) e os [requisitos de nome NTFS](https://docs.microsoft.com/windows/win32/fileio/naming-a-file). A razão para esses requisitos é que os arquivos podem ser copiados do armazenamento blob para um disco NTFS local para processamento.

## <a name="long-running-operations"></a>Operações de longo prazo

As operações `x-ms-long-running-operation` marcadas com os [arquivos de swagger](https://github.com/Azure/azure-rest-api-specs/blob/master/specification/mediaservices/resource-manager/Microsoft.Media/stable/2018-07-01/streamingservice.json) do Azure Media Services são operações de longa duração. 

Para obter detalhes sobre como rastrear operações assíncronas do Azure, consulte [operações do Async](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-async-operations#monitor-status-of-operation).

Os Serviços de Mídia têm as seguintes operações de longa duração:

* [Criar eventos ao vivo](https://docs.microsoft.com/rest/api/media/liveevents/create)
* [Atualizar eventos ao vivo](https://docs.microsoft.com/rest/api/media/liveevents/update)
* [Excluir evento ao vivo](https://docs.microsoft.com/rest/api/media/liveevents/delete)
* [Iniciar evento ao vivo](https://docs.microsoft.com/rest/api/media/liveevents/start)
* [Pare liveevent](https://docs.microsoft.com/rest/api/media/liveevents/stop)

  Use `removeOutputsOnStop` o parâmetro para excluir todas as saídas ao vivo associadas ao interromper o evento.  
* [Redefinir LiveEvent](https://docs.microsoft.com/rest/api/media/liveevents/reset)
* [Criar liveoutput](https://docs.microsoft.com/rest/api/media/liveevents/create)
* [Excluir LiveOutput](https://docs.microsoft.com/rest/api/media/liveevents/delete)
* [Criar StreamingEndpoint](https://docs.microsoft.com/rest/api/media/streamingendpoints/create)
* [Atualizar streamingEndpoint](https://docs.microsoft.com/rest/api/media/streamingendpoints/update)
* [Excluir streamingEndpoint](https://docs.microsoft.com/rest/api/media/streamingendpoints/delete)
* [Iniciar StreamingEndpoint](https://docs.microsoft.com/rest/api/media/streamingendpoints/start)
* [Parar streamingEndpoint](https://docs.microsoft.com/rest/api/media/streamingendpoints/stop)
* [Scale StreamingEndpoint](https://docs.microsoft.com/rest/api/media/streamingendpoints/scale)

Após a apresentação bem-sucedida de uma operação longa, você recebe um '202 Aceito' e deve fazer uma pesquisa para conclusão da operação usando o ID de operação retornado.

O artigo [Acompanhar as operações assíncronas do Azure](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-async-operations) explica detalhadamente como acompanhar o status das operações assíncronas do Azure por meio dos valores retornados na resposta.

Apenas uma operação de longa duração é suportada para um determinado Evento Ao Vivo ou qualquer uma de suas saídas ao vivo associadas. Uma vez iniciada, uma operação de longa duração deve ser concluída antes de iniciar uma operação subsequente de longo prazo no mesmo LiveEvent ou em quaisquer saídas ao vivo associadas. Para eventos ao vivo com várias saídas ao vivo, você deve aguardar a conclusão de uma operação de longa duração em uma saída ao vivo antes de desencadear uma operação de longa duração em outra saída ao vivo. 

## <a name="sdks"></a>SDKs

> [!NOTE]
> Os SDKs V3 do Azure Media Services não são garantidos como seguros para threads. Ao desenvolver um aplicativo com vários threads, você deve adicionar sua própria lógica de sincronização de threads para proteger o cliente ou usar um novo objeto AzureMediaServicesClient por thread. Você também deve tomar cuidado com problemas de multi-threading introduzidos por objetos opcionais fornecidos pelo código ao cliente (como uma instância do HttpClient no .NET).

|.|Referência|
|---|---|
|[.NET SDK](https://aka.ms/ams-v3-dotnet-sdk)|[Referência do .NET](https://aka.ms/ams-v3-dotnet-ref)|
|[Java SDK](https://aka.ms/ams-v3-java-sdk)|[Referência de Java](https://aka.ms/ams-v3-java-ref)|
|[SDK do Python](https://aka.ms/ams-v3-python-sdk)|[Referência do Python](https://aka.ms/ams-v3-python-ref)|
|[SDK do Node.js](https://aka.ms/ams-v3-nodejs-sdk) |[Referência do Node.js](/javascript/api/overview/azure/mediaservices/management)| 
|[SDK do Go](https://aka.ms/ams-v3-go-sdk) |[Referência do Go](https://aka.ms/ams-v3-go-ref)|
|[SDK do Ruby](https://aka.ms/ams-v3-ruby-sdk)||

### <a name="see-also"></a>Confira também

- [SDK do .NET EventGrid que inclui eventos de serviço de mídia](https://www.nuget.org/packages/Microsoft.Azure.EventGrid/)
- [Definições de eventos de Serviços de Mídia](https://github.com/Azure/azure-rest-api-specs/blob/master/specification/eventgrid/data-plane/Microsoft.Media/stable/2018-01-01/MediaServices.json)

## <a name="azure-media-services-explorer"></a>Gerenciador dos Serviços de Mídia do Azure

O [AMSE](https://github.com/Azure/Azure-Media-Services-Explorer) (Gerenciador dos Serviços de Mídia do Azure) é uma ferramenta disponível para clientes do Windows que desejam saber mais sobre os Serviços de Mídia. O AMSE é um aplicativo do WinForms/C# que carrega, baixa, codifica e transmite VoD e conteúdo ao vivo com os Serviços de Mídia. A ferramenta AMSE destina-se aos clientes que desejam testar os Serviços de Mídia sem escrever nenhum código. O código AMSE é fornecido como um recurso para clientes que desejam desenvolver com os Serviços de Mídia.

O AMSE é um projeto de software livre, cujo suporte é fornecido pela comunidade (os problemas podem ser relatados a https://github.com/Azure/Azure-Media-Services-Explorer/issues). Este projeto adotou o [Código de Conduta de Software Livre da Microsoft](https://opensource.microsoft.com/codeofconduct/). Para obter mais informações, consulte o opencode@microsoft.com Código de Conduta [ou](https://opensource.microsoft.com/codeofconduct/faq/) entre em contato com quaisquer outras perguntas ou comentários.

## <a name="filtering-ordering-paging-of-media-services-entities"></a>Filtragem, classificação, paginação de entidades dos Serviços de Mídia

Consulte [Filtragem, encomenda, paginação de entidades do Azure Media Services](entities-overview.md).

## <a name="ask-questions-give-feedback-get-updates"></a>Fazer perguntas, comentar, obter atualizações

Confira o artigo [comunidade dos Serviços de Mídia do Azure](media-services-community.md) para ver diferentes maneiras de fazer perguntas, comentários e obter atualizações sobre os serviços de mídia.

## <a name="see-also"></a>Confira também

[Azure CLI](https://docs.microsoft.com/cli/azure/ams?view=azure-cli-latest)

## <a name="next-steps"></a>Próximas etapas

* [Conecte-se aos serviços de mídia com Java](configure-connect-java-howto.md)
* [Conecte-se aos Serviços de Mídia com .NET](configure-connect-dotnet-howto.md)
* [Conecte-se aos Serviços de Mídia com node.js](configure-connect-nodejs-howto.md)
* [Conecte-se aos serviços de mídia com python](configure-connect-python-howto.md)
