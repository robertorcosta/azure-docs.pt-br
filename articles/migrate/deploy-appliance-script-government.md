---
title: Configurar um dispositivo de migrações para Azure no Azure governamental
description: Saiba como configurar um dispositivo de migrações para Azure no Azure governamental
ms.topic: article
ms.date: 04/16/2020
ms.openlocfilehash: f0ebc882646b5ff3f62ddddf91cffc85cb5e0da6
ms.sourcegitcommit: d7008edadc9993df960817ad4c5521efa69ffa9f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/08/2020
ms.locfileid: "86109970"
---
# <a name="set-up-an-appliance-in-azure-government"></a>Configurar um dispositivo no Azure governamental 

Siga este artigo para implantar um [dispositivo de migração do Azure](./migrate-appliance-architecture.md) para VMs VMware, VMS do Hyper-V e servidores físicos em uma nuvem do Azure governamental. Você executa um script para criar o dispositivo e verifica se ele pode se conectar ao Azure. Se você quiser configurar um dispositivo na nuvem pública, siga [Este artigo](deploy-appliance-script.md).


> [!NOTE]
> A opção de implantar um dispositivo usando um modelo (para VMs do VMware e máquinas virtuais do Hyper-V) não tem suporte no Azure governamental.


## <a name="prerequisites"></a>Pré-requisitos

O script configura o dispositivo migrações para Azure em uma VM ou máquina física existente.

