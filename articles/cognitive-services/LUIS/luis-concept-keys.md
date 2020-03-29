---
title: Como usar as chaves de autore tempo de execução com LUIS
titleSuffix: Azure Cognitive Services
description: Luis usa duas teclas, a chave de criação para criar seu modelo e a chave de tempo de execução para consultar o ponto final de previsão com declarações do usuário.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 10/25/2019
ms.author: diberry
ms.openlocfilehash: 9d213c8fa03ad2ca5e5fd7e620e52aa502749be2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79220958"
---
# <a name="authoring-and-runtime-keys"></a>Chaves de criação e de runtime

O Entendimento do Idioma (LUIS) tem dois serviços e conjuntos de API: 

* Autoria (anteriormente conhecida como _programática)_
* Tempo de execução da previsão

Existem vários tipos-chave, dependendo do serviço com o qual você quer trabalhar e como você quer trabalhar com ele.

## <a name="non-azure-resources-for-luis"></a>Recursos não-Azure para LUIS

### <a name="starter-key"></a>Tecla de partida

Quando você começa a usar LUIS, uma **chave inicial** é criada para você. Este recurso fornece:

* solicitações gratuitas de serviços de autoria através do portal LUIS ou APIs (incluindo SDKs)
* 1.000 solicitações de ponto final de previsão gratuitas por mês através de um navegador, API ou SDKs

## <a name="azure-resources-for-luis"></a>Recursos do Azure para LUIS

<a name="programmatic-key" ></a>
<a name="endpoint-key"></a>
<a name="authoring-key"></a>

Luis permite três tipos de recursos do Azure: 
 
