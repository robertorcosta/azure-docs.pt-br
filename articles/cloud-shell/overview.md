---
title: Visão Geral Azure Cloud Shell | Microsoft Docs
description: Visão geral do Azure Cloud Shell.
services: ''
documentationcenter: ''
author: maertendMSFT
manager: timlt
tags: azure-resource-manager
ms.assetid: ''
ms.service: azure
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 09/03/2019
ms.author: damaerte
ms.openlocfilehash: 513c3da8031774f5f111ee357b5a3c43e1d09d95
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/28/2020
ms.locfileid: "75832500"
---
# <a name="overview-of-azure-cloud-shell"></a>Visão geral do Azure Cloud Shell
O Azure Cloud Shell é um shell interativo, autenticado e acessível pelo navegador para o gerenciamento de recursos do Azure.
Ele dá a você a flexibilidade de escolher a experiência de shell que melhor se adequa ao modo como você trabalha, seja com o Bash ou o PowerShell.

Tente do shell.azure.com clicando abaixo.

[![Lançamento de inserção](https://shell.azure.com/images/launchcloudshell.png "Iniciar o Azure Cloud Shell")](https://shell.azure.com)

Tente no Portal do Azure usando o ícone do Cloud Shell.

![Inicialização do portal](media/overview/portal-launch-icon.png)

## <a name="features"></a>Recursos

### <a name="browser-based-shell-experience"></a>Experiência de shell baseada no navegador
O Cloud Shell permite o acesso a uma experiência de linha de comando baseada em navegador, que foi criada tendo em mente as tarefas de gerenciamento do Azure.
Utilize o Cloud Shell para trabalhar de forma independente de um computador local, de uma maneira que somente a nuvem pode proporcionar.

### <a name="choice-of-preferred-shell-experience"></a>Opção de experiência de shell preferida
Os usuários podem escolher entre o bash ou o PowerShell.
1. Selecione **Cloud Shell**.

    ![Ícone de Cloud Shell](media/overview/overview-cloudshell-icon.png)

2. Selecione **bash** ou **PowerShell**.

    ![Escolha o bash ou o PowerShell](media/overview/overview-choices.png)

### <a name="authenticated-and-configured-azure-workstation"></a>Estação de trabalho configurada e autenticada do Azure
O Cloud Shell é gerenciado pela Microsoft, portanto vem com ferramentas de linha de comando populares e suporte para idiomas. O Cloud Shell também autentica com segurança automaticamente para acesso instantâneo aos recursos por meio dos cmdlets da CLI do Azure ou Azure PowerShell.

Exiba toda a [lista das ferramentas instaladas no Cloud Shell.](features.md#tools)

### <a name="integrated-cloud-shell-editor"></a>Editor de Cloud Shell integrado
O Cloud Shell oferece um editor de texto gráfico integrado com base no Monaco Editor de software livre. Basta criar e editar arquivos de configuração executando `code .` para implantação perfeita por meio da CLI do Azure ou do Azure PowerShell.

[Saiba mais sobre o editor do Cloud Shell](using-cloud-shell-editor.md).

### <a name="integrated-with-docsmicrosoftcom"></a>Integrado a docs.microsoft.com

Você pode usar o Cloud Shell diretamente da documentação hospedada em [docs.microsoft.com](https://docs.microsoft.com). Ele está integrado ao [Microsoft Learn](https://docs.microsoft.com/learn/), ao [Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview) e à [documentação da CLI do Azure](https://docs.microsoft.com/cli/azure); clique no botão &quot;Experimentar&quot; em um snippet de código para abrir a experiência imersiva de shell. 

### <a name="multiple-access-points"></a>Vários pontos de acesso
O Cloud Shell é uma ferramenta flexível que pode ser usada a partir de:
* [portal.azure.com](https://portal.azure.com)
* [shell.azure.com](https://shell.azure.com)
* [Documentação da CLI do Azure](https://docs.microsoft.com/cli/azure)
* [Documentação do Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview)
* [Aplicativo móvel do Azure](https://azure.microsoft.com/features/azure-portal/mobile-app/)
* [Extensão da Conta do Azure para o Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=ms-vscode.azure-account)

### <a name="connect-your-microsoft-azure-files-storage"></a>Conectar seu armazenamento de arquivos do Microsoft Azure
Cloud Shell computadores são temporários, mas os arquivos são persistidos de duas maneiras: por meio de uma imagem de disco e por meio de `clouddrive`um compartilhamento de arquivos montado chamado.  Na primeira inicialização, o Cloud Shell solicita a criação de um grupo de recursos, uma conta de armazenamento e um compartilhamento de Arquivos do Azure em seu nome. Essa é uma etapa única e será anexada automaticamente para todas as sessões. Um compartilhamento de arquivo único pode ser mapeado e será usado pelo Bash e pelo PowerShell no Cloud Shell.

Leia mais para saber como montar uma [conta de armazenamento nova ou existente](persisting-shell-storage.md) ou para saber mais sobre os [mecanismos de persistência usados no Cloud Shell](persisting-shell-storage.md#how-cloud-shell-storage-works).

> [!NOTE]
> O Firewall do armazenamento do Azure não tem suporte para contas de armazenamento do Cloud Shell.

## <a name="concepts"></a>Conceitos
* O Cloud Shell é executado em um host temporário fornecido por sessão e por usuário
* O Cloud Shell atinge o tempo limite após 20 minutos sem atividade interativa
* O Cloud Shell exige que um compartilhamento de arquivos do Azure seja montado
* O Cloud Shell usa o mesmo compartilhamento de arquivos para o Bash e o PowerShell
* É atribuído ao Cloud Shell um computador por conta de usuário
* O Cloud Shell persiste o $HOME usando uma imagem de 5 GB mantida no compartilhamento de arquivos
* As permissões são definidas da mesma forma que para um usuário normal do Linux em Bash

Saiba mais sobre os recursos em [Bash no Cloud Shell](features.md) e [PowerShell no Cloud Shell](features-powershell.md).

## <a name="pricing"></a>Preços
O computador que hospeda o Cloud Shell é gratuito, com um pré-requisito de um compartilhamento de Arquivos do Azure montado. Custos de armazenamento regulares se aplicam.

## <a name="next-steps"></a>Próximas etapas
[Início rápido do Bash no Cloud Shell](quickstart.md) <br>
[Início rápido do PowerShell no Azure Cloud Shell](quickstart-powershell.md)
