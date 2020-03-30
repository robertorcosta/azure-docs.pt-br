---
title: Configure um aparelho Azure Migrate com um script
description: Saiba como configurar um aparelho Do Azure Migrate com um script
ms.topic: article
ms.date: 03/23/2020
ms.openlocfilehash: bf8d7148f685d4ac6a5f33603020a0503b0c62e5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80337682"
---
# <a name="set-up-an-appliance-with-a-script"></a>Configure um aparelho com um script

Este artigo descreve como configurar o [aparelho Azure Migrate](deploy-appliance.md) usando um script instalador PowerShell.

O script fornece:
- Uma alternativa para configurar o aparelho usando um modelo OVA, para avaliação e migração sem agente de VMware VMs.
- Uma alternativa para configurar o aparelho usando um modelo VHD, para avaliação e migração de VMs Hyper-V.
- Para avaliação de servidores físicos (ou VMs que você deseja migrar como servidores físicos), o script é o único método para configurar o aparelho.

## <a name="prerequisites"></a>Pré-requisitos

O script configura o aparelho Azure Migrate em uma máquina física ou VM existente.

- A máquina que funcionará como aparelho deve estar executando o Windows Server 2016, com 32 GB de memória, oito vCPUs, cerca de 80 GB de armazenamento em disco e um switch virtual externo. Ele requer um endereço IP estático ou dinâmico e acesso à internet.
- Antes de implantar o aparelho, revise os requisitos detalhados do aparelho para [VMs,](migrate-appliance.md#appliance---vmware) [VMs Hyper-V](migrate-appliance.md#appliance---hyper-v)e [servidores físicos.](migrate-appliance.md#appliance---physical)
- Não execute o script em um aparelho Azure Migrate existente.


## <a name="download-the-script"></a>Baixe o script

1. Localize a máquina/VM que funcionará como o aparelho Azure Migrate.
2. Na máquina, faça o seguinte:

    - Para usar o aparelho com VMs VMs ou VMs Hyper-V, baixe a pasta com [zíper](https://go.microsoft.com/fwlink/?linkid=2105112) contendo o script do instalador e os MSIs.
    - Para usar o aparelho com servidores físicos, baixe o script do portal Azure Migrate, conforme descrito neste [tutorial](tutorial-assess-physical.md#set-up-the-appliance).

## <a name="verify-file-security"></a>Verificar a segurança do arquivo

Verifique se o arquivo compactado é seguro antes de implantá-lo.

1. No computador no qual você baixou o arquivo, abra uma janela de comando do administrador.
2. Execute o seguinte comando para gerar o hash para o arquivo zip
    - ```C:\>CertUtil -HashFile <file_location> [Hashing Algorithm]```
    - Exemplo de uso: ```C:\>CertUtil -HashFile C:\Users\administrator\Desktop\AzureMigrateInstaller.zip SHA256```

3. Verifique se os valores de hash gerados correspondem a essas configurações (para a versão mais recente do aparelho):

    **Algoritmo** | **Valor de hash**
      --- | ---
      MD5 | 1e92ede3e87c03bd148e56a708cdd33f
      SHA256 | a3fa78edc8ff8aff9ab5ae66be1b64e66de7b9f475b6542beef114b20bfdac3c

## <a name="run-the-script"></a>Executar o script

Eis o que o roteiro faz:

- Instala agentes e um aplicativo web.
- Instala funções do Windows, incluindo o Windows Activation Service, IIS e PowerShell ISE.
- Baixa e instala um módulo regravável IIS. [Saiba mais](https://www.microsoft.com/download/details.aspx?id=7435).
- Atualiza uma chave de registro (HKLM), com configurações persistentes para o Azure Migrate.
- Cria arquivos de log e configuração da seguinte forma:
    - **Arquivos de Configuração**: %ProgramData%\Microsoft Azure\Config
    - **Arquivos de Log**: %ProgramData%\Microsoft Azure\Logs

Para executar o script:

1. Extrair o arquivo fechado para uma pasta na máquina que hospedará o aparelho.
2. Inicie o PowerShell na máquina, com privilégios de administrador (elevados).
3. Altere o diretório PowerShell para a pasta que contém o conteúdo extraído do arquivo fechado baixado.
4. Execute o script **AzureMigrateInstaller.ps1** da seguinte forma:

    - Para VMware: 
        ```
        PS C:\Users\administrator\Desktop\AzureMigrateInstaller> AzureMigrateInstaller.ps1 -scenario VMware
        ```
    - Para Hiper-V:
        ```
        PS C:\Users\administrator\Desktop\AzureMigrateInstaller> AzureMigrateInstaller.ps1 -scenario Hyperv
        ```
    - Para servidores físicos:
        ```
        PS C:\Users\administrator\Desktop\AzureMigrateInstaller> AzureMigrateInstaller.ps1
        ```      
5. Depois que o script é executado com sucesso, ele inicia a aplicação web do aparelho para que você possa configurar o aparelho. Se você encontrar algum problema, poderá visualizar os logs de script em C:\ProgramData\Microsoft Azure\Logs\AzureMigrateScenarioInstaller_<em>Marcação de tempo</em>.log.

## <a name="next-steps"></a>Próximas etapas

Depois de configurar o aparelho usando o script, siga estas instruções:

- Configure o aparelho para [VMware](how-to-set-up-appliance-vmware.md#configure-the-appliance).
- Configure o aparelho para [Hyper-V](how-to-set-up-appliance-hyper-v.md#configure-the-appliance).
- Configure o aparelho para [servidores físicos](how-to-set-up-appliance-physical.md).