---
title: Configure um aparelho Azure Migrate com um script
description: Saiba como configurar um aparelho Do Azure Migrate com um script
ms.topic: article
ms.date: 04/16/2020
ms.openlocfilehash: faed7f96ea8c1850af5523d35f9f891011a48df8
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/16/2020
ms.locfileid: "81537705"
---
# <a name="set-up-an-appliance-with-a-script"></a>Configure um aparelho com um script

Este artigo descreve como configurar o [aparelho Azure Migrate](deploy-appliance.md) usando um script instalador PowerShell, para VMs VMware e VMs Hyper-V. Se você quiser configurar o aparelho para servidores físicos, [revise este artigo](how-to-set-up-appliance-physical.md).


Você pode implantar o aparelho usando alguns métodos:


- Usando um modelo para VMware VMs (OVA) ou Hyper-V VMs (VHD).
- Usando um script. Este é o método descrito neste artigo. O script fornece:
    - Uma alternativa para configurar o aparelho usando um modelo OVA, para avaliação e migração sem agente de VMware VMs.
    - Uma alternativa para configurar o aparelho usando um modelo VHD, para avaliação e migração de VMs Hyper-V.
    - Para avaliação de servidores físicos (ou VMs que você deseja migrar como servidores físicos), o script é o único método para configurar o aparelho.
    - Uma maneira de implantar o aparelho no governo Azure.


Depois de criar o aparelho, verifique se ele pode se conectar ao Azure Migrate. Em seguida, configure o aparelho pela primeira vez e registre-o no projeto Azure Migrate.


## <a name="prerequisites"></a>Pré-requisitos

O script configura o aparelho Azure Migrate em uma máquina física ou VM existente.

