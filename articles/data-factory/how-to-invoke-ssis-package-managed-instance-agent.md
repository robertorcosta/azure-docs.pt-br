---
title: Executar pacotes SSIS pelo azure SQL Managed Instance Agent
description: Saiba como executar pacotes SSIS pelo Azure SQL Managed Instance Agent.
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.author: lle
author: lle
ms.date: 04/14/2020
ms.openlocfilehash: b3b7a25149a9d075c81b30307ade2beb71907637
ms.sourcegitcommit: d6e4eebf663df8adf8efe07deabdc3586616d1e4
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81394716"
---
# <a name="execute-ssis-packages-by-azure-sql-managed-instance-agent"></a>Executar pacotes SSIS pelo azure SQL Managed Instance Agent
Este artigo descreve como executar um pacote SSIS (SSIS) sql server integration services usando o Azure SQL Managed Instance Agent. Esse recurso fornece comportamentos semelhantes, assim como quando você agenda pacotes SSIS pelo SQL Server Agent em seu ambiente on-prem.

Com esse recurso, você pode executar pacotes SSIS armazenados no SSISDB do Azure SQL Managed Instance ou Do Sistema de Arquivos, como arquivos Azure.

## <a name="prerequisites"></a>Pré-requisitos
Para usar esse recurso, baixe e instale a versão mais recente do SSMS, que é a versão 18.5 ou posterior. Baixe-o [neste site](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms?view=sql-server-2017).

E você precisa provisionar um Runtime de integração Azure-SSIS na Fábrica de Dados Azure, que usa a instância gerenciada do Azure SQL como servidor de ponto final. Se você ainda não provisionou, provisão-o seguindo instruções no [tutorial](tutorial-create-azure-ssis-runtime-portal.md). 

## <a name="run-ssis-packages-in-ssisdb-by-azure-sql-managed-instance-agent"></a>Executar pacotes SSIS no SSISDB pelo Azure SQL Managed Instance Agent
Nesta etapa, você usa o Azure SQL Managed Instance Agent para invocar pacotes SSIS armazenados no SSISDB na Instância Gerenciada do Azure SQL.
1. Na versão mais recente do SSMS, conecte-se à Instância Gerenciada do Azure SQL.
2. Crie um novo trabalho de agente e um novo passo de trabalho.

![Trabalho de novo agente](./media/how-to-invoke-ssis-package-managed-instance-agent/new-agent-job.png)

3. Na página Nova etapa **de trabalho,** escolha o tipo **de pacote de serviços de integração do servidor SQL.**

![Novo passo de trabalho do SSIS](./media/how-to-invoke-ssis-package-managed-instance-agent/new-ssis-job-step.png)

4. Na guia **Pacote,** escolha **O Catálogo SSIS** como tipo de origem do pacote.
5. Como o SSISDB está na mesma instância gerenciada do Azure SQL, você não precisa especificar autenticação.
6. Especifique um pacote SSIS do seu SSISDB.

![Tipo de origem do pacote - Catálogo SSIS](./media/how-to-invoke-ssis-package-managed-instance-agent/package-source-ssisdb.png)

7. Na guia **Configurações,** você pode especificar valores **de parâmetros,** substituir valores em **Gerentes de conexão,** substituir **propriedade** e escolher **nível de registro**.

![Tipo de origem do pacote - Configuração do catálogo SSIS](./media/how-to-invoke-ssis-package-managed-instance-agent/package-source-ssisdb-configuration.png)

8. Depois de terminar toda a configuração acima, clique em **OK** para salvar a configuração Agent Job.
9. Inicie o Trabalho de Agente para executar o pacote SSIS.


## <a name="run-ssis-packages-in-file-system-by-azure-sql-managed-instance-agent"></a>Executar pacotes SSIS no sistema de arquivos pelo agente de instância gerenciada Azure SQL
Nesta etapa, você usa o Azure SQL Managed Instance Agent para invocar pacotes SSIS armazenados no Sistema de Arquivos para serem executados.
1. Na versão mais recente do SSMS, conecte-se à Instância Gerenciada do Azure SQL.
2. Crie um novo trabalho de agente e um novo passo de trabalho.

   ![Trabalho de novo agente](./media/how-to-invoke-ssis-package-managed-instance-agent/new-agent-job.png)

