---
title: Avaliar servidores por meio de dados de servidor importados com a Avaliação de Servidor das Migrações para Azure
description: Descreve como avaliar servidores locais para a migração para o Azure por meio da Avaliação de Servidor das Migrações para Azure usando dados importados.
author: rayne-wiselman
manager: carmonm
ms.service: azure-migrate
ms.topic: tutorial
ms.date: 10/23/2019
ms.author: raynew
ms.openlocfilehash: ff7e423063859a6cdc1a4362fb030c0deb75eb32
ms.sourcegitcommit: f8d2ae6f91be1ab0bc91ee45c379811905185d07
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/10/2020
ms.locfileid: "89658701"
---
# <a name="assess-servers-by-using-imported-data"></a>Avaliar servidores pelo uso de dados importados

Este artigo explica como avaliar servidores locais com a ferramenta [Migrações para Azure: Avaliação do Servidor](migrate-services-overview.md#azure-migrate-server-assessment-tool), importando metadados do servidor em formato CSV (valores separados por vírgula). Esse método de avaliação não exige que você configure o dispositivo das Migrações para Azure para criar uma avaliação. Ele é útil se:

- Você quiser criar uma avaliação inicial rápida antes de implantar o dispositivo.
- Você não conseguir implantar o dispositivo das Migrações para Azure em sua organização.
- Não for possível compartilhar credenciais que permitam o acesso a servidores locais.
- As restrições de segurança impedem que você colete e envie os dados coletados pelo dispositivo ao Azure. Você pode controlar os dados que compartilha em um arquivo importado. Além disso, grande parte dos dados (por exemplo, fornecimento de endereços IP) é opcional.

## <a name="before-you-start"></a>Antes de começar

Lembre-se destes pontos:

- É possível adicionar até um máximo de 20.000 servidores em um arquivo CSV.
- Você pode adicionar até 20.000 servidores em um projeto das Migrações para Azure por meio de CSV.
- É possível carregar informações do servidor para a Avaliação de Servidor diversas vezes por meio de CSV.
- Coletar informações sobre o aplicativo é útil quando o ambiente local é avaliado para migração. No entanto, a Avaliação de Servidor atualmente não realiza a avaliação no nível do aplicativo, nem considera os aplicativos quando uma avaliação é criada.

Neste tutorial, você aprenderá como:
> [!div class="checklist"]
> * Configurar um projeto das Migrações para Azure.
> * Preencha um arquivo CSV com as informações do servidor.
> * Importe o arquivo para adicionar informações do servidor à Avaliação de Servidor.
> * Crie e examine uma avaliação.

> [!NOTE]
> Os tutoriais mostram o caminho de implantação mais simples para um cenário para que você possa configurar rapidamente uma prova de conceito. Os tutoriais usam opções padrão quando possível e não mostram todas as configurações e todos os caminhos possíveis. Para obter instruções detalhadas, examine os guias de instruções.

Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/pricing/free-trial/) antes de começar.

## <a name="set-azure-permissions-for-azure-migrate"></a>Configurar as permissões do Azure para as Migrações para Azure

sua conta do Azure precisa de permissões para criar um projeto de Migrações para Azure.

1. No portal do Azure, abra a assinatura e selecione **Controle de acesso (IAM).**
2. Em **Verificar acesso**, localize a conta relevante e então selecione-a para exibir as permissões.
3. Você precisa ter permissões de **Colaborador** e de **Proprietário**.
    - Se você acaba de criar uma conta gratuita do Azure, você é o proprietário da assinatura.
    - Se você não for o proprietário da assinatura, trabalhe com o proprietário para atribuir a função.

## <a name="set-up-an-azure-migrate-project"></a>Configurar um projeto das Migrações para Azure

Para configurar um novo projeto das Migrações para Azure:

1. No portal do Azure, em **Todos os serviços**, pesquise por **Migrações para Azure**.
2. Em **Serviços**, selecione **Migrações para Azure**.
3. Em **Visão Geral**, em **Descobrir, avaliar e migrar servidores**, selecione **Avaliar e migrar servidores**.

    ![Descobrir e avaliar servidores](./media/tutorial-assess-import/assess-migrate.png)

