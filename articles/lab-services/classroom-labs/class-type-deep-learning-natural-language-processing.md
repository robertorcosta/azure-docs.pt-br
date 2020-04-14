---
title: Montar um laboratório focado em deep learning usando o Azure Lab Services | Microsoft Docs
description: Aprenda a montar um laboratório para ensinar scripting shell no Linux.
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
ms.openlocfilehash: 889d0d1e98f5c9947588011774d02e54f05edca1
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/13/2020
ms.locfileid: "81257752"
---
# <a name="set-up-a-lab-focused-on-deep-learning-in-natural-language-processing-using-azure-lab-services"></a>Configurar um laboratório voltado para o aprendizado profundo em processamento em idioma natural usando o Azure Lab Services
Este artigo mostra como montar um laboratório focado em deep learning in natural language processing (NLP) usando o Azure Lab Services. O NLP (processamento de idioma natural) é uma forma de IA (inteligência artificial) que habilita os computadores com tradução, reconhecimento de fala e outras funcionalidades de reconhecimento vocal.  

Os alunos que fazem uma aula de NLP obtêm uma VM (máquina virtual) do Linux para saber como aplicar algoritmos de rede neural para desenvolver modelos de aprendizado profundo que são usados para analisar a linguagem humana escrita. 

## <a name="lab-configuration"></a>Configuração do laboratório
Para configurar este laboratório, você precisa de uma assinatura do Azure para começar. Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/) antes de começar. Depois de ter uma assinatura do Azure, você pode criar uma nova conta de laboratório no Azure Lab Services ou usar uma conta de laboratório existente. Veja o tutorial a seguir para criar uma nova conta de laboratório: [Tutorial to Setup a Lab Account](tutorial-setup-lab-account.md).
 
Depois de criar a conta de laboratório, habilite as seguintes configurações na conta do laboratório: 

| Configuração da conta de laboratório | Instruções |
| ----------- | ------------ |  
| Imagens de mercado | Habilite a imagem da Máquina Virtual de Ciência de Dados para Linux (Ubuntu) para uso dentro de sua conta de laboratório.  Consulte o artigo a seguir para obter instruções: [Especifique imagens de mercado disponíveis para os criadores de laboratório](specify-marketplace-images.md). | 

Siga [este tutorial](tutorial-setup-classroom-lab.md) para criar um novo laboratório e aplicar as seguintes configurações:

| Configurações de laboratório | Valor/instruções | 
| ------------ | ------------------ |
| Tamanho da máquina virtual (VM) | GPU pequena (Computação). Este tamanho é mais adequado para aplicações intensivas em computação e com uso intensivo de rede, como Inteligência Artificial e Deep Learning. |
| Imagem vm | [Máquina Virtual de Ciência de Dados para Linux (Ubuntu)](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft-dsvm.ubuntu-1804). Essa imagem fornece estruturas e ferramentas de aprendizagem profunda para aprendizado de máquina e ciência de dados. Para ver a lista completa de ferramentas instaladas nesta imagem, veja o seguinte artigo: [O que está incluído no DSVM?](../../machine-learning/data-science-virtual-machine/overview.md#whats-included-on-the-dsvm). |
| Habilite a conexão remota da área de trabalho | Habilitar. <p>A ativação dessa configuração permitirá que professores e alunos se conectem às suas Máquinas Virtuais (VM) usando RDP (Remote Desktop).</p><p>**Importante**: o RDP já está instalado e configurado na Máquina Virtual de Data Science para imagem Linux. Como resultado, professores/alunos podem se conectar a VMs via RDP sem quaisquer etapas adicionais. Além disso, se você precisar se conectar à área de trabalho gráfica, essa imagem já tem o [X2Go Server](https://wiki.x2go.org/doku.php/doc:newtox2go) instalado na máquina virtual. Os alunos devem instalar o cliente X2Go em suas máquinas locais e devem usar o cliente para conectar. Para obter mais informações, consulte os seguintes guias: <ul><li>[Como acessar uma Máquina Virtual de Ciência de Dados para Linux](../../machine-learning/data-science-virtual-machine/dsvm-ubuntu-intro.md#how-to-access-the-ubuntu-data-science-virtual-machine)</li><li>[Conecte-se ao modelo VM para instalar pacotes RDP e GUI](how-to-enable-remote-desktop-linux.md#connect-to-the-template-vm)</li></ul></p>   |

A Máquina Virtual de Data Science para imagem Linux fornece as estruturas e ferramentas de aprendizado profundo necessárias para este tipo de aula. Como resultado, após a criação da máquina de modelo, você não precisa personalizá-la mais. Pode ser publicado para os alunos usarem. Selecione o botão **Publicar** na página do modelo para publicar o modelo no laboratório.  

## <a name="cost"></a>Custo
Se você quiser estimar o custo deste laboratório, você pode usar o seguinte exemplo: 

Para uma turma de 25 alunos com 20 horas de aula programadas e 10 horas de cota para lição de casa ou tarefas, o preço para o laboratório seria - 25 alunos * (20 + 10) horas * 139 Unidades de Laboratório * 0,01 USD por hora = 1042,5 USD

Mais detalhes sobre preços, consulte [Preços de Serviços do Laboratório Azure](https://azure.microsoft.com/pricing/details/lab-services/).

## <a name="conclusion"></a>Conclusão
Este artigo te levou através das etapas para criar um laboratório para aulas de processamento de linguagem natural. Você pode usar uma configuração semelhante para outras aulas de aprendizagem profunda.

## <a name="next-steps"></a>Próximas etapas
Os próximos passos são comuns à criação de qualquer laboratório:

- [Adicionar usuários](tutorial-setup-classroom-lab.md#add-users-to-the-lab)
- [Definir cota](how-to-configure-student-usage.md#set-quotas-for-users)
- [Defina um cronograma](tutorial-setup-classroom-lab.md#set-a-schedule-for-the-lab) 
- [Links de inscrição por e-mail para estudantes](how-to-configure-student-usage.md#send-invitations-to-users). 

