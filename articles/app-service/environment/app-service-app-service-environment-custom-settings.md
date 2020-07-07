---
title: Definir configurações personalizadas
description: Defina as configurações que se aplicam a todo o Ambiente do Serviço de Aplicativo do Azure. Saiba como fazer isso com modelos do Azure Resource Manager.
author: stefsch
ms.assetid: 1d1d85f3-6cc6-4d57-ae1a-5b37c642d812
ms.topic: tutorial
ms.date: 12/19/2019
ms.author: stefsch
ms.custom: mvc, seodec18
ms.openlocfilehash: 09c41c7480b262e6f1a912ad4b708e485d86bf56
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/01/2020
ms.locfileid: "85833495"
---
# <a name="custom-configuration-settings-for-app-service-environments"></a>Definições de configuração personalizadas para Ambientes de Serviço de Aplicativo
## <a name="overview"></a>Visão geral
Como os ASEs (Ambientes do Serviço de Aplicativo) são isolados em um único cliente, há certas definições de configuração que podem ser aplicadas exclusivamente a Ambientes de Serviço de Aplicativo. Este artigo documenta as várias personalizações específicas que estão disponíveis para Ambientes de Serviço de Aplicativo.

Se você não tiver um Ambiente do Serviço de Aplicativo, consulte [Como criar um Ambiente do Serviço de Aplicativo](app-service-web-how-to-create-an-app-service-environment.md).

Você pode armazenar as personalizações de Ambiente de Serviço de Aplicativo usando uma matriz no novo atributo **clusterSettings** . Esse atributo é encontrado no dicionário de "Propriedades" da entidade do Azure Resource Manager *hostingEnvironments* .

O snippet de código de modelo do Resource Manager abreviado a seguir mostra o atributo **clusterSettings** :

```json
"resources": [
{
    "apiVersion": "2015-08-01",
    "type": "Microsoft.Web/hostingEnvironments",
    "name": ...,
    "location": ...,
    "properties": {
        "clusterSettings": [
            {
                "name": "nameOfCustomSetting",
                "value": "valueOfCustomSetting"
            }
        ],
        "workerPools": [ ...],
        etc...
    }
}
```

O atributo **clusterSettings** pode ser incluído em um modelo do Resource Manager para atualizar o Ambiente de Serviço de Aplicativo.

