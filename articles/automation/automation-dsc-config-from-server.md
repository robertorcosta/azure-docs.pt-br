---
title: Criar configurações a partir de servidores existentes - Azure Automation
description: Saiba como criar configurações a partir de servidores existentes para a Automação Azure.
keywords: DSC,powershell,configuração,instalação
services: automation
ms.service: automation
ms.subservice: dsc
author: mgreenegit
ms.author: migreene
ms.date: 08/08/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: d888c8fd3bf7cd44c37e7225618bd197f419d275
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "76030135"
---
# <a name="create-configurations-from-existing-servers"></a>Criar configurações a partir de servidores existentes

> Aplica-se a: Windows PowerShell 5.1

Criar configurações a partir de servidores existentes pode ser uma tarefa desafiadora.
Você pode não querer *todas as* configurações, apenas aquelas que você se importa.
Mesmo assim, você precisa saber em que ordem as configurações devem ser aplicadas para que a configuração seja aplicada com sucesso.

> [!NOTE]
> Este artigo refere-se a uma solução que é mantida pela comunidade Open Source.
> O suporte só está disponível na forma de colaboração do GitHub, não da Microsoft.

## <a name="community-project-reversedsc"></a>Projeto comunitário: ReverseDSC

Uma solução mantida pela comunidade chamada [ReverseDSC](https://github.com/microsoft/reversedsc) foi criada para trabalhar nesta área iniciando o SharePoint.

A solução se baseia no [recurso SharePointDSC](https://github.com/powershell/sharepointdsc) e a estende para orquestrar a coleta de [informações](https://github.com/Microsoft/sharepointDSC.reverse#how-to-use) dos servidores SharePoint existentes.
A versão mais recente tem vários [modos de extração](https://github.com/Microsoft/SharePointDSC.Reverse/wiki/Extraction-Modes) para determinar qual nível de informação incluir.

O resultado do uso da solução é a geração de [dados de configuração](https://github.com/Microsoft/sharepointDSC.reverse#configuration-data) para serem usados com scripts de configuração do SharePointDSC.

Uma vez que os arquivos de dados tenham sido gerados, você pode usá-los com [scripts de configuração DSC](/powershell/scripting/dsc/overview/overview) para gerar arquivos MOF e [carregar os arquivos MOF para a Automação Do Azure](/azure/automation/tutorial-configure-servers-desired-state#create-and-upload-a-configuration-to-azure-automation).
Em seguida, registre seus servidores [no local](/azure/automation/automation-dsc-onboarding#physicalvirtual-windows-machines-on-premises-or-in-a-cloud-other-than-azure-including-aws-ec2-instances) ou [no Azure](/azure/automation/automation-dsc-onboarding#azure-virtual-machines) para puxar configurações.

Para testar o ReverseDSC, visite a [Galeria PowerShell](https://www.powershellgallery.com/packages/ReverseDSC/) e baixe a solução ou clique em "Site do projeto" para visualizar a [documentação](https://github.com/Microsoft/sharepointDSC.reverse).

## <a name="next-steps"></a>Próximas etapas

- [Visão Geral da Configuração de Estado Desejado do Windows PowerShell](/powershell/scripting/dsc/overview/overview)
- [Recursos dsc](/powershell/scripting/dsc/resources/resources)
- [Configuração do gerenciador de configuração local](/powershell/scripting/dsc/managing-nodes/metaconfig)
