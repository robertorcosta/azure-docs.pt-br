---
title: Gerenciar políticas de conta de Azure Data Lake Analytics
description: Saiba como usar políticas de conta para controlar o uso de uma conta de Data Lake Analytics, como o máximo de AUs e máximo de trabalhos.
services: data-lake-analytics
ms.service: data-lake-analytics
ms.reviewer: jasonh
ms.assetid: 0a6102d1-7554-4df2-b487-4dae9a7287b6
ms.topic: how-to
ms.date: 04/30/2018
ms.openlocfilehash: cf64424d1d422e599585b76fc068c940f9311b05
ms.sourcegitcommit: 0e8a4671aa3f5a9a54231fea48bcfb432a1e528c
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/24/2020
ms.locfileid: "87127699"
---
# <a name="manage-azure-data-lake-analytics-using-account-policies"></a>Gerenciar Azure Data Lake Analytics usando políticas de conta

As políticas de conta ajudam a controlar como os recursos de uma conta de Azure Data Lake Analytics são usados. Essas políticas permitem que você controle o custo do uso do Azure Data Lake Analytics. Por exemplo, com essas políticas, você pode evitar picos de custo inesperados limitando o número de AUs que a conta pode usar simultaneamente. # # políticas de nível de conta

Essas políticas se aplicam a todos os trabalhos em uma conta de Data Lake Analytics. # # # número máximo de AUs em uma conta de Data Lake Analytics uma política controla o número total de unidades de análise (AUs) que sua conta de Data Lake Analytics pode usar. Por padrão, o valor é definido como 250. Por exemplo, se esse valor for definido como 250 AUs, você poderá ter um trabalho em execução com 250 AUs atribuídas ou 10 trabalhos em execução com 25 AUs cada um. Os trabalhos adicionais enviados são enfileirados até que os trabalhos em execução sejam concluídos. Quando os trabalhos em execução são concluídos, as AUs são liberadas para que os trabalhos na fila sejam executados.

Para alterar o número de AUs da sua conta do Data Lake Analytics:

1. No portal do Azure, acesse sua conta do Data Lake Analytics.
2. Clique em **limites e políticas**.
3. Em **Máximo de AUs**, mova o controle deslizante para selecionar um valor ou insira o valor na caixa de texto. 
4. Clique em **Save** (Salvar).

   > [!NOTE]
   > Se você precisar de mais do que as AUs padrão (250), no portal, clique em **Ajuda + Suporte** para enviar uma solicitação de suporte. O número de AUs disponíveis em sua conta do Data Lake Analytics pode ser aumentado.

### <a name="maximum-number-of-jobs-that-can-run-simultaneously"></a>Número máximo de trabalhos que podem ser executados simultaneamente
Essa política limita o número de trabalhos que podem ser executados simultaneamente. Por padrão, esse valor é definido como 20. Se o Data Lake Analytics tiver AUs disponíveis, novos trabalhos serão agendados para execução imediata até que o número total de trabalhos em execução atinja o valor dessa política. Quando você atinge o número máximo de trabalhos que podem ser executados simultaneamente, os trabalhos subsequentes são enfileirados em ordem de prioridade até que um ou mais trabalhos em execução sejam concluídos (dependendo da AUs disponível).

Para alterar o número de trabalhos que podem ser executadas simultaneamente:

1. No portal do Azure, acesse sua conta do Data Lake Analytics.
2. Clique em **limites e políticas**.
3. Em **Número Máximo de Trabalhos em Execução**, mova o controle deslizante para selecionar um valor ou insira o valor na caixa de texto. 
4. Clique em **Save** (Salvar).

   > [!NOTE]
   > Se você precisar executar mais do que o número de trabalhos padrão (20) no portal, clique em **Ajuda + Suporte** para enviar uma solicitação de suporte. O número de trabalhos que podem ser executados simultaneamente em sua conta do Data Lake Analytics pode ser aumentado.

### <a name="how-long-to-keep-job-metadata-and-resources"></a>Por quanto tempo os metadados e recursos de trabalho devem ser mantidos 
Quando os usuários executam trabalhos do U-SQL, o serviço de Data Lake Analytics mantém todos os arquivos relacionados. Esses arquivos incluem o script U-SQL, os arquivos DLL referenciados no script U-SQL, recursos compilados e estatísticas. Os arquivos estão na pasta /system/ da conta de armazenamento padrão do Azure Data Lake. Esta política controla por quanto tempo esses recursos serão armazenados antes de serem excluídos automaticamente (o padrão é 30 dias). Você pode usar esses arquivos para depuração e ajuste de desempenho de trabalhos que serão executados novamente no futuro.

