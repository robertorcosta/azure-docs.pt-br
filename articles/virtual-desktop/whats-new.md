---
title: O que há de novo na Área de Trabalho Virtual do Windows? - Azure
description: Novos recursos e atualizações de produto para a Área de Trabalho Virtual do Windows.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: overview
ms.date: 06/15/2020
ms.author: helohr
ms.reviewer: thhickli; darank
manager: lizross
ms.openlocfilehash: 383b59d10fa1de1b2ee0ea8b505c164577487255
ms.sourcegitcommit: 9bfd94307c21d5a0c08fe675b566b1f67d0c642d
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/17/2020
ms.locfileid: "84974374"
---
# <a name="whats-new-in-windows-virtual-desktop"></a>O que há de novo na Área de Trabalho Virtual do Windows?

A Área de Trabalho Virtual do Windows é atualizada regularmente. Neste artigo, você descobrirá:

- As últimas atualizações
- Novos recursos
- Melhorias aos recursos existentes
- Correções de bug

Este artigo é atualizado mensalmente. Volte a consultá-lo com frequência para acompanhar as atualizações.

## <a name="june-2020"></a>Junho de 2020

No mês passado, apresentamos a atualização da Área de Trabalho Virtual do Windows Spring 2020 em versão prévia. Essa atualização tem muitos recursos novos incríveis que adoraríamos apresentar. Aqui estão as novidades na atualização Spring 2020.

### <a name="windows-virtual-desktop-is-now-integrated-with-azure-resource-manager-preview"></a>A Área de Trabalho Virtual do Windows agora está integrada ao Azure Resource Manager (versão prévia)

A Área de Trabalho Virtual do Windows agora está integrada ao Azure Resource Manager. Na atualização mais recente, todos os objetos da Área de Trabalho Virtual do Windows agora são recursos do Azure Resource Manager. Essa atualização também é integrada ao RBAC (Controle de Acesso Baseado em Função) do Azure. Confira [O que é o Azure Resource Manager?](../azure-resource-manager/management/overview.md) para saber mais.

Isto é o que essa alteração faz para você:

- A Área de Trabalho Virtual do Windows agora está integrada ao portal do Azure. Isso significa que você pode gerenciar tudo diretamente no portal, sem o PowerShell, aplicativos Web ou ferramentas de terceiros. Para começar, confira nosso tutorial em [Criar um pool de host com o portal do Azure](create-host-pools-azure-marketplace.md).

- Antes da atualização Spring 2020, você só podia publicar RemoteApps e Desktops para usuários individuais. Com o Azure Resource Manager, agora você pode publicar recursos em grupos do Azure Active Directory.

- A versão anterior da Área de Trabalho Virtual do Windows tinha quatro funções de administrador internas que você podia atribuir a um locatário ou a um pool de host. Essas funções agora estão no [controle de acesso baseado em função](../role-based-access-control/overview.md) do Azure. Você pode aplicar essas funções a cada objeto do Azure Resource Manager da Área de Trabalho Virtual do Windows, o que lhe permite ter um modelo de delegação completo e avançado.

- Na atualização Spring 2020, você não precisa mais executar o Azure Marketplace nem o modelo do GitHub repetidamente para expandir um pool de host. Para expandir um pool de host, basta acessá-lo no portal do Azure e selecionar **+ Adicionar** para implantar hosts de sessão adicionais.

- A implantação do pool de host agora está totalmente integrada à [Galeria de Imagens Compartilhadas do Azure](../virtual-machines/windows/shared-image-galleries.md). A Galeria de Imagens Compartilhadas é um serviço do Azure separado que armazena definições de imagem de VM (máquina virtual), incluindo o controle de versão de imagem. Você também pode usar a replicação global para copiar e enviar suas imagens para outras regiões do Azure para implantação local.

- As funções de monitoramento que costumavam ser feitas por meio do PowerShell ou do aplicativo Web do Serviço de Diagnóstico agora foram movidas para Log Analytics no portal do Azure. Agora, você também tem duas opções para visualizar seus relatórios. Você pode executar consultas do Kusto e usar Pastas de Trabalho para criar relatórios visuais.

- Não é mais necessário o consentimento completo do Azure Active Directory (Azure AD) para usar a Área de Trabalho Virtual do Windows. Na atualização Spring 2020, o locatário do Azure AD na sua assinatura do Azure autentica os usuários e fornece controles RBAC para seus administradores.


### <a name="powershell-support"></a>Suporte ao PowerShell

Adicionamos novos cmdlets AzWvd ao módulo Azure PowerShell Az com a atualização Spring 2020. Esse novo módulo tem suporte no PowerShell Core, que é executado no .NET Core.

Para instalar o módulo, siga as instruções em [Configurar o módulo do PowerShell para a Área de Trabalho Virtual do Windows](powershell-module.md).

Você também pode ver uma lista de comandos disponíveis na [referência do PowerShell AzWvd](/powershell/module/az.desktopvirtualization/?view=azps-4.2.0#desktopvirtualization).

Para obter mais informações sobre os novos recursos, confira [nossa postagem no blog](https://techcommunity.microsoft.com/t5/itops-talk-blog/windows-virtual-desktop-spring-update-enters-public-preview/ba-p/1340245). 

### <a name="additional-gateways"></a>Gateways adicionais

Adicionamos um cluster de gateway na África do Sul para reduzir a latência de conexão.

### <a name="microsoft-teams-on-windows-virtual-desktop-preview"></a>Microsoft Teams na Área de Trabalho Virtual do Windows (versão prévia)

Fizemos algumas melhorias ao Microsoft Teams para Área de Trabalho Virtual do Windows. O mais importante é que a Área de Trabalho Virtual do Windows agora dá suporte ao redirecionamento audiovisual para chamadas. O redirecionamento melhora a latência criando caminhos diretos entre os usuários quando eles chamam o uso de áudio ou vídeo. Menos distância significa menos saltos, tornando as chamadas mais tranquilas.

Para saber mais, confira [nossa postagem no blog](https://azure.microsoft.com/updates/windows-virtual-desktop-media-optimization-for-microsoft-teams-is-now-available-in-public-preview/).

## <a name="next-steps"></a>Próximas etapas

Confira estes artigos para saber mais sobre as atualizações para nossos clientes para Área de Trabalho Virtual do Windows e Serviços de Área de Trabalho Remota:

- [Windows](/windows-server/remote/remote-desktop-services/clients/windowsdesktop-whatsnew)
- [macOS](/windows-server/remote/remote-desktop-services/clients/mac-whatsnew)
- [iOS](/windows-server/remote/remote-desktop-services/clients/ios-whatsnew)
- [Android](/windows-server/remote/remote-desktop-services/clients/android-whatsnew)
- [Web](/windows-server/remote/remote-desktop-services/clients/web-client-whatsnew)
