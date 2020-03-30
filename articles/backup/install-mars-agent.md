---
title: Instale o agente Microsoft Azure Recovery Services (MARS)
description: Saiba como instalar o agente Mars (Microsoft Azure Recovery Services, serviços de recuperação do Microsoft Azure) para fazer backup das máquinas Windows.
ms.topic: conceptual
ms.date: 03/03/2020
ms.openlocfilehash: b9a6791709d5aff82d11bbf10e5f084fd8c1a000
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79247755"
---
# <a name="install-the-azure-backup-mars-agent"></a>Instale o agente MARS de backup do Azure

Este artigo explica como instalar o agente Microsoft Azure Recovery Services (MARS). A MARS também é conhecida como o agente de backup do Azure.

## <a name="about-the-mars-agent"></a>Sobre o agente MARS

O Azure Backup usa o agente MARS para fazer backup de arquivos, pastas e estado do sistema a partir de máquinas locais e VMs do Azure. Esses backups são armazenados em um cofre de Serviços de Recuperação no Azure. Você pode executar o agente:

* Diretamente em máquinas Windows no local. Essas máquinas podem fazer backup diretamente em um cofre dos Serviços de Recuperação no Azure.
* Nas VMs do Azure que executam o Windows lado a lado com a extensão de backup do Azure VM. O agente faz backup de arquivos e pastas específicos na VM.
* Em uma instância MABS (Microsoft Azure Backup Server, servidor de backup do Microsoft Azure) ou um servidor DPM (System Center Data Protection Manager, gerente de proteção de dados do centro de dados). Neste cenário, máquinas e cargas de trabalho voltam para MABS ou Data Protection Manager. Em seguida, o MABS ou o Data Protection Manager usa o agente MARS para fazer backup de um cofre no Azure.

Os dados disponíveis para backup dependem de onde o agente está instalado.

