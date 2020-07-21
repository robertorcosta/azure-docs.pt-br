---
title: Usar a análise de alterações do aplicativo no Azure Monitor para encontrar problemas do aplicativo Web | Microsoft Docs
description: Use a análise de alterações do aplicativo no Azure Monitor para solucionar problemas de aplicativos em sites ativos no serviço Azure App.
ms.topic: conceptual
author: cawams
ms.author: cawa
ms.date: 05/04/2020
ms.openlocfilehash: d53097c7884b9908cd3a2c7f21dc059ed9d00c39
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/20/2020
ms.locfileid: "86540155"
---
# <a name="use-application-change-analysis-preview-in-azure-monitor"></a>Usar a análise de alterações do aplicativo (versão prévia) no Azure Monitor

Quando ocorre um problema ou uma interrupção do site ativo, determinar rapidamente a causa raiz é fundamental. As soluções de monitoramento padrão podem alertá-lo sobre um problema. Eles podem até mesmo indicar qual componente está falhando. Mas esse alerta nem sempre explicará imediatamente a causa da falha. Você sabe que seu site funcionou cinco minutos atrás e agora ele está interrompido. O que mudou nos últimos cinco minutos? Essa é a pergunta que a análise de alterações do aplicativo foi projetada para responder em Azure Monitor.

Aproveitando o poder do [grafo de recursos do Azure](../../governance/resource-graph/overview.md), a análise de alterações fornece informações sobre as alterações do aplicativo do Azure para aumentar a capacidade de observação e reduzir o MTTR (tempo médio de reparo).

