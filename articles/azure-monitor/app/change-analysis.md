---
title: Use a análise de alteração de aplicativos no Azure Monitor para encontrar problemas de aplicativos web | Microsoft Docs
description: Use a análise de alteração de aplicativo no Azure Monitor para solucionar problemas de aplicativos em sites ao vivo no Azure App Service.
ms.topic: conceptual
author: cawams
ms.author: cawa
ms.date: 05/07/2019
ms.openlocfilehash: 036b8c084bdfdc11c02274758c550c76bdc7b1e7
ms.sourcegitcommit: 8a9c54c82ab8f922be54fb2fcfd880815f25de77
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "80348741"
---
# <a name="use-application-change-analysis-preview-in-azure-monitor"></a>Use a análise de alteração de aplicativo (visualização) no Monitor Do Azure

Quando ocorre um problema de site ao vivo ou paralisação, determinar rapidamente a causa raiz é fundamental. Soluções de monitoramento padrão podem alertá-lo para um problema. Eles podem até indicar qual componente está falhando. Mas este alerta nem sempre explica imediatamente a causa da falha. Você sabe que seu site funcionou há cinco minutos, e agora está quebrado. O que mudou nos últimos cinco minutos? Esta é a pergunta que a Application Change Analysis foi projetada para responder no Azure Monitor.