- A máquina que funcionará como aparelho deve estar executando o Windows Server 2016, com 32 GB de memória, oito vCPUs, cerca de 80 GB de armazenamento em disco e um switch virtual externo. Ele requer um endereço IP estático ou dinâmico e acesso à internet.
- Antes de implantar o aparelho, revise os requisitos detalhados do aparelho para [VMs,](migrate-appliance.md#appliance---vmware) [VMs Hyper-V](migrate-appliance.md#appliance---hyper-v)e [servidores físicos.](migrate-appliance.md#appliance---physical)
- Não execute o script em um aparelho Azure Migrate existente.

## <a name="set-up-the-appliance-for-vmware"></a>Configure o aparelho para VMware

Para configurar o aparelho para VMware, você baixa um arquivo fechado do portal Azure e extrai o conteúdo. Você executa o script PowerShell para iniciar o aplicativo web do aparelho. Você configura o aparelho e o configura pela primeira vez. Em seguida, você registra o aparelho com o projeto Azure Migrate.

### <a name="download-the-script"></a>Baixe o script

1.  Em **Metas de Migração** > **Servidores** > **Migrações para Azure: Avaliação de Servidor**, clique em **Descobrir**.
2.  Em **Descobrir computadores** > **Os computadores estão virtualizados?** , selecione **Sim, com o hipervisor do VMware vSphere**.
3.  Clique **em Download,** para baixar o arquivo fechado. 


### <a name="verify-file-security"></a>Verificar a segurança do arquivo

Verifique se o arquivo compactado é seguro antes de implantá-lo.

1. No computador no qual você baixou o arquivo, abra uma janela de comando do administrador.
2. Execute o seguinte comando para gerar o hash para o arquivo zip
    - ```C:\>CertUtil -HashFile <file_location> [Hashing Algorithm]```
    - Uso de exemplo para nuvem pública:```C:\>CertUtil -HashFile C:\Users\administrator\Desktop\AzureMigrateInstaller.zip SHA256```
    - Exemplo de uso para nuvem governamental:```C:\>CertUtil -HashFile C:\Users\administrator\Desktop\AzureMigrateInstaller-VMWare-USGov.zip```

3. Verifique os valores de hash gerados:

    - Para a nuvem pública (para a versão mais recente do aparelho):

        **Algoritmo** | **Valor de hash**
          --- | ---
          MD5 | 1e92ede3e87c03bd148e56a708cdd33f
          SHA256 | a3fa78edc8ff8aff9ab5ae66be1b64e66de7b9f475b6542beef114b20bfdac3c

    - Para o governo Azure (para a versão mais recente do aparelho):

        **Algoritmo** | **Valor de hash**
          --- | ---
          MD5 | 6316bcc8bc932204295bfe33f4be3949
          

### <a name="run-the-script"></a>Executar o script

Eis o que o roteiro faz:

- Instala agentes e um aplicativo web.
- Instala funções do Windows, incluindo o Windows Activation Service, IIS e PowerShell ISE.
- Baixa e instala um módulo regravável IIS. [Saiba mais](https://www.microsoft.com/download/details.aspx?id=7435).
- Atualiza uma chave de registro (HKLM), com configurações persistentes para o Azure Migrate.
- Cria arquivos de log e configuração da seguinte forma:
    - **Arquivos de Configuração**: %ProgramData%\Microsoft Azure\Config
    - **Arquivos de Log**: %ProgramData%\Microsoft Azure\Logs

Para executar o script:

1. Extrair o arquivo fechado para uma pasta na máquina que hospedará o aparelho. Certifique-se de que você não execute o script em uma máquina em um aparelho Azure Migrate existente.
2. Inicie o PowerShell na máquina, com privilégios de administrador (elevados).
3. Altere o diretório PowerShell para a pasta que contém o conteúdo extraído do arquivo fechado baixado.
4. Execute o script **AzureMigrateInstaller.ps1**, da seguinte forma:
    - Para a nuvem pública:``` PS C:\Users\administrator\Desktop\AzureMigrateInstaller> AzureMigrateInstaller.ps1 -scenario VMware ```
    - Para o governo azure:``` PS C:\Users\Administrators\Desktop\AzureMigrateInstaller-VMWare-USGov>AzureMigrateInstaller.ps1 ```
   
5. Depois que o script é executado com sucesso, ele inicia a aplicação web do aparelho para que você possa configurar o aparelho. Se você encontrar algum problema, revise os logs de script em C:\ProgramData\Microsoft Azure\Logs\AzureMigrateScenarioInstaller_<em>Marcação de tempo</em>.log.

### <a name="verify-access"></a>Verificar acesso

Certifique-se de que o aparelho pode se conectar a URLs do Azure para [o público](migrate-appliance.md#public-cloud-urls) e [nuvens do governo](migrar-appliance.md#government-cloud-urls.


## <a name="set-up-the-appliance-for-hyper-v"></a>Configure o aparelho para Hyper-V

Para configurar o aparelho para Hyper-V, você baixa um arquivo fechado do portal Azure e extrai o conteúdo. Você executa o script PowerShell para iniciar o aplicativo web do aparelho. Você configura o aparelho e o configura pela primeira vez. Em seguida, você registra o aparelho com o projeto Azure Migrate.

### <a name="download-the-script"></a>Baixe o script

1.  Em **Metas de Migração** > **Servidores** > **Migrações para Azure: Avaliação de Servidor**, clique em **Descobrir**.
2.  Em **Descobrir computadores** > **Os computadores estão virtualizados?** , selecione **Sim, com o Hyper-V**.
3.  Clique **em Download,** para baixar o arquivo fechado. 


### <a name="verify-file-security"></a>Verificar a segurança do arquivo

Verifique se o arquivo compactado é seguro antes de implantá-lo.

1. No computador no qual você baixou o arquivo, abra uma janela de comando do administrador.
2. Execute o seguinte comando para gerar o hash para o arquivo zip
    - ```C:\>CertUtil -HashFile <file_location> [Hashing Algorithm]```
    - Uso de exemplo para nuvem pública:```C:\>CertUtil -HashFile C:\Users\administrator\Desktop\AzureMigrateInstaller.zip SHA256```
    - Exemplo de uso para nuvem governamental:```C:\>CertUtil -HashFile C:\Users\administrator\Desktop\AzureMigrateInstaller-HyperV-USGov.zip MD5```

3. Verifique os valores de hash gerados:

    - Para a nuvem pública (para a versão mais recente do aparelho):

        **Algoritmo** | **Valor de hash**
          --- | ---
          MD5 | 1e92ede3e87c03bd148e56a708cdd33f
          SHA256 | a3fa78edc8ff8aff9ab5ae66be1b64e66de7b9f475b6542beef114b20bfdac3c

    - Para o governo Azure (para a versão mais recente do aparelho):

        **Algoritmo** | **Valor de hash**
          --- | ---
          MD5 | 717f8b9185f565006b5aff0215ecadac
          

### <a name="run-the-script"></a>Executar o script

Eis o que o roteiro faz:

- Instala agentes e um aplicativo web.
- Instala funções do Windows, incluindo o Windows Activation Service, IIS e PowerShell ISE.
- Baixa e instala um módulo regravável IIS. [Saiba mais](https://www.microsoft.com/download/details.aspx?id=7435).
- Atualiza uma chave de registro (HKLM), com configurações persistentes para o Azure Migrate.
- Cria arquivos de log e configuração da seguinte forma:
    - **Arquivos de Configuração**: %ProgramData%\Microsoft Azure\Config
    - **Arquivos de Log**: %ProgramData%\Microsoft Azure\Logs

Para executar o script:

1. Extrair o arquivo fechado para uma pasta na máquina que hospedará o aparelho. Certifique-se de que você não execute o script em uma máquina em um aparelho Azure Migrate existente.
2. Inicie o PowerShell na máquina, com privilégios de administrador (elevados).
3. Altere o diretório PowerShell para a pasta que contém o conteúdo extraído do arquivo fechado baixado.
4. Execute o script **AzureMigrateInstaller.ps1**, da seguinte forma:
    - Para a nuvem pública:``` PS C:\Users\administrator\Desktop\AzureMigrateInstaller> AzureMigrateInstaller.ps1 -scenario Hyperv ```
    - Para o governo azure:``` PS C:\Users\Administrators\Desktop\AzureMigrateInstaller-HyperV-USGov>AzureMigrateInstaller.ps1 ```
   
5. Depois que o script é executado com sucesso, ele inicia a aplicação web do aparelho para que você possa configurar o aparelho. Se você encontrar algum problema, revise os logs de script em C:\ProgramData\Microsoft Azure\Logs\AzureMigrateScenarioInstaller_<em>Marcação de tempo</em>.log.

### <a name="verify-access"></a>Verificar acesso

Certifique-se de que o aparelho pode se conectar a URLs do Azure para [o público](migrate-appliance.md#public-cloud-urls) e [nuvens do governo](migrar-appliance.md#government-cloud-urls.



## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre como configurar o aparelho com um modelo, ou para servidores físicos, revise estes artigos:

- Configure o aparelho para [VMware](how-to-set-up-appliance-vmware.md#configure-the-appliance).
- Configure o aparelho para [Hyper-V](how-to-set-up-appliance-hyper-v.md#configure-the-appliance).
- Configure o aparelho para [servidores físicos](how-to-set-up-appliance-physical.md).