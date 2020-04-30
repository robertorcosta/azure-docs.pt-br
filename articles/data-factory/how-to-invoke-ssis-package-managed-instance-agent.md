---
title: Executar pacotes do SSIS usando o agente de Instância Gerenciada do Banco de Dados SQL do Azure
description: Saiba como executar pacotes do SSIS usando o Instância Gerenciada do Banco de Dados SQL do Azure Agent.
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.author: lle
author: lle
ms.date: 04/14/2020
ms.openlocfilehash: fcbfeb5ab3a3a80fdb8f7e355f290451d4afe804
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/28/2020
ms.locfileid: "82144794"
---
# <a name="run-ssis-packages-by-using-azure-sql-database-managed-instance-agent"></a>Executar pacotes do SSIS usando o agente de Instância Gerenciada do Banco de Dados SQL do Azure
Este artigo descreve como executar um pacote SQL Server Integration Services (SSIS) usando o agente Instância Gerenciada do Banco de Dados SQL do Azure. Esse recurso fornece comportamentos semelhantes a quando você agenda pacotes do SSIS usando SQL Server Agent em seu ambiente local.

Com esse recurso, você pode executar pacotes do SSIS armazenados no SSISDB em uma instância gerenciada do banco de dados SQL do Azure ou um sistema de arquivos como arquivos do Azure.

## <a name="prerequisites"></a>Pré-requisitos
Para usar esse recurso, [Baixe](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms?view=sql-server-2017) e instale a versão mais recente do SQL Server Management Studio (SSMS), que é a versão 18,5.

Você também precisa [provisionar um tempo de execução de integração do Azure-SSIS](tutorial-create-azure-ssis-runtime-portal.md) no Azure data Factory. Ele usa uma instância gerenciada do banco de dados SQL do Azure como um servidor de ponto de extremidade. 

## <a name="run-an-ssis-package-in-ssisdb"></a>Executar um pacote do SSIS no SSISDB
Neste procedimento, você usa Instância Gerenciada do Banco de Dados SQL do Azure Agent para invocar um pacote do SSIS que é armazenado no SSISDB.

1. Na versão mais recente do SSMS, conecte-se a uma instância gerenciada do banco de dados SQL do Azure.
1. Crie um novo trabalho do Agent e uma nova etapa de trabalho. Em **SQL Server Agent**, clique com o botão direito do mouse na pasta **trabalhos** e selecione **novo trabalho**.

   ![Seleções para criar um novo trabalho do agente](./media/how-to-invoke-ssis-package-managed-instance-agent/new-agent-job.png)

1. Na página **nova etapa de trabalho** , selecione **SQL Server Integration Services pacote** como o tipo.

   ![Seleções para criar uma nova etapa de trabalho do SSIS](./media/how-to-invoke-ssis-package-managed-instance-agent/new-ssis-job-step.png)

1. Na guia **pacote** , selecione **catálogo do SSIS** como o tipo de origem do pacote.
1. Como o SSISDB está em uma instância gerenciada do banco de dados SQL do Azure, você não precisa especificar a autenticação.
1. Especifique um pacote do SSIS do SSISDB.

   ![Guia pacote com seleções para o tipo de origem do pacote](./media/how-to-invoke-ssis-package-managed-instance-agent/package-source-ssisdb.png)

1. Na guia **configuração** , você pode:
  
   - Especifique valores de parâmetro em **parâmetros**.
   - Substitua os valores em **gerenciadores de conexões**.
   - Substitua a propriedade e escolha o nível de log em **avançado**.

   ![Guia de configuração com seleções para o tipo de origem do pacote](./media/how-to-invoke-ssis-package-managed-instance-agent/package-source-ssisdb-configuration.png)

1. Selecione **OK** para salvar a configuração de trabalho do agente.
1. Inicie o trabalho do Agent para executar o pacote do SSIS.


## <a name="run-an-ssis-package-in-the-file-system"></a>Executar um pacote do SSIS no sistema de arquivos
Neste procedimento, você usa Instância Gerenciada do Banco de Dados SQL do Azure Agent para executar um pacote do SSIS que é armazenado no sistema de arquivos.

