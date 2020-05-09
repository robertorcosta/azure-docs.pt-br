---
title: Configurar um laboratório voltado para o aprendizado profundo usando o Azure Lab Services | Microsoft Docs
description: Saiba como configurar um laboratório para ensinar o script de Shell no Linux.
services: lab-services
documentationcenter: na
author: spelluru
manager: ''
editor: ''
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/30/2019
ms.author: spelluru
ms.openlocfilehash: 0dab5f3dcdfb6ddabbd94960dcf8a8bf2bce98af
ms.sourcegitcommit: b396c674aa8f66597fa2dd6d6ed200dd7f409915
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/07/2020
ms.locfileid: "82890766"
---
# <a name="set-up-a-lab-focused-on-deep-learning-in-natural-language-processing-using-azure-lab-services"></a>Configurar um laboratório voltado para o aprendizado profundo em processamento em idioma natural usando o Azure Lab Services
Este artigo mostra como configurar um laboratório voltado para o aprendizado profundo no NLP (processamento de idioma natural) usando Azure Lab Services. O NLP (processamento de idioma natural) é uma forma de IA (inteligência artificial) que habilita os computadores com tradução, reconhecimento de fala e outras funcionalidades de reconhecimento vocal.  

Os alunos que fazem uma aula de NLP obtêm uma VM (máquina virtual) do Linux para saber como aplicar algoritmos de rede neural para desenvolver modelos de aprendizado profundo que são usados para analisar a linguagem humana escrita. 

## <a name="lab-configuration"></a>Configuração do laboratório
Para configurar este laboratório, você precisa de uma assinatura do Azure para começar. Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/) antes de começar. Depois de ter uma assinatura do Azure, você pode criar uma nova conta de laboratório no Azure Lab Services ou usar uma conta de laboratório existente. Consulte o tutorial a seguir para criar uma nova conta de laboratório: [tutorial para configurar uma conta de laboratório](tutorial-setup-lab-account.md).
 
Depois de criar a conta de laboratório, habilite as seguintes configurações na conta do laboratório: 

| Configuração de conta do laboratório | Instruções |
| ----------- | ------------ |  
| Imagens do Marketplace | Habilite a imagem do Máquina Virtual de Ciência de Dados para Linux (Ubuntu) para uso em sua conta de laboratório.  Consulte o seguinte artigo para obter instruções: [especificar imagens do Marketplace disponíveis para criadores de laboratório](specify-marketplace-images.md). | 

Siga [este tutorial](tutorial-setup-classroom-lab.md) para criar um novo laboratório e aplicar as seguintes configurações:

| Configurações do laboratório | Valor/instruções | 
| ------------ | ------------------ |
| Tamanho da VM (máquina virtual) | GPU pequena (computação). Esse tamanho é mais adequado para aplicativos com uso intensivo de computação e rede, como inteligência artificial e aprendizado profundo. |
| Imagem da VM | [Máquina virtual de ciência de dados para Linux (Ubuntu)](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft-dsvm.ubuntu-1804). Esta imagem fornece estruturas e ferramentas de aprendizado profundo para aprendizado de máquina e ciência de dados. Para exibir a lista completa de ferramentas instaladas nesta imagem, consulte o seguinte artigo: [o que está incluído no DSVM?](../../machine-learning/data-science-virtual-machine/overview.md#whats-included-on-the-dsvm). |
| Habilitar conexão de área de trabalho remota | <p>Habilitar essa configuração permitirá que professores e alunos se conectem às suas máquinas virtuais (VM) usando o Área de Trabalho Remota (RDP).</p><p>**Importante**: a habilitação dessa configuração abre apenas a porta **RDP** em computadores Linux. Se o RDP já estiver instalado e configurado na imagem da máquina virtual, você/alunos poderão se conectar a VMs via RDP sem seguir nenhuma etapa adicional. <p>Se a imagem da VM não tiver o RDP instalado e configurado, você precisará se conectar ao computador Linux usando o SSH pela primeira vez e instalar os pacotes RDP e GUI para que você/alunos possam se conectar ao computador Linux usando o RDP mais tarde. Para obter mais informações, consulte [instalar e configurar o área de trabalho remota para se conectar a uma VM do Linux no Azure](../../virtual-machines/linux/use-remote-desktop.md). Em seguida, você publica a imagem para que os alunos possam RDP nas VMs do Linux do aluno.  |

O Máquina Virtual de Ciência de Dados para imagem do Linux fornece as estruturas e ferramentas de aprendizado profundo necessárias para esse tipo de classe. Como resultado, após a criação da máquina de modelo, você não precisa personalizá-la ainda mais. Ele pode ser publicado para que os alunos usem. Selecione o botão **publicar** na página modelo para publicar o modelo no laboratório.  

## <a name="cost"></a>Custo
Se você quiser estimar o custo deste laboratório, poderá usar o exemplo a seguir: 

Para uma classe de 25 alunos com 20 horas de tempo de classe agendada e 10 horas de cota para a casa ou as atribuições, o preço do laboratório seria-25 alunos * (20 + 10) horas * 139 unidades de laboratório * 0, 1 USD por hora = 1042,5 USD

Mais detalhes sobre preços, consulte [preços de Azure Lab Services](https://azure.microsoft.com/pricing/details/lab-services/).

## <a name="conclusion"></a>Conclusão
Este artigo descreveu as etapas para criar um laboratório para a classe de processamento de idioma natural. Você pode usar uma configuração semelhante para outras classes de aprendizado profundo.

## <a name="next-steps"></a>Próximas etapas
As próximas etapas são comuns à configuração de qualquer laboratório:

- [Adicionar usuários](tutorial-setup-classroom-lab.md#add-users-to-the-lab)
- [Definir cota](how-to-configure-student-usage.md#set-quotas-for-users)
- [Definir um agendamento](tutorial-setup-classroom-lab.md#set-a-schedule-for-the-lab) 
- [Links de registro de email para alunos](how-to-configure-student-usage.md#send-invitations-to-users). 