4. Em **Introdução**, selecione **Adicionar ferramentas**.
5. Em **Migrar projeto**, selecione sua assinatura do Azure e crie um grupo de recursos, caso não tenha um.
6. Em **Detalhes do projeto**, especifique o nome do projeto e a geografia em que deseja criá-lo. Para mais informações:

    - Examine as geografias compatíveis para [nuvens públicas](migrate-support-matrix.md#supported-geographies-public-cloud) e [governamentais](migrate-support-matrix.md#supported-geographies-azure-government).
    - Você pode selecionar qualquer região de destino ao executar uma migração.

    ![Criar um projeto das Migrações para Azure](./media/tutorial-assess-import/migrate-project.png)

7. Selecione **Avançar**.
8. Em **Selecionar ferramenta de avaliação**, selecione **Migrações para Azure: Avaliação de Servidor** > **Avançar**.

    ![Criar uma avaliação das Migrações para Azure](./media/tutorial-assess-import/assessment-tool.png)

9. Em **Selecionar ferramenta de migração**, selecione **Ignorar a adição de uma ferramenta de migração por enquanto** > **Avançar**.
10. Em **Examinar + adicionar ferramentas**, examine as configurações e selecione **Adicionar ferramentas**.
11. Aguarde alguns minutos até que o projeto das Migrações para Azure seja implantado. Você será levado então para a página do projeto. Caso não veja o projeto, acesse-o em **Servidores** no painel das Migrações para Azure.

## <a name="prepare-the-csv"></a>Preparar o arquivo CSV

Baixe o modelo do CSV e adicione informações do servidor a ele.

### <a name="download-the-template"></a>Baixe o modelo

1. Em **Metas de Migração** > **Servidores** > **Migrações para Azure: Avaliação de Servidor**, selecione **Descobrir**.
2. Em **Descobrir computadores**, escolha **Importar usando CSV**.
3. Selecione **Baixar** para baixar o modelo CSV. Se preferir, também poderá [baixá-lo diretamente](https://go.microsoft.com/fwlink/?linkid=2109031).

    ![Baixar o modelo CSV](./media/tutorial-assess-import/download-template.png)

### <a name="add-server-information"></a>Adicionar informações do servidor

Colete dados do servidor e adicione-os ao arquivo CSV.

- Para coletar dados, você pode exportá-los das ferramentas usadas no gerenciamento do servidor local, como o VMware vSphere ou o CMDB (banco de dados de gerenciamento de configuração).
- Para analisar os dados de exemplo, baixe nosso [arquivo de exemplo](https://go.microsoft.com/fwlink/?linkid=2108405).

A tabela a seguir resume os campos de arquivo a serem preenchidos:

**Nome do campo** | **Obrigatório** | **Detalhes**
--- | --- | ---
**Nome do servidor** | Sim | Recomendamos especificar o FQDN (nome de domínio totalmente qualificado).
**Endereço IP** | Não | Endereço do servidor.
**Núcleos** | Sim | O número de núcleos do processador alocados para o servidor.
**Memória** | Sim | Total de RAM (MB) alocado para o servidor.
**Nome do SO** | Sim | Sistema operacional do servidor. <br/> Nomes de sistema operacional que correspondem ou contêm os nomes [desta](#supported-operating-system-names) lista são reconhecidos pela avaliação.
**Versão do SO** | Não | Versão do sistema operacional do servidor.
**Arquitetura do SO** | Não | Arquitetura do SO do servidor <br/> Os valores válidos são: x64, x86, amd64, 32 bits ou 64 bits
**Número de discos** | Não | Não é necessário se forem fornecidos detalhes de disco individuais.
**Tamanho do Disco 1**  | Não | Tamanho máximo do disco (GB).<br/>Para adicionar detalhes de outros discos, [adicione colunas](#add-multiple-disks) no modelo. Você pode adicionar até oito discos.
**Operações de leitura do Disco 1** | Não | Operações de leitura do disco por segundo.
**Operações de gravação do Disco 1** | Não | Operações de gravação do disco por segundo.
**Taxa de transferência de leitura do Disco 1** | Não | Dados lidos no disco por segundo (MB/s).
**Taxa de transferência de gravação do Disco 1** | Não | Dados gravados no disco por segundo (MB/s).
**Porcentagem de utilização de CPU** | Não | O percentual de CPU em uso.
**Porcentagem de utilização de memória** | Não | O percentual de RAM em uso.
**Total de operações de leitura de discos** | Não | Operações de leitura por segundo para todos os discos combinados. <br/> Use este campo se não for possível fornecer dados no nível do disco. 
**Total de operações de gravação de discos** | Não | Operações de gravação por segundo para todos os discos combinados. <br/> Use este campo se não for possível fornecer dados no nível do disco.
**Taxa de transferência de leitura de discos** | Não | Dados lidos em todos os discos (MB/s). <br/> Use este campo se não for possível fornecer dados no nível do disco. 
**Taxa de transferência do total de gravação de discos** | Não | Dados gravados em todos os discos (MB/s). <br/> Use este campo se não for possível fornecer dados no nível do disco.
**Taxa de transferência da entrada na rede** | Não | Dados recebidos pelo servidor (MB/s).
**Taxa de transferência da saída da rede** | Não | Dados transmitidos pelo servidor (MB/s).
**Tipo de firmware** | Não | Firmware do servidor. Os valores podem ser "BIOS" ou "UEFI".
**Endereço MAC**| Não | Endereço MAC do servidor.


### <a name="add-operating-systems"></a>Adicionar sistemas operacionais

A avaliação reconhece nomes de sistemas operacionais específicos. Qualquer nome especificado deve corresponder exatamente a uma das opções na [lista de nomes compatíveis](#supported-operating-system-names).

### <a name="add-multiple-disks"></a>Adicionar vários discos

O modelo fornece campos padrão para o primeiro disco. Você pode adicionar colunas semelhantes para até oito discos.

Por exemplo, para especificar todos os campos para um segundo disco, adicione estas colunas:

- Tamanho do disco 2
- Operações de leitura do disco 2
- Operações de gravação do disco 2
- Taxa de transferência de leitura do disco 2
- Taxa de transferência de gravação do disco 2

Se não for possível fornecer dados no nível do disco, forneça dados de desempenho do disco por servidor usando os campos a seguir. Consulte [esta](#add-server-information) seção para obter detalhes sobre cada campo.
- Total de operações de leitura de discos
- Total de operações de gravação de discos
- Taxa de transferência de leitura de discos
- Taxa de transferência do total de gravação de discos

## <a name="import-the-server-information"></a>Importar as informações do servidor

Depois de adicionar informações ao modelo CSV, importe os servidores para a Avaliação do Servidor.

1. Nas Migrações para Azure, em **Descobrir computadores**, navegue até o modelo concluído.
2. Selecione **Importar**.
3. O status da importação é exibido.
    - Se surgirem avisos no status, corrija-os ou continue sem solucioná-los.
    - Para aprimorar a precisão da avaliação, aprimore as informações do servidor.
    - Para exibir e corrigir avisos, selecione **Baixar o .CSV de detalhes de avisos**. Esta operação baixa o CSV com avisos incluídos. Examine os avisos e corrija os problemas conforme necessário.
    - Se forem exibidos erros no status (o status da importação será mostrado como **Com falha**), você precisará corrigi-los para poder continuar com a importação:
        1. Baixe o CSV, que agora inclui os detalhes do erro.
        1. Examine os erros e solucione-os conforme necessário. 
        1. Carregue novamente o arquivo modificado novamente.
4. Se o status da importação for **Concluído**, as informações do servidor terão sido importadas.

## <a name="update-server-information"></a>Atualizar informações do servidor

Para atualizar as informações do servidor, importe os dados para o servidor novamente usando o mesmo **Nome do servidor**. O campo **Nome do servidor** não pode ser modificado. Atualmente, não há suporte para a exclusão de servidores.

## <a name="verify-servers-in-the-portal"></a>Verificar servidores no portal

Após a descoberta, verifique se os servidores são exibidos no portal do Azure:

1. Abra o painel das Migrações para Azure.
2. Na página **Migrações para Azure – Servidores** > **Migrações para Azure: Avaliação de Servidor**, selecione o ícone que exibe a contagem de **Servidores descobertos**.
3. Selecione a guia **Baseado em importação**.

## <a name="set-up-and-run-an-assessment"></a>Configurar e executar uma avaliação

Você pode criar dois tipos de avaliações usando a Avaliação de Servidor.


**Tipo de avaliação** | **Detalhes**
--- | --- 
**VM do Azure** | Avaliações para migrar servidores locais para máquinas virtuais do Azure. <br/><br/> Avalie as [VMs do VMware](how-to-set-up-appliance-vmware.md), [VMs do Hyper-V](how-to-set-up-appliance-hyper-v.md) e [servidores físicos](how-to-set-up-appliance-physical.md) locais para migração para o Azure usando esse tipo de avaliação.
**AVS (Solução VMware no Azure)** | Avaliações para migrar servidores locais para a [AVS (Solução VMware no Azure)](../azure-vmware/introduction.md). <br/><br/> Avalie as [VMs do VMware](how-to-set-up-appliance-vmware.md) locais para migração para a AVS (Solução VMware no Azure) usando esse tipo de avaliação.[Saiba mais](concepts-azure-vmware-solution-assessment-calculation.md)

### <a name="sizing-criteria"></a>Critérios de dimensionamento

A avaliação do servidor fornece duas opções de critérios de dimensionamento:

**Critérios de dimensionamento** | **Detalhes** | **Dados**
--- | --- | ---
**Com base no desempenho** | Avaliações que fazem recomendações com base nos dados de desempenho coletados | **Avaliação da VM do Azure**: a recomendação de tamanho da VM é baseada nos dados de utilização da CPU e de memória.<br/><br/> A recomendação de tipo de disco (HDD/SSD Standard ou discos gerenciados Premium) é baseada na IOPS e na taxa de transferência dos discos locais.<br/><br/> **Avaliação da AVS (Solução VMware no Azure)** : a recomendação de nós da AVS é baseada nos dados de utilização da CPU e de memória.
**No estado em que se encontra localmente** | Avaliações que não usam dados de desempenho para fazer recomendações. | **Avaliação da VM do Azure**: a recomendação de tamanho da VM é baseada no tamanho da VM local<br/><br> O tipo de disco recomendado é baseado no que você seleciona na configuração de tipo de armazenamento para a avaliação.<br/><br/> **Avaliação da AVS (Solução VMware no Azure)** : a recomendação de nós da AVS é baseada no tamanho da VM local.


Para executar uma avaliação:

1. Examine as [melhores práticas](best-practices-assessment.md) para a criação de avaliações.
2. Na guia **Servidores**, no bloco **Migrações para Azure: Avaliação de Servidor**, selecione **Avaliar**.

    ![Avaliar](./media/tutorial-assess-physical/assess.png)

3. Em **Avaliar servidores**, especifique o nome da avaliação e selecione o tipo de **avaliação** como *VM do Azure* se pretender executar avaliações de VM do Azure ou *AVS (Solução VMware no Azure)* se pretender executar avaliações de AVS.

    ![Noções básicas sobre a avaliação](./media/how-to-create-assessment/assess-servers-azurevm.png)

4. Em **Origem da descoberta**, escolha **Computadores adicionados por meio da importação para Migrações para Azure**.

5. Selecione **Exibir tudo** para examinar as propriedades da avaliação.

    ![Propriedades de avaliação](./media/tutorial-assess-physical/view-all.png)

6. Clique em **avançar** para **Selecionar computadores para avaliação**. Em **Selecionar ou criar um grupo**, selecione **Criar** e especifique um nome de grupo. Um grupo reúne uma ou mais VMs para avaliação.
7. Em **Adicionar computadores ao grupo**, escolha os computadores que serão adicionados ao grupo.
8. Clique em **avançar** para **Examinar + criar avaliação** para examinar os detalhes da avaliação.
9. Clique em **Criar avaliação** para criar o grupo e depois execute a avaliação.

    ![Criar uma avaliação](./media/tutorial-assess-physical/assessment-create.png)

9. Após a criação da avaliação, veja-a em **Servidores** > **Migrações para Azure: Avaliação de Servidor** > **Avaliações**.
10. Selecione **Exportar avaliação** para baixá-la como um arquivo do Microsoft Excel.

Para conhecer mais detalhes sobre a avaliação de **AVS (Solução VMware no Azure)** , clique [aqui](how-to-create-azure-vmware-solution-assessment.md). 

## <a name="review-an-azure-vm-assessment"></a>Examinar uma avaliação de VM do Azure

Uma avaliação de VM do Azure descreve:

- **Preparação para o Azure**: indica se os servidores são adequados para a migração para o Azure.
- **Estimativa de custo mensal**: os custos mensais estimados de computação e armazenamento para execução dos servidores no Azure.
- **Estimativa de custo de armazenamento mensal**: custos estimados para o armazenamento em disco após a migração.

### <a name="view-an-assessment"></a>Exibir uma avaliação

1. Em **Metas de migração** > **Servidores**, selecione **Avaliações** em **Migrações para Azure: Avaliação de Servidor**.
2. Em **Avaliações**, selecione uma avaliação para abri-la.

    ![Resumo da avaliação](./media/tutorial-assess-physical/assessment-summary.png)

### <a name="review-azure-readiness"></a>Examinar a Preparação para o Azure

1. Em **Preparação para o Azure**, verifique se os servidores estão preparados para a migração para o Azure.
2. Examine o status:
    - **Pronto para o Azure**: as Migrações para Azure recomendam um tamanho de VM e estimativas de custo para as VMs na avaliação.
    - **Pronto com condições**: mostra os problemas e a correção sugerida.
    - **Não está pronto para o Azure**: mostra os problemas e a correção sugerida.
    - **Preparação desconhecida**: as Migrações para Azure não podem avaliar a preparação, devido a problemas de disponibilidade de dados.

3. Selecione um status de **Preparação para o Azure**. Você pode exibir os detalhes de preparação do servidor e fazer uma busca detalhada para ver os detalhes do servidor, incluindo as configurações de computação, armazenamento e rede.

### <a name="review-cost-details"></a>Examinar os detalhes de custo

Essa exibição mostra o custo estimado de computação e armazenamento da execução das VMs no Azure. Você pode:

- Examine os custos mensais de computação e armazenamento. Os custos são agregados para todos os servidores no grupo avaliado.

    - As estimativas de custo são baseadas nas recomendações de tamanho para um computador e em seus discos e propriedades.
    - Os custos mensais estimados de computação e armazenamento são mostrados.
    - A estimativa de custo é para executar os servidores locais como VMs de IaaS (infraestrutura como serviço). A Avaliação do Servidor não considera nenhum custo de PaaS (Plataforma como Serviço) ou SaaS (Software como Serviço).

- Examine as estimativas de custo mensal de armazenamento. Essa exibição mostra os custos agregados de armazenamento para o grupo avaliado, divididos em diferentes tipos de discos de armazenamento.
- Faça uma busca detalhada para ver os detalhes de VMs específicas.

> [!NOTE]
> As classificações de confiança não são atribuídas a avaliações de servidores importados para a Avaliação de Servidor pelo uso de CSV.

## <a name="supported-operating-system-names"></a>Nomes de sistemas operacionais com suporte

Os nomes de sistema operacional fornecidos no CSV devem corresponder ou conter os nomes desta lista. Isso é necessário para que os nomes especificados sejam reconhecidos como válidos pela avaliação.

<!-- BEGIN A - H -->

:::row:::
   :::column span="2":::
      **A - H**
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="":::
      Apple Mac OS X 10
   :::column-end:::
   :::column span="":::
      Asianux 3<br/>
      Asianux 4<br/>
      Asianux 5
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="":::
      CentOS<br/>
      CentOS 4/5
   :::column-end:::
   :::column span="":::
      CoreOS Linux
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="":::
      Debian GNU/Linux 4<br/>
      Debian GNU/Linux 5<br/>
      Debian GNU/Linux 6<br/>
      Debian GNU/Linux 7<br/>
      Debian GNU/Linux 8
   :::column-end:::
   :::column span="":::
      FreeBSD
   :::column-end:::
:::row-end:::

<!-- BEGIN I - R -->

:::row:::
   :::column span="2":::
      **I - R**
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="":::
      IBM OS/2
   :::column-end:::
   :::column span="":::
      MS-DOS
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="":::
      Novell NetWare 5<br/>
      Novell NetWare 6
   :::column-end:::
   :::column span="":::
      Oracle Linux<br/>
       Oracle Linux 4/5<br/>
      Oracle Solaris 10<br/>
       Oracle Solaris 11
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="":::
      Red Hat Enterprise Linux 2<br/>
      Red Hat Enterprise Linux 3<br/>
      Red Hat Enterprise Linux 4<br/>
      Red Hat Enterprise Linux 5<br/>
      Red Hat Enterprise Linux 6<br/>
      Red Hat Enterprise Linux 7<br/>
      Red Hat Fedora
   :::column-end:::
:::row-end:::

<!-- BEGIN S - T -->

:::row:::
   :::column span="2":::
      **S – T**
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="":::
      SCO OpenServer 5<br/>
      SCO OpenServer 6<br/>
      SCO UnixWare 7
   :::column-end:::
   :::column span="":::
      Serenity Systems eComStation 1<br/>
      Serenity Systems eComStation 2
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="":::
      Sun Microsystems Solaris 8<br/>
      Sun Microsystems Solaris 9
   :::column-end:::
   :::column span="":::
      SUSE Linux Enterprise 10<br/>
      SUSE Linux Enterprise 11<br/>
      SUSE Linux Enterprise 12<br/>
      SUSE Linux Enterprise 8/9<br/>
      SUSE Linux Enterprise 11<br/>
      SUSE openSUSE
   :::column-end:::
:::row-end:::

<!-- BEGIN U - Z -->
:::row:::
   :::column span="2":::
      **U – Z**
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="":::
      Ubuntu Linux
   :::column-end:::
   :::column span="":::
      VMware ESXi 4<br/>
      VMware ESXi 5<br/>
      VMware ESXi 6
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="":::
      Windows 10<br/>
      Windows 2000<br/>
      Windows 3<br/>
      Windows 7<br/>
      Windows 8<br/>
      Windows 95<br/>
      Windows 98<br/>
      Windows NT<br/>
      Windows Server (R) 2008<br/>
      Windows Server 2003
   :::column-end:::
   :::column span="":::
      Windows Server 2008<br/>
      Windows Server 2008 R2<br/>
      Windows Server 2012<br/>
      Windows Server 2012 R2<br/>
      Windows Server 2016<br/>
      Windows Server 2019<br/>
      Windows Server Threshold<br/>
      Windows Vista<br/>
      Windows Web Server 2008 R2<br/>
      Windows XP Professional
   :::column-end:::
:::row-end:::

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você:

> [!div class="checklist"]
> * Importou servidores para as Migrações para Azure: Avaliação de Servidor por meio de CSV.
> * Criou e examinou uma avaliação.

Agora, [implante um dispositivo](./migrate-appliance.md) para ter avaliações mais precisas e reúna servidores em grupos para realizar uma avaliação mais profunda por meio da [análise de dependência](./concepts-dependency-visualization.md).
