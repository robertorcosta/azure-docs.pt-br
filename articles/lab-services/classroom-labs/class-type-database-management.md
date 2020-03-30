---
title: Criar um laboratório para ensinar gerenciamento de banco de dados para bancos de dados relacionais | Microsoft Docs
description: Aprenda a montar um laboratório para ensinar a gestão de bases de dados relacionais.
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
ms.openlocfilehash: 4c375487b30595251753021033c98cf0ca1e8dd7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "77469911"
---
# <a name="set-up-a-lab-to-teach-database-management-for-relational-databases"></a>Criar um laboratório para ensinar gerenciamento de banco de dados para bancos de dados relacionais

Este artigo descreve como criar um laboratório para uma aula básica de gerenciamento de bancos de dados no Azure Lab Services. Os conceitos de bancos de dados são um dos cursos introdutórios ensinados na maioria dos departamentos de ciência da computação na faculdade. O Structured Query Language (SQL) é um padrão internacional. SQL é a linguagem padrão para gerenciamento de banco de dados de relacionamento, incluindo adicionar, acessar e gerenciar conteúdo em um banco de dados.  É mais conhecido por seu processamento rápido, confiabilidade comprovada, facilidade e flexibilidade de uso.

Neste artigo, mostraremos como configurar um modelo de máquina virtual em um laboratório com servidor MySQL Database Server e SQL Server 2019.  [MySQL](https://www.mysql.com/) é um RdBMS (Serviço de Gerenciamento de Banco de Dados Relacional) de código aberto disponível gratuitamente.  [SQL Server 2019](https://www.microsoft.com/sql-server/sql-server-2019) é a versão mais recente do RDBMS da Microsoft.

## <a name="lab-configuration"></a>Configuração do laboratório

Para configurar este laboratório, você precisa de uma assinatura do Azure e uma conta de laboratório para começar. Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/) antes de começar. Depois de obter uma assinatura do Azure, você pode criar uma nova conta de laboratório no Azure Lab Services. Para obter mais informações sobre a criação de uma nova conta de laboratório, consulte [Tutorial to Setup a Lab Account](tutorial-setup-lab-account.md).  Você também pode usar uma conta de laboratório existente.

### <a name="lab-account-settings"></a>Configurações da conta de laboratório

Habilite as configurações descritas na tabela abaixo para a conta do laboratório. Para obter mais informações sobre como habilitar imagens de marketplace, consulte [Especificar imagens do Marketplace disponíveis para criadores de laboratórios](https://docs.microsoft.com/azure/lab-services/classroom-labs/specify-marketplace-images).

| Configuração da conta de laboratório | Instruções |
| ------------------- | ------------ |
|Imagem de mercado| Habilite a imagem 'SQL Server 2019 Standard on Windows Server 2019' para uso dentro de sua conta de laboratório.|

### <a name="lab-settings"></a>Configurações de laboratório

Use as configurações na tabela abaixo ao montar um laboratório de sala de aula.  Para obter mais informações sobre como criar um laboratório de sala de aula, consulte [configurar um tutorial de laboratório em sala de aula](tutorial-setup-classroom-lab.md).

| Configurações de laboratório | Valor/instruções |
| ------------ | ------------------ |
|Tamanho da Máquina Virtual| Média. Este tamanho é mais adequado para bancos de dados relacionais, cache na memória e análises.|
|Imagem de máquina virtual| Padrão SQL Server 2019 no Windows Server 2019|

## <a name="template-machine-configuration"></a>Configuração da máquina de modelo

Para instalar o MySQL no Windows Server 2019, você pode seguir as etapas mencionadas no [Install and Run MySQL Community Server em uma máquina virtual](https://docs.microsoft.com/previous-versions/azure/virtual-machines/windows/classic/mysql-2008r2?toc=%2Fazure%2Fvirtual-machines%2Fwindows%2Fclassic%2Ftoc.json#install-and-run-mysql-community-server-on-the-virtual-machine).

O SQL Server 2019 está pré-instalado na imagem da máquina virtual que escolhemos ao criar o novo laboratório.

## <a name="cost-estimate"></a>Estimativa de custos

Vamos cobrir uma possível estimativa de custos para esta classe.  Usaremos uma turma de 25 alunos.  Há 20 horas de aula programadas.  Além disso, cada aluno recebe 10 horas de cota para lição de casa ou tarefas fora do horário de aula programado.  O tamanho da máquina virtual que escolhemos era médio, que é de 42 unidades de laboratório.

Aqui está um exemplo de uma possível estimativa de custos para esta classe:

25 \* estudantes (20 horas programadas \* + 10 horas de cota) 0,42 USD por hora = 315,00 USD

Mais detalhes sobre preços, consulte [Preços de Serviços do Laboratório Azure](https://azure.microsoft.com/pricing/details/lab-services/).

## <a name="conclusion"></a>Conclusão

Este artigo caminhou você através das etapas necessárias para criar um laboratório para conceitos básicos de gerenciamento de banco de dados usando tanto o MySQL quanto o SQL Server. Você pode usar uma configuração semelhante para outras classes de banco de dados.

## <a name="next-steps"></a>Próximas etapas

Os próximos passos são comuns à criação de qualquer laboratório.

- [Criar e gerenciar um modelo](how-to-create-manage-template.md)
- [Adicionar usuários](tutorial-setup-classroom-lab.md#add-users-to-the-lab)
- [Definir cota](how-to-configure-student-usage.md#set-quotas-for-users)
- [Defina um cronograma](tutorial-setup-classroom-lab.md#set-a-schedule-for-the-lab)
- [Links de inscrição por e-mail para estudantes](how-to-configure-student-usage.md#send-invitations-to-users)
