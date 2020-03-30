---
title: Estender os laboratórios Azure DevTest usando funções do Azure | Microsoft Docs
description: Saiba como estender o Azure DevTest Labs usando funções do Azure.
services: devtest-lab,lab-services
documentationcenter: na
author: spelluru
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/22/2019
ms.author: spelluru
ms.openlocfilehash: dd1fc4c1076d89c12b25837db9fa6a0ac3e1f3a5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "70014354"
---
# <a name="use-azure-functions-to-extend-devtest-labs"></a>Usar o Azure Functions para estender o DevTest Labs
Você pode usar funções do Azure para suportar cenários adicionais além dos que já são suportados pelo DevTest Labs. As funções do Azure podem ser usadas para estender a funcionalidade incorporada do serviço para atender às suas necessidades específicas dos negócios. A lista a seguir fornece alguns dos cenários possíveis. Este artigo mostra como implementar um desses cenários amostrais.

- Forneça um resumo de alto nível de máquinas virtuais (VMs) no Laboratório
- [Configurar um laboratório para usar um gateway de área de trabalho remota](configure-lab-remote-desktop-gateway.md)
- Relatórios de conformidade na página de suporte interno
- Permitir que os usuários completem operações que requerem permissões aumentadas na assinatura
- [Iniciar fluxos de trabalho com base em eventos do DevTest Labs](https://github.com/RogerBestMsft/DTL-SecureArtifactData)

## <a name="overview"></a>Visão geral
[Azure Functions](../azure-functions/functions-overview.md) é uma plataforma de computação sem servidor no Azure. O uso de funções do Azure em uma solução com o DevTest Labs nos permite aumentar os recursos existentes com nosso próprio código personalizado. Para obter mais informações sobre funções do Azure, consulte [a documentação de funções do Azure](../azure-functions/functions-overview.md). Para ilustrar como as funções do Azure podem ajudar a cumprir seus requisitos ou cenários completos no DevTest Labs, este artigo usa um exemplo de fornecer um resumo de alto nível de VMs no Laboratório da seguinte forma:

**Exemplo de exigência/cenário**: Os usuários podem ver detalhes sobre todas as VMs em um laboratório, incluindo o sistema operacional, proprietário e quaisquer artefatos aplicados.  Além disso, se o artefato **Aplicar atualizações do Windows** não foi aplicado recentemente, há uma maneira fácil de aplicá-lo.

Para completar o cenário, você usará duas funções conforme descrito no diagrama a seguir:  

![Fluxo geral](./media/extend-devtest-labs-azure-functions/flow.png)

O código-fonte dessas funções de amostra está localizado no [repositório DevTest Labs GitHub](https://github.com/Azure/azure-devtestlab/tree/master/samples/DevTestLabs/AzureFunctions) (ambas as implementações C# e PowerShell estão disponíveis).

- **AtualizaçãoInternalSupportPage**: Esta função consulta O DevTest Labs e atualiza a página de suporte interno diretamente com detalhes sobre as máquinas virtuais.
- **ApplyWindowsUpdateArtifact**: Para uma VM em um laboratório, esta função aplica o artefato de atualização do **Windows.**

## <a name="how-it-works"></a>Como ele funciona
Quando os usuários selecionam a página **de suporte interno** no DevTest Labs, eles têm uma página pré-preenchida com informações sobre VMs, proprietários de laboratório e contatos de suporte.  

Quando você seleciona o **botão Selecionar aqui para atualizar,** a página chama a primeira função Azure: **UpdateInternalSupportPage**. A função consulta DevTest Labs para obter informações e, em seguida, reescreve a página **de suporte interno** com as novas informações.

Há uma ação adicional que pode ser tomada, para quaisquer VMs em que os artefatos do Windows Update não foram aplicados recentemente, haverá um botão para aplicar atualizações do Windows à VM. Quando você seleciona o botão de atualização ***Executar o Windows** para uma VM, a página chama a segunda função Azure: **ApplyWindowsUpdateArtifact**. Esta função verifica se a máquina virtual está funcionando e, se sim, aplica diretamente o artefato [do Windows Update.](https://github.com/Azure/azure-devtestlab/tree/master/Artifacts/windows-install-windows-updates)

## <a name="step-by-step-walkthrough"></a>Passo a passo
Esta seção fornece instruções passo a passo para configurar os Recursos do Azure necessários para atualizar a página **de suporte interno.** Este passo a passo fornece um exemplo de extensão do DevTest Labs. Você pode usar este padrão para outros cenários.

### <a name="step-1-create-a-service-principal"></a>Passo 1: Crie um diretor de serviços 
O primeiro passo é obter um diretor de serviço com permissão para a assinatura que contém o laboratório. O principal do serviço deve usar a autenticação baseada em senha. Pode ser feito com [a Cli Azure,](/cli/azure/create-an-azure-service-principal-azure-cli?view=azure-cli-latest) [Azure PowerShell](/powershell/azure/create-azure-service-principal-azureps?view=azps-2.5.0)ou [o portal Azure.](../active-directory/develop/howto-create-service-principal-portal.md) Se você já tem um diretor de serviço para usar, você pode pular este passo.

Anote o **ID do aplicativo,** **a chave**e o **ID** do inquilino para o diretor do serviço. Você vai precisar deles mais tarde neste passo a passo. 

### <a name="step-2-download-the-sample-and-open-in-visual-studio-2019"></a>Passo 2: Baixe a amostra e abra no Visual Studio 2019
Baixe uma cópia da [amostra C# Azure Functions](https://github.com/Azure/azure-devtestlab/tree/master/samples/DevTestLabs/AzureFunctions/CSharp) localmente (clonando o repositório ou baixando o repositório [daqui).](https://github.com/Azure/azure-devtestlab/archive/master.zip)  

1. Abra a solução de amostra com o Visual Studio 2019.  
1. Instale a carga **de trabalho de desenvolvimento do Azure** para o Visual Studio se você ainda não tiver ela instalada. Ele pode ser instalado através do item de menu **Ferramentas** -> **Get Tools e Features).**

    ![Carga de trabalho de desenvolvimento do Azure](./media/extend-devtest-labs-azure-functions/azure-development-workload-vs.png)
1. Compile a solução. Selecione **Criar** e, em seguida, criar o item do menu **Solução.**

### <a name="step-3-deploy-the-sample-to-azure"></a>Passo 3: Implante a amostra para o Azure
No Visual Studio, na janela **Solution Explorer,** clique com o botão direito do mouse no projeto **AzureFunctions** e selecione **Publicar**. Siga o assistente para concluir a publicação em um aplicativo de função Azure novo ou existente. Para obter informações detalhadas sobre o desenvolvimento e a implantação de funções do Azure usando o Visual Studio, consulte [Develop Azure Functions usando o Visual Studio](../azure-functions/functions-develop-vs.md).

![Caixa de diálogo Publicar](./media/extend-devtest-labs-azure-functions/publish-dialog.png)


### <a name="step-4--gather-application-settings"></a>Passo 4: Reunir as configurações do aplicativo
Uma vez que as funções são publicadas, você precisa obter URLs para essas funções a partir do portal Azure. 

1. Navegue até o [Portal do Azure](https://portal.azure.com). 
1. Encontre o aplicativo de função.
1. Na página **Aplicativos de função,** selecione a função. 
1. Selecione **Obter URL de função** como mostrado na imagem a seguir. 

    ![Funções do Azure URLs](./media/extend-devtest-labs-azure-functions/function-url.png)
4. Copie e salve a URL. Repita estas etapas para a outra função Azure. 

Você também precisará de informações adicionais sobre o principal do serviço, como ID de aplicativo, chave e ID do inquilino.


### <a name="step-5--update-application-settings"></a>Passo 5: Atualizar as configurações do aplicativo
No Visual Studio, após a publicação da função Azure, selecione as **Configurações de serviço do aplicativo Edit Azure** em **Ações**. Atualize as seguintes configurações do aplicativo (controle remoto):

- AzureFunctionUrl_ApplyUpdates
- AzureFunctionUrl_UpdateSupportPage
- WindowsUpdateAllowedDays (padrão para 7)
- ServicePrincipal_AppId
- ServicePrincipal_Key
- ServicePrincipal_Tenant

    ![Configurações do aplicativo](./media/extend-devtest-labs-azure-functions/application-settings.png)

### <a name="step-6-test-the-azure-function"></a>Passo 6: Teste a função Azure
O último passo neste passo é testar a função Azure.  

1. Navegue até a função **UpdateInternalSupportPage** no aplicativo de função criado na etapa 3. 
1. Selecione **Teste** no lado direito da página. 
1. Digite as propriedades de rota (LABNAME, RESOURCEGROUPNAME e SUBSCRIPTIONID).
1. Selecione **Executar** para executar a função.  

    Esta função atualizará a página de suporte interno do laboratório especificado. Ele também inclui um botão para os usuários chamardiretamente a função da próxima vez

    ![Função de teste](./media/extend-devtest-labs-azure-functions/test-function.png)

## <a name="next-steps"></a>Próximas etapas
As funções do Azure podem ajudar a estender a funcionalidade do DevTest Labs para além do que já está incorporado e ajudar os clientes a atender aos seus requisitos exclusivos para suas equipes. Esse padrão pode ser ampliado & expandido ainda mais para cobrir ainda mais.  Para saber mais sobre o DevTest Labs, consulte os seguintes artigos: 

- [Arquitetura de referência corporativa do DevTest Labs](devtest-lab-reference-architecture.md)
- [Perguntas frequentes](devtest-lab-faq.md)
- [Dimensionamento de laboratórios de devtest](devtest-lab-guidance-scale.md)
- [Automatizando laboratórios de devtest com powershell](https://github.com/Azure/azure-devtestlab/tree/master/samples/DevTestLabs/Modules/Library/Tests)








