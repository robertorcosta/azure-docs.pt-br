---
title: Avaliar VMs do VMware usando a avaliação de servidor das Migrações para Azure
description: Descreve como avaliar as VMs locais do VMware para migração para o Azure usando a Avaliação de Servidor de Migrações para Azure.
ms.topic: tutorial
ms.date: 06/03/2020
ms.custom: mvc
ms.openlocfilehash: 231daff5972e9b2f115df9e6184c43a553f55b83
ms.sourcegitcommit: 99d016949595c818fdee920754618d22ffa1cd49
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/15/2020
ms.locfileid: "84771301"
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
1. Em **Serviços**, selecione **Migrações para Azure**.
1. Em **Visão Geral**, em **Descobrir, avaliar e migrar servidores**, selecione **Avaliar e migrar servidores**.

   ![Botão para avaliar e migrar servidores](./media/tutorial-assess-vmware/assess-migrate.png)

1. Em **Introdução**, selecione **Adicionar ferramentas**.
1. Em **Migrar projeto**, selecione sua assinatura do Azure e crie um grupo de recursos, caso não tenha um.     
1. Em **Detalhes do Projeto**, especifique o nome do projeto e a geografia em que deseja criá-lo. Examine as geografias compatíveis para [nuvens públicas](migrate-support-matrix.md#supported-geographies-public-cloud) e [governamentais](migrate-support-matrix.md#supported-geographies-azure-government).

   ![Caixas para nome e região do projeto](./media/tutorial-assess-vmware/migrate-project.png)

1. Selecione **Avançar**.
1. Em **Selecionar ferramenta de avaliação**, selecione **Migrações para Azure: Avaliação de Servidor** > **Avançar**.

   ![Seleção para a ferramenta de Avaliação do Servidor](./media/tutorial-assess-vmware/assessment-tool.png)

1. Em **Selecionar ferramenta de migração**, selecione **Ignorar a adição de uma ferramenta de migração por enquanto** > **Avançar**.
1. Em **Examinar + adicionar ferramentas**, examine as configurações e selecione **Adicionar ferramentas**.
1. Aguarde alguns minutos até que o projeto das Migrações para Azure seja implantado. Você será levado para a página do projeto. Caso não veja o projeto, acesse-o em **Servidores** no painel das Migrações para Azure.

## <a name="set-up-the-azure-migrate-appliance"></a>Configurar o dispositivo das Migrações para Azure

O recurso Migrações para Azure: Avaliação do Servidor usa um dispositivo leve de Migrações para Azure. O dispositivo executa a descoberta de VM e envia os metadados de VM e os dados de desempenho para as Migrações para Azure. O dispositivo pode ser configurado de várias maneiras.

- Configurar em uma VM do VMware usando um modelo OVA baixado. Esse é o método usado neste tutorial.
- Configurar em uma VM do VMware ou computador físico com um script do instalador do PowerShell. [Esse método](deploy-appliance-script.md) deverá ser usado se você não puder configurar uma VM usando um modelo OVA ou se você estiver no Azure Government.

Depois de criar o dispositivo, você verifica se é possível conectá-lo ao Migrações para Azure: Avaliação do Servidor, configurá-lo pela primeira vez e registrá-lo com o projeto de Migrações para Azure.


### <a name="download-the-ova-template"></a>Baixar o modelo OVA

1. Em **Metas de Migração** > **Servidores** > **Migrações para Azure: Avaliação de Servidor**, selecione **Descobrir**.
1. Em **Descobrir computadores** > **Os computadores estão virtualizados?** , selecione **Sim, com o hipervisor do VMware vSphere**.
1. Selecione **Baixar** para baixar o arquivo de modelo OVA.

   ![Seleções para baixar um arquivo OVA](./media/tutorial-assess-vmware/download-ova.png)

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
        VMware (10,9 GB) | [Última versão](https://aka.ms/migrate/appliance/vmware) | cacbdaef927fe5477fa4e1f494fcb7203cbd6b6ce7402b79f234bc0fe69663dd

    - Para o Azure Government:
    
        **Algoritmo** | **Download** | **SHA256**
        --- | --- | ---
        VMware (63,1 MB) | [Última versão](https://go.microsoft.com/fwlink/?linkid=2120300&clcid=0x409 ) | 3d5822038646b81f458d89d706832c0a2c0e827bfa9b0a55cc478eaf2757a4de


### <a name="create-the-appliance-vm"></a>Criar a VM do dispositivo

Importe o arquivo baixado e crie uma VM:

1. No console do cliente do vSphere, selecione **Arquivo** > **Implantar o Modelo de OVF**.

   ![Comando de menu para implantar um modelo de OVF](./media/tutorial-assess-vmware/deploy-ovf.png)

1. No Assistente do Modelo de Implantação de OVF > **Origem**, especifique o local do arquivo OVA.
1. Em **Nome** e **Local**, especifique um nome amigável para a VM. Selecione o objeto de inventário no qual a VM será hospedada.
1. Em **Host/Cluster**, especifique o host ou o cluster no qual a VM será executada.
1. Em **Armazenamento**, especifique o destino de armazenamento para a VM.
1. Em **Formato de Disco**, especifique o tipo e o tamanho do disco.
1. Em **Mapeamento de Rede**, especifique a rede à qual a VM se conectará. A rede precisa ter conectividade com a Internet para enviar metadados para a Avaliação de Servidor das Migrações para Azure.
1. Examine e confirme as configurações e selecione **Concluir**.

## <a name="verify-appliance-access-to-azure"></a>Verificar o acesso do dispositivo ao Azure

Verifique se a VM do dispositivo pode se conectar às URLs do Azure para as nuvens [pública](migrate-appliance.md#public-cloud-urls) e [governamental](migrate-appliance.md#government-cloud-urls).

### <a name="configure-the-appliance"></a>Configurar o dispositivo

Configure o dispositivo pela primeira vez.

> [!NOTE]
> Se você configurar o dispositivo usando um [script do PowerShell](deploy-appliance-script.md), em vez do OVA baixado, as duas primeiras etapas neste procedimento não serão relevantes.

1. No console do cliente do vSphere, clique com o botão direito do mouse na VM e selecione **Abrir console**.
1. Forneça o idioma, o fuso horário e a senha do dispositivo.
1. Abra um navegador em qualquer computador que possa se conectar à VM e abra a URL do aplicativo Web do dispositivo: **https://*nome do dispositivo ou endereço IP*: 44368**.

   Como alternativa, você pode abrir o aplicativo na área de trabalho do dispositivo selecionando o atalho do aplicativo.
1. No aplicativo Web > **Configurar os pré-requisitos**, faça o seguinte:
   - **Licença**: Aceite os termos de licença e leia as informações de terceiros.
   - **Conectividade**: o aplicativo verifica se a VM tem acesso à Internet. Se a VM usar um proxy:
     - Selecione **Configurações de proxy** e especifique o endereço proxy e a porta de escuta, no formato http://ProxyIPAddress ou http://ProxyFQDN.
     - Especifique as credenciais caso o proxy exija autenticação.
     - Há suporte apenas para o proxy HTTP.
   - **Sincronização do horário**: O horário no dispositivo deve ser sincronizado com o horário na Internet para que a descoberta funcione corretamente.
   - **Instalar as atualizações**: O dispositivo garante que as atualizações mais recentes serão instaladas.
   - **Instalar o VDDK**: O dispositivo verifica se o VDDK (Kit de Desenvolvimento de Disco Virtual) do VMware vSphere está instalado. Se ele não estiver instalado, baixe o VDDK 6.7 da VMware e extraia o conteúdo do zip baixado para a localização especificada no dispositivo.

     A Migração de Servidor de Migrações para Azure usa o VDDK para replicar computadores durante a migração para o Azure.       

### <a name="register-the-appliance-with-azure-migrate"></a>Registrar o dispositivo nas Migrações para Azure

1. Selecione **Fazer logon**. Se essa opção não for exibida, verifique se você desabilitou o bloqueador de pop-ups no navegador.
1. Na nova guia, entre usando seu nome de usuário e senha do Azure.
   
   Não há suporte para a entrada com um PIN.
1. Depois de entrar com êxito, volte para o aplicativo Web.
1. Selecione a assinatura na qual o projeto das Migrações para Azure foi criado e, em seguida, selecione o projeto.
1. Especifique um nome para o dispositivo. O nome deve ser alfanumérico com 14 caracteres ou menos.
1. Selecione **Registrar**.


## <a name="start-continuous-discovery"></a>Iniciar a descoberta contínua

O dispositivo precisa se conectar ao vCenter Server para descobrir a configuração e os dados de desempenho das VMs.

### <a name="specify-vcenter-server-details"></a>Especificar detalhes do vCenter Server
1. Em **Especificar detalhes do vCenter Server**, especifique o nome (FQDN) ou o endereço IP da instância do vCenter Server. Você pode manter a porta padrão ou especificar uma porta personalizada na qual o vCenter Server escutará.
2. Em **Nome de usuário** e **Senha**, especifique as credenciais de conta do vCenter Server que o dispositivo usará para descobrir VMs na instância do vCenter Server. 

    - Você deve ter configurado uma conta com as permissões necessárias no [tutorial anterior](tutorial-prepare-vmware.md#set-up-permissions-for-assessment).
    - Se você quiser definir o escopo da descoberta para objetos específicos do VMware (data centers, clusters, uma pasta de clusters, hosts, uma pasta de hosts ou VMs individuais do vCenter Server), examine as instruções [neste artigo](set-discovery-scope.md) para restringir a conta usada por Migrações para Azure.

3. Selecione **Validar conexão** para garantir que o dispositivo possa se conectar ao vCenter Server.
4. Em **Descobrir aplicativos e dependências de VMs**, opcionalmente, clique em **Adicionar credenciais** e especifique o sistema operacional para o qual as credenciais são relevantes e o nome de usuário e a senha das credenciais. Clique em **Adicionar**.

    - Opcionalmente, adicione credenciais aqui se você tiver criado uma conta a ser usada para o [recurso de descoberta de aplicativo](how-to-discover-applications.md) ou o [recurso de análise de dependência sem agente](how-to-create-group-machine-dependencies-agentless.md).
    - Se você não estiver usando esses recursos, poderá ignorar essa configuração.
    - Examine as credenciais necessárias para [descoberta do aplicativo](migrate-support-matrix-vmware.md#application-discovery-requirements) ou para [análise sem agente](migrate-support-matrix-vmware.md#dependency-analysis-requirements-agentless).

5. **Salve e inicie a descoberta** para iniciar a descoberta de VM.

A descoberta funciona da seguinte maneira:
- São necessários cerca de 15 minutos para que os metadados das VMs descobertas sejam exibidos no portal.
- A descoberta de aplicativos, funções e recursos instalados leva algum tempo. A duração depende do número de VMs sendo descobertas. Para as VMs 500, leva aproximadamente uma hora para o inventário de aplicativos aparecer no portal de Migrações para Azure.

### <a name="verify-vms-in-the-portal"></a>Verifique as VMs no portal

Após a descoberta, você poderá verificar se as VMs são exibidas no portal do Azure:

1. Abra o painel das Migrações para Azure.
1. Na página **Migrações para Azure – Servidores** > **Migrações para Azure: Avaliação de Servidor**, selecione o ícone que exibe a contagem de **Servidores descobertos**.

## <a name="set-up-an-assessment"></a>Configurar uma avaliação

Você pode criar dois tipos de avaliações usando a Avaliação de Servidor das Migrações para Azure:

**Avaliação** | **Detalhes** | **Dados**
--- | --- | ---
**Com base no desempenho** | Avaliações com base nos dados de desempenho coletados | **Tamanho de VM recomendado**: com base nos dados de utilização da CPU e de memória.<br/><br/> **Tipo de disco recomendado (disco gerenciado Standard ou Premium)** : com base na IOPS e na taxa de transferência dos discos locais.
**Como local** | Avaliações com base no dimensionamento local | **Tamanho de VM recomendado**: Com base no tamanho da VM local.<br/><br> **Tipo de disco recomendado**: Com base na configuração de tipo de armazenamento selecionada para a avaliação.

## <a name="run-an-assessment"></a>Ler uma avaliação

Execute uma avaliação da seguinte maneira:

1. Examine as [melhores práticas](best-practices-assessment.md) para a criação de avaliações.
1. Na guia **Servidores**, no bloco **Migrações para Azure: Avaliação de Servidor**, selecione **Avaliar**.

   ![Localização do botão Avaliar](./media/tutorial-assess-vmware/assess.png)

1. Em **Avaliar servidores**, especifique um nome para a avaliação.
1. Selecione **Exibir tudo** e examine as propriedades da avaliação.

   ![Propriedades de avaliação](./media/tutorial-assess-vmware/view-all.png)

1. Em **Selecionar ou criar um grupo**, selecione **Criar** e especifique um nome de grupo. Um grupo reúne uma ou mais VMs para avaliação.
1. Em **Adicionar computadores ao grupo**, selecione as VMs a serem adicionadas ao grupo.
1. Selecione **Criar Avaliação** para criar o grupo e executar a avaliação.

   ![Avaliar servidores](./media/tutorial-assess-vmware/assessment-create.png)

1. Após a criação da avaliação, veja-a em **Servidores** > **Migrações para Azure: Avaliação de Servidor** > **Avaliações**.
1. Selecione **Exportar avaliação** para baixá-la como um arquivo do Excel.

## <a name="review-an-assessment"></a>Examinar uma avaliação

Uma avaliação descreve:

- **Preparação para o Azure**: indica se as VMs são adequadas para a migração para o Azure.
- **Estimativa de custo mensal**: os custos mensais estimados de computação e armazenamento para execução das VMs no Azure.
- **Estimativa de custo de armazenamento mensal**: custos estimados para o armazenamento em disco após a migração.

Para exibir uma avaliação:

1. Em **Metas de migração** > **Servidores**, selecione **Avaliações** em **Migrações para Azure: Avaliação de Servidor**.
1. Em **Avaliações**, selecione uma avaliação para abri-la.

   ![Resumo da avaliação](./media/tutorial-assess-vmware/assessment-summary.png)

### <a name="review-azure-readiness"></a>Examinar a Preparação para o Azure

1. Em **Preparação para o Azure**, verifique se as VMs estão prontas para a migração para o Azure.
1. Examine o status da VM:
    - **Pronto para o Azure**: Usado quando as Migrações para Azure recomendam um tamanho de VM e estimativas de custo para as VMs na avaliação.
    - **Pronto com condições**: mostra os problemas e a correção sugerida.
    - **Não está pronto para o Azure**: mostra os problemas e a correção sugerida.
    - **Preparação desconhecida**: Usada quando as Migrações para Azure não podem avaliar a preparação devido a problemas de disponibilidade de dados.

1. Selecione um status de **Preparação para o Azure**. Você pode exibir os detalhes de preparação da VM. Você também pode fazer drill down para ver os detalhes da VM, incluindo as configurações de computação, armazenamento e rede.

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