1. Na versão mais recente do SSMS, conecte-se a uma instância gerenciada do banco de dados SQL do Azure.
1. Crie um novo trabalho do Agent e uma nova etapa de trabalho. Em **SQL Server Agent**, clique com o botão direito do mouse na pasta **trabalhos** e selecione **novo trabalho**.

   ![Seleções para criar um novo trabalho do agente](./media/how-to-invoke-ssis-package-managed-instance-agent/new-agent-job.png)

1. Na página **nova etapa de trabalho** , selecione **SQL Server Integration Services pacote** como o tipo.

   ![Seleções para criar uma nova etapa de trabalho do SSIS](./media/how-to-invoke-ssis-package-managed-instance-agent/new-ssis-job-step.png)

1. Na guia **pacote** :

   1. Para **origem do pacote**, selecione **sistema de arquivos**.
   
   1. Para o **tipo de origem do arquivo**:   

      - Se o pacote for carregado nos arquivos do Azure, selecione **compartilhamento de arquivos do Azure**.

        ![Opções para tipo de origem de arquivo](./media/how-to-invoke-ssis-package-managed-instance-agent/package-source-file-system.png)
      
        O caminho do pacote é ** \\ <storage account name>.\<File.Core.Windows.net nome do compartilhamento \<de arquivos>nome do pacote>. dtsx**.
      
        Em **pacote de credenciais de acesso ao arquivo**, insira o nome da conta do arquivo do Azure e a chave de conta para acessar o arquivo do Azure. O domínio é definido como **Azure**.

      - Se o pacote for carregado em um compartilhamento de rede, selecione **compartilhamento de rede**.
      
        O caminho do pacote é o caminho UNC do arquivo de pacote com sua extensão. dtsx.
      
        Insira o domínio, o nome de usuário e a senha correspondentes para acessar o arquivo do pacote de compartilhamento de rede.
   1. Se o arquivo de pacote for criptografado com uma senha, selecione **senha de criptografia** e insira a senha.
1. Na guia **configurações** , insira o caminho do arquivo de configuração se você precisar de um arquivo de configuração para executar o pacote do SSIS.
1. Na guia **Opções de execução** , você pode escolher se deseja usar a **autenticação do Windows** ou o **tempo de execução de 32 bits** para executar o pacote SSIS.
1. Na guia **log** , você pode escolher o caminho de log e a credencial de acesso de log correspondente para armazenar os arquivos de log. Por padrão, o caminho de log é o mesmo que o caminho da pasta do pacote, e a credencial de acesso ao log é a mesma que a credencial de acesso ao pacote.
1. Na guia **definir valores** , você pode inserir o valor e o caminho da propriedade para substituir as propriedades do pacote.
 
   Por exemplo, para substituir o valor de sua variável de usuário, insira seu caminho no seguinte formato: **\package.Variables [User::<variable name>]. Valor**.
1. Selecione **OK** para salvar a configuração de trabalho do agente.
1. Inicie o trabalho do Agent para executar o pacote do SSIS.


## <a name="cancel-ssis-package-execution"></a>Cancelar a execução do pacote SSIS
Para cancelar a execução do pacote de um trabalho do Instância Gerenciada do Banco de Dados SQL do Azure Agent, execute as seguintes etapas em vez de parar diretamente o trabalho do agente:

1. Localize o **JobID** do SQL Agent em **msdb. dbo. sysjobs**.
1. Localize a **execução** do SSIS correspondente com base na ID do trabalho, usando esta consulta:
   ```sql
   select * from ssisdb.internal.execution_parameter_values_noncatalog where  parameter_value = 'SQL_Agent_Job_{jobId}' order by execution_id desc
   ```
1. Clique com o botão direito do mouse no catálogo do SSISDB e selecione **operações ativas**.

   !["Operações ativas" no menu de atalho para o catálogo do SSISDB](./media/how-to-invoke-ssis-package-managed-instance-agent/catalog-active-operations.png)

1. Pare a operação correspondente com base na **ExecutionID**.

## <a name="next-steps"></a>Próximas etapas
Você também pode agendar pacotes SSIS usando Azure Data Factory. Para obter instruções detalhadas, consulte [Azure data Factory gatilho de evento](how-to-create-event-trigger.md). 