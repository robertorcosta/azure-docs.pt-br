---
title: 'Erro de solução de problemas: O tempo de execução das funções do Azure é inalcançável'
description: Saiba como solucionar o problema de uma conta de armazenamento inválida.
author: alexkarcher-msft
ms.topic: article
ms.date: 09/05/2018
ms.author: alkarche
ms.openlocfilehash: 8fcd0661e2c7cab505121cf0d4d7b4c1d29017f8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "77063774"
---
# <a name="troubleshoot-error-azure-functions-runtime-is-unreachable"></a>Erro de solução de problemas: "O tempo de execução das funções do Azure é inalcançável"

Este artigo ajuda você a solucionar problemas da seguinte seqüência de erros que aparece no portal Azure:

> "Erro: O tempo de execução das funções do Azure é inalcançável. Clique aqui para obter detalhes sobre a configuração de armazenamento."

Esse problema ocorre quando o Tempo de execução de funções do Azure não pode ser inicializado. A razão mais comum para o problema é que o aplicativo de função perdeu o acesso à sua conta de armazenamento. Para obter mais informações, consulte [os requisitos da conta de armazenamento](https://docs.microsoft.com/azure/azure-functions/functions-create-function-app-portal#storage-account-requirements).

O resto deste artigo ajuda você a solucionar as seguintes causas deste erro, incluindo como identificar e resolver cada caso.

## <a name="storage-account-was-deleted"></a>A conta de armazenamento foi excluída

Cada aplicativo de funções exige uma conta de armazenamento para ser operado. Se essa conta for excluída, sua função não funcionará.

Comece pesquisando o nome da sua conta de armazenamento nas configurações do aplicativo. Ou `AzureWebJobsStorage` `WEBSITE_CONTENTAZUREFILECONNECTIONSTRING` ou contém o nome da sua conta de armazenamento embrulhado em uma seqüência de conexão. Para obter mais informações, consulte [a referência de configurações do aplicativo para funções do Azure](https://docs.microsoft.com/azure/azure-functions/functions-app-settings#azurewebjobsstorage).

Procure sua conta de armazenamento no portal Azure para ver se ela ainda existe. Se ele tiver sido excluído, recrie a conta de armazenamento e substitua as strings de conexão de armazenamento. Seu código de função está perdido, e você precisa reimplantá-lo.

## <a name="storage-account-application-settings-were-deleted"></a>As configurações do aplicativo da conta de armazenamento foram excluídas

Na etapa anterior, se você não conseguir encontrar uma seqüência de conexão de conta de armazenamento, ela provavelmente foi excluída ou sobreescrita. A exclusão das configurações do aplicativo acontece mais comumente quando você está usando slots de implantação ou scripts do Azure Resource Manager para definir as configurações do aplicativo.

### <a name="required-application-settings"></a>Configurações de aplicativo obrigatórias

* Obrigatório:
    * [`AzureWebJobsStorage`](https://docs.microsoft.com/azure/azure-functions/functions-app-settings#azurewebjobsstorage)
* Necessário para funções do plano de consumo:
    * [`WEBSITE_CONTENTAZUREFILECONNECTIONSTRING`](https://docs.microsoft.com/azure/azure-functions/functions-app-settings)
    * [`WEBSITE_CONTENTSHARE`](https://docs.microsoft.com/azure/azure-functions/functions-app-settings)

Para obter mais informações, consulte [a referência de configurações do aplicativo para funções do Azure](https://docs.microsoft.com/azure/azure-functions/functions-app-settings).

### <a name="guidance"></a>Orientação

* Não verifique "configuração de slot" para qualquer uma dessas configurações. Se você trocar os slots de implantação, o aplicativo de função será pausado.
* Não modifique essas configurações como parte de implantações automatizadas.
* Essas configurações precisam ser fornecidas e estarem válidas no momento da criação. Uma implantação automatizada que não contém essas configurações resulta em um aplicativo de função que não será executado, mesmo que as configurações sejam adicionadas mais tarde.

## <a name="storage-account-credentials-are-invalid"></a>As credenciais da conta de armazenamento são inválidas

As seqüências de conexão de conta de armazenamento discutidas anteriormente devem ser atualizadas se você regenerar as chaves de armazenamento. Para obter mais informações sobre o gerenciamento de chaves de armazenamento, consulte [Criar uma conta de armazenamento do Azure](https://docs.microsoft.com/azure/storage/common/storage-create-storage-account).

## <a name="storage-account-is-inaccessible"></a>A conta de armazenamento é inacessível

Seu aplicativo de função deve ser capaz de acessar a conta de armazenamento. Problemas comuns que bloqueiam o acesso de um aplicativo de função a uma conta de armazenamento são:

* O aplicativo de função é implantado no ambiente de serviço do aplicativo sem as regras de rede corretas para permitir o tráfego de e para a conta de armazenamento.

* O firewall da conta de armazenamento está ativado e não está configurado para permitir o tráfego de e para funções. Para saber mais, consulte [Configurar Redes Virtuais e Firewalls de Armazenamento do Azure](https://docs.microsoft.com/azure/storage/common/storage-network-security?toc=%2fazure%2fstorage%2ffiles%2ftoc.json).

## <a name="daily-execution-quota-is-full"></a>A cota de execução diária está cheia

Se você tiver uma cota de execução diária configurada, seu aplicativo de função será temporariamente desativado, o que faz com que muitos dos controles do portal fiquem indisponíveis. 

Para verificar a cota no [portal Azure,](https://portal.azure.com)selecione**Configurações do aplicativo de função recursos**  > da **plataforma**em seu aplicativo de função. Se você estiver sobre a **Cota de Uso Diário** definida, a seguinte mensagem será exibida:

  > "O App Function atingiu a cota de uso diário e foi parado até as próximas 24 horas."

Para resolver esse problema, remova ou aumente a cota diária e, em seguida, reinicie seu aplicativo. Caso contrário, a execução do seu aplicativo é bloqueada até o dia seguinte.

## <a name="app-is-behind-a-firewall"></a>Aplicativo está por trás de um firewall

O tempo de execução da função pode ser inalcançável por qualquer uma das seguintes razões:

* Seu aplicativo de função está hospedado em um [ambiente de serviço de aplicativo balanceado de carga interna](../app-service/environment/create-ilb-ase.md) e está configurado para bloquear o tráfego de internet de entrada.

* Seu aplicativo de função tem [restrições de IP de entrada](functions-networking-options.md#inbound-ip-restrictions) configuradas para bloquear o acesso à Internet. 

O portal Azure faz chamadas diretamente para o aplicativo em execução para buscar a lista de funções, e faz chamadas HTTP para o ponto final do Kudu. As configurações em nível de plataforma na guia Recursos da **plataforma** ainda estão disponíveis.

Para verificar a configuração do ambiente de serviço do aplicativo:
1. Vá para o grupo de segurança de rede (NSG) da sub-rede onde reside o Ambiente de Serviço do Aplicativo.
1. Valide as regras de entrada para permitir o tráfego proveniente do IP público do computador onde você está acessando o aplicativo. 
   
Você também pode usar o portal a partir de um computador conectado à rede virtual que está executando seu aplicativo ou a uma máquina virtual que está sendo executado em sua rede virtual. 

Para obter mais informações sobre a configuração de regras de entrada, consulte a seção "Grupos de segurança de rede" das [considerações](https://docs.microsoft.com/azure/app-service/environment/network-info#network-security-groups)de Rede para um Ambiente de Serviço de Aplicativo .

## <a name="next-steps"></a>Próximas etapas

Saiba como monitorar seus aplicativos de função:

> [!div class="nextstepaction"]
> [Monitorar Azure Functions](functions-monitoring.md)