3. Na página Nova etapa **de trabalho,** escolha o tipo **de pacote de serviços de integração do servidor SQL.**

   ![Novo passo de trabalho do SSIS](./media/how-to-invoke-ssis-package-managed-instance-agent/new-ssis-job-step.png)

4. Na guia **Pacote,** escolha **o sistema Arquivo** como tipo de origem do pacote.

   ![Tipo de origem do pacote - Sistema de arquivos](./media/how-to-invoke-ssis-package-managed-instance-agent/package-source-file-system.png)

   1. Se o pacote for carregado no Azure File, escolha **o compartilhamento de arquivos Do Zure** como tipo de origem do arquivo.
      - O caminho do pacote é ** \\ <storage account name>o nome do compartilhamento de arquivo .file.core.windows.net\<>\<nome do pacote>.dtsx**
      - Digite o nome da conta do arquivo Azure e a chave da conta na **credencial de acesso ao arquivo do pacote** para acessar o arquivo Azure. O domínio é definido como **Azure**.
   2. Se o pacote for carregado para um compartilhamento de rede, escolha **o compartilhamento de rede** como tipo de origem do arquivo.
      - O caminho do pacote é o **caminho UNC** do arquivo do pacote com sua extensão dtsx.
      - Digite **o domínio**correspondente, **nome de usuário**e **senha** para acessar o arquivo do pacote de compartilhamento de rede.
   3. Se o arquivo do pacote estiver criptografado com senha, selecione **Criptografia senha** e digite a senha.

 5. Na guia **Configurações,** digite o caminho do **arquivo de configuração** se precisar de um arquivo de configuração para executar o pacote SSIS.
 6. Na guia **Opções de execução,** você pode escolher se deve usar a **autenticação do windows** ou **o tempo de execução de 32 bits** para executar o pacote SSIS.
 7. Na guia **Registro,** você pode escolher o caminho de **registro** e a credencial de acesso de registro correspondente para armazenar os arquivos de log. Por padrão, o caminho de registro será o mesmo que o caminho da pasta do pacote e a credencial de acesso ao registro será a mesma que a credencial de acesso ao pacote.
 8. Na guia **Definir valores,** você pode digitar o Caminho e **o Valor** da **propriedade** para substituir as propriedades do pacote.
 Por exemplo, para substituir o valor da variável de usuário, insira seu caminho no seguinte formato: **\Pacote.Variáveis[Usuário::<variable name>]. Valor**.
 9. Depois de terminar toda a configuração acima, clique em **OK** para salvar a configuração Agent Job.
 10. Inicie o Trabalho de Agente para executar o pacote SSIS.


 ## <a name="cancel-ssis-package-execution"></a>Cancelar a execução do pacote SSIS
 Para cancelar a execução do pacote de um trabalho de Agente Gerenciado pelo Azure SQL, você deve seguir as etapas abaixo, em vez de parar diretamente o trabalho do agente.
 1. Encontre seu agente SQL **jobId** do **msdb.dbo.sysjobs**.
 2. Encontre o SSIS **executionId** correspondente com base no ID de trabalho pela consulta abaixo:
    ```sql
    select * from ssisdb.internal.execution_parameter_values_noncatalog where  parameter_value = 'SQL_Agent_Job_{jobId}' order by execution_id desc
    ```
 3. Selecione **Operações Ativas** no catálogo SSIS.

    ![Tipo de origem do pacote - Sistema de arquivos](./media/how-to-invoke-ssis-package-managed-instance-agent/catalog-active-operations.png)

 4. Parar a operação correspondente com base em **executionId**.

## <a name="next-steps"></a>Próximas etapas
 Você também pode agendar pacotes SSIS usando a Fábrica de Dados do Azure. Para obter instruções passo a passo, consulte [Azure Data Factory Event Trigger](how-to-create-event-trigger.md). 