> [!IMPORTANT]
> A análise de alterações está atualmente em visualização. Esta versão de visualização é fornecida sem um contrato de nível de serviço. Esta versão não é recomendada para cargas de trabalho de produção. Alguns recursos podem não ter suporte ou podem ter recursos restritos. Para obter mais informações, consulte os [Termos de uso complementares de versões prévias do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="overview"></a>Visão geral

A análise de alterações detecta vários tipos de alterações, da camada de infraestrutura até a implantação do aplicativo. É um provedor de recursos do Azure de nível de assinatura que verifica as alterações de recursos na assinatura. A análise de alterações fornece dados para várias ferramentas de diagnóstico para ajudar os usuários a entender quais alterações podem ter causado problemas.

O diagrama a seguir ilustra a arquitetura da análise de alterações:

![Diagrama de arquitetura de como a análise de alterações obtém dados de alteração e fornece a ele ferramentas de cliente](./media/change-analysis/overview.png)

## <a name="data-sources"></a>Fontes de dados

Consultas de análise de alteração de aplicativo para Azure Resource Manager propriedades rastreadas, configurações de proxy e aplicativo Web em alterações de convidado. Além disso, o serviço rastreia as alterações de dependência de recursos para diagnosticar e monitorar um aplicativo de ponta a ponta.

### <a name="azure-resource-manager-tracked-properties-changes"></a>Azure Resource Manager alterações de propriedades rastreadas

Usando o [grafo de recursos do Azure](../../governance/resource-graph/overview.md), a análise de alterações fornece um registro histórico de como os recursos do Azure que hospedam seu aplicativo foram alterados ao longo do tempo. Configurações rastreadas, como identidades gerenciadas, atualização do sistema operacional da plataforma e nomes de host podem ser detectadas.

### <a name="azure-resource-manager-proxied-setting-changes"></a>Alterações de configuração Azure Resource Manager com proxy

As configurações como regra de configuração de IP, configurações de TLS e versões de extensão ainda não estão disponíveis no grafo de recursos do Azure, portanto, as consultas de análise de alterações e calculam essas alterações com segurança para fornecer mais detalhes sobre o que mudou no aplicativo.

### <a name="changes-in-web-app-deployment-and-configuration-in-guest-changes"></a>Alterações na implantação e na configuração do aplicativo Web (alterações no convidado)

A análise de alterações captura o estado de implantação e configuração de um aplicativo a cada 4 horas. Ele pode detectar, por exemplo, alterações nas variáveis de ambiente do aplicativo. A ferramenta computa as diferenças e apresenta o que mudou. Ao contrário das alterações do Resource Manager, as informações de alteração da implantação de código podem não estar disponíveis imediatamente na ferramenta. Para exibir as alterações mais recentes na análise de alterações, selecione **Atualizar**.

![Captura de tela do botão "examinar alterações agora"](./media/change-analysis/scan-changes.png)

### <a name="dependency-changes"></a>Alterações de dependência

As alterações nas dependências de recursos também podem causar problemas em um aplicativo Web. Por exemplo, se um aplicativo Web chamar um cache Redis, a SKU do cache Redis poderá afetar o desempenho do aplicativo Web. Para detectar alterações nas dependências, a análise de alterações verifica o registro DNS do aplicativo Web. Dessa forma, ele identifica as alterações em todos os componentes de aplicativo que podem causar problemas.
Atualmente, há suporte para as seguintes dependências:
- Aplicativos Web
- Armazenamento do Azure
- SQL do Azure

## <a name="application-change-analysis-service"></a>Serviço de análise de alterações do aplicativo

O serviço de análise de alterações do aplicativo calcula e agrega dados de alteração das fontes de dados mencionadas acima. Ele fornece um conjunto de análises para que os usuários naveguem facilmente por todas as alterações de recursos e identifiquem qual alteração é relevante no contexto de solução de problemas ou monitoramento.
O provedor de recursos "Microsoft. ChangeAnalysis" precisa ser registrado com uma assinatura para o Azure Resource Manager propriedades rastreadas e as configurações de proxy alteram os dados para que estejam disponíveis. À medida que você insere a ferramenta diagnosticar e solucionar problemas do aplicativo Web ou ativa a guia autônomo da análise de alterações, esse provedor de recursos é registrado automaticamente. Ele não tem nenhuma implementação de desempenho ou custo para sua assinatura. Quando você habilita a análise de alterações para aplicativos Web (ou habilita a ferramenta diagnosticar e solucionar problemas), ele terá um impacto insignificante no desempenho do aplicativo Web e nenhum custo de cobrança.
Para alterações no convidado do aplicativo Web, a habilitação separada é necessária para a verificação de arquivos de código em um aplicativo Web. Para obter mais informações, consulte [análise de alterações na seção ferramenta diagnosticar e solucionar problemas](#application-change-analysis-in-the-diagnose-and-solve-problems-tool) mais adiante neste artigo para obter mais detalhes.

## <a name="visualizations-for-application-change-analysis"></a>Visualizações para análise de alterações de aplicativo

### <a name="standalone-ui"></a>Interface do usuário autônoma

Em Azure Monitor, há um painel autônomo para análise de alterações para exibir todas as alterações com informações sobre dependências e recursos do aplicativo.

Pesquise a análise de alterações na barra de pesquisa em portal do Azure para iniciar a experiência.

![Captura de tela da pesquisa de análise de alterações no portal do Azure](./media/change-analysis/search-change-analysis.png)

Todos os recursos em uma assinatura selecionada são exibidos com as alterações das últimas 24 horas. Para otimizar o desempenho de carregamento de página, o serviço está exibindo 10 recursos por vez. Clique em próximas páginas para exibir mais recursos. Estamos trabalhando para remover essa limitação.

![Captura de tela da folha de análise de alterações no portal do Azure](./media/change-analysis/change-analysis-standalone-blade.png)

Clicando em um recurso para exibir todas as suas alterações. Se necessário, faça uma busca detalhada em uma alteração para exibir detalhes e informações de alteração formatada em JSON.

![Captura de tela de detalhes de alteração](./media/change-analysis/change-details.png)

Para qualquer comentário, use o botão enviar comentários na folha ou no email changeanalysisteam@microsoft.com .

![Captura de tela do botão de comentários na folha de análise de alterações](./media/change-analysis/change-analysis-feedback.png)

### <a name="web-app-diagnose-and-solve-problems"></a>Diagnóstico e resolução de problemas do aplicativo Web

Em Azure Monitor, a análise de alterações também é incorporada à experiência de **diagnóstico e solução de problemas** de autoatendimento. Acesse essa experiência na página **visão geral** do aplicativo do serviço de aplicativo.

![Captura de tela do botão "visão geral" e o botão "diagnosticar e resolver problemas"](./media/change-analysis/change-analysis.png)

### <a name="application-change-analysis-in-the-diagnose-and-solve-problems-tool"></a>Análise de alteração de aplicativo na ferramenta diagnosticar e resolver problemas

A análise de alterações do aplicativo é um detector autônomo nas ferramentas de diagnóstico e solução de problemas do aplicativo Web. Ele também é agregado em **falhas de aplicativo** e **detectores de aplicativo Web**. À medida que você insere a ferramenta diagnosticar e resolver problemas, o provedor de recursos **Microsoft. ChangeAnalysis** será registrado automaticamente. Siga estas instruções para habilitar o controle de alterações do aplicativo Web no convidado.

1. Selecione **disponibilidade e desempenho**.

    ![Captura de tela das opções de solução de problemas de "disponibilidade e desempenho"](./media/change-analysis/availability-and-performance.png)

2. Selecione **alterações do aplicativo**. O recurso também está disponível em **falhas do aplicativo**.

   ![Captura de tela do botão "falhas do aplicativo"](./media/change-analysis/application-changes.png)

3. Para habilitar a análise de alterações, selecione **habilitar agora**.

   ![Captura de tela de opções de "falhas do aplicativo"](./media/change-analysis/enable-changeanalysis.png)

4. Ative a **análise de alterações** e selecione **salvar**. A ferramenta exibe todos os aplicativos Web em um plano do serviço de aplicativo. Você pode usar a opção de nível de plano para ativar a análise de alterações para todos os aplicativos Web em um plano.

    ![Captura de tela da interface do usuário "Habilitar análise de alteração"](./media/change-analysis/change-analysis-on.png)

5. Para acessar a análise de alterações, selecione **diagnosticar e solucionar problemas**  >  **de falhas de aplicativo de desempenho e disponibilidade**  >  **Application Crashes**. Você verá um grafo que resume o tipo de alterações ao longo do tempo, juntamente com detalhes sobre essas alterações. Por padrão, as alterações nas últimas 24 horas são exibidas para ajudar com problemas imediatos.

     ![Captura de tela da exibição alterar comparação](./media/change-analysis/change-view.png)

### <a name="enable-change-analysis-at-scale"></a>Habilitar análise de alterações em escala

Se sua assinatura inclui vários aplicativos Web, a habilitação do serviço no nível do aplicativo Web seria ineficiente. Execute o script a seguir para habilitar todos os aplicativos Web em sua assinatura.

Pré-requisitos:

- Módulo AZ do PowerShell. Siga as instruções em [instalar o módulo Azure PowerShell](/powershell/azure/install-az-ps?view=azps-2.6.0)

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

### <a name="virtual-machine-diagnose-and-solve-problems"></a>Problemas de diagnóstico e resolução da máquina virtual

Acesse a ferramenta diagnosticar e solucionar problemas para uma máquina virtual.  Vá para **ferramentas de solução de problemas**, navegue para baixo na página e selecione **analisar alterações recentes** para exibir as alterações na máquina virtual.

![Captura de tela da VM diagnosticar e resolver problemas](./media/change-analysis/vm-dnsp-troubleshootingtools.png)

![Captura de tela da VM diagnosticar e resolver problemas](./media/change-analysis/analyze-recent-changes.png)

## <a name="next-steps"></a>Próximas etapas

- Habilitar Application Insights para [aplicativos de serviços Azure apps](azure-web-apps.md).
- Habilite Application Insights para a [VM do Azure e o conjunto de dimensionamento de máquinas virtuais do Azure aplicativos hospedados pelo IIS](azure-vm-vmss-apps.md).
- Saiba mais sobre o [grafo de recursos do Azure](../../governance/resource-graph/overview.md), que ajuda a análise de alterações de energia.
