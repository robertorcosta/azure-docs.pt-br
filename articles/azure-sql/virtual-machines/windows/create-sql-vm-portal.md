---
title: Provisionar máquina virtual com o portal do Azure
description: Este guia de instruções descreve as opções para a criação de máquinas virtuais do Windows SQL Server 2017 no portal do Azure.
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
manager: craigg
tags: azure-resource-manager
ms.assetid: 1aff691f-a40a-4de2-b6a0-def1384e086e
ms.service: virtual-machines-sql
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: infrastructure-services
ms.date: 11/07/2019
ms.author: mathoma
ms.reviewer: jroth
ms.custom: seo-lt-2019
ms.openlocfilehash: 0c534b87250a4cc17ec9dd67c7a4ab5af19b3384
ms.sourcegitcommit: 053e5e7103ab666454faf26ed51b0dfcd7661996
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/27/2020
ms.locfileid: "84032797"
---
# <a name="how-to-provision-a-windows-sql-server-virtual-machine-in-the-azure-portal"></a>Como provisionar uma máquina virtual do Windows SQL Server no portal do Azure
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

Este guia oferece detalhes sobre as diferentes opções disponíveis quando você cria uma máquina virtual do Windows SQL Server no portal do Azure. Este artigo aborda mais opções de configuração que o [guia de início rápido de VM do SQL Server](sql-vm-create-portal-quickstart.md), que aborda mais uma possível tarefa de provisionamento. 

Use este guia para criar sua própria VM do SQL Server. Ou use-o como uma referência para as opções disponíveis no portal do Azure.

> [!TIP]
> Em caso de dúvidas sobre máquinas virtuais do SQL Server, consulte as [Perguntas frequentes](frequently-asked-questions-faq.md).

Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="sql-server-virtual-machine-gallery-images"></a><a id="select"></a> Imagens de galeria de máquinas virtuais SQL Server

Quando você cria uma máquina virtual SQL Server, pode selecionar uma das várias imagens pré-configuradas da galeria de máquinas virtuais. As etapas a seguir demonstram como selecionar uma das imagens do SQL Server 2017.