> [!NOTE]
> Geralmente, você faz backup de uma VM azure usando uma extensão de backup do Azure na VM. Este método apoia toda a VM. Se você quiser fazer backup de arquivos e pastas específicos na VM, instale e use o agente MARS ao lado da extensão. Para obter mais informações, consulte [Arquitetura de um backup azure VM incorporado](backup-architecture.md#architecture-built-in-azure-vm-backup).

![Etapas do processo de backup](./media/backup-configure-vault/initial-backup-process.png)

## <a name="before-you-start"></a>Antes de começar

* Saiba como [o Azure Backup usa o agente MARS para fazer backup das máquinas Windows](backup-architecture.md#architecture-direct-backup-of-on-premises-windows-server-machines-or-azure-vm-files-or-folders).
* Conheça a [arquitetura de backup](backup-architecture.md#architecture-back-up-to-dpmmabs) que executa o agente MARS em um servidor MABS ou Data Protection Manager secundário.
* Revise [o que é suportado e o que você pode fazer backup](backup-support-matrix-mars-agent.md) pelo agente da MARS.
* Certifique-se de que você tem uma conta Azure se você precisar fazer backup de um servidor ou cliente para o Azure. Se você não tem uma conta, você pode criar uma [gratuita](https://azure.microsoft.com/free/) em apenas alguns minutos.
* Verifique o acesso à internet nas máquinas que você deseja fazer backup.

[!INCLUDE [How to create a Recovery Services vault](../../includes/backup-create-rs-vault.md)]

## <a name="modify-storage-replication"></a>Modificar a replicação de armazenamento

Por padrão, os cofres usam [armazenamento geo-redundante (GRS)](https://docs.microsoft.com/azure/storage/common/storage-redundancy-grs).

* Se o cofre for seu principal mecanismo de backup, recomendamos que você use GRS.
* Você pode usar [o LRS (Locally Redundante Storage, armazenamento localmente redundante)](https://docs.microsoft.com/azure/storage/common/storage-redundancy-lrs?toc=%2fazure%2fstorage%2fblobs%2ftoc.json) para reduzir os custos de armazenamento do Azure.

Para modificar o tipo de replicação de armazenamento:

1. No novo cofre, selecione **Propriedades** na seção **Configurações.**

1. Na página **Propriedades,** em **Configuração de backup,** selecione **Atualizar**.

1. Selecione o tipo de replicação de armazenamento e selecione **Salvar**.

    ![Configuração de backup de atualização](./media/backup-afs/backup-configuration.png)

> [!NOTE]
> Você não pode modificar o tipo de replicação de armazenamento depois que o cofre estiver configurado e contiver itens de backup. Se você quiser fazer isso, você precisa recriar o cofre.
>

### <a name="verify-internet-access"></a>Verifique o acesso à Internet

Se a máquina tiver acesso limitado à Internet, certifique-se de que as configurações de firewall na máquina ou proxy permitam as seguintes URLs e endereços IP:

* URLs
  * `www\.msftncsi.com`
  * `*.Microsoft.com`
  * `*.WindowsAzure.com`
  * `*.microsoftonline.com`
  * `*.windows.net`
* Endereços IP
  * 20.190.128.0/18
  * 40.126.0.0/18

### <a name="use-azure-expressroute"></a>Use Azure ExpressRoute

Você pode fazer backup de seus dados no Azure ExpressRoute usando peering público (disponível para circuitos antigos) e peering da Microsoft. Não é suportado backup sobre peering privado.

Para usar peering público, primeiro certifique-se de acesso aos seguintes domínios e endereços:

* `http://www.msftncsi.com/ncsi.txt`
* `microsoft.com`
* `.WindowsAzure.com`
* `.microsoftonline.com`
* `.windows.net`

Para usar o peering da Microsoft, selecione os seguintes serviços, regiões e valores relevantes da comunidade:

* Diretório Ativo azure (12076:5060)
* Região do Azure, de acordo com a localização do cofre dos Serviços de Recuperação
* Armazenamento azure, de acordo com a localização do cofre dos Serviços de Recuperação

Para obter mais informações, consulte [os requisitos de roteamento expressroute](https://docs.microsoft.com/azure/expressroute/expressroute-routing).

> [!NOTE]
> O peering público é preterido por novos circuitos.

Todos os URLs e endereços IP anteriores usam o protocolo HTTPS na porta 443.

### <a name="private-endpoints"></a>Pontos finais privados

[!INCLUDE [Private Endpoints](../../includes/backup-private-endpoints.md)]

## <a name="download-the-mars-agent"></a>Baixe o agente MARS

Baixe o agente MARS para que você possa instalá-lo nas máquinas que você deseja fazer backup.

Se você já instalou o agente em qualquer máquina, certifique-se de que você está executando a versão mais recente do agente. Encontre a versão mais recente no portal, ou vá diretamente para o [download](https://aka.ms/azurebackup_agent).

1. No cofre, em **Getting Started,** selecione **Backup**.

    ![Abra o gol de backup](./media/backup-try-azure-backup-in-10-mins/open-backup-settings.png)

1. Em **Onde sua carga de trabalho está sendo executado?**, selecione **On-premises**. Selecione esta opção mesmo se você quiser instalar o agente MARS em uma VM Azure.
1. Em **O que você deseja fazer backup?**, selecione Arquivos e **pastas**. Você também pode selecionar **Estado do Sistema**. Muitas outras opções estão disponíveis, mas essas opções são suportadas apenas se você estiver executando um servidor de backup secundário. Selecione **Preparar infra-estrutura**.

    ![Configurar arquivos e pastas](./media/backup-try-azure-backup-in-10-mins/set-file-folder.png)

1. Para **preparar infra-estrutura,** em **Instalar o agente Serviços de Recuperação,** baixe o agente MARS.

    ![Preparar a infraestrutura](./media/backup-try-azure-backup-in-10-mins/choose-agent-for-server-client.png)

1. No menu de download, selecione **Salvar**. Por padrão, o arquivo *MARSagentinstaller.exe* será salvo em sua pasta Downloads.

1. Selecione **Já baixe ou use o agente de serviços de recuperação mais recente**e baixe as credenciais do cofre.

    ![Baixar as credenciais do cofre](./media/backup-try-azure-backup-in-10-mins/download-vault-credentials.png)

1. Selecione **Salvar**. O arquivo é baixado para sua pasta Downloads. Você não pode abrir o arquivo de credenciais do cofre.

## <a name="install-and-register-the-agent"></a>Instalar e registrar o agente

1. Execute o arquivo *MARSagentinstaller.exe* nas máquinas que você deseja fazer backup.
1. No assistente de configuração do agente MARS, selecione **Configurações de instalação**. Lá, escolha onde instalar o agente e escolha um local para o cache. Em seguida, selecione **Next**.
   * O Azure Backup usa o cache para armazenar instantâneos de dados antes de enviá-los ao Azure.
   * A localização do cache deve ter espaço livre igual a pelo menos 5% do tamanho dos dados que você fará backup.

    ![Escolha as configurações de instalação no assistente de configuração do agente MARS](./media/backup-configure-vault/mars1.png)

1. Para **configuração proxy,** especifique como o agente que é executado na máquina Windows se conectará à internet. Em seguida, selecione **Next**.

   * Se você usar um proxy personalizado, especifique as configurações e credenciais necessárias do proxy.
   * Lembre-se que o agente precisa ter acesso a [URLs específicos.](#before-you-start)

    ![Configurar o acesso à internet no assistente MARS](./media/backup-configure-vault/mars2.png)

1. Para **instalação,** revise os pré-requisitos e **selecione Instalar**.
1. Depois que o agente for instalado, **selecione Proceder ao Registro**.
1. No **Register Server Wizard** > **Vault Identification**, navegue e selecione o arquivo de credenciais que você baixou. Em seguida, selecione **Next**.

    ![Adicionar credenciais de cofre usando o Assistente do Servidor de Registro](./media/backup-configure-vault/register1.png)

1. Na página **Configuração de criptografia,** especifique uma senha que será usada para criptografar e descriptografar backups para a máquina.

    * Salve a senha em um local seguro. Você precisa dele para restaurar um backup.
    * Se você perder ou esquecer a senha, a Microsoft não poderá ajudá-lo a recuperar os dados de backup.

1. Selecione **Concluir**. O agente está instalado, e sua máquina está registrada no cofre. Você está pronto para configurar e agendar o backup.

## <a name="next-steps"></a>Próximas etapas

Aprenda a [fazer backup de máquinas Windows usando o agente MARS do Azure Backup](backup-windows-with-mars-agent.md)
