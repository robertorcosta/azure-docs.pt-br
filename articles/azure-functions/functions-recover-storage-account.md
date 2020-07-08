---
title: 'Erro de solução de problemas: Azure Functions Runtime está inacessível'
description: Saiba como solucionar o problema de uma conta de armazenamento inválida.
author: alexkarcher-msft
ms.topic: article
ms.date: 09/05/2018
ms.author: alkarche
ms.openlocfilehash: 8fcd0661e2c7cab505121cf0d4d7b4c1d29017f8
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: pt-BR
ms.lasthandoff: 07/02/2020
ms.locfileid: "77063774"
---
# <a name="troubleshoot-error-azure-functions-runtime-is-unreachable"></a>Erro de solução de problemas: "Azure Functions Runtime está inacessível"

Este artigo ajuda você a solucionar problemas da seguinte cadeia de caracteres de erro que aparece no portal do Azure:

> "Erro: Azure Functions Runtime está inacessível. Clique aqui para obter detalhes sobre a configuração de armazenamento. "

Esse problema ocorre quando o Azure Functions Runtime não pode iniciar. O motivo mais comum para o problema é que o aplicativo de funções perdeu o acesso à sua conta de armazenamento. Para obter mais informações, consulte [requisitos da conta de armazenamento](https://docs.microsoft.com/azure/azure-functions/functions-create-function-app-portal#storage-account-requirements).

O restante deste artigo ajuda você a solucionar as seguintes causas desse erro, incluindo como identificar e resolver cada caso.

## <a name="storage-account-was-deleted"></a>A conta de armazenamento foi excluída

Cada aplicativo de funções exige uma conta de armazenamento para ser operado. Se essa conta for excluída, a função não funcionará.

Comece pesquisando o nome da conta de armazenamento nas configurações do aplicativo. `AzureWebJobsStorage`Ou `WEBSITE_CONTENTAZUREFILECONNECTIONSTRING` contém o nome da sua conta de armazenamento empacotado em uma cadeia de conexão. Para obter mais informações, consulte [referência de configurações de aplicativo para Azure Functions](https://docs.microsoft.com/azure/azure-functions/functions-app-settings#azurewebjobsstorage).

Pesquise sua conta de armazenamento no portal do Azure para ver se ela ainda existe. Se ele tiver sido excluído, recrie a conta de armazenamento e substitua as cadeias de conexão de armazenamento. O código de função é perdido e você precisa reimplantá-lo.

## <a name="storage-account-application-settings-were-deleted"></a>As configurações do aplicativo da conta de armazenamento foram excluídas

Na etapa anterior, se você não encontrar uma cadeia de conexão da conta de armazenamento, ela provavelmente foi excluída ou substituída. A exclusão de configurações do aplicativo geralmente ocorre quando você está usando slots de implantação ou Azure Resource Manager scripts para definir as configurações do aplicativo.

### <a name="required-application-settings"></a>Configurações de aplicativo obrigatórias

* Obrigatório:
    * [`AzureWebJobsStorage`](https://docs.microsoft.com/azure/azure-functions/functions-app-settings#azurewebjobsstorage)
* Necessário para funções de plano de consumo:
    * [`WEBSITE_CONTENTAZUREFILECONNECTIONSTRING`](https://docs.microsoft.com/azure/azure-functions/functions-app-settings)
    * [`WEBSITE_CONTENTSHARE`](https://docs.microsoft.com/azure/azure-functions/functions-app-settings)

Para obter mais informações, consulte [referência de configurações de aplicativo para Azure Functions](https://docs.microsoft.com/azure/azure-functions/functions-app-settings).

### <a name="guidance"></a>Diretrizes

* Não marque "configuração de slot" para qualquer uma dessas configurações. Se você trocar os slots de implantação, o aplicativo de funções será interrompido.
* Não modifique essas configurações como parte das implantações automatizadas.
* Essas configurações precisam ser fornecidas e estarem válidas no momento da criação. Uma implantação automatizada que não contém essas configurações resulta em um aplicativo de funções que não será executado, mesmo que as configurações sejam adicionadas posteriormente.

## <a name="storage-account-credentials-are-invalid"></a>As credenciais da conta de armazenamento são inválidas

As cadeias de conexão da conta de armazenamento abordadas anteriormente devem ser atualizadas se você regenerar as chaves de armazenamento. Para obter mais informações sobre o gerenciamento de chaves de armazenamento, consulte [criar uma conta de armazenamento do Azure](https://docs.microsoft.com/azure/storage/common/storage-create-storage-account).

## <a name="storage-account-is-inaccessible"></a>A conta de armazenamento está inacessível

Seu aplicativo de funções deve ser capaz de acessar a conta de armazenamento. Os problemas comuns que bloqueiam o acesso de um aplicativo de funções a uma conta de armazenamento são:

* O aplicativo de funções é implantado em seu Ambiente do Serviço de Aplicativo sem as regras de rede corretas para permitir o tráfego de e para a conta de armazenamento.

* O firewall da conta de armazenamento está habilitado e não está configurado para permitir o tráfego de e para funções. Para saber mais, consulte [Configurar Redes Virtuais e Firewalls de Armazenamento do Azure](https://docs.microsoft.com/azure/storage/common/storage-network-security?toc=%2fazure%2fstorage%2ffiles%2ftoc.json).

## <a name="daily-execution-quota-is-full"></a>A cota de execução diária está cheia

Se você tiver uma cota de execução diária configurada, seu aplicativo de funções estará temporariamente desabilitado, o que faz com que muitos dos controles do portal fiquem indisponíveis. 

Para verificar a cota no [portal do Azure](https://portal.azure.com), selecione **recursos de plataforma**  >  **aplicativo de funções configurações** em seu aplicativo de funções. Se você estiver acima da **cota de uso diário** que você definiu, a seguinte mensagem será exibida:

  > "O Aplicativo de funções atingiu a cota de uso diário e foi interrompido até o próximo período de 24 horas."

Para resolver esse problema, remova ou aumente a cota diária e reinicie o aplicativo. Caso contrário, a execução do seu aplicativo será bloqueada até o dia seguinte.

## <a name="app-is-behind-a-firewall"></a>O aplicativo está protegido por um firewall

Seu tempo de execução de função pode estar inacessível por qualquer um dos seguintes motivos:

* Seu aplicativo de funções está hospedado em um [ambiente do serviço de aplicativo com balanceamento de carga internamente](../app-service/environment/create-ilb-ase.md) e está configurado para bloquear o tráfego de Internet de entrada.

* Seu aplicativo de funções tem [restrições de IP de entrada](functions-networking-options.md#inbound-ip-restrictions) que são configuradas para bloquear o acesso à Internet. 

O portal do Azure faz chamadas diretamente para o aplicativo em execução para buscar a lista de funções e faz chamadas HTTP para o ponto de extremidade kudu. As configurações de nível de plataforma na guia **recursos da plataforma** ainda estão disponíveis.

Para verificar sua configuração de Ambiente do Serviço de Aplicativo:
1. Vá para o grupo de segurança de rede (NSG) da sub-rede onde reside o Ambiente do Serviço de Aplicativo.
1. Valide as regras de entrada para permitir o tráfego proveniente do IP público do computador em que você está acessando o aplicativo. 
   
Você também pode usar o portal de um computador que está conectado à rede virtual que está executando seu aplicativo ou a uma máquina virtual que está sendo executada em sua rede virtual. 

Para obter mais informações sobre a configuração de regra de entrada, consulte a seção "grupos de segurança de rede" de [considerações de rede para um ambiente do serviço de aplicativo](https://docs.microsoft.com/azure/app-service/environment/network-info#network-security-groups).

## <a name="next-steps"></a>Próximas etapas

Saiba mais sobre como monitorar seus aplicativos de funções:

> [!div class="nextstepaction"]
> [Monitorar Azure Functions](functions-monitoring.md)