- O computador que atuará como o dispositivo deve estar executando o Windows Server 2016, com 32 GB de memória, oito vCPUs, cerca de 80 GB de armazenamento em disco e um comutador virtual externo. Ele requer um endereço IP estático ou dinâmico e o acesso à Internet.
- Antes de implantar o dispositivo, examine os requisitos de dispositivo detalhados para [VMs VMware](migrate-appliance.md#appliance---vmware), [VMs do Hyper-V](migrate-appliance.md#appliance---hyper-v)e [servidores físicos](migrate-appliance.md#appliance---physical).
- Não execute o script em um dispositivo de migrações do Azure existente.

## <a name="set-up-the-appliance-for-vmware"></a>Configurar o dispositivo para VMware

Para configurar o dispositivo para VMware, você baixa um arquivo compactado do portal do Azure e extrai o conteúdo. Você executa o script do PowerShell para iniciar o aplicativo Web do dispositivo. Você configura o dispositivo e o configura pela primeira vez. Em seguida, você registra o dispositivo com o projeto de migrações para Azure.

### <a name="download-the-script"></a>Baixar o script

1.  Em **Metas de Migração** > **Servidores** > **Migrações para Azure: Avaliação de Servidor**, clique em **Descobrir**.
2.  Em **Descobrir computadores** > **Os computadores estão virtualizados?** , selecione **Sim, com o hipervisor do VMware vSphere**.
3.  Clique em **baixar**para baixar o arquivo compactado. 


### <a name="verify-file-security"></a>Verificar a segurança do arquivo

Verifique se o arquivo compactado é seguro antes de implantá-lo.

1. No computador no qual você baixou o arquivo, abra uma janela de comando do administrador.
2. Execute o seguinte comando para gerar o hash para o arquivo zip
    - ```C:\>CertUtil -HashFile <file_location> [Hashing Algorithm]```
    - Exemplo: ```C:\>CertUtil -HashFile C:\Users\administrator\Desktop\AzureMigrateInstaller-VMWare-USGov.zip SHA256```

3. Verifique a versão mais recente do dispositivo e o valor de hash:

    **Algoritmo** | **Download** | **SHA256**
    --- | --- | ---
    VMware (63,1 MB) | [Última versão](https://go.microsoft.com/fwlink/?linkid=2120300&clcid=0x409 ) | 3d5822038646b81f458d89d706832c0a2c0e827bfa9b0a55cc478eaf2757a4de


### <a name="run-the-script"></a>Executar o script

Veja o que o script faz:

- Instala agentes e um aplicativo Web.
- Instala funções do Windows, incluindo o serviço de ativação do Windows, o IIS e o PowerShell ISE.
- Baixa e instala um módulo regravável do IIS. [Saiba mais](https://www.microsoft.com/download/details.aspx?id=7435).
- Atualiza uma chave do registro (HKLM), com configurações persistentes para migrações para Azure.
- Cria arquivos de log e de configuração da seguinte maneira:
    - **Arquivos de configuração**:%ProgramData%\Microsoft Azure\Config
    - **Arquivos de log**:%ProgramData%\Microsoft Azure\Logs

Para executar o script:

1. Extraia o arquivo compactado em uma pasta no computador que hospedará o dispositivo. Você não deve executar o script em um computador em um dispositivo de Migrações para Azure existente.
2. Inicie o PowerShell no computador com privilégios de administrador (elevado).
3. Altere o diretório do PowerShell para a pasta que contém o conteúdo extraído do arquivo compactado baixado.
4. Execute o script **AzureMigrateInstaller.ps1**, da seguinte maneira:``` PS C:\Users\Administrators\Desktop\AzureMigrateInstaller-VMWare-USGov>AzureMigrateInstaller.ps1 ```
5. Depois que o script é executado com êxito, ele inicia o aplicativo Web do dispositivo para que você possa configurar o dispositivo. Se você encontrar problemas, examine os logs de script em C:\ProgramData\Microsoft Azure\Logs\ AzureMigrateScenarioInstaller_<em>timestamp</em>. log.

### <a name="verify-access"></a>Verificar acesso

Verifique se o dispositivo pode se conectar a URLs do Azure para [nuvens governamentais](migrate-appliance.md#government-cloud-urls).


## <a name="set-up-the-appliance-for-hyper-v"></a>Configurar o dispositivo para Hyper-V

Para configurar o dispositivo para Hyper-V, você baixa um arquivo compactado do portal do Azure e extrai o conteúdo. Você executa o script do PowerShell para iniciar o aplicativo Web do dispositivo. Você configura o dispositivo e o configura pela primeira vez. Em seguida, você registra o dispositivo com o projeto de migrações para Azure.

### <a name="download-the-script"></a>Baixar o script

1.  Em **Metas de Migração** > **Servidores** > **Migrações para Azure: Avaliação de Servidor**, clique em **Descobrir**.
2.  Em **Descobrir computadores** > **Os computadores estão virtualizados?** , selecione **Sim, com o Hyper-V**.
3.  Clique em **baixar**para baixar o arquivo compactado. 


### <a name="verify-file-security"></a>Verificar a segurança do arquivo

Verifique se o arquivo compactado é seguro antes de implantá-lo.

1. No computador no qual você baixou o arquivo, abra uma janela de comando do administrador.
2. Execute o seguinte comando para gerar o hash para o arquivo zip
    - ```C:\>CertUtil -HashFile <file_location> [Hashing Algorithm]```
    - Exemplo: ```C:\>CertUtil -HashFile C:\Users\administrator\Desktop\AzureMigrateInstaller-HyperV-USGov.zip SHA256```

3. Verifique a versão mais recente do dispositivo e o valor de hash:

    **Cenário** | **Download** | **SHA256**
    --- | --- | ---
    Hyper-V (63,1 MB) | [Última versão](https://go.microsoft.com/fwlink/?linkid=2120200&clcid=0x409) |  2c5e73a1e5525d4fae468934408e43ab55ff397b7da200b92121972e683f9aa3

          

### <a name="run-the-script"></a>Executar o script

Veja o que o script faz:

- Instala agentes e um aplicativo Web.
- Instala funções do Windows, incluindo o serviço de ativação do Windows, o IIS e o PowerShell ISE.
- Baixa e instala um módulo regravável do IIS. [Saiba mais](https://www.microsoft.com/download/details.aspx?id=7435).
- Atualiza uma chave do registro (HKLM), com configurações persistentes para migrações para Azure.
- Cria arquivos de log e de configuração da seguinte maneira:
    - **Arquivos de configuração**:%ProgramData%\Microsoft Azure\Config
    - **Arquivos de log**:%ProgramData%\Microsoft Azure\Logs

Para executar o script:

1. Extraia o arquivo compactado em uma pasta no computador que hospedará o dispositivo. Você não deve executar o script em um computador em um dispositivo de Migrações para Azure existente.
2. Inicie o PowerShell no computador com privilégios de administrador (elevado).
3. Altere o diretório do PowerShell para a pasta que contém o conteúdo extraído do arquivo compactado baixado.
4. Execute o script **AzureMigrateInstaller.ps1**, da seguinte maneira:``` PS C:\Users\Administrators\Desktop\AzureMigrateInstaller-HyperV-USGov>AzureMigrateInstaller.ps1 ``` 
5. Depois que o script é executado com êxito, ele inicia o aplicativo Web do dispositivo para que você possa configurar o dispositivo. Se você encontrar problemas, examine os logs de script em C:\ProgramData\Microsoft Azure\Logs\ AzureMigrateScenarioInstaller_<em>timestamp</em>. log.

### <a name="verify-access"></a>Verificar acesso

Verifique se o dispositivo pode se conectar a URLs do Azure para [nuvens governamentais](migrate-appliance.md#government-cloud-urls).


## <a name="set-up-the-appliance-for-physical-servers"></a>Configurar o dispositivo para servidores físicos

Para configurar o dispositivo para VMware, você baixa um arquivo compactado do portal do Azure e extrai o conteúdo. Você executa o script do PowerShell para iniciar o aplicativo Web do dispositivo. Você configura o dispositivo e o configura pela primeira vez. Em seguida, você registra o dispositivo com o projeto de migrações para Azure.

### <a name="download-the-script"></a>Baixar o script

1.  Em **Metas de Migração** > **Servidores** > **Migrações para Azure: Avaliação de Servidor**, clique em **Descobrir**.
2.  Em **descobrir computadores**  >  **são seus computadores virtualizados?**, selecione **não virtualizados/outros**.
3.  Clique em **baixar**para baixar o arquivo compactado. 


### <a name="verify-file-security"></a>Verificar a segurança do arquivo

Verifique se o arquivo compactado é seguro antes de implantá-lo.

1. No computador no qual você baixou o arquivo, abra uma janela de comando do administrador.
2. Execute o seguinte comando para gerar o hash para o arquivo zip
    - ```C:\>CertUtil -HashFile <file_location> [Hashing Algorithm]```
    - Exemplo: ```C:\>CertUtil -HashFile C:\Users\administrator\Desktop\AzureMigrateInstaller-Server-USGov.zip SHA256```

3. Verifique a versão mais recente do dispositivo e o valor de hash:

    **Cenário** | **Baixar*** | **Valor de hash**
    --- | --- | ---
    Físico (63,1 MB) | [Última versão](https://go.microsoft.com/fwlink/?linkid=2120100&clcid=0x409) | 93dfef131026e70acdfad2769cd208ff745ab96a96f013cdf3f9e1e61c9b37e1
          

### <a name="run-the-script"></a>Executar o script

Veja o que o script faz:

- Instala agentes e um aplicativo Web.
- Instala funções do Windows, incluindo o serviço de ativação do Windows, o IIS e o PowerShell ISE.
- Baixa e instala um módulo regravável do IIS. [Saiba mais](https://www.microsoft.com/download/details.aspx?id=7435).
- Atualiza uma chave do registro (HKLM), com configurações persistentes para migrações para Azure.
- Cria arquivos de log e de configuração da seguinte maneira:
    - **Arquivos de configuração**:%ProgramData%\Microsoft Azure\Config
    - **Arquivos de log**:%ProgramData%\Microsoft Azure\Logs

Para executar o script:

1. Extraia o arquivo compactado em uma pasta no computador que hospedará o dispositivo. Você não deve executar o script em um computador em um dispositivo de Migrações para Azure existente.
2. Inicie o PowerShell no computador com privilégios de administrador (elevado).
3. Altere o diretório do PowerShell para a pasta que contém o conteúdo extraído do arquivo compactado baixado.
4. Execute o script **AzureMigrateInstaller.ps1**, da seguinte maneira:``` PS C:\Users\Administrators\Desktop\AzureMigrateInstaller-Server-USGov>AzureMigrateInstaller.ps1 ```
5. Depois que o script é executado com êxito, ele inicia o aplicativo Web do dispositivo para que você possa configurar o dispositivo. Se você encontrar problemas, examine os logs de script em C:\ProgramData\Microsoft Azure\Logs\ AzureMigrateScenarioInstaller_<em>timestamp</em>. log.

### <a name="verify-access"></a>Verificar acesso

Verifique se o dispositivo pode se conectar a URLs do Azure para [nuvens governamentais](migrate-appliance.md#government-cloud-urls).

## <a name="next-steps"></a>Próximas etapas

Depois de implantar o dispositivo, você precisa configurá-lo pela primeira vez e registrá-lo com o projeto de migrações para Azure.

- Configure o dispositivo para [VMware](how-to-set-up-appliance-vmware.md#configure-the-appliance).
- Configure o dispositivo para o [Hyper-V](how-to-set-up-appliance-hyper-v.md#configure-the-appliance).
- Configure o dispositivo para [servidores físicos](how-to-set-up-appliance-physical.md).