Com base no poder do Gráfico de Recursos do [Azure,](https://docs.microsoft.com/azure/governance/resource-graph/overview)a Análise de Alterações fornece insights sobre as alterações do aplicativo Azure para aumentar a observabilidade e reduzir o MTTR (tempo médio para reparar).

> [!IMPORTANT]
> A Análise de Alterações está atualmente em pré-visualização. Esta versão de visualização é fornecida sem um contrato de nível de serviço. Esta versão não é recomendada para cargas de trabalho de produção. Alguns recursos podem não ser suportados ou ter recursos restritos. Para obter mais informações, consulte [termos de uso suplementares para visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="overview"></a>Visão geral

A Análise de Alterações detecta vários tipos de alterações, desde a camada de infra-estrutura até a implantação do aplicativo. É um provedor de recursos Azure de nível de assinatura que verifica as alterações de recursos na assinatura. A Change Analysis fornece dados para várias ferramentas de diagnóstico para ajudar os usuários a entender quais mudanças podem ter causado problemas.

O diagrama a seguir ilustra a arquitetura da Análise de Alterações:

![Diagrama de arquitetura de como a Análise de Alterações obtém dados de alteração e fornece-os às ferramentas do cliente](./media/change-analysis/overview.png)

Atualmente, a Análise de Alterações está integrada à experiência de Diagnóstico e Resolução de **Problemas** no aplicativo web App Service, bem como disponível como uma guia autônoma no portal Azure.
Consulte as *alterações de visualização de todos os recursos na* seção Azure para acessar a lâmina de análise de alterações e a seção de recursos *'Análise de alterações para os aplicativos da Web'* para usá-la no portal do Web App mais tarde neste artigo.

### <a name="azure-resource-manager-tracked-properties-changes"></a>O Azure Resource Manager rastreou as alterações de propriedades

Usando [o Gráfico de Recursos do Azure,](https://docs.microsoft.com/azure/governance/resource-graph/overview)a Análise de Alterações fornece um registro histórico de como os recursos do Azure que hospedam seu aplicativo mudaram ao longo do tempo. Podem ser detectadas configurações rastreadas, como identidades gerenciadas, upgrade do sistema operacional da plataforma e nomes de host.

### <a name="azure-resource-manager-proxied-setting-changes"></a>Azure Resource Manager proxied alterações de configuração
Configurações como regra de configuração IP, configurações TLS e versões de extensão ainda não estão disponíveis no ARG, então a Análise de alterações consulta e calcula essas alterações com segurança para fornecer mais detalhes sobre o que mudou no aplicativo. Essas informações ainda não estão disponíveis no Gráfico de Recursos do Azure, mas estarão disponíveis em breve.

### <a name="changes-in-web-app-deployment-and-configuration-in-guest-changes"></a>Alterações na implantação e configuração de aplicativos web (alterações no convidado)

A Análise de Alterações captura o estado de implantação e configuração de um aplicativo a cada 4 horas. Ele pode detectar, por exemplo, alterações nas variáveis do ambiente de aplicação. A ferramenta calcula as diferenças e apresenta o que mudou. Ao contrário das alterações do Gerenciador de recursos, as informações de alteração de implantação de código podem não estar disponíveis imediatamente na ferramenta. Para exibir as últimas alterações na Análise de Alterações, selecione **Scan changes now**.

![Captura de tela do botão "Scan changes now"](./media/change-analysis/scan-changes.png)

### <a name="dependency-changes"></a>Mudanças de dependência

Alterações nas dependências de recursos também podem causar problemas em um aplicativo web. Por exemplo, se um aplicativo web chamar um cache Redis, o SKU do cache Redis poderá afetar o desempenho do aplicativo web. Para detectar alterações nas dependências, a Análise de Alterações verifica o registro de DNS do aplicativo web. Dessa forma, identifica alterações em todos os componentes do aplicativo que podem causar problemas.
Atualmente, as seguintes dependências são suportadas:
- Aplicativos Web
- Armazenamento do Azure
- SQL do Azure

### <a name="enablement"></a>Habilitação
O provedor de recursos "Microsoft.ChangeAnalysis" precisa ser registrado com uma assinatura do Azure Resource Manager rastreado propriedades e configurações próximas alterar dados para estar disponível. Ao entrar na ferramenta de diagnóstico e resolução de problemas do Web App ou trazer a guia independente Análise de Alteração, este provedor de recursos é automaticamente registrado. Ele não tem nenhuma implementação de desempenho e custo para sua assinatura. Quando você habilita a Análise de Alterações para aplicativos web (ou habilitação na ferramenta Diagnosticar e Resolver problemas), ela terá um impacto de desempenho insignificante no aplicativo web e sem custo de faturamento.
Para alterações no aplicativo da Web, é necessária uma ativação separada para digitalizar arquivos de código dentro de um aplicativo web. Para obter mais informações, consulte Ativar análise de alteração na seção [De diagnóstico e resolver problemas](https://docs.microsoft.com/azure/azure-monitor/app/change-analysis#enable-change-analysis-in-the-diagnose-and-solve-problems-tool) mais tarde neste artigo para obter mais detalhes.


## <a name="viewing-changes-for-all-resources-in-azure"></a>Visualização de alterações para todos os recursos no Azure
No Azure Monitor, há uma lâmina autônoma para análise de alterações para visualizar todas as alterações com insights e recursos de dependênciade aplicativos.

Procure análise de alteração na barra de pesquisa no portal Azure para lançar a lâmina.

![Captura de tela da análise de alteração de pesquisa no portal Azure](./media/change-analysis/search-change-analysis.png)

Selecione Grupo de recursos e recursos para iniciar a visualização de alterações.

![Captura de tela da lâmina de análise de alteração no portal Azure](./media/change-analysis/change-analysis-standalone-blade.png)

Você pode ver insights e recursos de dependências relacionados que hospedam seu aplicativo. Essa exibição foi projetada para ser centrada em aplicativos para desenvolvedores solucionar problemas.

Os recursos suportados atualmente incluem:
- Máquinas Virtuais
- Conjunto de escala de máquina virtual
- Recursos de rede do Azure
- Aplicativo web com alterações de variáveis de rastreamento de arquivos e ambientes no convidado

Para qualquer feedback, use o botão enviar changeanalysisteam@microsoft.comfeedback na lâmina ou e-mail .

![Captura de tela do botão de feedback na lâmina de análise de alteração](./media/change-analysis/change-analysis-feedback.png)

## <a name="change-analysis-for-the-web-apps-feature"></a>Análise de alterações para o recurso Web Apps

No Azure Monitor, a Análise de Alterações também é incorporada à experiência de **autoatendimento Diagnóstico e resolução de problemas.** Acesse esta experiência a partir da página **Visão Geral** do seu aplicativo App Service.

![Captura de tela do botão "Visão geral" e do botão "Diagnosticar e resolver problemas"](./media/change-analysis/change-analysis.png)

### <a name="enable-change-analysis-in-the-diagnose-and-solve-problems-tool"></a>Habilite a Análise de Alterações na ferramenta Diagnosticar e resolver problemas

1. Selecione **Disponibilidade e Desempenho**.

    ![Captura de tela das opções de solução de problemas "Disponibilidade e Desempenho"](./media/change-analysis/availability-and-performance.png)

1. Selecione **Alterações de aplicativos**. Não que o recurso também esteja disponível em **Falhas de aplicativo**.

   ![Captura de tela do botão "Falhas de aplicativo"](./media/change-analysis/application-changes.png)

1. Para habilitar a análise de alterações, **selecione Ativar agora**.

   ![Captura de tela das opções "Falhas de aplicativos"](./media/change-analysis/enable-changeanalysis.png)

1. Ativar **análise de alteração** e selecionar **Salvar**. A ferramenta exibe todos os aplicativos da Web um plano de Serviço de Aplicativo. Você pode usar o interruptor de nível de plano para ativar a Análise de Alterações para todos os aplicativos da Web um plano.

    ![Captura de tela da interface de usuário "Habilitar análise de alterações"](./media/change-analysis/change-analysis-on.png)


1. Para acessar a Análise de Alterações, **selecione Diagnosticar e resolver problemas** > Falhas de disponibilidade**e desempenho** > **do aplicativo**. Você verá um gráfico que resume o tipo de alterações ao longo do tempo, juntamente com detalhes sobre essas alterações. Por padrão, as alterações nas últimas 24 horas são exibidas para ajudar com problemas imediatos.

     ![Captura de tela da exibição de difemde de alteração](./media/change-analysis/change-view.png)


### <a name="enable-change-analysis-at-scale"></a>Habilitar análise de alterações em escala

Se sua assinatura incluir inúmeros aplicativos web, habilitar o serviço no nível do aplicativo web seria ineficiente. Execute o script a seguir para habilitar todos os aplicativos da Web em sua assinatura.

Pré-requisitos:
* Módulo PowerShell AZ. Siga as instruções no [Install the Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-2.6.0)

Execute o seguinte script:

```PowerShell
# Log in to your Azure subscription
Connect-AzAccount

# Get subscription Id
$SubscriptionId = Read-Host -Prompt 'Input your subscription Id'

# Make Feature Flag visible to the subscription
Set-AzContext -SubscriptionId $SubscriptionId

# Register resource provider
Register-AzResourceProvider -ProviderNamespace "Microsoft.ChangeAnalysis"


# Enable each web app
$webapp_list = Get-AzWebApp | Where-Object {$_.kind -eq 'app'}
foreach ($webapp in $webapp_list)
{
    $tags = $webapp.Tags
    $tags[“hidden-related:diagnostics/changeAnalysisScanEnabled”]=$true
    Set-AzResource -ResourceId $webapp.Id -Tag $tags -Force
}

```



## <a name="next-steps"></a>Próximas etapas

- Habilite o Application Insights para [aplicativos do Azure App Services](azure-web-apps.md).
- Habilite o Application Insights para a escala de [máquina virtual Azure VM e Azure definir aplicativos hospedados no IIS](azure-vm-vmss-apps.md).
- Saiba mais sobre [o Azure Resource Graph](https://docs.microsoft.com/azure/governance/resource-graph/overview), que ajuda a power Change Analysis.
