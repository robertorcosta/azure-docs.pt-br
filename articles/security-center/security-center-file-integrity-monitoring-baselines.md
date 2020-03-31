---
title: Monitoramento de integridade de arquivo na Central de Segurança do Azure
description: Saiba como comparar as linhas de base com o Monitoramento de Integridade de Arquivos no Azure Security Center.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: c8a2a589-b737-46c1-b508-7ea52e301e8f
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/29/2019
ms.author: memildin
ms.openlocfilehash: bb45e1d1ee17a6daf16bd688982f79fda986bde5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "73664404"
---
# <a name="compare-baselines-using-file-integrity-monitoring-fim"></a>Comparar linhas de base usando o FIM (monitoramento de integridade de arquivo)

O File Integrity Monitoring (FIM) informa quando ocorrem alterações em áreas sensíveis em seus recursos, para que você possa investigar e abordar atividades não autorizadas. A FIM monitora arquivos windows, registros do Windows e arquivos Linux.

Este tópico explica como ativar o FIM nos arquivos e registros. Para obter mais informações sobre a FIM, consulte [O Monitoramento de Integridade de Arquivos no Azure Security Center](security-center-file-integrity-monitoring.md).

## <a name="why-use-fim"></a>Por que usar a FIM?

Sistema operacional, aplicativos e configurações associadas controlam o comportamento e o estado de segurança de seus recursos. Portanto, os invasores visam os arquivos que controlam seus recursos, a fim de ultrapassar o sistema operacional de um recurso e/ou executar atividades sem serem detectados.

De fato, muitas normas de conformidade normativa, como PCI-DSS & ISO 17799 exigem a implementação de controles FIM.  

## <a name="enable-built-in-recursive-registry-checks"></a>Habilite verificações de registro recursivas incorporadas

Os padrões de colmeia de registro FIM fornecem uma maneira conveniente de monitorar alterações recursivas dentro de áreas de segurança comuns.  Por exemplo, um adversário pode configurar um script para executar em LOCAL_SYSTEM contexto, configurando uma execução na inicialização ou desligamento.  Para monitorar alterações desse tipo, habilite a verificação incorporada.  

![Registro](./media/security-center-file-integrity-monitoring-baselines/baselines-registry.png)

>[!NOTE]
> Verificações recursivas aplicam-se apenas às colmeias de segurança recomendadas e não aos caminhos de registro personalizados.  

## <a name="adding-a-custom-registry-check"></a>Adicionando uma verificação de registro personalizado

As linhas de base da FIM começam identificando características de um estado conhecido para o sistema operacional e aplicativo de suporte.  Para este exemplo, nos concentraremos nas configurações da política de senha para o Windows Server 2008 ou superior.


|Nome da política                 | Configuração do Registro|
|---------------------------------------|-------------|
|Controlador de domínio: Recusar alterações de senha de conta de computador| MÁQUINA\Sistema\Corrente'ControleConjunto\Serviços \Netlogon\Parâmetros\RefusePasswordChange|
|Membro do domínio: Criptografar ou assinar digitalmente os dados de canal seguro (sempre)|MÁQUINA\Sistema\Corrente'ControleConjunto\Serviços \Netlogon\Parâmetros\RequireSignOrSeal|
|Membro do domínio: Criptografar digitalmente os dados de canal seguro (quando possível)|MÁQUINA\Sistema\Corrente'ControleConjunto\Serviços \Netlogon\Parameters\SealSecureChannel|
|Membro do domínio: Assinar digitalmente os dados de canal seguro (quando possível)|MÁQUINA\Sistema\Corrente'ControleConjunto\Serviços \Netlogon\Parâmetros\SignSecureChannel|
|Membro do domínio: Desativar alterações de senha de conta do computador|MÁQUINA\Sistema\Corrente'ControleConjunto\Serviços \Netlogon\Parâmetros\DesativarSenhadesenha|
|Membro do domínio: Idade máxima da senha de conta do computador|MACHINE\System\CurrentControlSet\Services \Netlogon\Parameters\MaximumPasswordAge|
|Membro do domínio: Requer uma chave de sessão forte (Windows 2000 ou posterior)|MACHINE\System\CurrentControlSet\Services \Netlogon\Parameters\RequireStrongKey|
|Segurança de rede: Restringir NTLM: Autenticação NTLM neste domínio|MÁQUINA\Sistema\CorrenteControleConjunto\Serviços \Netlogon\Parâmetros\RestrictNTLMInDomínioDomínio|
|Segurança de rede: Restringir NTLM: Adicionar exceções de servidor neste domínio|MÁQUINA\Sistema\CorrenteControleConjunto\Serviços \Netlogon\Parâmetros\DCAllowedNTLMServidores|
|Segurança de rede: Restringir NTLM: Autenticação NTLM neste domínio|MÁQUINA\Sistema\CorrenteControleConjunto\Serviços \Netlogon\Parâmetros\AuditNTLMInDomínioDomínio|

> [!NOTE]
> Para saber mais sobre as configurações de registro suportadas por várias versões do sistema operacional, consulte a [planilha de referência Configurações de diretiva de grupo](https://www.microsoft.com/download/confirmation.aspx?id=25250).

*Para configurar a FIM para monitorar as linhas de base do registro:*

1. Na janela **Adicionar registro do Windows para rastrear alterações,** na caixa de texto chave do registro do **Windows,** digite a chave de registro.

    <code>

    HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\Netlogon\Parameters
    </code>

      ![Habilite o FIM em um registro](./media/security-center-file-integrity-monitoring-baselines/baselines-add-registry.png)

## <a name="tracking-changes-to-windows-files"></a>Rastreamento de alterações em arquivos windows

1. Na janela **Adicionar arquivo do Windows para alterar rastreamento,** na caixa de texto Entrar no **caminho,** digite a pasta que contém os arquivos que você deseja rastrear. No exemplo na figura a seguir, **contoso Web App** reside no D:\ unidade dentro da estrutura da pasta **ContosWebApp.**  
1. Crie uma entrada de arquivo personalizada do Windows fornecendo um nome da classe de configuração, permitindo a recursão e especificando a pasta superior com um sufixo curinga (*).

    ![Habilite o FIM em um arquivo](./media/security-center-file-integrity-monitoring-baselines/baselines-add-file.png)

## <a name="retrieving-change-data"></a>Recuperando dados de alteração

Os dados de monitoramento de integridade do arquivo residem no conjunto de tabelas Azure Log Analytics / ConfigurationChange.  

 1. Defina um intervalo de tempo para recuperar um resumo das alterações por recurso.
No exemplo a seguir, estamos recuperando todas as alterações nos últimos quatorze dias nas categorias de registro e arquivos:

    <code>

    > ConfigurationChange

    > | where TimeGenerated > ago(14d)

    > | where ConfigChangeType in ('Registry', 'Files')

    > | summarize count() by Computer, ConfigChangeType

    </code>

1. Para exibir detalhes das alterações de registro:

    1. Remover **arquivos** da cláusula **onde,** 
    1. Remova a linha de sumário e substitua-a por uma cláusula de pedido:

    <code>

    > ConfigurationChange

    > | where TimeGenerated > ago(14d)

    > | where ConfigChangeType in ('Registry')

    > | order by Computer, RegistryKey

    </code>

Os relatórios podem ser exportados para csv para arquivamento e/ou canalizados para um relatório power bi.  

![Dados da FIM](./media/security-center-file-integrity-monitoring-baselines/baselines-data.png)