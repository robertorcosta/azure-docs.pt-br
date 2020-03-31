---
title: Recomendações de proteção de endpoint em Centros de Segurança do Azure
description: Como as soluções de proteção de ponto final são descobertas e identificadas como saudáveis.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: 2730a2f5-20bc-4027-a1c2-db9ed0539532
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/29/2019
ms.author: memildin
ms.openlocfilehash: dcf7df501665ea3885d00b9f7668a95cbbf02428
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "78208535"
---
# <a name="endpoint-protection-assessment-and-recommendations-in-azure-security-center"></a>Avaliação e recomendações de proteção de endpoint no Azure Security Center

O Azure Security Center fornece avaliações de saúde de versões [suportadas](security-center-services.md#endpoint-supported) de soluções de proteção Endpoint. Este artigo explica os cenários que levam o Security Center a gerar as seguintes duas recomendações:

* **Instale soluções de proteção de ponto final em sua máquina virtual**
* **Resolver problemas de integridade da proteção do endpoint em suas máquinas**

## <a name="windows-defender"></a>Windows Defender

* O Security Center recomenda que você **"Instale soluções de proteção de ponto final na máquina virtual"** quando [o Get-MpComputerStatus](https://docs.microsoft.com/powershell/module/defender/get-mpcomputerstatus?view=win10-ps) for executado e o resultado for **AMServiceEnabled: False**

* O Security Center recomenda que você **"Resolva problemas de saúde de proteção de ponto final em suas máquinas"** quando [o Get-MpComputerStatus](https://docs.microsoft.com/powershell/module/defender/get-mpcomputerstatus?view=win10-ps) for executado e qualquer um dos seguintes ocorre:

  * Qualquer uma das seguintes propriedades são falsas:

    **AMServiceEnabled**

    **AntispywareAtivado**

    **RealTimeProtectionEnabled**

    **BehaviorMonitorEnabled**

    **IoavProtectionEnabled**

    **OnAccessProtectionEnabled**

  * Se uma ou ambas as propriedades a seguir forem 7 ou mais.

    **AntispywareSignatureAge**

    **AntivírusSignatureAge**

## <a name="microsoft-system-center-endpoint-protection"></a>Proteção de ponto final do Microsoft System Center

* O Security Center recomenda que você **"Instale soluções de proteção de ponto final na máquina virtual"** ao importar **o SCEPMpModule ("$env:ProgramFiles\Microsoft Security Client\MpProvider\MpProvider.psd1")** e executar os resultados **do Get-MProtComputerStatus** com **AMServiceEnabled = false**

* O Security Center recomenda que você **"Resolva problemas de saúde de proteção de ponto final em suas máquinas"** quando **o Get-MprotComputerStatus** é executado e qualquer um dos seguintes ocorre:

    * Pelo menos uma das seguintes propriedades é falsa:

            **AMServiceEnabled**

            **AntispywareEnabled**
    
            **RealTimeProtectionEnabled**
    
            **BehaviorMonitorEnabled**
    
            **IoavProtectionEnabled**
    
            **OnAccessProtectionEnabled**
          
    * Se uma ou ambas as seguintes Atualizações de Assinatura forem maiores ou iguais a 7. 

            **AntispywareSignatureAge**
    
            **AntivirusSignatureAge**

## <a name="trend-micro"></a>Micro tendência

* O Security Center recomenda que você **"Instale soluções de proteção de ponto final na máquina virtual"** quando qualquer uma das seguintes verificações não for atendida:
    * **HKLM:\SOFTWARE\TrendMicro\Deep Security Agent** existe
    * **HKLM:\SOFTWARE\TrendMicro\Deep Security Agent\InstallationFolder** existe
    * O arquivo **dsa_query.cmd** é encontrado na pasta de instalação
    * Executando **dsa_query.cmd** resultados com **component.AM.mode: on - Trend Micro Deep Security Agent detectado**

## <a name="symantec-endpoint-protection"></a>Proteção de ponto final da Symantec
O Security Center recomenda que você **"Instale soluções de proteção de ponto final na máquina virtual"** quando qualquer uma das seguintes verificações não for atendida:

* **HKLM:\Software\Symantec\Symantec Endpoint Protection\CurrentVersion\PRODUCTNAME = "Symantec Endpoint Protection"**

* **HKLM:\Software\Symantec\Symantec Endpoint Protection\CurrentVersion\public-opstate\ASRunningStatus = 1**

Ou

* **HKLM:\Software\Wow6432Node\Symantec\Symantec Endpoint Protection\CurrentVersion\PRODUCTNAME = "Symantec Endpoint Protection"**

* **HKLM:\Software\Wow6432Node\Symantec\Symantec Endpoint Protection\CurrentVersion\public-opstate\ASRunningStatus = 1**

O Security Center recomenda que você **"Resolva problemas de saúde de proteção de ponto final em suas máquinas"** quando qualquer uma das seguintes verificações não for atendida:

* Verificar symantec versão >= 12: Local de registro: **HKLM:\Software\Symantec\Symantec Endpoint Protection\CurrentVersion" -Valor "PRODUCTVERSION"**

* Verifique o status de proteção em tempo real: **HKLM:\Software\Wow6432Node\Symantec\Symantec Endpoint Protection\AV\Storages\Filesystem\RealTimeScan\OnOff == 1**

* Verificar status de atualização de assinatura: **HKLM\Software\Symantec\Symantec Endpoint Protection\CurrentVersion\public-opstate\LatestVirusDefsDate <= 7 dias**

* Verifique o status de varredura completa: **HKLM:\Software\Symantec\Symantec Endpoint Protection\CurrentVersion\public-opstate\LastSuccessfulScanDateTime <= 7 dias**

* Encontre o número da versão de assinatura Path para a versão de assinatura do Symantec 12: **Registry Paths+ "CurrentVersion\SharedDefs" -Valor "SRTSP"** 

* Caminho para a versão de assinatura para Symantec 14: **Caminhos de Registro+ "CurrentVersion\SharedDefs\SDSDefs" -Valor "SRTSP"**

Caminhos de registro:

* **"HKLM:\Software\Symantec\Symantec Endpoint Protection" + $Path;**
* **"HKLM:\Software\Wow6432Node\Symantec\Symantec Endpoint Protection" + $Path**

## <a name="mcafee-endpoint-protection-for-windows"></a>Proteção de ponto final da McAfee para Windows

O Security Center recomenda que você **"Instale soluções de proteção de ponto final na máquina virtual"** quando qualquer uma das seguintes verificações não for atendida:

* **HKLM:\SOFTWARE\McAfee\Endpoint\AV\ProductVersion** existe

* **HKLM:\SOFTWARE\McAfee\AVSolution\MCSHIELDGLOBAL\GLOBAL\enableoas = 1**

O Security Center recomenda que você **"Resolva problemas de saúde de proteção de ponto final em suas máquinas"** quando qualquer uma das seguintes verificações não for atendida:

* Versão da McAfee: **HKLM:\SOFTWARE\McAfee\Endpoint\AV\ProductVersion >= 10**

* Encontrar versão de assinatura: **HKLM:\Software\McAfee\AVSolution\DS\DS -Valor "dwContentMajorVersion"**

* Encontrar data de assinatura: **HKLM:\Software\McAfee\AVSolution\DS\DS -Valor "szContentCreationDate" >= 7 dias**

* Encontrar data de varredura: **HKLM:\Software\McAfee\Endpoint\AV\ODS -Valor "LastFullScanOdsRunTime" >= 7 dias**

## <a name="mcafee-endpoint-security-for-linux-threat-prevention"></a>McAfee Endpoint Security para prevenção de ameaças Linux 

O Security Center recomenda que você **"Instale soluções de proteção de ponto final na máquina virtual"** quando qualquer uma das seguintes verificações não for atendida:

- Arquivo **/opt/isec/ens/threatprevention/bin/isecav** sai 

- **"/opt/isec/ens/threatprevention/bin/isecav --version"** é: **Nome McAfee = McAfee Endpoint Security para Prevenção de Ameaças Linux e versão mcAfee >= 10**

O Security Center recomenda que você **"Resolva problemas de saúde de proteção de ponto final em suas máquinas"** quando qualquer uma das seguintes verificações não for atendida:

- **"/opt/isec/ens/threatprevention/bin/isecav --listtask"** retorna **Scan rápido, varredura completa** e ambas as varreduras <= 7 dias

- **"/opt/isec/ens/threatprevention/bin/isecav --listtask"** retorna **dat e tempo** de atualização do motor e ambos <= 7 dias

- **"/opt/isec/ens/threatprevention/bin/isecav --getoasconfig --summary"** retorna no status **de varredura de acesso**

## <a name="sophos-antivirus-for-linux"></a>Sophos Antivirus para Linux 

O Security Center recomenda que você **"Instale soluções de proteção de ponto final na máquina virtual"** quando qualquer uma das seguintes verificações não for atendida:

- Arquivo **/opt/sophos-av/bin/savdstatus** saídas ou pesquisa por localização personalizada **"readlink $(que savscan)"**

- **"/opt/sophos-av/bin/savdstatus --version"** retorna nome Sophos = **Versão Sophos Anti-Virus e Sophos >= 9**

O Security Center recomenda que você **"Resolva problemas de saúde de proteção de ponto final em suas máquinas"** quando qualquer uma das seguintes verificações não for atendida:

- **"/opt/sophos-av/bin/savlog -maxage=7 | grep -i "Varredura programada . \* completou" | cauda -1"**, retorna um valor

- **"/opt/sophos-av/bin/savlog -maxage=7 | grep "scan acabado"** | cauda -1", retorna um valor

- **"/opt/sophos-av/bin/savdstatus --lastupdate"** retorna lastUpdate, que deve ser <= 7 dias 

- **"/opt/sophos-av/bin/savdstatus -v"** é igual a **"A varredura de acesso está sendo realizada"** 

- **Retornos "/opt/sophos-av/bin/savconfig get LiveProtection"** ativados

## <a name="troubleshoot-and-support"></a>Solução de problemas e suporte

### <a name="troubleshoot"></a>Solução de problemas

Os logs de extensão do Microsoft Antimalware estão disponíveis em: **%Systemdrive%\WindowsAzure\Logs\Plugins\Microsoft.Azure.Security.IaaSAntimalware(Ou PaaSAntimalware)\1.5.5.x(versão#)\CommandExecution.log**

### <a name="support"></a>Suporte

Para obter mais ajuda, entre em contato com os especialistas do Azure nos [fóruns MSDN Azure e Stack Overflow](https://azure.microsoft.com/support/forums/). Ou registrar um incidente de suporte ao Azure. Vá ao site de suporte do [Azure](https://azure.microsoft.com/support/options/) e selecione Obter suporte. Para saber mais sobre como usar o suporte do Azure, leia as [Perguntas frequentes sobre o suporte do Microsoft Azure](https://azure.microsoft.com/support/faq/).