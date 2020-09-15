---
title: Avaliar VMs do VMware usando a avaliação de servidor das Migrações para Azure
description: Descreve como avaliar as VMs locais do VMware para migração para o Azure usando a Avaliação de Servidor de Migrações para Azure.
ms.topic: tutorial
ms.date: 06/03/2020
ms.custom: mvc
ms.openlocfilehash: abd8aafebe9cc02b8ee88cce88616389c82af83f
ms.sourcegitcommit: 7f62a228b1eeab399d5a300ddb5305f09b80ee14
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/08/2020
ms.locfileid: "89514094"
---
# <a name="assess-vmware-vms-with-server-assessment"></a>Avaliar VMs do VMware com a Avaliação do Servidor

Este artigo mostra como avaliar as VMs (máquinas virtuais) do VMware locais usando a ferramenta [Migrações para Azure: Avaliação do Servidor](migrate-services-overview.md#azure-migrate-server-assessment-tool).


Este tutorial é o segundo de uma série que demonstra como avaliar e migrar VMs do VMware para o Azure. Neste tutorial, você aprenderá como:
> [!div class="checklist"]
> * Configurar um projeto das Migrações para Azure.
> * Configurar um dispositivo das Migrações para Azure que é executado localmente para avaliar as VMs.
> * Iniciar a descoberta contínua de VMs locais. O dispositivo envia dados de desempenho e configuração para as VMs descobertas ao Azure.
> * Agrupar as VMs descobertas e avaliar o grupo de VMs.
> * Examinar a avaliação.

> [!NOTE]
> Os tutoriais mostram o caminho de implantação mais simples para um cenário para que você possa configurar rapidamente uma prova de conceito. Os tutoriais usam opções padrão quando possível e não mostram todas as configurações e todos os caminhos possíveis. Para obter instruções detalhadas, examine os artigos de instruções.

Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/pricing/free-trial/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

- [Conclua o primeiro tutorial](tutorial-prepare-vmware.md) desta série. Caso contrário, as instruções deste tutorial não funcionarão.
- Veja o que você deve ter feito no primeiro tutorial:
    - [Preparar o Azure](tutorial-prepare-vmware.md#prepare-azure) para trabalhar com as Migrações para Azure.
    - [Preparar o VMware para a avaliação](tutorial-prepare-vmware.md#prepare-for-assessment) para avaliação. Isso inclui verificar as configurações do VMware, configurando uma conta que o Migrações para Azure possa usar para acessar o vCenter Server.
    - [Verificar](tutorial-prepare-vmware.md#verify-appliance-settings-for-assessment) o que você precisa para implantar o dispositivo de Migrações para Azure para avaliação do VMware.

## <a name="set-up-an-azure-migrate-project"></a>Configurar um projeto das Migrações para Azure

Configure um novo projeto das Migrações para Azure, conforme descrito a seguir:

1. No portal do Azure > **Todos os serviços**, pesquise **Migrações para Azure**.
2. Em **Serviços**, selecione **Migrações para Azure**.
3. Em **Visão Geral**, em **Descobrir, avaliar e migrar servidores**, selecione **Avaliar e migrar servidores**.

   ![Botão para avaliar e migrar servidores](./media/tutorial-assess-vmware/assess-migrate.png)

4. Em **Introdução**, selecione **Adicionar ferramentas**.
5. Em **Migrar projeto**, selecione sua assinatura do Azure e crie um grupo de recursos, caso não tenha um.     
6. Em **Detalhes do Projeto**, especifique o nome do projeto e a geografia em que deseja criá-lo. Examine as geografias compatíveis para [nuvens públicas](migrate-support-matrix.md#supported-geographies-public-cloud) e [governamentais](migrate-support-matrix.md#supported-geographies-azure-government).

   ![Caixas para nome e região do projeto](./media/tutorial-assess-vmware/migrate-project.png)

7. Selecione **Avançar**.
8. Em **Selecionar ferramenta de avaliação**, selecione **Migrações para Azure: Avaliação de Servidor** > **Avançar**.

   ![Seleção para a ferramenta de Avaliação do Servidor](./media/tutorial-assess-vmware/assessment-tool.png)

9. Em **Selecionar ferramenta de migração**, selecione **Ignorar a adição de uma ferramenta de migração por enquanto** > **Avançar**.
10. Em **Examinar + adicionar ferramentas**, examine as configurações e selecione **Adicionar ferramentas**.
11. Aguarde alguns minutos até que o projeto das Migrações para Azure seja implantado. Você será levado para a página do projeto. Caso não veja o projeto, acesse-o em **Servidores** no painel das Migrações para Azure.

## <a name="set-up-the-azure-migrate-appliance"></a>Configurar o dispositivo das Migrações para Azure

O recurso Migrações para Azure: Avaliação do Servidor usa um dispositivo leve de Migrações para Azure. O dispositivo executa a descoberta de VM e envia os metadados de VM e os dados de desempenho para as Migrações para Azure. O dispositivo pode ser configurado de várias maneiras.

- Configurar em uma VM do VMware usando um modelo OVA baixado. **Esse é o método usado neste tutorial.**
- Configurar em uma VM do VMware ou computador físico com um script do instalador do PowerShell. [Esse método](deploy-appliance-script.md) deverá ser usado se você não puder configurar uma VM usando um modelo OVA ou se você estiver no Azure Government.

Depois de criar o dispositivo, você verifica se é possível conectá-lo ao Migrações para Azure: Avaliação do Servidor, configurá-lo pela primeira vez e registrá-lo com o projeto de Migrações para Azure.


### <a name="generate-the-azure-migrate-project-key"></a>Gerar a chave do projeto das Migrações para Azure

1. Em **Metas de Migração** > **Servidores** > **Migrações para Azure: Avaliação de Servidor**, selecione **Descobrir**.
2. Em **Descobrir computadores** > **As suas máquinas são virtualizadas?** , selecione **Sim, com o hipervisor do VMware vSphere**.
3. Em **1: Gerar chave de projeto das Migrações para Azure**, forneça um nome para o dispositivo das Migrações para Azure que você vai configurar para a descoberta das VMs VMware. O nome deverá conter até 14 caracteres alfanuméricos.
1. Clique em **Gerar chave** para iniciar a criação dos recursos do Azure necessários. Não feche a página Descobrir computadores durante a criação de recursos.
1. Após a criação bem-sucedida dos recursos do Azure, uma **chave de projeto das Migrações para Azure** é gerada.
1. Copie a chave, pois você precisará dela para concluir o registro do dispositivo durante a configuração dele.

### <a name="download-the-ova-template"></a>Baixar o modelo OVA
Em **2: Baixar o dispositivo das Migrações para Azure**, selecione o arquivo .OVA e clique em **Baixar**. 


   ![Seleções para Descobrir computadores](./media/tutorial-assess-vmware/servers-discover.png)


   ![Seleções para Gerar Chave](./media/tutorial-assess-vmware/generate-key-vmware.png)


### <a name="verify-security"></a>Verificar a segurança

Verifique se o arquivo OVA é seguro antes de implantá-lo:

1. No computador no qual você baixou o arquivo, abra uma janela de comando do administrador.
2. Execute o seguinte comando para gerar o hash para o arquivo OVA:
  
   ```C:\>CertUtil -HashFile <file_location> [Hashing Algorithm]```
   
   Exemplo de uso: ```C:\>CertUtil -HashFile C:\AzureMigrate\AzureMigrate.ova SHA256```

3. Verifique as versões mais recentes do dispositivo e os valores de hash:

    - Para a nuvem pública do Azure:
    
        **Algoritmo** | **Download** | **SHA256**
        --- | --- | ---
        VMware (11,6 GB) | [Última versão](https://go.microsoft.com/fwlink/?linkid=2140333) | e9c9a1fe4f3ebae81008328e8f3a7933d78ff835ecd871d1b17f367621ce3c74

    - Para o Azure Government:
    
        **Algoritmo** | **Download** | **SHA256**
        --- | --- | ---
        VMware (85 MB) | [Última versão](https://go.microsoft.com/fwlink/?linkid=2140337) | 47179f47eba2842337bbe533c424dd1da56baccdcf68b1d87b71a5a4280108c2


### <a name="create-the-appliance-vm"></a>Criar a VM do dispositivo

Importe o arquivo baixado e crie uma VM:

1. No console do cliente do vSphere, selecione **Arquivo** > **Implantar o Modelo de OVF**.

   ![Comando de menu para implantar um modelo de OVF](./media/tutorial-assess-vmware/deploy-ovf.png)

2. No Assistente do Modelo de Implantação de OVF > **Origem**, especifique o local do arquivo OVA.
3. Em **Nome** e **Local**, especifique um nome amigável para a VM. Selecione o objeto de inventário no qual a VM será hospedada.
4. Em **Host/Cluster**, especifique o host ou o cluster no qual a VM será executada.
5. Em **Armazenamento**, especifique o destino de armazenamento para a VM.
6. Em **Formato de Disco**, especifique o tipo e o tamanho do disco.
7. Em **Mapeamento de Rede**, especifique a rede à qual a VM se conectará. A rede precisa ter conectividade com a Internet para enviar metadados para a Avaliação de Servidor das Migrações para Azure.
8. Examine e confirme as configurações e selecione **Concluir**.

## <a name="verify-appliance-access-to-azure"></a>Verificar o acesso do dispositivo ao Azure

Verifique se a VM do dispositivo pode se conectar às URLs do Azure para as nuvens [pública](migrate-appliance.md#public-cloud-urls) e [governamental](migrate-appliance.md#government-cloud-urls).

### <a name="configure-the-appliance"></a>Configurar o dispositivo

Configure o dispositivo pela primeira vez.

> [!NOTE]
> Se você configurar o dispositivo usando um [script do PowerShell](deploy-appliance-script.md), em vez do OVA baixado, as duas primeiras etapas neste procedimento não serão relevantes.

1. No console do cliente do vSphere, clique com o botão direito do mouse na VM e selecione **Abrir console**.
2. Forneça o idioma, o fuso horário e a senha do dispositivo.
3. Abra um navegador em qualquer computador que possa se conectar à VM e abra a URL do aplicativo Web do dispositivo: **https://*nome do dispositivo ou endereço IP*: 44368**.

   Como alternativa, você pode abrir o aplicativo na área de trabalho do dispositivo selecionando o atalho do aplicativo.
1. Aceite os **termos de licença** e leia as informações de terceiros.
1. No aplicativo Web > **Configurar os pré-requisitos**, faça o seguinte:
   - **Conectividade**: o aplicativo verifica se a VM tem acesso à Internet. Se a VM usar um proxy:
     - Clique em **Configurar proxy** para especificar o endereço de proxy (na forma http://ProxyIPAddress ou http://ProxyFQDN) e na porta de escuta).
     - Especifique as credenciais caso o proxy exija autenticação.
     - Há suporte apenas para o proxy HTTP.
     - Se você tiver adicionado detalhes de proxy ou desabilitado o proxy e/ou a autenticação, clique em **Salvar** para disparar a verificação de conectividade novamente.
   - **Sincronização do horário**: O horário no dispositivo deve ser sincronizado com o horário na Internet para que a descoberta funcione corretamente.
   - **Instalar as atualizações**: O dispositivo garante que as atualizações mais recentes serão instaladas. Após a conclusão da verificação, você poderá clicar em **Exibir serviços de dispositivo** para ver o status e as versões dos componentes em execução no dispositivo.
   - **Instalar o VDDK**: o dispositivo verifica se o VDDK (Kit de Desenvolvimento de Disco Virtual) do VMware vSphere está instalado. Se ele não estiver instalado, baixe o VDDK 6.7 do VMware e extraia o conteúdo do zip baixado para a localização especificada no dispositivo, conforme fornecido nas **Instruções de instalação**.

     A Migração de Servidor de Migrações para Azure usa o VDDK para replicar computadores durante a migração para o Azure. 
1. Se desejar, você poderá **executar os pré-requisitos novamente** a qualquer momento durante a configuração do dispositivo para verificar se ele atende a todos os pré-requisitos.

### <a name="register-the-appliance-with-azure-migrate"></a>Registrar o dispositivo nas Migrações para Azure

1. Cole a **chave do projeto das Migrações para Azure** copiada do portal. Se você não tiver a chave, acesse **Avaliação do Servidor> Descobrir> Gerenciar dispositivos existentes**, selecione o nome do dispositivo fornecido no momento da geração da chave e copie a chave correspondente.
1. Clique em **Fazer logon**. Será aberto um prompt de logon do Azure em uma nova guia do navegador. Se essa opção não for exibida, verifique se você desabilitou o bloqueador de pop-ups no navegador.
1. Na nova guia, entre usando seu nome de usuário e senha do Azure.
   
   Não há suporte para a entrada com um PIN.
3. Depois de fazer logon com êxito, volte para o aplicativo Web. 
4. Se a conta de usuário do Azure usada para o registro em log tiver as [permissões](tutorial-prepare-vmware.md#prepare-azure) corretas nos recursos do Azure criados durante a geração de chave, o registro do dispositivo será iniciado.
1. Depois que o dispositivo for registrado com êxito, você poderá ver os detalhes do registro clicando em **Exibir detalhes**.


## <a name="start-continuous-discovery"></a>Iniciar a descoberta contínua

O dispositivo precisa se conectar ao vCenter Server para descobrir a configuração e os dados de desempenho das VMs.

1. Na **Etapa 1: Fornecer as credenciais do vCenter Server**, clique em **Adicionar credenciais** para especificar um nome amigável para as credenciais, adicionar o **Nome de usuário** e a **Senha** para a conta do vCenter Server que o dispositivo usará para descobrir as VMs na instância do vCenter Server.
    - Você deve ter configurado uma conta com as permissões necessárias no [tutorial anterior](tutorial-prepare-vmware.md#set-up-permissions-for-assessment).
    - Se você quiser definir o escopo da descoberta para objetos específicos do VMware (data centers, clusters, uma pasta de clusters, hosts, uma pasta de hosts ou VMs individuais do vCenter Server), examine as instruções [neste artigo](set-discovery-scope.md) para restringir a conta usada por Migrações para Azure.
1. Na **Etapa 2: Fornecer detalhes do vCenter Server**, clique em **Adicionar origem da descoberta** para selecionar o nome amigável de credenciais na lista suspensa e especifique o **Endereço IP/FQDN** da instância do vCenter Server. Você pode deixar a **Porta** como padrão (443) ou especificar uma porta personalizada na qual o vCenter Server escuta e clique em **Salvar**.
1. Quando você clicar em Salvar, o dispositivo tentará validar a conexão com o vCenter Server com as credenciais fornecidas e mostrará o **Status de validação** na tabela com relação ao endereço IP/FQDN do vCenter Server.
1. Você pode **revalidar** a conectividade com o vCenter Server a qualquer momento antes de iniciar a descoberta.
1. Na **Etapa 3: Fornecer as credenciais da VM para descobrir os aplicativos instalados e executar o mapeamento de dependência sem agente**, clique em **Adicionar credenciais** e especifique o sistema operacional para o qual as credenciais são fornecidas, o nome amigável para as credenciais e o **Nome de usuário** e a **Senha**. Em seguida, clique em **Salvar**.

    - Opcionalmente, adicione credenciais aqui se você tiver criado uma conta a ser usada para o [recurso de descoberta de aplicativo](how-to-discover-applications.md) ou o [recurso de análise de dependência sem agente](how-to-create-group-machine-dependencies-agentless.md).
    - Se você não desejar usar esses recursos, poderá clicar no controle deslizante para ignorar a etapa. Você poderá reverter a intenção a qualquer momento posterior.
    - Examine as credenciais necessárias para a [descoberta do aplicativo](migrate-support-matrix-vmware.md#application-discovery-requirements) ou para a [análise de dependência sem agente](migrate-support-matrix-vmware.md#dependency-analysis-requirements-agentless).

5. Clique em **Iniciar descoberta** para iniciar a descoberta da VM. Depois que a descoberta for iniciada com êxito, você poderá verificar o status da descoberta com relação ao endereço IP/FQDN do vCenter Server na tabela.

A descoberta funciona da seguinte maneira:
- São necessários cerca de 15 minutos para que os metadados das VMs descobertas sejam exibidos no portal.
- A descoberta de aplicativos, funções e recursos instalados leva algum tempo. A duração depende do número de VMs sendo descobertas. Para as VMs 500, leva aproximadamente uma hora para o inventário de aplicativos aparecer no portal de Migrações para Azure.

### <a name="verify-vms-in-the-portal"></a>Verifique as VMs no portal

Após a descoberta, você poderá verificar se as VMs são exibidas no portal do Azure:

1. Abra o painel das Migrações para Azure.
2. Na página **Migrações para Azure – Servidores** > **Migrações para Azure: Avaliação de Servidor**, selecione o ícone que exibe a contagem de **Servidores descobertos**.

## <a name="set-up-an-assessment"></a>Configurar uma avaliação

Você pode criar dois tipos de avaliações usando a Avaliação de Servidor das Migrações para Azure:

**Tipo de avaliação** | **Detalhes**
--- | --- 
**VM do Azure** | Avaliações para migrar servidores locais para máquinas virtuais do Azure. <br/><br/> Avalie as [VMs do VMware](how-to-set-up-appliance-vmware.md), [VMs do Hyper-V](how-to-set-up-appliance-hyper-v.md) e [servidores físicos](how-to-set-up-appliance-physical.md) locais para migração para o Azure usando esse tipo de avaliação. [Saiba mais](concepts-assessment-calculation.md)
**AVS (Solução VMware no Azure)** | Avaliações para migrar servidores locais para a [AVS (Solução VMware no Azure)](../azure-vmware/introduction.md). <br/><br/> Avalie as [VMs do VMware](how-to-set-up-appliance-vmware.md) locais para migração para a AVS (Solução VMware no Azure) usando esse tipo de avaliação. [Saiba mais](concepts-azure-vmware-solution-assessment-calculation.md)

A avaliação do servidor fornece duas opções de critérios de dimensionamento:

**Critérios de dimensionamento** | **Detalhes** | **Dados**
--- | --- | ---
**Com base no desempenho** | Avaliações que fazem recomendações com base nos dados de desempenho coletados | **Avaliação da VM do Azure**: a recomendação de tamanho da VM é baseada nos dados de utilização da CPU e de memória.<br/><br/> A recomendação de tipo de disco (HDD/SSD Standard ou discos gerenciados Premium) é baseada na IOPS e na taxa de transferência dos discos locais.<br/><br/> **Avaliação da AVS (Solução VMware no Azure)** : a recomendação de nós da AVS é baseada nos dados de utilização da CPU e de memória.
**No estado em que se encontra localmente** | Avaliações que não usam dados de desempenho para fazer recomendações. | **Avaliação da VM do Azure**: a recomendação de tamanho da VM é baseada no tamanho da VM local<br/><br> O tipo de disco recomendado é baseado no que você seleciona na configuração de tipo de armazenamento para a avaliação.<br/><br/> **Avaliação da AVS (Solução VMware no Azure)** : a recomendação de nós da AVS é baseada no tamanho da VM local.

## <a name="run-an-assessment"></a>Ler uma avaliação

Execute uma *avaliação da VM do Azure* da seguinte maneira:

1. Examine as [melhores práticas](best-practices-assessment.md) para a criação de avaliações.
2. Na guia **Servidores**, no bloco **Migrações para Azure: Avaliação de Servidor**, selecione **Avaliar**.

   ![Localização do botão Avaliar](./media/tutorial-assess-vmware/assess.png)

3. Em **Avaliar servidores**, selecione o tipo de avaliação como "VM do Azure", selecione a origem da descoberta e especifique o nome da avaliação.

    ![Noções básicas sobre a avaliação](./media/tutorial-assess-vmware/assess-servers-azurevm.png)
 
4. Selecione **Exibir tudo** e examine as propriedades da avaliação.

   ![Propriedades de avaliação](./media/tutorial-assess-vmware/view-all.png)

5. Clique em **avançar** para **Selecionar computadores para avaliação**. Em **Selecionar ou criar um grupo**, selecione **Criar** e especifique um nome de grupo. Um grupo reúne uma ou mais VMs para avaliação.
6. Em **Adicionar computadores ao grupo**, selecione as VMs a serem adicionadas ao grupo.
7. Clique em **avançar** para **Examinar + criar avaliação** para examinar os detalhes da avaliação.
8. Selecione **Criar Avaliação** para criar o grupo e executar a avaliação.

   ![Avaliar servidores](./media/tutorial-assess-vmware/assessment-create.png)

8. Após a criação da avaliação, veja-a em **Servidores** > **Migrações para Azure: Avaliação de Servidor** > **Avaliações**.
9. Selecione **Exportar avaliação** para baixá-la como um arquivo do Excel.

Se quiser executar uma **Avaliação da AVS (Solução VMware no Azure)** , siga as etapas mencionadas [aqui](how-to-create-azure-vmware-solution-assessment.md).


## <a name="review-an-assessment"></a>Examinar uma avaliação

Uma avaliação descreve:

- **Preparação para o Azure**: indica se as VMs são adequadas para a migração para o Azure.
- **Estimativa de custo mensal**: os custos mensais estimados de computação e armazenamento para execução das VMs no Azure.
- **Estimativa de custo de armazenamento mensal**: custos estimados para o armazenamento em disco após a migração.

Para exibir uma avaliação:

1. Em **Metas de migração** > **Servidores**, selecione **Avaliações** em **Migrações para Azure: Avaliação de Servidor**.
2. Em **Avaliações**, selecione uma avaliação para abri-la.

   ![Resumo da avaliação](./media/tutorial-assess-vmware/assessment-summary.png)

### <a name="review-azure-readiness"></a>Examinar a Preparação para o Azure

1. Em **Preparação para o Azure**, verifique se as VMs estão prontas para a migração para o Azure.
2. Examine o status da VM:
    - **Pronto para o Azure**: Usado quando as Migrações para Azure recomendam um tamanho de VM e estimativas de custo para as VMs na avaliação.
    - **Pronto com condições**: mostra os problemas e a correção sugerida.
    - **Não está pronto para o Azure**: mostra os problemas e a correção sugerida.
    - **Preparação desconhecida**: Usada quando as Migrações para Azure não podem avaliar a preparação devido a problemas de disponibilidade de dados.

3. Selecione um status de **Preparação para o Azure**. Você pode exibir os detalhes de preparação da VM. Você também pode fazer drill down para ver os detalhes da VM, incluindo as configurações de computação, armazenamento e rede.

### <a name="review-cost-details"></a>Examinar os detalhes de custo

O resumo da avaliação mostra o custo estimado de computação e armazenamento da execução das VMs no Azure. Os custos são agregados para todas as VMs no grupo avaliado. Você pode fazer drill down para ver os detalhes de custo de VMs específicas.

> [!NOTE]
> As estimativas de custo são baseadas nas recomendações de tamanho para um computador, seus discos e suas propriedades. As estimativas referem-se à execução das VMs locais como VMs de IaaS. A Avaliação de Servidor das Migrações para Azure não considera os custos de PaaS ou SaaS.

Os custos de armazenamento agregados para o grupo avaliado são divididos em diferentes tipos de discos de armazenamento. 

### <a name="review-confidence-rating"></a>Revisar classificação de confiança

A Avaliação de Servidor de Migrações para Azure atribui uma classificação de confiança a uma avaliação baseada em desempenho, de uma estrela (a mais baixa) a cinco estrelas (a mais alta).

![Classificação de confiança](./media/tutorial-assess-vmware/confidence-rating.png)

A classificação de confiança ajuda você a estimar a confiabilidade das recomendações de tamanho da avaliação. A classificação é baseada na disponibilidade dos pontos de dados necessários para calcular a avaliação:

**Disponibilidade do ponto de dados** | **Classificação de confiança**
--- | ---
0%-20% | 1 estrela
21%-40% | 2 estrelas
41%-60% | 3 estrelas
61%-80% | 4 estrelas
81%-100% | 5 estrelas

[Saiba mais sobre as melhores práticas](best-practices-assessment.md#best-practices-for-confidence-ratings) de classificações de confiança.

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você configurou um dispositivo das Migrações para Azure. Você também criou e examinou uma avaliação.

Para saber como migrar VMs VMware para o Azure usando a Migração de Servidor de Migrações para Azure, continue para o terceiro tutorial na série:

> [!div class="nextstepaction"]
> [Migrar VMs do VMware](./tutorial-migrate-vmware.md)
