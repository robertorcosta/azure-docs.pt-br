---
title: Configure ambientes de encenação
description: Aprenda a implantar aplicativos em um slot de não-produção e troca automaticamente na produção. Aumente a confiabilidade e elimine o tempo de inatividade do aplicativo a partir de implantações.
ms.assetid: e224fc4f-800d-469a-8d6a-72bcde612450
ms.topic: article
ms.date: 03/04/2020
ms.custom: fasttrack-edit
ms.openlocfilehash: 21e025088e59c7f65f848b332ecb393b05918261
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "78300836"
---
# <a name="set-up-staging-environments-in-azure-app-service"></a>Configurar ambientes de preparo no Serviço de Aplicativo do Azure
<a name="Overview"></a>

Quando você implanta seu aplicativo web, aplicativo web no Linux, back-end móvel ou aplicativo API para [o Azure App Service,](https://go.microsoft.com/fwlink/?LinkId=529714)você pode usar um slot de implantação separado em vez do slot de produção padrão quando estiver em execução no nível de plano **padrão,** **premium**ou serviço de aplicativo **isolado.** Os slots de implantação são aplicativos ao vivo com seus próprios nomes de host. Os elementos de configurações e conteúdo de aplicativo podem ser trocados entre dois slots de implantação, incluindo o slot de produção. 

A implantação do aplicativo em um slot de não produção traz os seguintes benefícios:

* É possível validar as alterações no aplicativo em um slot de implantação de preparo antes de permutá-lo pelo slot de produção.
* Implantar um aplicativo em um slot primeiro e alterná-lo para produção garantem que todas as instâncias do slot estejam aquecidas antes de alterná-lo para produção. Isso elimina o tempo de inatividade quando você for implantar seu aplicativo. O redirecionamento de tráfego é contínuo, e nenhuma solicitação é removida devido a operações de alternância. Você pode automatizar todo esse fluxo de trabalho configurando [troca automática](#Auto-Swap) quando a validação de pré-swap não é necessária.
* Após a troca, o slot com o aplicativo de preparo anterior terá o aplicativo de produção anterior. Se as alterações alternadas para o slot de produção não correspondem às suas expectativas, você pode realizar a mesma alternância imediatamente para ter o "último site válido conhecido" de volta.

Cada tipo de plano do Serviço de Aplicativo dá suporte a um número diferente de slots de implantação. Não há custo adicional para usar slots de implantação. Para descobrir o número de slots que o nível de suporte do aplicativo suporta, consulte [os limites do App Service](../azure-resource-manager/management/azure-subscription-service-limits.md#app-service-limits). 

Para dimensionar seu aplicativo para um nível diferente, certifique-se de que o nível de destino suporta o número de slots que seu aplicativo já usa. Por exemplo, se o seu aplicativo tiver mais de cinco slots, você não pode dimensioná-lo para o nível **Padrão,** porque o nível **Padrão** suporta apenas cinco slots de implantação. 

<a name="Add"></a>

## <a name="add-a-slot"></a>Adicionar um slot
O aplicativo deve estar em execução no nível **Padrão,** **Premium**ou **Isolado** para que você habilite vários slots de implantação.


1. no [portal Azure,](https://portal.azure.com/)procure e selecione **Serviços de Aplicativos** e selecione seu aplicativo. 
   
    ![Procure serviços de aplicativos](./media/web-sites-staged-publishing/search-for-app-services.png)
   

2. No painel esquerdo, selecione **'Espaços de implantação' Adicionar** > **slot**.
   
    ![Adicionar um novo slot de implantação](./media/web-sites-staged-publishing/QGAddNewDeploymentSlot.png)
   
   > [!NOTE]
   > Se o aplicativo ainda não estiver no nível **Padrão,** **Premium**ou **Isolado,** você receberá uma mensagem que indica os níveis suportados para habilitar a publicação em etapas. Neste ponto, você tem a opção de selecionar **Atualizar** e ir para a guia **Escala** do seu aplicativo antes de continuar.
   > 

3. Na caixa de diálogo **Adicionar um slot,** dar um nome ao slot e selecionar se deve clonar uma configuração de aplicativo de outro slot de implantação. Selecione **Adicionar** para continuar.
   
    ![Fonte de configuração](./media/web-sites-staged-publishing/ConfigurationSource1.png)
   
    Você pode clonar uma configuração de qualquer slot existente. As configurações que podem ser clonadas incluem configurações de aplicativo, cadeias de conexão, versões da estrutura de linguagem, soquetes da Web, versão HTTP e número de bits da plataforma.

4. Depois que o slot for adicionado, selecione **Fechar** para fechar a caixa de diálogo. O novo slot agora é mostrado na página **de slots de implantação.** Por padrão, **o Tráfego %** é definido como 0 para o novo slot, com todo o tráfego do cliente encaminhado para o slot de produção.

5. Selecione o novo slot de implantação para abrir a página de recursos desse slot.
   
    ![Título de slot de implantação](./media/web-sites-staged-publishing/StagingTitle.png)

    O slot de preparo tem uma página de gerenciamento como qualquer outro aplicativo do Serviço de Aplicativo. É possível alterar a configuração do slot. O nome do slot é mostrado na parte superior da página para lembrá-lo de que você está exibindo o slot de implantação.

6. Selecione a URL do aplicativo na página de recursos do slot. O slot de implantação tem seu próprio nome de host e também é um aplicativo ao vivo. Para limitar o acesso público ao slot de implantação, consulte [as restrições de IP do Azure App Service](app-service-ip-restrictions.md).

O novo slot de implantação não tem nenhum conteúdo, mesmo se as configurações são clonadas de outro slot. Por exemplo, você pode [publicar para este slot com Git](app-service-deploy-local-git.md). É possível fazer uma implantação no slot de outro branch do repositório ou de outro repositório. 

<a name="AboutConfiguration"></a>

## <a name="what-happens-during-a-swap"></a>O que acontece durante uma troca

### <a name="swap-operation-steps"></a>Etapas de operação de swap

Quando você troca dois slots (geralmente de um slot de preparação para o slot de produção), o App Service faz o seguinte para garantir que o slot de destino não tenha tempo de inatividade:

1. Aplique as seguintes configurações do slot de destino (por exemplo, o slot de produção) em todas as instâncias do slot de origem: 
    - [Configurações de](#which-settings-are-swapped) aplicativo específicas para slot e strings de conexão, se aplicável.
    - [Configurações de implantação contínuas,](deploy-continuous-deployment.md) se ativadas.
    - [Configurações de autenticação do Serviço](overview-authentication-authorization.md) de Aplicativo, se ativada.
    
    Qualquer um desses casos aciona todas as instâncias no slot de origem para reiniciar. Durante [a troca com a visualização,](#Multi-Phase)isso marca o fim da primeira fase. A operação de swap é pausada e você pode validar que o slot de origem funciona corretamente com as configurações do slot de destino.

1. Aguarde todas as instâncias no slot de origem para concluir sua reinicialização. Se qualquer instância não for reiniciada, a operação de swap reverte todas as alterações no slot de origem e interrompe a operação.

1. Se [o cache local](overview-local-cache.md) estiver ativado, acione a inicialização do cache local fazendo uma solicitação HTTP para a raiz do aplicativo ("/") em cada instância do slot de origem. Aguarde até que cada instância retorne qualquer resposta HTTP. A inicialização do cache local faz com que outra reinicialização em cada instância.

1. Se [a troca automática](#Auto-Swap) estiver ativada com aquecimento [personalizado,](#Warm-up)acione [a iniciação do aplicativo](https://docs.microsoft.com/iis/get-started/whats-new-in-iis-8/iis-80-application-initialization) fazendo uma solicitação HTTP para a raiz do aplicativo ("/") em cada instância do slot de origem.

    Se `applicationInitialization` não for especificado, acione uma solicitação HTTP para a raiz do aplicativo do slot de origem em cada instância. 
    
    Se uma instância retornar qualquer resposta HTTP, ela será considerada aquecida.

1. Se todas as instâncias do slot de origem forem aquecidas com sucesso, troque os dois slots mudando as regras de roteamento para os dois slots. Após essa etapa, o slot de destino (por exemplo, o slot de produção) tem o aplicativo que já foi aquecido no slot de origem.

1. Agora que o slot de origem tem o aplicativo de pré-swap anteriormente no slot de destino, execute a mesma operação aplicando todas as configurações e reiniciando as instâncias.

Em qualquer ponto da operação de swap, todo o trabalho de inicialização dos aplicativos trocados acontece no slot de origem. O slot de destino permanece on-line enquanto o slot de origem está sendo preparado e aquecido, independentemente de onde a troca tenha sucesso ou falhe. Para trocar um slot de preparação com o slot de produção, certifique-se de que o slot de produção é sempre o slot de destino. Dessa forma, a operação de swap não afeta seu aplicativo de produção.

### <a name="which-settings-are-swapped"></a>Quais configurações são trocadas?

[!INCLUDE [app-service-deployment-slots-settings](../../includes/app-service-deployment-slots-settings.md)]

Para configurar uma configuração de aplicativo ou uma seqüência de conexão para ficar com um slot específico (não trocado), vá para a página **Configuração** para esse slot. Adicione ou edite uma configuração e, em seguida, selecione **a configuração de slot de implantação**. A seleção desta caixa de seleção diz ao App Service que a configuração não pode ser trocada. 

![Configuração do slot](./media/web-sites-staged-publishing/SlotSetting.png)

<a name="Swap"></a>

## <a name="swap-two-slots"></a>Alternar dois slots 
Você pode trocar os slots de implantação na página **de slots de implantação do** aplicativo e na página Visão **Geral.** Para obter detalhes técnicos sobre a troca de slot, consulte [o que acontece durante a troca](#AboutConfiguration).

> [!IMPORTANT]
> Antes de trocar um aplicativo de um slot de implantação para a produção, certifique-se de que a produção é o seu slot de destino e que todas as configurações no slot de origem estão configuradas exatamente como você deseja tê-los em produção.
> 
> 

Para trocar os slots de implantação:

1. Vá para a página **de slots de implantação do** aplicativo e selecione **Swap**.
   
    ![Botão de troca](./media/web-sites-staged-publishing/SwapButtonBar.png)

    A caixa de diálogo **Swap** mostra as configurações nos slots de origem e destino selecionados que serão alterados.

2. Selecione os slots de **Origem** e de **Destino** desejados. Geralmente, o destino é o slot de produção. Além disso, selecione as **guias 'Alterações de origem'** e **alterações de destino** e verifique se as alterações de configuração são esperadas. Quando terminar, poderá trocar os slots imediatamente selecionando **Swap**.

    ![Troca completa](./media/web-sites-staged-publishing/SwapImmediately.png)

    Para ver como o slot de destino seria executado com as novas configurações antes da troca realmente acontecer, não selecione **Swap,** mas siga as instruções em [Swap com visualização](#Multi-Phase).

3. Quando terminar, feche a caixa de diálogo selecionando **Fechar**.

Se você tiver algum problema, consulte [Trocas de solução de problemas](#troubleshoot-swaps).

<a name="Multi-Phase"></a>

### <a name="swap-with-preview-multi-phase-swap"></a>Troca com visualização (troca de várias fases)

Antes de trocar a produção como o slot de destino, valide se o aplicativo é executado com as configurações trocadas. O slot de origem também é aquecido antes da conclusão do swap, o que é desejável para aplicações de missão crítica.

Quando você realiza uma troca com a visualização, o App Service executa a mesma [operação de swap,](#AboutConfiguration) mas faz uma pausa após a primeira etapa. Em seguida, você pode verificar o resultado no slot de preparação antes de concluir a troca. 

Se você cancelar a troca, o Serviço de Aplicativo reaplicará elementos de configuração no slot de origem.

Para trocar com a visualização:

1. Siga as etapas nos [slots de implantação do Swap,](#Swap) mas selecione **Executar swap com visualização**.

    ![Alternância com visualização](./media/web-sites-staged-publishing/SwapWithPreview.png)

    A caixa de diálogo mostra como a configuração no slot de origem muda na fase 1 e como o slot de origem e destino mudam na fase 2.

2. Quando estiver pronto para iniciar a troca, selecione **Iniciar swap**.

    Quando a fase 1 terminar, você é notificado na caixa de diálogo. Visualizar a troca no slot `https://<app_name>-<source-slot-name>.azurewebsites.net`de origem indo para . 

3. Quando estiver pronto para concluir a troca pendente, selecione **Swap completo** na **ação Swap** e selecione Swap **completo**.

    Para cancelar uma troca pendente, **selecione '''''Troca de** cancelamento'.

4. Quando terminar, feche a caixa de diálogo selecionando **Fechar**.

Se você tiver algum problema, consulte [Trocas de solução de problemas](#troubleshoot-swaps).

Para automatizar uma troca de várias fases, consulte [Automate com powerShell](#automate-with-powershell).

<a name="Rollback"></a>

## <a name="roll-back-a-swap"></a>Reverter uma troca
Se ocorrerem erros no slot de destino (por exemplo, o slot de produção) após uma alternância de slot, restaure os slots para seus estados de pré-alternância alternando os mesmos dois slots imediatamente.

<a name="Auto-Swap"></a>

## <a name="configure-auto-swap"></a>Configurar a alternância automática

> [!NOTE]
> A troca automática não é suportada em aplicativos web no Linux.

A troca automática simplifica os cenários do Azure DevOps onde você deseja implantar seu aplicativo continuamente com zero partidas frias e zero tempo de inatividade para os clientes do aplicativo. Quando a troca automática é ativada a partir de um slot para a produção, toda vez que você empurra seu código altera para esse slot, o App Service troca automaticamente [o aplicativo em produção](#swap-operation-steps) depois que ele é aquecido no slot de origem.

   > [!NOTE]
   > Antes de configurar a troca automática para o slot de produção, considere testar a troca automática em um slot de destino não-produzido.
   > 

Para configurar a troca automática:

1. Acesse a página de recursos do seu aplicativo. Selecione **Slots de implantação desejados** > *\<slot de origem>*  > **configurações gerais**de **configuração** > .
   
2. Para **troca automática ativada, selecione** **Em**. Em seguida, selecione o slot de destino desejado para **slot de implantação de troca automática**e **selecione Salvar** na barra de comando. 
   
    ![Seleções para configurar troca automática](./media/web-sites-staged-publishing/AutoSwap02.png)

3. Execute um push de código para o slot de origem. A troca automática acontece após um curto período de tempo, e a atualização é refletida na URL do seu slot de destino.

Se você tiver algum problema, consulte [Trocas de solução de problemas](#troubleshoot-swaps).

<a name="Warm-up"></a>

## <a name="specify-custom-warm-up"></a>Especificar aquecimento personalizado

Alguns aplicativos podem exigir ações personalizadas de aquecimento antes da troca. O `applicationInitialization` elemento de configuração no web.config permite especificar ações de inicialização personalizadas. A [operação de troca](#AboutConfiguration) aguarda o término deste aquecimento personalizado antes de ser trocada pelo slot de destino. Aqui está uma amostra de fragmento web.config.

    <system.webServer>
        <applicationInitialization>
            <add initializationPage="/" hostName="[app hostname]" />
            <add initializationPage="/Home/About" hostName="[app hostname]" />
        </applicationInitialization>
    </system.webServer>

Para obter mais informações `applicationInitialization` sobre a personalização do elemento, consulte [Falhas de troca de slot de implantação mais comuns e como corrigi-las](https://ruslany.net/2017/11/most-common-deployment-slot-swap-failures-and-how-to-fix-them/).

Você também pode personalizar o comportamento de aquecimento com uma ou ambas as configurações do aplicativo a [seguir:](configure-common.md)

- `WEBSITE_SWAP_WARMUP_PING_PATH`: O caminho para ping para aquecer seu site. Adicione essa configuração de aplicativo especificando um caminho personalizado que começa com uma barra (“/”) como o valor. Um exemplo é `/statuscheck`. O valor padrão é `/`. 
- `WEBSITE_SWAP_WARMUP_PING_STATUSES`: Códigos de resposta HTTP válidos para a operação de aquecimento. Adicione essa configuração de aplicativo com uma lista separada por vírgulas dos códigos HTTP. Um exemplo `200,202` é. Se o código de status retornado não estiver na lista, as operações de aquecimento e troca serão interrompidas. Por padrão, todos os códigos de resposta são válidos.

> [!NOTE]
> O `<applicationInitialization>` elemento de configuração faz parte de cada inicialinicialde aplicativo, enquanto as duas configurações do aplicativo de comportamento de aquecimento se aplicam apenas a swaps de slot.

Se você tiver algum problema, consulte [Trocas de solução de problemas](#troubleshoot-swaps).

## <a name="monitor-a-swap"></a>Monitore uma troca

Se a [operação de swap](#AboutConfiguration) demorar muito tempo para ser concluída, você poderá obter informações sobre a operação de swap no registro de [atividades](../monitoring-and-diagnostics/monitoring-overview-activity-logs.md).

Na página de recursos do seu aplicativo no portal, no painel esquerdo, selecione **Registro de atividades**.

Uma operação de troca é exibida na consulta de log como `Swap Web App Slots`. Você pode expandi-lo e selecionar uma das suboperações ou um dos erros para ver os detalhes.

## <a name="route-traffic"></a>Rotear o tráfego

Por padrão, todas as solicitações de cliente para a URL de produção do aplicativo (`http://<app_name>.azurewebsites.net`) são roteadas para o slot de produção. É possível rotear uma parte do tráfego para outro slot. Esse recurso é útil se você precisa de comentários do usuário para uma nova atualização, mas não está pronto para liberá-la para produção.

### <a name="route-production-traffic-automatically"></a>Rotear o tráfego de produção automaticamente

Para direcionar o tráfego de produção automaticamente:

1. Vá para a página de recursos do seu aplicativo e selecione **slots de implantação**.

2. Na coluna **% do Tráfego** do slot para o qual você deseja rotear, especifique um percentual (entre 0 e 100) para representar a quantidade de tráfego total que deseja rotear. Selecione **Salvar**.

    ![Definindo uma porcentagem de tráfego](./media/web-sites-staged-publishing/RouteTraffic.png)

Depois que a configuração é salva, a porcentagem especificada de clientes é roteada aleatoriamente para o slot de não produção. 

Depois que um cliente é automaticamente encaminhado para um slot específico, ele é "fixado" a esse slot para a vida útil daquela sessão de cliente. No navegador do cliente, você pode ver em qual slot a sessão está fixada observando o cookie `x-ms-routing-name` nos cabeçalhos HTTP. Uma solicitação roteada para o slot "de preparo" tem o cookie `x-ms-routing-name=staging`. Uma solicitação roteada para o slot de produção tem o cookie `x-ms-routing-name=self`.

   > [!NOTE]
   > Ao lado do portal Azure, [`az webapp traffic-routing set`](/cli/azure/webapp/traffic-routing#az-webapp-traffic-routing-set) você também pode usar o comando no Azure CLI para definir as porcentagens de roteamento de ferramentas de CI/CD, como pipelines DevOps ou outros sistemas de automação.
   > 

### <a name="route-production-traffic-manually"></a>Rotear o tráfego de produção manualmente

Além do roteamento de tráfego automático, o Serviço de Aplicativo pode rotear solicitações para um slot específico. Isso é útil quando você quer que seus usuários possam optar ou optar por sair do seu aplicativo beta. Para rotear o tráfego de produção manualmente, use o parâmetro de consulta `x-ms-routing-name`.

Para permitir que os usuários optem por sair do seu aplicativo beta, por exemplo, você pode colocar este link em sua página web:

```HTML
<a href="<webappname>.azurewebsites.net/?x-ms-routing-name=self">Go back to production app</a>
```

A cadeia de caracteres `x-ms-routing-name=self` especifica o local de produção. Depois que o navegador cliente acessa o link, ele é redirecionado para o slot de produção. Cada solicitação subseqüente tem o `x-ms-routing-name=self` cookie que prende a sessão ao slot de produção.

Para permitir que os usuários optem pelo seu aplicativo beta, defina o mesmo parâmetro de consulta para o nome do slot de não produção. Aqui está um exemplo:

```
<webappname>.azurewebsites.net/?x-ms-routing-name=staging
```

Por padrão, novos slots recebem uma `0%`regra de roteamento de , mostrado em cinza. Quando você define explicitamente `0%` esse valor para (mostrado em texto preto), seus usuários `x-ms-routing-name` podem acessar o slot de preparação manualmente usando o parâmetro de consulta. Mas eles não serão encaminhados automaticamente para o slot porque a porcentagem de roteamento está definida como 0. Este é um cenário avançado onde você pode "esconder" seu slot de preparação do público, permitindo que equipes internas testem mudanças no slot.

<a name="Delete"></a>

## <a name="delete-a-slot"></a>Exclua um slot

Pesquise e selecione seu aplicativo. Selecione **slots** > *\<de *implantação para excluir> >  **Visão Geral**. Selecione **Excluir** na barra de comando.  

![Excluir um slot de implantação](./media/web-sites-staged-publishing/DeleteStagingSiteButton.png)

<!-- ======== AZURE POWERSHELL CMDLETS =========== -->

<a name="PowerShell"></a>

## <a name="automate-with-powershell"></a>Automatizar com o PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

O Azure PowerShell é um módulo que fornece cmdlets para gerenciar o Azure por meio do Windows PowerShell, incluindo suporte ao gerenciamento de slots de implantação no Serviço de Aplicativo do Azure.

Para obter mais informações sobre como instalar e configurar o PowerShell do Azure, e como autenticar o PowerShell do Azure com sua assinatura do Azure, consulte [Como instalar e configurar o PowerShell do Microsoft Azure](/powershell/azure/overview).  

---
### <a name="create-a-web-app"></a>Criar um aplicativo Web
```powershell
New-AzWebApp -ResourceGroupName [resource group name] -Name [app name] -Location [location] -AppServicePlan [app service plan name]
```

---
### <a name="create-a-slot"></a>Criar um slot
```powershell
New-AzWebAppSlot -ResourceGroupName [resource group name] -Name [app name] -Slot [deployment slot name] -AppServicePlan [app service plan name]
```

---
### <a name="initiate-a-swap-with-a-preview-multi-phase-swap-and-apply-destination-slot-configuration-to-the-source-slot"></a>Inicie uma troca com uma visualização (troca de várias fases) e aplique a configuração do slot de destino no slot de origem
```powershell
$ParametersObject = @{targetSlot  = "[slot name – e.g. "production"]"}
Invoke-AzResourceAction -ResourceGroupName [resource group name] -ResourceType Microsoft.Web/sites/slots -ResourceName [app name]/[slot name] -Action applySlotConfig -Parameters $ParametersObject -ApiVersion 2015-07-01
```

---
### <a name="cancel-a-pending-swap-swap-with-review-and-restore-the-source-slot-configuration"></a>Cancele uma troca pendente (swap com revisão) e restaure a configuração do slot de origem
```powershell
Invoke-AzResourceAction -ResourceGroupName [resource group name] -ResourceType Microsoft.Web/sites/slots -ResourceName [app name]/[slot name] -Action resetSlotConfig -ApiVersion 2015-07-01
```

---
### <a name="swap-deployment-slots"></a>Permute slots de implantação
```powershell
$ParametersObject = @{targetSlot  = "[slot name – e.g. "production"]"}
Invoke-AzResourceAction -ResourceGroupName [resource group name] -ResourceType Microsoft.Web/sites/slots -ResourceName [app name]/[slot name] -Action slotsswap -Parameters $ParametersObject -ApiVersion 2015-07-01
```

### <a name="monitor-swap-events-in-the-activity-log"></a>Monitore eventos de swap no registro de atividades
```powershell
Get-AzLog -ResourceGroup [resource group name] -StartTime 2018-03-07 -Caller SlotSwapJobProcessor  
```

---
### <a name="delete-a-slot"></a>Exclua um slot
```powershell
Remove-AzResource -ResourceGroupName [resource group name] -ResourceType Microsoft.Web/sites/slots –Name [app name]/[slot name] -ApiVersion 2015-07-01
```

## <a name="automate-with-resource-manager-templates"></a>Automatize com modelos do Gerenciador de Recursos

[Os modelos do Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/template-deployment-overview) são arquivos JSON declarativos usados para automatizar a implantação e a configuração dos recursos do Azure. Para trocar slots usando modelos do Gerenciador de recursos, você definirá duas propriedades nos recursos *Microsoft.Web/sites/slots* e *Microsoft.Web/sites:*

- `buildVersion`: esta é uma propriedade string que representa a versão atual do aplicativo implantado no slot. Por exemplo: "v1", "1.0.0.1" ou "2019-09-20T11:53:25.2887393-07:00".
- `targetBuildVersion`: esta é uma propriedade `buildVersion` de string que especifica o que o slot deve ter. Se o targetBuildVersion não `buildVersion`for igual à corrente, isso desencadeará a operação `buildVersion`de swap encontrando o slot que tem o especificado .

### <a name="example-resource-manager-template"></a>Exemplo de modelo do Gerenciador de recursos

O modelo seguinte do `buildVersion` Gerenciador de Recursos atualizará o slot de preparação e definirá o `targetBuildVersion` slot de produção. Isso vai trocar os dois slots. O modelo pressupõe que você já tenha um webapp criado com um slot chamado "staging".

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "my_site_name": {
            "defaultValue": "SwapAPIDemo",
            "type": "String"
        },
        "sites_buildVersion": {
            "defaultValue": "v1",
            "type": "String"
        }
    },
    "resources": [
        {
            "type": "Microsoft.Web/sites/slots",
            "apiVersion": "2018-02-01",
            "name": "[concat(parameters('my_site_name'), '/staging')]",
            "location": "East US",
            "kind": "app",
            "properties": {
                "buildVersion": "[parameters('sites_buildVersion')]"
            }
        },
        {
            "type": "Microsoft.Web/sites",
            "apiVersion": "2018-02-01",
            "name": "[parameters('my_site_name')]",
            "location": "East US",
            "kind": "app",
            "dependsOn": [
                "[resourceId('Microsoft.Web/sites/slots', parameters('my_site_name'), 'staging')]"
            ],
            "properties": {
                "targetBuildVersion": "[parameters('sites_buildVersion')]"
            }
        }        
    ]
}
```

Este modelo de Gerenciador de recursos é impotente, o que significa que ele pode ser executado repetidamente e produzir o mesmo estado dos slots. Após a primeira `targetBuildVersion` execução, `buildVersion`corresponderá à corrente, de modo que uma troca não será acionada.

<!-- ======== Azure CLI =========== -->

<a name="CLI"></a>

## <a name="automate-with-the-cli"></a>Automatize com a CLI

Para obter os comandos da [CLI do Azure](https://github.com/Azure/azure-cli) para slots de implantação, confira [Slot de implantação do az webapp](/cli/azure/webapp/deployment/slot).

## <a name="troubleshoot-swaps"></a>Trocas de solução de problemas

Se ocorrer algum erro durante uma [troca de slot,](#AboutConfiguration)ele será registrado em *D:\home\LogFiles\eventlog.xml*. Também está registrado no registro de erro específico do aplicativo.

Aqui estão alguns erros comuns de swap:

- Uma solicitação HTTP para a raiz do aplicativo é cronometrada. A operação de troca espera por 90 segundos para cada solicitação HTTP e tenta tentativas em até 5 vezes. Se todas as repetições estiverem cronometradas, a operação de troca será interrompida.

- A inicialização do cache local pode falhar quando o conteúdo do aplicativo exceder a cota de disco local especificada para o cache local. Para obter mais informações, consulte [visão geral do cache local](overview-local-cache.md).

- Durante [o aquecimento personalizado,](#Warm-up)as solicitações HTTP são feitas internamente (sem passar pela URL externa). Eles podem falhar com certas regras de reescrita de URL na *Web.config*. Por exemplo, regras para redirecionar nomes de domínio ou aplicar HTTPS podem impedir que solicitações de aquecimento cheguem ao código do aplicativo. Para contornar esse problema, modifique suas regras de reescrita adicionando as duas condições a seguir:

    ```xml
    <conditions>
      <add input="{WARMUP_REQUEST}" pattern="1" negate="true" />
      <add input="{REMOTE_ADDR}" pattern="^100?\." negate="true" />
      ...
    </conditions>
    ```
- Sem um aquecimento personalizado, as regras de reescrita de URL ainda podem bloquear solicitações HTTP. Para contornar esse problema, modifique suas regras de reescrita adicionando a seguinte condição:

    ```xml
    <conditions>
      <add input="{REMOTE_ADDR}" pattern="^100?\." negate="true" />
      ...
    </conditions>
    ```
- Algumas [regras de restrição](app-service-ip-restrictions.md) de IP podem impedir que a operação de swap envie solicitações HTTP para o seu aplicativo. Os intervalos de endereços `10.` `100.` IPv4 que começam e são internos para sua implantação. Você deve permitir que eles se conectem ao seu aplicativo.

- Após trocas de slot, o aplicativo pode sofrer reinicializações inesperadas. Isso porque após uma troca, a configuração de vinculação do nome de host fica fora de sincronia, o que por si só não causa reinicializações. No entanto, certos eventos de armazenamento subjacentes (como failovers de volume de armazenamento) podem detectar essas discrepâncias e forçar todos os processos de trabalhadores a reiniciar. Para minimizar esses tipos de reinicializações, defina a [ `WEBSITE_ADD_SITENAME_BINDINGS_IN_APPHOST_CONFIG=1` configuração](https://github.com/projectkudu/kudu/wiki/Configurable-settings#disable-the-generation-of-bindings-in-applicationhostconfig) do aplicativo em *todos os slots*. No entanto, esta configuração do aplicativo *não* funciona com aplicativos WCF (Windows Communication Foundation).

## <a name="next-steps"></a>Próximas etapas
[Bloquear o acesso aos slots de não produção](app-service-ip-restrictions.md)
