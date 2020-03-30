---
title: Atualizando o depurador de instantâneos do aplicativo Azure
description: Como atualizar o Snapshot Debugger para aplicativos .NET para a versão mais recente no Azure App Services ou através de pacotes Nuget
ms.topic: conceptual
author: pharring
ms.author: pharring
ms.date: 03/28/2019
ms.reviewer: mbullwin
ms.openlocfilehash: 6991db6055ca8556adff67f194bac0f4aa04cb06
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "77671385"
---
# <a name="upgrading-the-snapshot-debugger"></a>Atualizando o Snapshot Debugger

Para fornecer a melhor segurança possível para seus dados, a Microsoft está se afastando do TLS 1.0 e do TLS 1.1, que se mostraram vulneráveis a determinados atacantes. Se você estiver usando uma versão mais antiga da extensão do site, será necessário um upgrade para continuar funcionando. Este documento descreve as etapas necessárias para atualizar o depurador de snapshot para a versão mais recente. Existem dois caminhos de atualização principais, dependendo se você habilitou o Depurador de instantâneos usando uma extensão de site ou se você usou um SDK/Nuget adicionado ao seu aplicativo. Ambos os caminhos de atualização são discutidos abaixo. 

## <a name="upgrading-the-site-extension"></a>Atualizando a extensão do site

> [!IMPORTANT]
> Versões mais antigas do Application Insights usaram uma extensão de site privada chamada _extensão Application Insights para o Azure App Service_. A experiência atual do Application Insights é habilitada definindo configurações de aplicativos para iluminar uma extensão de site pré-instalada.
> Para evitar conflitos, o que pode fazer com que seu site pare de funcionar, é importante excluir a extensão do site privado primeiro. Veja o passo 4 abaixo.

Se você habilitou o depurador de instantâneos usando a extensão do site, você pode atualizar usando o seguinte procedimento:

1. Entre no portal do Azure.
2. Navegue até o recurso que tenha o Depurador de Aplicativos e o depurador de instantâneos ativados. Por exemplo, para um aplicativo web, navegue até o recurso App Service:

   ![Captura de tela do recurso individual do App Service chamado DiagService01](./media/snapshot-debugger-upgrade/app-service-resource.png)

3. Depois de navegar até o recurso, clique na lâmina Extensões e aguarde a lista de extensões para preencher:

   ![Captura de tela das extensões do serviço de aplicativos mostrando a extensão do Application Insights para o Azure App Service instalado](./media/snapshot-debugger-upgrade/application-insights-site-extension-to-be-deleted.png)

4. Se qualquer versão da _extensão Do Aplicativo Insights para o Azure App Service_ estiver instalada, selecione-a e clique em Excluir. Confirme **Sim** para excluir a extensão e aguardar que a exclusão seja concluída antes de passar para a próxima etapa.

   ![Captura de tela das extensões do serviço de aplicativo mostrando a extensão do Application Insights para o Azure App Service com o botão Excluir destacado](./media/snapshot-debugger-upgrade/application-insights-site-extension-delete.png)

5. Vá para a lâmina visão geral do seu recurso e clique em Insights de aplicativos:

   ![Captura de tela de três botões. O botão central com o nome Application Insights é selecionado](./media/snapshot-debugger-upgrade/application-insights-button.png)

6. Se esta é a primeira vez que você visualiza a lâmina Application Insights para este Serviço de Aplicativo, você será solicitado a ativar o Application Insights. Selecione **Ativar insights de aplicativos**.
 
   ![Captura de tela da primeira experiência para a lâmina Application Insights com o botão Ativar insights de aplicativo destacado](./media/snapshot-debugger-upgrade/turn-on-application-insights.png)

7. As configurações atuais do Application Insights são exibidas. A menos que você queira aproveitar a oportunidade para mudar suas configurações, você pode deixá-las como estão. O botão **Aplicar** na parte inferior da lâmina não está habilitado por padrão e você terá que alternar uma das configurações para ativar o botão. Você não precisa alterar nenhuma configuração real, em vez disso, você pode alterar a configuração e, em seguida, imediatamente alterá-la de volta. Recomendamos alternar a configuração Profiler e, em seguida, selecionar **Aplicar**.

   ![Captura de tela da página de configuração do serviço de aplicativo Do Aplicativo Insights com botão Aplicar destacado em vermelho](./media/snapshot-debugger-upgrade/view-application-insights-data.png)

8. Uma vez que você clique **em Aplicar,** você será solicitado a confirmar as alterações.

    > [!NOTE]
    > O local será reiniciado como parte do processo de atualização.

   ![Captura de tela do aplicativo Aplicativo aplicar aviso de monitoramento. Caixa de texto exibe mensagem: "Agora aplicaremos alterações nas configurações do aplicativo e instalaremos nossas ferramentas para vincular seu recurso Application Insights ao aplicativo web. Isso reiniciará o site. Deseja continuar?"](./media/snapshot-debugger-upgrade/apply-monitoring-settings.png)

9. Clique **em Sim** para aplicar as alterações e aguardar a conclusão do processo.

O site já foi atualizado e está pronto para uso.

## <a name="upgrading-snapshot-debugger-using-sdknuget"></a>Atualizando o snapshot depurador usando SDK/Nuget

Se o aplicativo estiver `Microsoft.ApplicationInsights.SnapshotCollector` usando uma versão abaixo da versão 1.3.1, ele precisará ser atualizado para uma [versão mais recente](https://www.nuget.org/packages/Microsoft.ApplicationInsights.SnapshotCollector) para continuar funcionando.