1. Selecione **SQL do Azure** no menu à esquerda do portal do Azure. Se o **SQL do Azure** não estiver na lista, selecione **Todos os serviços** e, em seguida, digite SQL do Azure na caixa de pesquisa. (Opcional) Selecione a estrela ao lado de **SQL do Azure** para marcá-lo como favorito e adicioná-lo como um item no menu de navegação à esquerda. 
1. Selecione **+ Adicionar** para abrir a página **Selecionar opção de implantação do SQL**. Você pode ver informações adicionais selecionando **Mostrar detalhes**. 
1. Digite `2017` na caixa de pesquisa de imagem de SQL Server no bloco **Máquinas virtuais do SQL** e, em seguida, selecione **Licença Gratuita do SQL Server: SQL Server 2017 Developer no Windows Server 2016** da lista suspensa. 


   ![Selecionar imagem da VM do SQL](./media/create-sql-vm-portal/select-sql-vm-image-portal.png)

   > [!TIP]
   > A edição Developer é usada neste tutorial porque é uma edição completa e gratuita do SQL Server para fins de teste de desenvolvimento. Você paga apenas o custo da execução da VM. No entanto, você é livre para escolher qualquer uma das imagens para usar neste passo a passo. Para obter uma descrição das imagens disponíveis, confira a [visão geral das máquinas virtuais SQL Server Windows](sql-server-on-azure-vm-iaas-what-is-overview.md#payasyougo).

   > [!TIP]
   > Os custos de licenciamento do SQL Server são incorporados nos preços por segundo da VM criada e varia por edição e núcleos. No entanto, o a edição SQL Server Developer é gratuito para desenvolvimento/teste (não para produção) e o SQL Express é gratuito para cargas de trabalho leves (menores que 1 GB de memória e menores que 10 GB de armazenamento). Você também pode BYOL (trazer sua própria licença) e pagar somente pela máquina virtual. Os nomes de imagem têm o prefixo {BYOL}. 
   >
   > Para obter mais informações sobre essas opções, consulte [Diretrizes para os preço das VMs do Azure do SQL Server](pricing-guidance.md).


1. Selecione **Criar**.


## <a name="1-configure-basic-settings"></a>1. Definir as configurações básicas


Na folha **Informações Básicas**, forneça as seguintes informações:

* Em **Detalhes do Projeto**, verifique se a assinatura correta está selecionada. 
*  Na seção **Grupo de recursos** selecione um grupo de recursos existente na lista ou então escolha **Criar** para criar um grupo de recursos. Um grupo de recursos é uma coleção de recursos relacionados no Azure (máquinas virtuais, contas de armazenamento, redes virtuais etc.). 

    ![Subscription](./media/create-sql-vm-portal/basics-project-details.png)

  > [!NOTE]
  > O uso de um novo grupo de recursos é útil se você estiver apenas testando ou aprendendo sobre implantações do SQL Server no Azure. Após concluir o teste, exclua o grupo de recursos para excluir automaticamente a VM e todos os recursos associados a esse grupo de recursos. Para saber mais sobre os grupos de recursos, confira [Visão geral do Azure Resource Manager](../../../active-directory-b2c/overview.md).


* Em **Detalhes da instância**:
    1. Digite um **Nome de máquina virtual** exclusivo.  
    1. Escolha uma localização para sua **região**. 
    1. Para a finalidade deste guia, mantenha **Opções de disponibilidade** definido como _Nenhuma redundância de infraestrutura necessária_. Para obter mais informações sobre as opções de disponibilidade, consulte [Disponibilidade](../../../virtual-machines/windows/availability.md). 
    1. Na lista **Imagem**, selecione _Licença Gratuita do SQL Server: SQL Server 2017 Developer no Windows Server 2016_.  
    1. Escolha **Alterar tamanho** para o **Tamanho** da máquina virtual e selecione a oferta **A2 Básico**. Limpe os recursos assim que terminar de trabalhar com eles para evitar encargos inesperados. Para as cargas de trabalho de produção, consulte os tamanhos recomendados de máquina e a configuração em [Práticas recomendadas de desempenho para o SQL Server em Máquinas Virtuais do Azure](performance-guidelines-best-practices.md).

    ![Detalhes da instância](./media/create-sql-vm-portal/basics-instance-details.png)

> [!IMPORTANT]
> O custo mensal estimado exibido na janela **Escolher um tamanho** não inclui os custos de licenciamento do SQL Server. Essa estimativa é o custo apenas da VM. Para as edições Express e Developer do SQL Server, isso é o custo estimado total. Para outras edições, confira a [Página de preços das Máquinas Virtuais do Windows](https://azure.microsoft.com/pricing/details/virtual-machines/windows/) e selecione a edição de destino do SQL Server. Confira também [Diretrizes de preços para VMs do Azure do SQL Server](pricing-guidance.md) e [Tamanhos de máquinas virtuais](../../../virtual-machines/windows/sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

* Em **Conta de administrador**, forneça um nome de usuário e uma senha. A senha deve ter no mínimo 12 caracteres e atender a [requisitos de complexidade definidos](../../../virtual-machines/windows/faq.md#what-are-the-password-requirements-when-creating-a-vm).

   ![Conta de administrador](./media/create-sql-vm-portal/basics-administrator-account.png)

* Em **Regras de portas de entrada**, escolha **Permitir portas selecionadas** e, em seguida, selecione **RDP (3389)** na lista suspensa. 

   ![Regras de porta de entrada](./media/create-sql-vm-portal/basics-inbound-port-rules.png)


## <a name="2-configure-optional-features"></a>2. Configurar os recursos opcionais

### <a name="disks"></a>Discos

Na guia **Discos**, configure as opções de disco. 

* Em **Tipo de disco do SO**, selecione o tipo de disco desejado para o seu SO na lista suspensa. O Premium é recomendado para sistemas de produção, mas não está disponível para uma VM Básica. Para utilizar SSD Premium, altere o tamanho da máquina virtual. 
* Em **Avançado**, selecione **Sim** em usar **Managed Disks**.

   > [!NOTE]
   > A Microsoft recomenda os Managed Disks para o SQL Server. O Managed Disks lida com o armazenamento nos bastidores. Além disso, quando as máquinas virtuais com Managed Disks estão no mesmo conjunto de disponibilidade, o Azure distribui os recursos de armazenamento para fornecer a redundância apropriada. Para saber mais, veja [Visão geral dos Azure Managed Disks](../../../virtual-machines/windows/managed-disks-overview.md). Para obter informações específicas sobre os discos gerenciados em um conjunto de disponibilidade, consulte [Usar managed disks para VMs no conjunto de disponibilidade](../../../virtual-machines/windows/manage-availability.md).

![Configurações de disco de VM do SQL](./media/create-sql-vm-portal/azure-sqlvm-disks.png)
  
  
### <a name="networking"></a>Rede

Na guia **Rede**, configure as opções de rede. 

* Você pode criar uma **rede virtual** ou usar uma VNet existente para sua VM do SQL Server. Designe também uma **sub-rede**. 

* Em **Grupo de segurança de rede da NIC**, selecione um grupo de segurança básico ou o grupo de segurança avançado. A escolha da opção Básico permite que você selecione portas de entrada para a VM do SQL Server (os mesmos valores que foram configurados na guia **Básico**). A seleção da opção Avançado permite que você escolha um grupo de segurança de rede existente ou crie um. 

* Você pode fazer outras alterações para configurações de rede ou manter os valores padrão.

![Configurações de Rede de VM do SQL](./media/create-sql-vm-portal/azure-sqlvm-networking.png)

#### <a name="monitoring"></a>Monitoramento

Na guia **Monitoramento**, configure o monitoramento e o desligamento automático. 

* O Azure habilita o **Diagnóstico de inicialização** por padrão com a mesma conta de armazenamento designada para a VM. Você pode alterar essas configurações aqui, bem como habilitar o **diagnóstico de convidado do SO**. 
* Você pode habilitar a **identidade gerenciada atribuída ao sistema** e o **desligamento automático** nessa guia também. 

![Configurações de gerenciamento de VM do SQL](./media/create-sql-vm-portal/azure-sqlvm-management.png)


## <a name="3-configure-sql-server-settings"></a>3. Definir as configurações do SQL Server

Na guia **Configurações do SQL Server**, defina configurações e otimizações específicas para o SQL Server. As configurações possíveis para o SQL Server incluem as seguintes:

| Configuração |
| --- |
| [Conectividade](#connectivity) |
| [Autenticação](#authentication) |
| [Integração do Cofre da Chave do Azure](#azure-key-vault-integration) |
| [Configuração de armazenamento](#storage-configuration) |
| [Aplicação de patch automatizada](#automated-patching) |
| [Backup Automatizado](#automated-backup) |
| [Serviços de Machine Learning](#machine-learning-services) |


### <a name="connectivity"></a>Conectividade

Em **Conectividade SQL**, especifique o tipo de acesso desejado para a instância do SQL Server nesta VM. Para este passo a passo, escolha **Pública (Internet)** para permitir conexões com o SQL Server a partir de máquinas ou serviços na Internet. Com essa opção selecionada, o Azure configura automaticamente o firewall e o grupo de segurança de rede para permitir o tráfego na porta selecionada.

> [!TIP]
> Por padrão, o SQL Server atende em uma porta bem conhecida, **1433**. Para ter uma maior segurança, altere a porta na caixa de diálogo anterior para atender em uma porta não padrão, como 1401. Se você alterar a porta, deverá se conectar usando essa porta em qualquer ferramenta do cliente, como o SSMS.

![Segurança da VM do SQL](./media/create-sql-vm-portal/azure-sqlvm-security.png)

Para conectar-se ao SQL Server pela Internet, você precisará habilitar a Autenticação do SQL Server, que está descrita na próxima seção.

Se você preferir não permitir conexões com o Mecanismo de Banco de Dados pela internet, escolha uma das seguintes opções:

* **Local (apenas dentro da VM)** para permitir conexões com o SQL Server somente de dentro da VM.
* **Privada (dentro da Rede Virtual)** para permitir conexões com o SQL Server a partir de computadores ou serviços na mesma rede virtual.

Em geral, melhore a segurança escolhendo a conectividade mais restritiva que seu cenário permite. No entanto, todas as opções são protegidas por meio de regras do Grupo de Segurança de Rede e por meio da Autenticação do SQL/Windows. Você pode editar o Grupo de Segurança de Rede depois da VM ser criada. Para obter mais informações, veja [Considerações sobre segurança para o SQL Server em Máquinas Virtuais do Azure](security-considerations-best-practices.md).



### <a name="authentication"></a>Autenticação

Se você precisar da Autenticação do SQL Server, clique em **Habilitar** em **Autenticação do SQL** na guia **Configurações do SQL Server**.

![Autenticação do SQL Server](./media/create-sql-vm-portal/azure-sqlvm-authentication.png)

> [!NOTE]
> Se você pretende acessar o SQL Server pela Internet (a opção de conectividade Pública), habilite a autenticação do SQL aqui. O acesso público ao SQL Server exige o uso da Autenticação do SQL.

Se você habilitar a Autenticação do SQL Server, especifique um **Nome de logon** e **Senha**. Esse nome de logon é configurado como um logon de Autenticação do SQL Server e membro da função de servidor fixa **sysadmin**. Para saber mais sobre os Modos de autenticação, consulte [Escolher um modo de autenticação](https://docs.microsoft.com/sql/relational-databases/security/choose-an-authentication-mode).

Se você não habilitar a Autenticação do SQL Server, poderá usar a conta local de Administrador na VM para se conectar à instância do SQL Server.


### <a name="azure-key-vault-integration"></a>Integração do Cofre da Chave do Azure

Para armazenar segredos de segurança no Azure para criptografia, selecione **Configurações do SQL Server** e role para baixo até **Integração do Azure Key Vault**. Selecione **Habilitar** e preencha as informações solicitadas. 

![Integração do Cofre da Chave do Azure](./media/create-sql-vm-portal/azure-sqlvm-akv.png)

A tabela a seguir lista os parâmetros necessários para configurar a integração do Cofre da Chave do Azure.

| PARÂMETRO | DESCRIPTION | EXEMPLO |
| --- | --- | --- |
| **URL do cofre da chave** |O local do cofre da chave. |https:\//contosokeyvault.vault.azure.net/ |
| **Nome de entidade** |Nome de entidade de serviço do Active Directory do Azure Ele também é chamado de ID do Cliente. |fde2b411-33d5-4e11-af04eb07b669ccf2 |
| **Segredo da entidade** |Segredo da entidade de serviço do Azure Active Directory. O segredo também é chamado de Segredo do Cliente. |9VTJSQwzlFepD8XODnzy8n2V01Jd8dAjwm/azF1XDKM= |
| **Nome da credencial** |**Nome da credencial**: A Integração AKV cria uma credencial no SQL Server, permitindo que a VM tenha acesso ao cofre da chave. Escolha um nome para essa credencial. |mycred1 |

Para saber mais, consulte [Configurar a Integração do Cofre de Chaves do Azure para o SQL nas VMs do Azure](azure-key-vault-integration-configure.md).

### <a name="storage-configuration"></a>Configuração de armazenamento

Na guia **Configurações do SQL Server**, em **Configuração de armazenamento**, selecione **Alterar configuração** para abrir a página Configuração de Armazenamento Otimizado para Desempenho e especifique os requisitos de armazenamento.

![Configuração do Armazenamento de VM do SQL](./media/create-sql-vm-portal/sql-vm-storage-configuration-provisioning.png)

Em **Armazenamento otimizado para**, escolha uma das seguintes opções:

* **Geral** é a configuração padrão e dá suporte à maioria das cargas de trabalho.
* **Processamento de transações** otimiza o armazenamento para cargas de trabalho OLTP tradicionais do banco de dados.
* **Data warehouse** otimiza o armazenamento para as cargas de trabalho de análise e emissão de relatórios.

![Configuração do Armazenamento de VM do SQL](./media/create-sql-vm-portal/sql-vm-storage-configuration.png)

Você pode optar por manter os valores padrão ou alterar manualmente a topologia do armazenamento para atender às suas necessidades de IOPS. Para obter mais informações, confira [configuração de armazenamento](storage-configuration.md). 

### <a name="sql-server-license"></a>Licença do SQL Server
Se você for um cliente do Software Assurance, poderá utilizar o [Benefício Híbrido do Azure](https://azure.microsoft.com/pricing/hybrid-benefit/) para trazer sua própria licença do SQL Server e salvar os recursos. 

![Licença de VM do SQL](./media/create-sql-vm-portal/azure-sqlvm-license.png)

### <a name="automated-patching"></a>Aplicação de patch automatizada

**Automated patching** está habilitada por padrão. A aplicação de patch automatizada permite que o Azure aplique patches automaticamente no SQL Server e no sistema operacional. Especifique um dia da semana, hora e duração de um período de manutenção. O Azure realiza a aplicação de patch na janela de manutenção. O agendamento do período de manutenção usa a localidade da VM para a hora. Se você não quiser que o Azure aplique automaticamente o patch no SQL Server e no sistema operacional, clique em **Desabilitar**.  

![Aplicação de patch automatizada de VM do SQL](./media/create-sql-vm-portal/azure-sqlvm-automated-patching.png)

Para saber mais, consulte [Aplicação de Patch Automatizada para SQL Server nas Máquinas Virtuais do Azure](automated-patching.md).

### <a name="automated-backup"></a>Backup Automatizado

Habilite backups automáticos do banco de dados para todos os bancos de dados em **Backup automatizado**. O backup automatizado está desabilitado por padrão.

Ao habilitar o backup automatizado do SQL, é possível definir as seguintes configurações:

* Período de retenção (dias) para backups
* Conta de armazenamento a ser usada para backups
* Opção de criptografia e senha para backups
* Bancos de dados do sistema de backup
* Configurar agendamento de backup

Para criptografar o backup, clique em **Habilitar**. Em seguida, especifique a **Senha**. O Azure cria um certificado para criptografar os backups e usa a senha especificada para proteger esse certificado. Por padrão, a agenda é definido automaticamente, mas você pode criar uma agenda manual selecionando **Manual**. 

![Backups automatizados da VM do SQL](./media/create-sql-vm-portal/automated-backup.png)

Para obter mais informações, veja [Backup Automatizado para o SQL Server em Máquinas Virtuais do Azure](automated-backup-sql-2014.md).


### <a name="machine-learning-services"></a>Serviços de Machine Learning

Você tem a opção de habilitar os [Serviços de Machine Learning](/sql/advanced-analytics/). Essa opção permite que você use machine learning com Python e R no SQL Server 2017. Selecione **Habilitar** na janela **Configurações do SQL Server**.


## <a name="4-review--create"></a>4. Examinar + criar

Na guia **Examinar + criar**, examine o resumo e selecione **Criar** para criar o SQL Server, o grupo de recursos e os recursos específicos dessa VM.

Você pode monitorar a implantação no portal do Azure. O botão **Notificações** na parte superior da tela mostra o status básico da implantação.

> [!NOTE]
> Para você ter uma ideia sobre os tempos de implantação, eu implantei uma VM do SQL na região Leste dos EUA com as configurações padrão. Essa implantação de teste demorou aproximadamente 12 minutos para ser concluída. Mas sua implantação pode ser rápida ou mais lenta de acordo com sua região e com as configurações selecionadas.

## <a name="open-the-vm-with-remote-desktop"></a><a id="remotedesktop"></a> Abrir a VM usando a Área de Trabalho Remota

Use as etapas a seguir para se conectar à máquina virtual do SQL Server com a Área de Trabalho Remota:

[!INCLUDE [Connect to SQL Server VM with remote desktop](../../../../includes/virtual-machines-sql-server-remote-desktop-connect.md)]

Depois de se conectar à máquina virtual do SQL Server, você pode iniciar o SQL Server Management Studio e conectar-se à Autenticação do Windows usando suas credenciais de administrador local. Se você habilitou a Autenticação do SQL Server, também será possível se conectar com a Autenticação do SQL usando o logon e a senha do SQL configurados durante o provisionamento.

O acesso à máquina permite que você altere diretamente as configurações da máquina e do SQL Server com base em suas necessidades. Por exemplo, você pode definir as configurações do firewall ou alterar as configuração do SQL Server.

## <a name="connect-to-sql-server-remotely"></a><a id="connect"></a> Conectar-se ao SQL Server remotamente

Neste passo a passo, você selecionou o acesso **Público** para a máquina virtual e a **Autenticação do SQL Server**. Essas configurações definiram automaticamente a máquina virtual para permitir conexões do SQL Server de qualquer cliente pela internet (supondo que o logon do SQL esteja correto).

> [!NOTE]
> Se você não selecionou Público durante o provisionamento, poderá alterar as configurações de conectividade do SQL no portal após o provisionamento. Para obter mais informações, consulte [Alterar suas configurações de conectividade do SQL](ways-to-connect-to-sql.md#change).

As seções a seguir mostram como se conectar à sua instância de VM do SQL Server pela Internet.

[!INCLUDE [Connect to SQL Server in a VM Resource Manager](../../../../includes/virtual-machines-sql-server-connection-steps-resource-manager.md)]

  > [!NOTE]
  > Este exemplo usa a porta comum 1433. No entanto, esse valor precisará ser modificado se uma porta diferente (como 1401) tiver sido especificada durante a implantação da VM do SQL Server. 


## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre como usar o SQL Server no Azure, consulte [SQL Server nas Máquinas Virtuais do Azure](sql-server-on-azure-vm-iaas-what-is-overview.md) e as [Perguntas Frequentes](frequently-asked-questions-faq.md).
