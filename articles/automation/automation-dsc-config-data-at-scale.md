---
title: Dados de configuração em escala - Azure Automation
description: Saiba como configurar dados em escala para configuração de estado no Azure Automation.
keywords: DSC,powershell,configuração,instalação
services: automation
ms.service: automation
ms.subservice: dsc
author: mgreenegit
ms.author: migreene
ms.date: 08/08/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 644ea1c00af7e71ff56852298fff18e5293c137b
ms.sourcegitcommit: 3c318f6c2a46e0d062a725d88cc8eb2d3fa2f96a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/02/2020
ms.locfileid: "80585568"
---
# <a name="configuration-data-at-scale"></a>Dados de configuração em escala

> Aplica-se a: Windows PowerShell 5.1

Gerenciar centenas ou milhares de servidores pode ser um desafio.
Os clientes forneceram feedback de que o aspecto mais difícil é realmente gerenciar [dados de configuração.](/powershell/scripting/dsc/configurations/configdata)
Organizando informações em construções lógicas como localização, tipo e ambiente.

> [!NOTE]
> Este artigo refere-se a uma solução que é mantida pela comunidade Open Source.
> O suporte só está disponível na forma de colaboração do GitHub, não da Microsoft.

## <a name="community-project-datum"></a>Projeto comunitário: Datum

Uma solução mantida pela comunidade chamada [Datum](https://github.com/gaelcolas/Datum) foi criada para resolver esse desafio.
O Datum baseia-se em grandes ideias de outras plataformas de gerenciamento de configuração e implementa o mesmo tipo de solução para o PowerShell DSC.
As informações [são organizadas em arquivos de texto](https://github.com/gaelcolas/Datum#3-intended-usage) com base em idéias lógicas.
Exemplos seriam:

- Configurações que devem ser aplicadas globalmente
- Configurações que devem ser aplicadas a todos os servidores em um local
- Configurações que devem ser aplicadas a todos os servidores de banco de dados
- Configurações individuais do servidor

Essas informações estão organizadas no formato de arquivo que você preferir (JSON, Yaml ou PSD1).
Em seguida, os cmdlets são fornecidos para gerar arquivos de dados de [configuração, consolidando as informações](https://github.com/gaelcolas/Datum#datum-tree) de cada arquivo em uma única exibição de uma função servidor ou servidor.

Uma vez que os arquivos de dados tenham sido gerados, você pode usá-los com [scripts de configuração DSC](/powershell/scripting/dsc/configurations/write-compile-apply-configuration) para gerar arquivos MOF e [carregar os arquivos MOF para a Automação Do Azure](/azure/automation/tutorial-configure-servers-desired-state#create-and-upload-a-configuration-to-azure-automation).
Em seguida, registre seus servidores [no local](/azure/automation/automation-dsc-onboarding#onboarding-physicalvirtual-windows-machines-on-premises-or-in-a-cloud-other-than-azure-including-aws-ec2-instances) ou [no Azure](/azure/automation/automation-dsc-onboarding#onboarding-azure-vms) para puxar configurações.

Para testar o Datum, visite a [Galeria PowerShell](https://www.powershellgallery.com/packages/datum/) e baixe a solução ou clique em "Project Site" para visualizar a [documentação](https://github.com/gaelcolas/Datum#2-getting-started--concepts).

## <a name="next-steps"></a>Próximas etapas

- [Visão Geral da Configuração de Estado Desejado do Windows PowerShell](/powershell/scripting/dsc/overview/overview)
- [Recursos dsc](/powershell/scripting/dsc/resources/resources)
- [Configuração do gerenciador de configuração local](/powershell/scripting/dsc/managing-nodes/metaconfig)
