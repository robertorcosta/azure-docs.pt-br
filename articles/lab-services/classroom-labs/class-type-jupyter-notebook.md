---
title: Montar um laboratório para ensinar ciência de dados com Notebooks Python e Jupyter | Microsoft Docs
description: Aprenda a montar um laboratório para ensinar ciência de dados usando Python e Jupyter Notebooks.
services: lab-services
documentationcenter: na
author: emaher
manager: ''
editor: ''
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/19/2019
ms.author: enewman
ms.openlocfilehash: dfb133f9aa3dd9b76f8b4ea4c6188cfaf9a67b75
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "77444104"
---
# <a name="set-up-a-lab-to-teach-data-science-with-python-and-jupyter-notebooks"></a>Criar um laboratório para ensinar ciência de dados com Python e Jupyter Notebooks

Este artigo descreve como configurar uma máquina de modelo supércal em Serviços de Laboratório com as ferramentas necessárias para ensinar os alunos a usar [notebooks Jupyter](http://jupyter-notebook.readthedocs.io).  O Jupyter Notebooks é um projeto de código aberto que permite combinar facilmente texto rico e código-fonte executável [Python](https://www.python.org/) em uma única tela chamada notebook.  A execução de um notebook resulta em um registro linear de entradas e saídas.  Essas saídas podem incluir texto, tabelas de informações, gráficos de dispersão e muito mais.

## <a name="lab-configuration"></a>Configuração do laboratório

Para configurar este laboratório, você precisa de uma assinatura do Azure e uma conta de laboratório para começar. Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/) antes de começar. Depois de obter uma assinatura do Azure, você pode criar uma nova conta de laboratório no Azure Lab Services. Para obter mais informações sobre a criação de uma nova conta de laboratório, consulte [o tutorial para configurar uma conta de laboratório](tutorial-setup-lab-account.md).  Você também pode usar uma conta de laboratório existente.

### <a name="lab-account-settings"></a>Configurações da conta de laboratório

Habilite as configurações descritas na tabela abaixo para a conta do laboratório. Para obter mais informações sobre como habilitar imagens de marketplace, consulte [especificar imagens do Marketplace disponíveis para criadores de laboratórios](specify-marketplace-images.md).

| Configuração da conta de laboratório | Instruções |
| ------------------- | ------------ |
| Imagem de mercado | Habilite a [imagem Data Science Virtual Machine - Windows 2016](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft-dsvm.dsvm-windows) para uso dentro de sua conta de laboratório. |

>[!TIP]
>Este artigo se concentrará na configuração de uma máquina de modelo que usa o sistema operacional Windows Server.  Também é possível configurar uma aula de ciência de dados com Python e Jupyter Notebooks usando [imagens de Data Science Virtual Machine for Linux (CentOS)](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft-ads.linux-data-science-vm) ou [Data Science Virtual Machine for Linux (Ubuntu)](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft-dsvm.linux-data-science-vm-ubuntu) do Azure Marketplace.

### <a name="lab-settings"></a>Configurações de laboratório

Use as configurações na tabela abaixo ao montar um laboratório de sala de aula.  Para obter mais informações sobre como criar um laboratório de sala de aula, consulte [configurar um tutorial de laboratório em sala de aula](tutorial-setup-classroom-lab.md).

| Configurações de laboratório | Valor/instruções |
| ------------ | ------------------ |
|Tamanho da Máquina Virtual| GPU pequena (Computação). Este tamanho é mais adequado para aplicações intensivas em computação e com uso intensivo de rede, como Inteligência Artificial e Deep Learning. |
|Imagem de máquina virtual| Máquina Virtual de Ciência de Dados - Windows 2016|

## <a name="template-machine"></a>Máquina de modelo

A [imagem Data Science Virtual Machine - Windows 2016](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft-dsvm.dsvm-windows) fornece as estruturas e ferramentas de aprendizagem profunda necessárias para este tipo de aula.  A imagem inclui Jdépito Notebooks e Visual Studio Code.  [Jupyter Notebooks](http://jupyter-notebook.readthedocs.io) é um aplicativo web que permite aos cientistas de dados pegar dados brutos, executar cálculos e ver os resultados todos no mesmo ambiente.  Para nossa máquina de modelos, o aplicativo web estará sendo executado localmente.  [Visual Studio Code](https://code.visualstudio.com/) é um IDE que proporciona uma rica experiência interativa ao escrever e testar um notebook.  Para obter mais informações, consulte [Working with Jupyter Notebooks in Visual Studio Code](https://code.visualstudio.com/docs/python/jupyter-support).

A tarefa restante para configurar a classe é fornecer cadernos locais.  Para obter instruções sobre como usar as amostras de Aprendizado de Máquina do Azure, veja [como configurar um ambiente com notebooks Jupyter](../../machine-learning/how-to-configure-environment.md#jupyter).  Você também pode fornecer seus próprios notebooks na máquina de modelos.  Os cadernos serão copiados para todas as máquinas estudantis quando o modelo for publicado.

## <a name="cost-estimate"></a>Estimativa de custos

Vamos cobrir uma possível estimativa de custos para esta classe.  Usaremos uma turma de 25 alunos.  Há 20 horas de aula programadas.  Além disso, cada aluno recebe 10 horas de cota para lição de casa ou tarefas fora do horário de aula programado.  O tamanho da máquina virtual que escolhemos foi a pequena GPU (computação), que é de 139 unidades de laboratório.

Aqui está um exemplo de uma possível estimativa de custos para esta classe:

25 \* alunos (20 horas programadas \* + 10 \* horas de cota) 139 unidades de laboratório 0,01 USD por hora = 1042,5 USD

Mais detalhes sobre preços, consulte [Preços de Serviços do Laboratório Azure](https://azure.microsoft.com/pricing/details/lab-services/).

## <a name="conclusion"></a>Conclusão

Neste artigo, nós caminhamos pelas etapas para criar um laboratório para uma aula de Cadernos Jupyter. Você pode usar uma configuração semelhante para outras aulas de aprendizado de máquina.

## <a name="next-steps"></a>Próximas etapas

Os próximos passos são comuns à criação de qualquer laboratório.

- [Criar e gerenciar um modelo](how-to-create-manage-template.md)
- [Adicionar usuários](tutorial-setup-classroom-lab.md#add-users-to-the-lab)
- [Definir cota](how-to-configure-student-usage.md#set-quotas-for-users)
- [Defina um cronograma](tutorial-setup-classroom-lab.md#set-a-schedule-for-the-lab)
- [Links de inscrição por e-mail para estudantes](how-to-configure-student-usage.md#send-invitations-to-users)
