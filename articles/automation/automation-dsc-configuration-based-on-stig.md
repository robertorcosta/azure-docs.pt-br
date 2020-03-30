---
title: Configuração baseada no STIG para usar na configuração de estado - Azure Automation
description: Saiba mais sobre configurações baseadas no STIG para configuração de estado no Azure Automation.
keywords: DSC,powershell,configuração,instalação
services: automation
ms.service: automation
ms.subservice: dsc
author: mgreenegit
ms.author: migreene
ms.date: 08/08/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 275b3bd25f931b73e8a378433899ef9ade4d47c1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "76028282"
---
# <a name="configuration-based-on-stig"></a>Configuração baseada em STIG

> Aplica-se a: Windows PowerShell 5.1

Criar conteúdo de configuração pela primeira vez pode ser um desafio.
Em muitos casos, o objetivo é automatizar a configuração dos servidores seguindo uma "linha de base" que, esperançosamente, se alinha a uma recomendação do setor.

> [!NOTE]
> Este artigo refere-se a uma solução que é mantida pela comunidade Open Source.
> O suporte só está disponível na forma de colaboração do GitHub, não da Microsoft.

## <a name="community-project-powerstig"></a>Projeto comunitário: PowersTIG

Um projeto comunitário chamado [PowerSTIG](https://github.com/microsoft/powerstig) visa resolver esse problema gerando conteúdo DSC com base em [informações públicas](https://public.cyber.mil/stigs/) fornecidas sobre o STIG (Security Technical Implementation Guide),

Lidar com linhas de base é mais complicado do que parece.
Muitas organizações precisam [documentar exceções](https://github.com/microsoft/powerstig#powerstigdata) às regras e gerenciar esses dados em escala.
O PowerSTIG resolve o problema fornecendo [recursos compostos](https://github.com/microsoft/powerstig#powerstigdsc) para resolver cada área da configuração, em vez de tentar resolver toda a gama de configurações em um arquivo grande.

Uma vez que as configurações tenham sido geradas, você pode usar os [scripts de configuração dsc](/powershell/scripting/dsc/configurations/configurations) para gerar arquivos MOF e [carregar os arquivos MOF para a Automação Do Azure](/azure/automation/tutorial-configure-servers-desired-state#create-and-upload-a-configuration-to-azure-automation).
Em seguida, registre seus servidores [no local](/azure/automation/automation-dsc-onboarding#physicalvirtual-windows-machines-on-premises-or-in-a-cloud-other-than-azure-including-aws-ec2-instances) ou [no Azure](/azure/automation/automation-dsc-onboarding#azure-virtual-machines) para puxar configurações.

Para testar o PowerSTIG, visite a [Galeria PowerShell](https://www.powershellgallery.com) e baixe a solução ou clique em "Project Site" para visualizar a [documentação](https://github.com/microsoft/powerstig).

## <a name="next-steps"></a>Próximas etapas

- [Visão Geral da Configuração de Estado Desejado do Windows PowerShell](/powershell/scripting/dsc/overview/overview)
- [Recursos dsc](/powershell/scripting/dsc/resources/resources)
- [Configuração do gerenciador de configuração local](/powershell/scripting/dsc/managing-nodes/metaconfig)
