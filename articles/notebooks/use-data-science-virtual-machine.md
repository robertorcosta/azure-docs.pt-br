---
title: Usar máquinas virtuais de ciência de dados do Azure
description: Saiba como se conectar a uma Máquina Virtual de Ciência de Dados do Azure (DSVM) para estender a capacidade de computação disponível para Azure Notebooks visualização.
author: getroyer
manager: andneil
ms.author: getroyer
ms.topic: how-to
ms.date: 06/13/2019
ms.openlocfilehash: 7d65a39c1a1d302e565b59db1644af2c08befb6b
ms.sourcegitcommit: 0a9df8ec14ab332d939b49f7b72dea217c8b3e1e
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/18/2020
ms.locfileid: "94843155"
---
# <a name="use-azure-data-science-virtual-machines"></a>Usar máquinas virtuais de ciência de dados do Azure

[!INCLUDE [notebooks-status](../../includes/notebooks-status.md)]

Por padrão, os projetos são executados na camada de **computação gratuita** , que é limitada a 4 GB de memória e 1 GB de dados para evitar abusos. Você pode ignorar essas limitações usando uma máquina virtual diferente da provisionada em uma assinatura do Azure. Para essa finalidade, a melhor opção é uma Máquina Virtual de Ciência de Dados do Azure (DSVM) usando a imagem do **máquina virtual de ciência de dados para Linux (Ubuntu)** . Tal DSVM vem pré-configurado com tudo o que você precisa para Azure Notebooks e aparece automaticamente na lista suspensa **executar** no Azure notebooks.

> [!Note]
> Só há suporte para Azure Notebooks no DSVMs criado com a imagem do Linux Ubuntu. Não há suporte para blocos de anotações em imagens do Windows 2012, do Windows 2016 ou do Linux CentOS.

## <a name="create-a-dsvm-instance"></a>Criar uma instância do DSVM

Para criar uma nova instância de DSVM, siga as instruções para [Criar uma VM de Ciência de Dados do Ubuntu](../machine-learning/data-science-virtual-machine/dsvm-ubuntu-intro.md). Para obter mais informações, incluindo detalhes de preços, consulte [máquinas virtuais de ciência de dados](https://azure.microsoft.com/services/virtual-machines/data-science-virtual-machines/).

## <a name="connect-to-the-dsvm"></a>Conectar-se ao DSVM

Quando você tiver criado o DSVM, selecione a lista suspensa **executar** no painel Azure notebooks projeto e selecione a instância DSVM apropriada. A lista suspensa mostra instâncias de DSVM se as seguintes condições forem verdadeiras:

- Você está conectado ao Azure Notebooks com uma conta que usa o AAD (Azure Active Directory), por exemplo, uma conta corporativa.
- Sua conta está conectada a uma assinatura do Azure.
- Você tem uma ou mais máquinas virtuais nessa assinatura, com pelo menos acesso de leitor, que usa a imagem do Máquina Virtual de Ciência de Dados para Linux (Ubuntu).)

![Instâncias de Máquina Virtual de Ciência de Dados na lista suspensa no painel do projeto](media/project-compute-tier-dsvm.png)

Quando você seleciona uma instância de DSVM, o Azure Notebooks pode solicitar as credenciais de máquina específicas que usou quando criou a VM.

> [!Important]
> O nome de usuário deve estar em minúsculas para usá-lo com JupyterHub.

Se qualquer uma das condições não for atendida, você ainda poderá se conectar ao DSVM. Na lista suspensa, selecione a opção de **computação direta** , que solicita um nome (para mostrar na lista), o endereço IP e a porta da VM (geralmente 8000, a porta padrão para a qual o JupyterHub escuta) e as credenciais da VM:

![Prompt que coleta informações do servidor para a opção de Computação Direta](media/project-compute-tier-direct.png)

Você obtém esses valores da página DSVM no portal do Azure.

## <a name="accessing-azure-notebooks-files-from-the-dsvm"></a>Acessando Azure Notebooks arquivos do DSVM

O acesso ao sistema de arquivos tem suporte para DSVM versões 19.06.15 ou posteriores. Para verificar a versão, primeiro conecte-se ao seu DSVM via SSH e execute o seguinte comando: `curl -H Metadata:true "http://169.254.169.254/metadata/instance?api-version=2018-10-01"` (você deve usar o endereço IP exato mostrado aqui). O número de versão é mostrado na saída de "Version".

Para preservar a paridade de caminhos de arquivo com a camada de **computação gratuita** , você pode abrir apenas um projeto por vez em um DSVM. Para abrir um novo projeto, você deve desligar primeiro o projeto aberto.

Quando um projeto é executado em uma VM, os arquivos são montados no diretório raiz do servidor Jupyter (o diretório mostrado em JupyterHub), substituindo os arquivos de Azure Notebooks padrão. Quando você desliga a VM usando o botão de **desligamento** na interface do usuário do notebook, Azure notebooks restaura os arquivos padrão.

![Botão de desligamento no Azure Notebooks](media/shutdown.png)

## <a name="create-new-dsvm-users"></a>Criar novos usuários do DSVM

Se vários usuários compartilharem um DSVM, você poderá evitar o bloqueio uns aos outros criando e usando um usuário do DSVM para cada usuário do notebook:

1. Na [portal do Azure](https://portal.azure.com), navegue até sua máquina virtual.
1. Em **suporte + solução de problemas** na margem esquerda, selecione **Redefinir senha**.
1. Insira um novo **nome de usuário**. O nome de usuário deve estar em minúsculas para usá-lo com JupyterHub. Digite uma senha. Em seguida, selecione **Atualizar**. (Nomes de já existentes não são afetados.)
1. Repita a etapa anterior para quaisquer usuários adicionais.

## <a name="next-steps"></a>Próximas etapas

Saiba mais sobre o DSVMs na [introdução às máquinas virtuais de ciência de dados do Azure](../machine-learning/data-science-virtual-machine/overview.md).