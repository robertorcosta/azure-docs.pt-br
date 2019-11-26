---
title: Como solucionar o problema em que o Azure Functions Runtime está inacessível.
description: Saiba como solucionar o problema de uma conta de armazenamento inválida.
author: alexkarcher-msft
ms.topic: article
ms.date: 09/05/2018
ms.author: alkarche
ms.openlocfilehash: 212f10bd33479e5a9f7244d5b2090c0324f937c2
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/20/2019
ms.locfileid: "74226770"
---
# <a name="how-to-troubleshoot-functions-runtime-is-unreachable"></a>Como solucionar o problema "Functions Runtime inacessível"


## <a name="error-text"></a>Texto do erro
Este documento é indicado para solucionar o erro a seguir quando exibido no portal do Functions.

`Error: Azure Functions Runtime is unreachable. Click here for details on storage configuration`

### <a name="summary"></a>Resumo
Esse problema ocorre quando o Azure Functions Runtime não pode ser iniciado. O motivo mais comum para que esse erro ocorra é quando o aplicativo de funções perde o acesso à sua conta de armazenamento. [Leia mais sobre os requisitos da conta de armazenamento aqui](https://docs.microsoft.com/azure/azure-functions/functions-create-function-app-portal#storage-account-requirements)

### <a name="troubleshooting"></a>Solucionando problemas
Vamos percorrer os quatro casos de erro mais comuns e aprender como identificar e resolver cada caso.

1. Conta de armazenamento excluída
1. Configurações de aplicativo da conta de armazenamento excluídas
1. Credenciais da conta de armazenamento inválidas
1. Conta de armazenamento inacessível
1. Cota de execução diária total

## <a name="storage-account-deleted"></a>Conta de armazenamento excluída

Cada aplicativo de funções exige uma conta de armazenamento para ser operado. Se essa conta for excluída, a função não funcionará.

### <a name="how-to-find-your-storage-account"></a>Como localizar sua conta de armazenamento

Comece procurando o nome da conta de armazenamento nas configurações de aplicativo. Tanto `AzureWebJobsStorage` quanto `WEBSITE_CONTENTAZUREFILECONNECTIONSTRING` conterão o nome da conta de armazenamento inserida dentro de uma cadeia de conexão. Leia informações mais específicas na [referência de configuração de aplicativo aqui](https://docs.microsoft.com/azure/azure-functions/functions-app-settings#azurewebjobsstorage)

Pesquise sua conta de armazenamento no portal do Azure para ver se ela ainda existe. Se ela tiver sido excluída, você precisará recriar a conta de armazenamento e substituir suas cadeias de conexão de armazenamento. O código de função é perdido e será necessário reimplantá-lo novamente.

## <a name="storage-account-application-settings-deleted"></a>Configurações de aplicativo da conta de armazenamento excluídas

Na etapa anterior, se não havia uma cadeia de conexão da conta de armazenamento, provavelmente ela foi excluída ou substituída. A exclusão das configurações de aplicativo geralmente ocorre quando slots de implantação ou scripts do Azure Resource Manager são usados para definir as configurações de aplicativo.

### <a name="required-application-settings"></a>Configurações de aplicativo obrigatórias

* obrigatórios
    * [`AzureWebJobsStorage`](https://docs.microsoft.com/azure/azure-functions/functions-app-settings#azurewebjobsstorage)
* Obrigatório para funções de plano de consumo
    * [`WEBSITE_CONTENTAZUREFILECONNECTIONSTRING`](https://docs.microsoft.com/azure/azure-functions/functions-app-settings)
    * [`WEBSITE_CONTENTSHARE`](https://docs.microsoft.com/azure/azure-functions/functions-app-settings)

[Leia sobre essas configurações de aplicativo aqui](https://docs.microsoft.com/azure/azure-functions/functions-app-settings)

### <a name="guidance"></a>Diretrizes

* Não marque "configuração de slot" para nenhuma dessas configurações. Quando você trocar os slots de implantação, a função será interrompida.
* Não modifique essas configurações como parte das implantações automatizadas.
* Essas configurações precisam ser fornecidas e estarem válidas no momento da criação. Uma implantação automatizada que não contenha essas configurações resultará em um aplicativo não funcional, mesmo se as configurações forem adicionadas após o fato.

## <a name="storage-account-credentials-invalid"></a>Credenciais da conta de armazenamento inválidas

As cadeias de conexão da conta de armazenamento acima precisarão ser atualizadas se você regenerar as chaves de armazenamento. [Leia mais sobre o gerenciamento de chaves de armazenamento aqui](https://docs.microsoft.com/azure/storage/common/storage-create-storage-account)

## <a name="storage-account-inaccessible"></a>Conta de armazenamento inacessível

Seu aplicativo de funções precisa ser capaz de acessar a conta de armazenamento. Os problemas comuns que bloqueiam o acesso do Functions a uma conta de armazenamento são:

* Aplicativos de funções implantados em Ambientes do Serviço de Aplicativo sem as regras de rede corretas para permitir o tráfego de e para a conta de armazenamento
* O firewall da conta de armazenamento está habilitado e não está configurado para permitir o tráfego de e para as funções. [Leia mais sobre a configuração de firewall da conta de armazenamento aqui](https://docs.microsoft.com/azure/storage/common/storage-network-security?toc=%2fazure%2fstorage%2ffiles%2ftoc.json)

## <a name="daily-execution-quota-full"></a>Cota de execução diária total

Se uma cota de execução diária estiver configurada, seu Aplicativo de funções será temporariamente desabilitado e muitos dos controles de portal ficarão indisponíveis. 

* Para verificar, veja Recursos de Plataforma abertos > Configurações do Aplicativo de funções no portal. Nesse caso, aparecerá a seguinte mensagem de erro se estiver em uma cota
    * `The Function App has reached daily usage quota and has been stopped until the next 24 hours time frame.`
* Remova a cota e reinicie o aplicativo para resolver o problema.

## <a name="next-steps"></a>Próximas Etapas

Agora que seu aplicativo de funções retornou e está operacional, confira nossos guias de início rápido e nossas referências para o desenvolvedor para colocá-lo em funcionamento novamente!

* [Criar sua primeira Função do Azure](functions-create-first-azure-function.md)  
  Crie diretamente sua primeira função usando o início rápido do Azure Functions. 
* [Referência do desenvolvedor do Azure Functions](functions-reference.md)  
  Fornece informações mais técnicas sobre o Azure Functions runtime e uma referência para funções de codificação e definição de associações e gatilhos.
* [Testando o Azure Functions](functions-test-a-function.md)  
  Descreve várias ferramentas e técnicas para testar suas funções.
* [Como escalar o Azure Functions](functions-scale.md)  
  Discute os planos de serviço disponíveis com o Azure Functions, incluindo o plano de hospedagem de consumo e como escolher o plano certo. 
* [Saiba mais sobre o Serviço de Aplicativo do Azure](../app-service/overview.md)  
  O Azure Functions aproveita o Serviço de Aplicativo do Azure para a funcionalidade básica como implantações, variáveis de ambiente e diagnóstico. 