|Chave|Finalidade|Serviço cognitivo`kind`|Serviço cognitivo`type`|
|--|--|--|--|
|[Chave de criação](#programmatic-key)|Acesse e gerencie dados de aplicação com autoria, treinamento, publicação e teste. Crie uma chave de autoria LUIS se você pretende escrever programáticamente aplicativos LUIS.<br><br>O objetivo `LUIS.Authoring` da chave é permitir que você:<br>* gerenciar programáticamente aplicativos e modelos de compreensão de linguagem, incluindo treinamento e publicação<br> * autorizações de controle ao recurso de autoria atribuindo pessoas [à função contribuinte](#contributions-from-other-authors).|`LUIS.Authoring`|`Cognitive Services`|
|[Chave de previsão](#prediction-endpoint-runtime-key)| Solicitações de ponto final de previsão de consulta. Crie uma chave de previsão LUIS antes que seu aplicativo cliente solicite previsões além das 1.000 solicitações fornecidas pelo recurso inicial. |`LUIS`|`Cognitive Services`|
|[Chave de recursos multi-serviço do Serviço Cognitivo](../cognitive-services-apis-create-account-cli.md?tabs=windows#create-a-cognitive-services-resource)|Pedidos de ponto final de previsão de consulta compartilhados com luis e outros Serviços Cognitivos suportados.|`CognitiveServices`|`Cognitive Services`|

Quando o processo de criação de recursos estiver concluído, [atribua a chave](luis-how-to-azure-subscription.md) para o aplicativo no portal LUIS.

É importante escrever aplicativos LUIS em [regiões](luis-reference-regions.md#publishing-regions) onde você deseja publicar e consultar.

> [!CAUTION]
> Para facilitar, muitas das amostras usam a [tecla Starter](#starter-key) porque fornece algumas chamadas de ponto final de previsão gratuitas em sua [cota](luis-boundaries.md#key-limits).  


### <a name="query-prediction-resources"></a>Recursos de previsão de consulta

* A chave de tempo de execução pode ser usada para todos os seus aplicativos LUIS ou para aplicativos LUIS específicos. 
* Não use a chave de tempo de execução para a autoria de aplicativos LUIS. 

O ponto final de tempo de execução LUIS aceita dois estilos de consulta, ambos usam a tecla de tempo de execução de ponto final de previsão, mas em lugares diferentes.

O ponto final usado para acessar o tempo de execução usa um subdomínio exclusivo da região do seu recurso, denotado `{region}` na tabela a seguir. 

## <a name="assignment-of-the-key"></a>Atribuição da chave

Você pode [atribuir](luis-how-to-azure-subscription.md) a chave de tempo de execução no [portal LUIS](https://www.luis.ai) ou através das APIs correspondentes. 

## <a name="key-limits"></a>Limites de chave

Você pode criar até 10 chaves de autoria por região por assinatura. 

Consulte [Os limites de chave](luis-boundaries.md#key-limits) e as [regiões do Azure](luis-reference-regions.md). 

Regiões de publicação são diferentes de regiões de criação. Certifique-se de criar um aplicativo na região de autoria correspondente à região de publicação que deseja que seu aplicativo cliente seja localizado.

## <a name="key-limit-errors"></a>Erros no limite de chave
Se você exceder sua cota de transações por segundo (TPS), você receberá um erro HTTP 429. Se você exceder sua cota de transação por mês (TPS), você receberá um erro HTTP 403. 

## <a name="contributions-from-other-authors"></a>Contribuições de outros autores

**Para a autoria de aplicativos [migrados de recursos](luis-migration-authoring.md) **: os contribuintes são _gerenciados_ no portal Azure para o recurso de autoria, usando a página **de controle de acesso (IAM).** Saiba [como adicionar um usuário,](luis-how-to-collaborate.md)usando o endereço de e-mail do colaborador e a função de _contribuinte._ 

**Para aplicativos que ainda não migraram**: todos os colaboradores são _gerenciados_ no portal LUIS a partir da página **Gerenciar -> Colaboradores.**

## <a name="move-transfer-or-change-ownership"></a>Mover, transferir ou alterar a propriedade

Um aplicativo é definido por seus recursos do Azure, que é determinado pela assinatura do proprietário. 

Você pode mover seu aplicativo LUIS. Use os seguintes recursos de documentação no portal Azure ou no Azure CLI:

* [Mover aplicativo entre os recursos de autoria da LUIS](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/apps-move-app-to-another-luis-authoring-azure-resource)
* [Mova recurso para novo grupo de recursos ou assinatura](../../azure-resource-manager/management/move-resource-group-and-subscription.md)
* [Mova recurso dentro da mesma assinatura ou através de assinaturas](../../azure-resource-manager/management/move-limitations/app-service-move-limitations.md)

Para transferir [a propriedade](../../cost-management-billing/manage/billing-subscription-transfer.md) de sua assinatura: 

**Para usuários que migraram - [recursos de autoria migraram](luis-migration-authoring.md) aplicativos**: Como o proprietário do recurso, você pode adicionar um `contributor`.

**Para usuários que ainda não migraram**: Exporte seu aplicativo como um arquivo JSON. Outro usuário LUIS pode importar o aplicativo, tornando-se assim o proprietário do aplicativo. O novo app terá um ID de aplicativo diferente.  

## <a name="access-for-private-and-public-apps"></a>Acesso para aplicativos públicos e privados

Para um aplicativo **privado,** o acesso em tempo de execução está disponível para proprietários e colaboradores. Para um aplicativo **público,** o acesso em tempo de execução está disponível para todos que possuem seu próprio recurso de [tempo de execução](../cognitive-services-apis-create-account.md) Azure Cognitive Service ou [LUIS,](luis-how-to-azure-subscription.md#create-resources-in-the-azure-portal) e tem o ID do aplicativo público. 

Atualmente, não há um catálogo de aplicativos públicos.

### <a name="authoring-access"></a>Acesso de autoria
O acesso ao aplicativo a partir do portal [LUIS](luis-reference-regions.md#luis-website) ou das [APIs de autoria](https://go.microsoft.com/fwlink/?linkid=2092087) é controlado pelo recurso de autoria do Azure. 

O proprietário e todos os contribuintes têm acesso ao autor do aplicativo. 

|O acesso de criação inclui|Observações|
|--|--|
|Adicionar ou remover chaves de ponto de extremidade||
|Versão de exportação||
|Exportar logs de ponto de extremidade||
|Versão de importação||
|Tornar um aplicativo público|Quando um aplicativo for público, qualquer pessoa com uma chave de criação ou de ponto de extremidade poderá consultá-lo.|
|Modificar modelo|
|Publicar|
|Examinar declarações de ponto de extremidade para [aprendizado ativo](luis-how-to-review-endpoint-utterances.md)|
|Treinar|

<a name="prediction-endpoint-runtime-key"></a>

### <a name="prediction-endpoint-runtime-access"></a>Acesso em tempo final de ponto final de previsão

O acesso à consulta é controlado por uma configuração na página Informações do **aplicativo** na seção **Gerenciar.** 

|[Ponto final privado](#runtime-security-for-private-apps)|[Ponto de extremidade público](#runtime-security-for-public-apps)|
|:--|:--|
|Disponível para proprietários e colaboradores|Disponível para proprietários, contribuintes e qualquer outra pessoa que conheça a ID do aplicativo|

Você pode controlar quem vê sua chave de tempo de execução LUIS chamando-a em um ambiente servidor-para-servidor. Se você estiver usando o LUIS de um bot, a conexão entre o bot e o LUIS já estará segura. Se você estiver chamando o ponto de extremidade LUIS diretamente, deverá criar a API do servidor (como uma [função](https://azure.microsoft.com/services/functions/) do Azure) com acesso controlado (como [AAD](https://azure.microsoft.com/services/active-directory/)). Quando a API do lado do servidor for chamada e autenticada e a autorização for verificada, passe a chamada para LUIS. Embora essa estratégia não impeça ataques man-in-the-middle, ela ofusca sua URL de chave e ponto final de seus usuários, permite que você rastreie o acesso e permite adicionar registro de resposta de ponto final (como [o Application Insights](https://azure.microsoft.com/services/application-insights/)).

#### <a name="runtime-security-for-private-apps"></a>Segurança em tempo de execução para aplicativos privados

O tempo de execução de um aplicativo privado só está disponível para o seguinte:

|Chave e usuário|Explicação|
|--|--|
|Chave de criação do proprietário| Até 1000 ocorrências de ponto de extremidade|
|Chaves de autoria do colaborador/colaborador| Até 1000 ocorrências de ponto de extremidade|
|Qualquer chave atribuída ao LUIS por um autor ou colaborador/colaborador|Com base na camada de uso da chave|

#### <a name="runtime-security-for-public-apps"></a>Segurança em tempo de execução para aplicativos públicos

Depois que um aplicativo for configurado como público, _qualquer_ chave de criação LUIS válida ou chave do ponto de extremidade LUIS poderá consultar seu aplicativo, desde que a chave não tenha usado toda a cota de ponto de extremidade.

Um usuário que não é proprietário ou contribuinte, só pode acessar o tempo de execução de um aplicativo público se for dado o ID do aplicativo. O LUIS não tem um _mercado_ público ou outra maneira de pesquisar um aplicativo público.  

Um aplicativo público é publicado em todas as regiões para que um usuário com uma chave de recurso do LUIS baseada em região possa acessar o aplicativo em qualquer região associada com a chave de recurso.

## <a name="transfer-of-ownership"></a>Transferência de propriedade

Luis não tem o conceito de transferir a propriedade de um recurso. 

## <a name="securing-the-endpoint"></a>Protegendo o ponto de extremidade 

Você pode controlar quem pode ver sua chave de ponto final de tempo de execução de previsão do LUIS chamando-a em um ambiente servidor-para-servidor. Se você estiver usando o LUIS de um bot, a conexão entre o bot e o LUIS já estará segura. Se você estiver chamando o ponto de extremidade LUIS diretamente, deverá criar a API do servidor (como uma [função](https://azure.microsoft.com/services/functions/) do Azure) com acesso controlado (como [AAD](https://azure.microsoft.com/services/active-directory/)). Quando a API do servidor for chamada e a autenticação e a autorização forem verificadas, passe a chamada para o LUIS. Embora essa estratégia não impeça ataques "man-in-the-middle", ela oculta seu ponto de extremidade dos usuários, permite que você controle o acesso e que você adicione um log de resposta de ponto de extremidade (como o [Application Insights](https://azure.microsoft.com/services/application-insights/)).  

## <a name="next-steps"></a>Próximas etapas

* Entender os conceitos de [controle de versão](luis-concept-version.md). 
* [Aprenda a criar chaves](luis-how-to-azure-subscription.md).
