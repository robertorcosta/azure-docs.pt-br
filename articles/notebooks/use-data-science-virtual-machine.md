---
title: Use máquinas virtuais de data science do Azure
description: Saiba como se conectar a uma Máquina Virtual de Data Science (DSVM) do Azure para estender o poder de computação disponível para o Azure Notebooks Preview.
author: getroyer
manager: andneil
ms.author: getroyer
ms.topic: how-to
ms.date: 06/13/2019
ms.openlocfilehash: b4da63b7b2a6da4316215b85a09ca7420745251c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "78898414"
---
# <a name="use-azure-data-science-virtual-machines"></a>Use máquinas virtuais de data science do Azure

Por padrão, os projetos são executados no nível **De computação livre,** que é limitado a 4 GB de memória e 1 GB de dados para evitar abusos. Você pode ignorar essas limitações usando uma máquina virtual diferente da provisionada em uma assinatura do Azure. Para isso, a melhor escolha é uma Máquina Virtual de Data Science (DSVM) usando a **imagem da Máquina Virtual de Data Science para Linux (Ubuntu).** Tal DSVM vem pré-configurado com tudo o que você precisa para notebooks Azure e aparece automaticamente na lista suspensa **run** em Notebooks Azure.

> [!Note]
> O Azure Notebooks é suportado apenas em DSVMs criados com a imagem on Linux Ubuntu. Os notebooks não são suportados em imagens do Windows 2012, Windows 2016 ou Linux CentOS.

[!INCLUDE [notebooks-status](../../includes/notebooks-status.md)]

## <a name="create-a-dsvm-instance"></a>Criar uma instância DSVM

Para criar uma nova instância de DSVM, siga as instruções para [Criar uma VM de Ciência de Dados do Ubuntu](/azure/machine-learning/data-science-virtual-machine/dsvm-ubuntu-intro). Para obter mais informações, incluindo detalhes de preços, consulte [Máquinas Virtuais de Data Science](https://azure.microsoft.com/services/virtual-machines/data-science-virtual-machines/).

## <a name="connect-to-the-dsvm"></a>Conecte-se ao DSVM

Depois de criar o DSVM, selecione a lista de parada **execute** no painel de projeto do Azure Notebooks e selecione a instância DSVM apropriada. A lista de parada baixa mostra as instâncias do DSVM se as seguintes condições forem verdadeiras:

- Você está conectado ao Azure Notebooks com uma conta que usa o AAD (Azure Active Directory), por exemplo, uma conta corporativa.
- Sua conta está conectada a uma assinatura do Azure.
- Você tem uma ou mais máquinas virtuais nessa assinatura, com pelo menos acesso ao Reader, que usa a imagem da Máquina Virtual de Data Science para Linux (Ubuntu).

![Instâncias de Máquina Virtual de Ciência de Dados na lista suspensa no painel do projeto](media/project-compute-tier-dsvm.png)

Quando você seleciona uma instância de DSVM, o Azure Notebooks pode solicitar as credenciais de máquina específicas que usou quando criou a VM.

> [!Important]
> O nome de usuário deve ser minúsculo para usá-lo com o JupyterHub.

Se alguma das condições não for atendida, você ainda pode se conectar ao DSVM. Na lista de paradas, selecione a opção **Computação direta,** que solicita um nome (para mostrar na lista), o endereço IP e a porta da VM (tipicamente 8000, a porta padrão à qual o JupyterHub ouve) e as credenciais de VM:

![Prompt que coleta informações do servidor para a opção de Computação Direta](media/project-compute-tier-direct.png)

Você obtém esses valores na página DSVM no portal Azure.

## <a name="accessing-azure-notebooks-files-from-the-dsvm"></a>Acessando arquivos do Azure Notebooks do DSVM

O acesso ao sistema de arquivos é suportado para as versões DSVM 19.06.15 ou posteriores. Para verificar a versão, primeiro conecte-se ao seu DSVM `curl -H Metadata:true "http://169.254.169.254/metadata/instance?api-version=2018-10-01"` via SSH e execute o seguinte comando: (você deve usar o endereço IP exato mostrado aqui). O número da versão é mostrado na saída para "versão".

Para preservar a paridade dos caminhos de arquivo com o nível **de Cálculo Livre,** você só pode abrir um projeto por vez em um DSVM. Para abrir um novo projeto, você deve encerrar o projeto aberto primeiro.

Quando um projeto é executado em uma VM, os arquivos são montados no diretório raiz do servidor Jupyter (o diretório mostrado no JupyterHub), substituindo os arquivos padrão do Azure Notebooks. Quando você desliga a VM usando o botão **Desligar** na ui do notebook, o Azure Notebooks restaura os arquivos padrão.

![Botão de desligamento em Notebooks Azure](media/shutdown.png)

## <a name="create-new-dsvm-users"></a>Crie novos usuários de DSVM

Se vários usuários compartilham um DSVM, você pode evitar bloquear uns aos outros criando e usando um usuário DSVM para cada usuário do notebook:

1. No [portal Azure,](https://portal.azure.com)navegue até sua máquina virtual.
1. Em **Suporte + solução de problemas** na margem esquerda, selecione **Redefinir senha**.
1. Digite um novo **nome de usuário**. O nome de usuário deve ser minúsculo para usá-lo com o JupyterHub. Digite uma senha. Em seguida, **selecione Atualizar**. (Os nomes de usuário existentes não são afetados.)
1. Repita a etapa anterior para quaisquer usuários adicionais.

## <a name="next-steps"></a>Próximas etapas

Saiba mais sobre DSVMs sobre [a introdução de máquinas virtuais de data science do Azure](/azure/machine-learning/data-science-virtual-machine/overview).
