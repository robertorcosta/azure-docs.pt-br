---
title: Converter configurações em recursos compostos para configuração de estado - Automação do Azure
description: Aprenda a converter configurações em recursos compostos para configuração de estado no Azure Automation.
keywords: DSC,powershell,configuração,instalação
services: automation
ms.service: automation
ms.subservice: dsc
author: mgreenegit
ms.author: migreene
ms.date: 08/08/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 05f0a81a738688df15ea9060071d9e266b54b7d2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79136648"
---
# <a name="convert-configurations-to-composite-resources"></a>Converter configurações em recursos de composição

> Aplica-se a: Windows PowerShell 5.1

Uma vez iniciada a criação de configurações, você pode criar rapidamente "cenários" que gerenciam grupos de configurações.
Exemplos seriam:

- criar um servidor web
- criar um servidor DNS
- criar um servidor SharePoint
- configurar um cluster SQL
- gerenciar configurações de firewall
- gerenciar configurações de senha

Se você estiver interessado em compartilhar este trabalho com outros, a melhor opção é empacotar a configuração como um [recurso composto](/powershell/scripting/dsc/resources/authoringresourcecomposite).
Criar recursos compostos pela primeira vez pode ser esmagador.

> [!NOTE]
> Este artigo refere-se a uma solução que é mantida pela comunidade Open Source.
> O suporte só está disponível na forma de colaboração do GitHub, não da Microsoft.

## <a name="community-project-compositeresource"></a>Projeto comunitário: Recurso Composto

Uma solução mantida pela comunidade chamada [CompositeResource](https://github.com/microsoft/compositeresource) foi criada para resolver esse desafio.

O CompositeResource automatiza o processo de criação de um novo módulo a partir de sua configuração.
Você começa por [dot sourcing](https://blogs.technet.microsoft.com/heyscriptingguy/2010/08/10/how-to-reuse-windows-powershell-functions-in-scripts/) o script de configuração em sua estação de trabalho (ou servidor de compilação) para que ele seja carregado na memória.
Em seguida, em vez de executar a configuração para gerar um arquivo MOF, use a função fornecida pelo módulo CompositeResource para automatizar uma conversão.
O cmdlet carregará o conteúdo da sua configuração, obterá a lista de parâmetros e gerará um novo módulo com tudo o que você precisa.

Depois de gerar um módulo, você pode incrementar a versão e adicionar notas de versão cada vez que fizer alterações e publicá-la no seu próprio [repositório PowerShellGet](https://powershellexplained.com/2018-03-03-Powershell-Using-a-NuGet-server-for-a-PSRepository/?utm_source=blog&utm_medium=blog&utm_content=psscriptrepo).

Depois de criar um módulo de recurso composto contendo sua configuração (ou várias configurações), você pode usá-los na [Experiência de Criação Composable](/azure/automation/compose-configurationwithcompositeresources) no Azure, ou adicioná-los aos [scripts de configuração DSC](/powershell/scripting/dsc/configurations/configurations) para gerar arquivos MOF e [carregar os arquivos MOF para a Automação Do Zure](/azure/automation/tutorial-configure-servers-desired-state#create-and-upload-a-configuration-to-azure-automation).
Em seguida, registre seus servidores [no local](/azure/automation/automation-dsc-onboarding#physicalvirtual-windows-machines-on-premises-or-in-a-cloud-other-than-azure-including-aws-ec2-instances) ou [no Azure](/azure/automation/automation-dsc-onboarding#azure-virtual-machines) para puxar configurações.
A última atualização do projeto também publicou [runbooks](https://www.powershellgallery.com/packages?q=DscGallerySamples) para a Azure Automation para automatizar o processo de importação de configurações da PowerShell Gallery.

Para testar a criação automatizada de recursos compostos para DSC, visite a [Galeria PowerShell](https://www.powershellgallery.com/packages/compositeresource/) e baixe a solução ou clique em "Project Site" para visualizar a [documentação](https://github.com/microsoft/compositeresource).

## <a name="next-steps"></a>Próximas etapas

- [Visão Geral da Configuração de Estado Desejado do Windows PowerShell](/powershell/scripting/dsc/overview/overview)
- [Recursos dsc](/powershell/scripting/dsc/resources/resources)
- [Configuração do gerenciador de configuração local](/powershell/scripting/dsc/managing-nodes/metaconfig)