Para alterar o tempo para reter os recursos e metadados de trabalho:

1. No portal do Azure, acesse sua conta do Data Lake Analytics.
2. Clique em **limites e políticas**.
3. Em **Dias para Reter Consultas de Trabalho**, mova o controle deslizante para selecionar um valor ou insira o valor na caixa de texto.  
4. Clique em **Save** (Salvar).

## <a name="job-level-policies"></a>Políticas no nível do trabalho

As políticas de nível de trabalho permitem que você controle o máximo de AUs e a prioridade máxima que os usuários individuais (ou membros de grupos de segurança específicos) podem definir em trabalhos que eles enviam. Essa política lhe permite controlar os custos gerados pelos usuários. Ele também permite que você controle o efeito que os trabalhos agendados podem ter nos trabalhos de produção de alta prioridade que estão em execução na mesma conta do Data Lake Analytics.

Data Lake Analytics tem duas políticas que podem ser definidas no nível do trabalho: * **limite de au por trabalho**: os usuários só podem enviar trabalhos que tenham até esse número de AUS. Por padrão, esse limite é o mesmo que o limite máximo de AUs da conta.
* **Prioridade**: os usuários apenas podem enviar trabalhos com prioridade menor ou igual a esse valor. Um número maior indica uma prioridade mais baixa. Por padrão, esse limite é definido como 1, que é a prioridade mais alta possível.

Há uma política padrão definida em cada conta. A política padrão aplica-se a todos os usuários da conta. Você pode criar políticas adicionais para usuários e grupos específicos. 

> [!NOTE]
> As políticas no nível da conta e no nível do trabalho aplicam-se ao mesmo tempo.

### <a name="add-a-policy-for-a-specific-user-or-group"></a>Adicionar uma política para um usuário ou grupo específico

1. No portal do Azure, acesse sua conta do Data Lake Analytics.
2. Clique em **limites e políticas**.
3. Em **Limites de Envio de Trabalho**, clique no botão **Adicionar Política**. Em seguida, selecione ou insira as seguintes configurações:
    1. **Nome da Política de Computação**: insira um nome de política para lembrá-lo da finalidade da política.
    2. **Selecionar Usuário ou Grupo**: selecione o usuário ou o grupo ao qual essa política se aplica.
    3. **Definir o Limite de AUs de Trabalho**: defina o limite de AUs que se aplica ao usuário ou ao grupo selecionado.
    4. **Definir o Limite de Prioridade**: defina o limite de prioridade que se aplica ao usuário ou ao grupo selecionado.

4. Clique em **OK**.

5. A nova política é listada na tabela de política **Padrão**, em **Limites de Envio de Trabalho**. # # # Excluir ou editar uma política existente

1. No portal do Azure, acesse sua conta do Data Lake Analytics.
2. Clique em **limites e políticas**.
3. Em **Limites de Envio de Trabalho**, localize a política que deseja editar.
4.  Para ver as opções **excluir** e **Editar** , na coluna mais à direita da tabela, clique em `...` . # # recursos adicionais para políticas de trabalho
* [Postagem no blog de visão geral de política](https://blogs.msdn.microsoft.com/azuredatalake/2017/06/08/managing-your-azure-data-lake-analytics-compute-resources-overview/)
* [Postagem no blog de políticas no nível da conta](https://blogs.msdn.microsoft.com/azuredatalake/2017/06/08/managing-your-azure-data-lake-analytics-compute-resources-account-level-policy/)
* [Postagem no blog de políticas de nível de trabalho](https://blogs.msdn.microsoft.com/azuredatalake/2017/06/08/managing-your-azure-data-lake-analytics-compute-resources-job-level-policy/)# # próximas etapas

* [Visão geral do Azure Data Lake Analytics](data-lake-analytics-overview.md)
* [Introdução ao Data Lake Analytics usando o portal do Azure](data-lake-analytics-get-started-portal.md)
* [Gerenciar o Azure Data Lake Analytics usando o Azure PowerShell](data-lake-analytics-manage-use-powershell.md)