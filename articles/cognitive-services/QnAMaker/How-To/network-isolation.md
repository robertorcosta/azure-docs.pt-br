---
title: Isolamento da rede
description: Os usuários podem restringir o acesso público a recursos de QnA Maker.
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: conceptual
ms.date: 11/09/2020
ms.openlocfilehash: 4e9b45c72e5fb4cbd9e548727faf8946e3e5ba8f
ms.sourcegitcommit: dac05f662ac353c1c7c5294399fca2a99b4f89c8
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/04/2021
ms.locfileid: "102125025"
---
# <a name="recommended-settings-for-network-isolation"></a>Configurações recomendadas para isolamento de rede

Siga as etapas abaixo para restringir o acesso público a recursos de QnA Maker. Proteja um recurso de serviços cognitivas do acesso público [Configurando a rede virtual](../../cognitive-services-virtual-networks.md?tabs=portal).

## <a name="restrict-access-to-cognitive-search-resource"></a>Restringir o acesso ao recurso Pesquisa Cognitiva

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA (versão estável)](#tab/v1)

Configurar Pesquisa Cognitiva como um ponto de extremidade privado dentro de uma VNET. Quando uma instância de pesquisa é criada durante a criação de um recurso de QnA Maker, você pode forçar Pesquisa Cognitiva a dar suporte a uma configuração de ponto de extremidade particular criada inteiramente dentro da VNet de um cliente.

Todos os recursos devem ser criados na mesma região para usar um ponto de extremidade privado.

* Recurso do QnA Maker
* novo recurso de Pesquisa Cognitiva
* novo recurso de rede virtual

Conclua as seguintes etapas no [portal do Azure](https://portal.azure.com):

1. Crie um [recurso de QnA Maker](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesQnAMaker).
2. Crie um novo recurso de Pesquisa Cognitiva com conectividade de ponto de extremidade (dados) definida como _particular_. Crie o recurso na mesma região que o QnA Maker recurso criado na etapa 1. Saiba mais sobre como [criar um recurso de pesquisa cognitiva](../../../search/search-create-service-portal.md)e, em seguida, use este link para ir diretamente para a [página de criação do recurso](https://ms.portal.azure.com/#create/Microsoft.Search).
3. Crie um novo [recurso de rede virtual](https://ms.portal.azure.com/#create/Microsoft.VirtualNetwork-ARM).
4. Configure a VNET no recurso do serviço de aplicativo criado na etapa 1 deste procedimento. Crie uma nova entrada DNS na VNET para o novo recurso Pesquisa Cognitiva criado na etapa 2. para o endereço IP Pesquisa Cognitiva.
5. [Associe o serviço de aplicativo ao novo recurso de pesquisa cognitiva](../how-to/set-up-qnamaker-service-azure.md) criado na etapa 2. Em seguida, você pode excluir o recurso de Pesquisa Cognitiva original criado na etapa 1.
    
No [portal de QnA Maker](https://www.qnamaker.ai/), crie sua primeira base de dados de conhecimento.

#  <a name="qna-maker-managed-preview-release"></a>[QnA Maker gerenciado (versão prévia)](#tab/v2)

[Crie pontos de extremidade privados](../reference-private-endpoint.md) para o recurso de Azure Search.

---

## <a name="restrict-access-to-app-service-qna-runtime"></a>Restringir o acesso ao serviço de aplicativo (tempo de execução QnA)

Você pode adicionar IPs àlist de permissão do serviço de aplicativo para restringir o acesso ou configurar o serviço de aplicativo ambiente para hospedar QnA Maker serviço de aplicativo.

#### <a name="add-ips-to-app-service-allowlist"></a>Adicionar IPs àlist de permissão do serviço de aplicativo

1. Permitir tráfego somente de IPs de serviços cognitivas. Eles já estão incluídos na marca de serviço `CognitiveServicesManagement` . Isso é necessário para criar APIs (criar/atualizar KB) para invocar o serviço de aplicativo e atualizar o serviço de Azure Search de acordo. Confira [mais informações sobre marcas de serviço.](../../../virtual-network/service-tags-overview.md)
2. Certifique-se de também permitir outros pontos de entrada, como o serviço de bot do Azure, QnA Maker portal, etc. para acesso à API de previsão "GenerateAnswer".
3. Siga estas etapas para adicionar os intervalos de endereços IP a uma permissão:

   1. Baixe [intervalos de IP para todas as marcas de serviço](https://www.microsoft.com/download/details.aspx?id=56519).
   2. Selecione os IPs de "CognitiveServicesManagement".
   3. Navegue até a seção rede do recurso do serviço de aplicativo e clique na opção "configurar restrição de acesso" para adicionar os IPs a uma permissão.

Também temos um script automatizado para fazer o mesmo para seu serviço de aplicativo. Você pode encontrar o [script do PowerShell para configurar umalist de permissão](https://github.com/pchoudhari/QnAMakerBackupRestore/blob/master/AddRestrictedIPAzureAppService.ps1) no github. Você precisa inserir a ID da assinatura, o grupo de recursos e o nome real do serviço de aplicativo como parâmetros de script. A execução do script adicionará automaticamente os IPs àlist de permissão do serviço de aplicativo.

#### <a name="configure-app-service-environment-to-host-qna-maker-app-service"></a>Configurar Ambiente do Serviço de Aplicativo para hospedar QnA Maker serviço de aplicativo
    
O Ambiente do Serviço de Aplicativo (ASE) pode ser usado para hospedar QnA Maker serviço de aplicativo. Siga as etapas abaixo:

1. Crie um Ambiente do Serviço de Aplicativo e marque-o como "externo". Siga o [tutorial](../../../app-service/environment/create-external-ase.md) para obter instruções.
2.  Crie um serviço de aplicativo dentro do Ambiente do Serviço de Aplicativo.
    1. Verifique a configuração do serviço de aplicativo e adicione ' PrimaryEndpointKey ' como uma configuração de aplicativo. O valor de ' PrimaryEndpointKey ' deve ser definido como " \<app-name\> -PrimaryEndpointKey". O nome do aplicativo é definido na URL do serviço de aplicativo. Por exemplo, se a URL do serviço de aplicativo for "mywebsite.myase.p.azurewebsite.net", o nome do aplicativo será "mysite". Nesse caso, o valor de ' PrimaryEndpointKey ' deve ser definido como "mysite-PrimaryEndpointKey".
    2. Crie um serviço de Azure Search.
    3. Verifique se Azure Search e as configurações do aplicativo estão configuradas adequadamente. 
          Siga este [tutorial](../reference-app-service.md?tabs=v1#app-service).
3.  Atualizar o grupo de segurança de rede associado ao Ambiente do Serviço de Aplicativo
    1. Atualize as regras de segurança de entrada criadas previamente de acordo com seus requisitos.
    2. Adicione uma nova regra de segurança de entrada com a fonte como ' marca de serviço ' e a marca de serviço de origem como ' CognitiveServicesManagement '.
4.  Crie um QnA Maker instância de serviço cognitiva (Microsoft. Cognitivaservices/accounts) usando Azure Resource Manager, em que QnA Maker ponto de extremidade deve ser definido para o ponto de extremidade do serviço de aplicativo criado acima (https://mywebsite.myase.p.azurewebsite.net).
    
---