## <a name="use-azure-resource-explorer-to-update-an-app-service-environment"></a>Usar o Gerenciador de Recursos do Azure para atualizar um Ambiente de Serviço de Aplicativo
Como alternativa, você pode atualizar o Ambiente de Serviço de Aplicativo usando o [Gerenciador de Recursos do Azure](https://resources.azure.com).  

1. No Gerenciador de Recursos, acesse o nó para Ambiente de Serviço de Aplicativo (**subscriptions** > **resourceGroups** > **providers** > **Microsoft.Web** > **hostingEnvironments**). Em seguida, clique no Ambiente de Serviço de Aplicativo específico que você deseja atualizar.
2. No painel à direita, clique em **Leitura/gravação** na barra de ferramentas superior para permitir a edição interativa no Gerenciador de Recursos.  
3. Clique no botão azul **Editar** para tornar o modelo do Resource Manager editável.
4. Role até o final do painel à direita. O atributo **clusterSettings** está na parte inferior, na qual você poderá inserir ou atualizar seu valor.
5. Digite (ou copie e cole) a matriz de valores de configuração desejada no atributo **clusterSettings** .  
6. Clique no botão verde **PUT** localizado na parte superior do painel à direita para confirmar a alteração no Ambiente de Serviço de Aplicativo.

No entanto, você envia a alteração, isso demora aproximadamente 30 minutos, multiplicados pelo número de front-ends no Ambiente de Serviço de Aplicativo, para que a alteração tenha efeito.
Por exemplo, se um Ambiente de Serviço de Aplicativo tiver quatro front-ends, levará aproximadamente duas horas para que a atualização de configuração seja concluída. Embora a alteração de configuração esteja sendo revertida, nenhuma outra operação de colocação em escala ou operação de alteração pode ocorrer no Ambiente de Serviço de Aplicativo.

## <a name="enable-internal-encryption"></a>Habilitar criptografia interna

O Ambiente do Serviço de Aplicativo funciona como um sistema de caixa preta em que você não pode ver os componentes internos nem a comunicação dentro do sistema. Para habilitar uma taxa de transferência mais alta, a criptografia não é habilitada por padrão entre os componentes internos. O sistema é seguro, pois o tráfego está totalmente inacessível para ser monitorado ou acessado. Se você tiver um requisito de conformidade, embora exija a criptografia completa do caminho de dados de ponta a ponta, há uma maneira de habilitar isso com um clusterSetting.  

```json
"clusterSettings": [
    {
        "name": "InternalEncryption",
        "value": "1"
    }
],
```

Depois que o InternalEncryption clusterSetting estiver habilitado, o desempenho do sistema poderá ser afetado. Ao fazer a alteração para habilitar InternalEncryption, seu ASE estará em um estado instável até que a alteração seja totalmente propagada. A propagação completa da alteração pode levar algumas horas para ser concluída, dependendo de quantas instâncias você tem em seu ASE. É altamente recomendável não habilitar isso em um ASE enquanto ele estiver em uso. Se você precisa habilitá-lo em um ASE usado ativamente, recomendamos desviar o tráfego para um ambiente de backup até o fim da operação. 

## <a name="disable-tls-10-and-tls-11"></a>Desabilitar o TLS 1.0 e TLS 1.1

Se você deseja gerenciar as configurações de TLS em um aplicativo por aplicativo, é possível usar as orientações fornecidas com a documentação [Impor configurações de TLS](../configure-ssl-bindings.md#enforce-tls-versions). 

Se desejar desabilitar todo o tráfego de TLS 1.0 e TLS 1.1 para todos os aplicativos em um ASE, você poderá definir a seguinte entrada **clusterSettings**:

```json
"clusterSettings": [
    {
        "name": "DisableTls1.0",
        "value": "1"
    }
],
```

O nome da configuração informa 1.0, mas quando configurado, ele desabilita o TLS 1.0 e TLS 1.1.

## <a name="change-tls-cipher-suite-order"></a>Mudar a ordem do pacote de criptografia TLS
Outra pergunta feita pelos clientes é se eles podem modificar a lista de criptografia negociada pelo seu servidor e isso pode ser feito modificando **clusterSettings** conforme mostrado abaixo. A lista de pacotes de criptografia disponíveis pode ser recuperada [neste artigo do MSDN](https://msdn.microsoft.com/library/windows/desktop/aa374757\(v=vs.85\).aspx).

```json
"clusterSettings": [
    {
        "name": "FrontEndSSLCipherSuiteOrder",
        "value": "TLS_ECDHE_ECDSA_WITH_AES_256_GCM_SHA384_P256,TLS_ECDHE_ECDSA_WITH_AES_128_GCM_SHA256_P256,TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA384_P256,TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA256_P256,TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA_P256,TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA_P256"
    }
],
```

> [!WARNING]
> Se valores incorretos forem definidos para o pacote de criptografia e o SChannel não puder entendê-los, toda a comunicação TLS com o servidor poderá parar de funcionar. Nesse caso, você precisará remover a entrada *FrontEndSSLCipherSuiteOrder* de **clusterSettings** e enviar o modelo atualizado do Resource Manager para reverter para as configurações padrão do pacote de criptografia.  Use esta funcionalidade com cuidado.

## <a name="get-started"></a>Introdução
O site de modelo do Azure Quickstart Resource Manager inclui um modelo com a definição básica para a [criação de um Ambiente de Serviço de Aplicativo](https://azure.microsoft.com/documentation/templates/201-web-app-ase-create/).

<!-- LINKS -->

<!-- IMAGES -->
