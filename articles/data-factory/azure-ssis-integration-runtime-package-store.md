---
title: Gerenciar pacotes com o repositório de pacotes Azure-SSIS Integration Runtime
description: Saiba como gerenciar pacotes com Azure-SSIS Integration Runtime repositório de pacotes.
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
author: swinarko
ms.author: sawinark
ms.reviewer: douglasl
manager: mflasko
ms.custom: seo-lt-2019
ms.date: 05/25/2020
ms.openlocfilehash: fa1ab60ae5690bd3782e4c849ca7cb5c29d640c2
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: pt-BR
ms.lasthandoff: 07/02/2020
ms.locfileid: "84198863"
---
# <a name="manage-packages-with-azure-ssis-integration-runtime-package-store"></a>Gerenciar pacotes com o repositório de pacotes Azure-SSIS Integration Runtime

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Para aumentar & mudar suas cargas de trabalho do SSIS (SQL Server Integration Services local) para a nuvem, você pode provisionar Azure-SSIS Integration Runtime (IR) no Azure Data Factory (ADF). Para obter mais informações, consulte [provisionar um Azure-SSIS ir](https://docs.microsoft.com/azure/data-factory/tutorial-deploy-ssis-packages-azure). Um Azure-SSIS IR dá suporte a:

- Execução de pacotes implantados no catálogo do SSIS (SSISDB) hospedado por uma Instância Gerenciada/servidor do Banco de Dados SQL do Azure (modelo de implantação de projeto)
- Execução de pacotes implantados no sistema de arquivos, nos Arquivos do Azure ou no banco de dados do SQL Server (MSDB) hospedado pela Instância Gerenciada de SQL do Azure (modelo de implantação de pacote)

Ao usar o modelo de implantação de pacote, você pode escolher se deseja provisionar seu Azure-SSIS IR com repositórios de pacotes que fornecem uma camada de gerenciamento de pacotes sobre o sistema de arquivos/arquivos do Azure/MSDB hospedado pelo SQL Instância Gerenciada do Azure. Azure-SSIS IR repositório de pacotes permite que você importe/exporte/exclua/execute pacotes e monitore/interrompa a execução de pacotes via SQL Server Management Studio (SSMS) semelhante ao [repositório de pacotes SSIS herdado](https://docs.microsoft.com/sql/integration-services/service/package-management-ssis-service?view=sql-server-2017). 

## <a name="connect-to-azure-ssis-ir"></a>Conectar-se ao Azure-SSIS IR

Quando o Azure-SSIS IR for provisionado, você poderá se conectar a ele para procurar seus armazenamentos de pacotes no SSMS.

![Conectar-se ao Azure-SSIS IR](media/azure-ssis-integration-runtime-package-store/ssms-package-store-connect.png)

Na janela pesquisador de **objetos** do SSMS, selecione **Azure-SSIS Integration Runtime** no menu suspenso **conectar** . Em seguida, entre no Azure e selecione a assinatura, o ADF e o Azure-SSIS IR relevantes aos quais os armazenamentos de pacotes estão anexados. Seu Azure-SSIS IR será exibido com os nós **executando pacotes** e **pacotes armazenados** abaixo. Expanda o nó **pacotes armazenados** para ver os armazenamentos de pacotes abaixo. Expanda os armazenamentos de pacotes para ver as pastas e os pacotes abaixo. Você pode ser solicitado a inserir as credenciais de acesso para seus repositórios de pacotes, se o SSMS não conseguir se conectar a eles automaticamente. Por exemplo, se você expandir um repositório de pacotes na parte superior do MSDB, você poderá ser solicitado a se conectar ao SQL do Azure Instância Gerenciada primeiro.

![Conectar-se ao Azure SQL Instância Gerenciada](media/azure-ssis-integration-runtime-package-store/ssms-package-store-connect2.png)

## <a name="manage-folders-and-packages"></a>Gerenciar pastas e pacotes

Ao procurar seu Azure-SSIS IR no SSMS, você pode clicar com o botão direito do mouse em qualquer repositório/pasta/pacotes do pacote para exibir um menu e selecionar **nova pasta**, **Importar pacote**, **Exportar pacote**, **excluir**ou **Atualizar**.

   ![Gerenciar pastas e pacotes](media/azure-ssis-integration-runtime-package-store/ssms-package-store-manage.png)

   *  Selecione **nova pasta** para criar uma nova pasta para pacotes importados.

   *  Selecione **Importar pacote** para importar pacotes do **sistema de arquivos**, **SQL Server** (msdb) ou **armazenar pacote SSIS** herdado em seu repositório de pacotes.

      ![Importar pacote](media/azure-ssis-integration-runtime-package-store/ssms-package-store-import.png)

      Dependendo do local do **pacote** do qual importar, selecione o tipo de **Server** / **autenticação**de servidor relevante, insira as credenciais de acesso, se necessário, selecione o **caminho do pacote**e insira o novo **nome do pacote**. Ao importar pacotes, seu nível de proteção não pode ser alterado. Para alterá-lo, use SQL Server Data Tools (SSDT) ou `dtutil` Utilitário de linha de comando.

   *  Selecione **Exportar pacote** para exportar pacotes do seu repositório de pacotes para o **sistema de arquivos**, **SQL Server** (msdb) ou o **repositório de pacotes SSIS**herdados.

      ![Exportar pacote](media/azure-ssis-integration-runtime-package-store/ssms-package-store-export.png)

      Dependendo do local do **pacote** para o qual exportar, selecione o tipo de autenticação de **servidor**relevante / **Authentication type**, insira as credenciais de acesso, se necessário, e selecione o **caminho do pacote**. Ao exportar pacotes, se eles estiverem criptografados, insira as senhas para descriptografá-los primeiro e, em seguida, você poderá alterar o nível de proteção deles, por exemplo, para não salvar dados confidenciais ou para criptografar todos os dados confidenciais com a chave de usuário/senha.

   *  Selecione **excluir** para excluir pastas/pacotes existentes do seu repositório de pacotes.

   *  Selecione **Atualizar** para mostrar as pastas/pacotes recém-adicionados no seu repositório de pacotes.

## <a name="execute-packages"></a>Executar pacotes

Ao procurar seu Azure-SSIS IR no SSMS, você pode clicar com o botão direito do mouse em todos os pacotes armazenados para exibir um menu e selecionar **executar pacote**.  Isso abrirá a caixa de diálogo **Utilitário de execução de pacotes** , em que você pode configurar suas execuções de pacote em Azure-SSIS ir como executar atividades de pacote SSIS em pipelines do ADF.

![Utilitário de Execução de Pacotes páginas 1 & 2](media/azure-ssis-integration-runtime-package-store/ssms-package-store-execute.png)

![Utilitário de Execução de Pacotes páginas 3 & 4](media/azure-ssis-integration-runtime-package-store/ssms-package-store-execute2.png)

As páginas **geral**, **configurações**, **Opções de execução**e **log** de **Utilitário de execução de pacotes** correspondem à guia **configurações** da atividade executar pacote SSIS, na qual você pode inserir a senha de criptografia para seu pacote, acessar informações para o arquivo de configuração de pacote, credenciais/Propriedades de execução de pacote e informações de acesso para sua pasta de log.  A página **definir valores** da caixa de diálogo **Utilitário de execução de pacotes** corresponde à guia **substituições de propriedade** da atividade executar pacote SSIS, na qual você pode inserir suas propriedades de pacote existentes para substituir. Para obter mais informações, consulte [executar pacotes do SSIS como atividades de execução do pacote do SSIS em pipelines do ADF](https://docs.microsoft.com/azure/data-factory/how-to-invoke-ssis-package-ssis-activity).

Quando você seleciona o botão **executar** da caixa de diálogo **Utilitário de execução de pacotes** , um novo pipeline do ADF com a atividade executar pacote SSIS será gerado e disparado automaticamente. Se um pipeline do ADF com as mesmas configurações de execução de pacote já existir, ele será executado novamente e um novo pipeline não será gerado. O pipeline do ADF e a atividade executar pacote do SSIS serão nomeados `Pipeline_SSMS_YourPackageName_HashString` e `Activity_SSMS_YourPackageName` , respectivamente.

![Botão de Utilitário de Execução de Pacotes](media/azure-ssis-integration-runtime-package-store/ssms-package-store-execute3.png)

![Executar a atividade do pacote SSIS](media/azure-ssis-integration-runtime-package-store/ssis-activity-package-store.png)

## <a name="monitor-and-stop-running-packages"></a>Monitorar e interromper a execução de pacotes

Ao procurar seu Azure-SSIS IR no SSMS, você pode expandir o nó **pacotes em execução** para ver seus pacotes em execução no momento.  Clique com o botão direito do mouse em qualquer um deles para exibir um menu e selecionar **parar** ou **Atualizar**.

   ![Monitorar e interromper a execução de pacotes](media/azure-ssis-integration-runtime-package-store/ssms-package-store-monitor.png)

   *  Selecione **parar** para cancelar o pipeline do ADF em execução no momento que executa o pacote como a atividade executar pacote do SSIS.

   *  Selecione **Atualizar** para mostrar pacotes que executam recentemente de seus armazenamentos de pacotes.

## <a name="monitor-azure-ssis-ir-and-edit-package-stores"></a>Monitorar Azure-SSIS IR e editar armazenamentos de pacotes

Ao procurar seu Azure-SSIS IR no SSMS, você pode clicar com o botão direito do mouse nele para exibir um menu e selecionar **ir para o portal de Azure data Factory** ou **Atualizar**.

   ![Ir para o portal do ADF](media/azure-ssis-integration-runtime-package-store/ssms-package-store-monitor2.png)

   *  Selecione **ir para Azure data Factory portal** para abrir a página **tempos de execução de integração** do hub de monitoramento do ADF, onde você pode monitorar seu Azure-SSIS ir. No bloco **repositórios de pacotes** , você pode ver o número de repositórios de pacotes anexados à sua Azure-SSIS ir.  A seleção desse número exibirá uma janela em que você pode editar os serviços vinculados do ADF que armazenam as informações de acesso para os armazenamentos de pacotes.

      ![Editar armazenamentos de pacotes](media/azure-ssis-integration-runtime-package-store/ssms-package-store-monitor3.png)

   *  Selecione **Atualizar** para mostrar as pastas/pacotes recém-adicionados em suas lojas de pacotes e executar pacotes de seus repositórios de pacotes.

## <a name="next-steps"></a>Próximas etapas

Você pode executar novamente/editar os pipelines do ADF gerados automaticamente com atividades executar pacote do SSIS ou criar novos no portal do ADF. Para obter mais informações, consulte [executar pacotes do SSIS como atividades de execução do pacote do SSIS em pipelines do ADF](https://docs.microsoft.com/azure/data-factory/how-to-invoke-ssis-package-ssis-activity).