---
title: Como solucionar o problema em que o Azure Functions Runtime está inacessível.
description: Saiba como solucionar o problema de uma conta de armazenamento inválida.
author: alexkarcher-msft
ms.topic: article
ms.date: 09/05/2018
ms.author: alkarche
ms.openlocfilehash: 910b582cb40b9f8aff6a553621b4677d6b019826
ms.sourcegitcommit: 42517355cc32890b1686de996c7913c98634e348
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/02/2020
ms.locfileid: "76963879"
---
# <a name="how-to-troubleshoot-functions-runtime-is-unreachable"></a>Como solucionar o problema "Functions Runtime inacessível"

Este artigo destina-se a solucionar problemas da mensagem de erro "tempo de execução do Functions inacessível" quando ela é exibida no portal do Azure. Quando esse erro ocorrer, você verá a seguinte cadeia de caracteres de erro exibida no Portal.

`Error: Azure Functions Runtime is unreachable. Click here for details on storage configuration`

Isso ocorre quando o Azure Functions Runtime não pode iniciar. O motivo mais comum para que esse erro ocorra é quando o aplicativo de funções perde o acesso à sua conta de armazenamento. Para saber mais, consulte [requisitos da conta de armazenamento](storage-considerations.md#storage-account-requirements).

O restante deste artigo ajuda você a solucionar as seguintes causas desse erro, incluindo como identificar e resolver cada caso.

+ [Conta de armazenamento excluída](#storage-account-deleted)
+ [Configurações de aplicativo da conta de armazenamento excluídas](#storage-account-application-settings-deleted)
+ [Credenciais da conta de armazenamento inválidas](#storage-account-credentials-invalid)
+ [Conta de armazenamento inacessível](#storage-account-inaccessible)
+ [Cota de execução diária excedida](#daily-execution-quota-full)
+ [Seu aplicativo está protegido por um firewall](#app-is-behind-a-firewall)


## <a name="storage-account-deleted"></a>Conta de armazenamento excluída

Cada aplicativo de funções exige uma conta de armazenamento para ser operado. Se essa conta for excluída, a função não funcionará.

### <a name="how-to-find-your-storage-account"></a>Como localizar sua conta de armazenamento

Comece procurando o nome da conta de armazenamento nas configurações de aplicativo. Tanto `AzureWebJobsStorage` quanto `WEBSITE_CONTENTAZUREFILECONNECTIONSTRING` conterão o nome da conta de armazenamento inserida dentro de uma cadeia de conexão. Leia mais especificações na referência de [configuração do aplicativo aqui](https://docs.microsoft.com/azure/azure-functions/functions-app-settings#azurewebjobsstorage).

Pesquise sua conta de armazenamento no portal do Azure para ver se ela ainda existe. Se ela tiver sido excluída, você precisará recriar a conta de armazenamento e substituir suas cadeias de conexão de armazenamento. O código de função é perdido e será necessário reimplantá-lo novamente.

## <a name="storage-account-application-settings-deleted"></a>Configurações de aplicativo da conta de armazenamento excluídas

Na etapa anterior, se você não tiver uma cadeia de conexão da conta de armazenamento, ela provavelmente foi excluída ou substituída. A exclusão das configurações de aplicativo geralmente ocorre quando slots de implantação ou scripts do Azure Resource Manager são usados para definir as configurações de aplicativo.

### <a name="required-application-settings"></a>Configurações de aplicativo obrigatórias

* Obrigatório
    * [`AzureWebJobsStorage`](https://docs.microsoft.com/azure/azure-functions/functions-app-settings#azurewebjobsstorage)
* Obrigatório para funções de plano de consumo
    * [`WEBSITE_CONTENTAZUREFILECONNECTIONSTRING`](https://docs.microsoft.com/azure/azure-functions/functions-app-settings)
    * [`WEBSITE_CONTENTSHARE`](https://docs.microsoft.com/azure/azure-functions/functions-app-settings)

[Leia sobre essas configurações de aplicativo aqui](https://docs.microsoft.com/azure/azure-functions/functions-app-settings).

### <a name="guidance"></a>Diretriz

* Não marque "configuração de slot" para qualquer uma dessas configurações. Quando você permuta os slots de implantação, o aplicativo de funções é interrompido.
* Não modifique essas configurações como parte das implantações automatizadas.
* Essas configurações precisam ser fornecidas e estarem válidas no momento da criação. Uma implantação automatizada que não contém essas configurações resulta em um aplicativo de funções que não será executado, mesmo que as configurações sejam adicionadas posteriormente.

## <a name="storage-account-credentials-invalid"></a>Credenciais da conta de armazenamento inválidas

As cadeias de conexão da conta de armazenamento acima precisarão ser atualizadas se você regenerar as chaves de armazenamento. [Leia mais sobre o gerenciamento de chaves de armazenamento aqui](https://docs.microsoft.com/azure/storage/common/storage-create-storage-account).

## <a name="storage-account-inaccessible"></a>Conta de armazenamento inacessível

Seu aplicativo de funções deve ser capaz de acessar a conta de armazenamento. Os problemas comuns que bloqueiam o acesso do Functions a uma conta de armazenamento são:

+ aplicativos de funções implantados em ambientes de serviço de aplicativo (ASE) sem as regras de rede corretas para permitir o tráfego de e para a conta de armazenamento.

+ O firewall da conta de armazenamento está habilitado e não está configurado para permitir o tráfego de e para funções. Para saber mais, confira [Configurar redes virtuais e firewalls do Armazenamento do Azure](../storage/common/storage-network-security.md).

## <a name="daily-execution-quota-full"></a>Cota de execução diária cheia

Se você tiver uma cota de execução diária configurada, seu aplicativo de funções estará temporariamente desabilitado, o que faz com que muitos dos controles do portal fiquem indisponíveis. 

+ Para verificar na [portal do Azure](https://portal.azure.com), abra **recursos da plataforma** > **aplicativo de funções configurações** em seu aplicativo de funções. Quando você estiver acima da **cota de uso diário** que você definiu, verá a seguinte mensagem:

    `The function app has reached daily usage quota and has been stopped until the next 24 hours time frame.`

+ Para resolver esse problema, remova ou aumente a cota diária e reinicie o aplicativo. Caso contrário, a execução de seu aplicativo será bloqueada até o dia seguinte.

## <a name="app-is-behind-a-firewall"></a>O aplicativo está protegido por um firewall

Seu tempo de execução de função ficará inacessível se seu aplicativo de funções estiver hospedado em um [ambiente do serviço de aplicativo com balanceamento de carga internamente](../app-service/environment/create-ilb-ase.md) e estiver configurado para bloquear o tráfego de Internet de entrada ou tiver [restrições de IP de entrada](functions-networking-options.md#inbound-ip-restrictions) configuradas para bloquear o acesso à Internet. O portal do Azure faz chamadas diretamente para o aplicativo em execução para buscar a lista de funções e também faz chamadas HTTP para o ponto de extremidade KUDU. As configurações de nível de plataforma na guia `Platform Features` ainda estarão disponíveis.

Para verificar a configuração do ASE, navegue até o NSG da sub-rede em que o ASE reside e valide as regras de entrada para permitir o tráfego proveniente do IP público do computador em que você está acessando o aplicativo. Você também pode usar o portal de um computador conectado à rede virtual que executa seu aplicativo ou uma máquina virtual em execução em sua rede virtual. [Leia mais sobre a configuração de regra de entrada aqui](../app-service/environment/network-info.md#network-security-groups)

## <a name="next-steps"></a>Próximas etapas

Saiba mais sobre como monitorar seus aplicativos de funções:

> [!div class="nextstepaction"]
> [Azure Functions de monitor](functions-monitoring.md)
