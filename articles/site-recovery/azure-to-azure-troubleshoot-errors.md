---
title: Solução de problemas da replicação do Azure VM na recuperação do site do Azure
description: Solucionar problemas ao replicar máquinas virtuais do Azure para recuperação de desastres.
author: rochakm
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 04/08/2019
ms.author: rochakm
ms.openlocfilehash: c131609a5622061e2ea49db422bc4e9da96666d1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80276675"
---
# <a name="troubleshoot-azure-to-azure-vm-replication-errors"></a>Solucionar problemas de replicação do Azure-to-Azure VM

Este artigo descreve como solucionar erros comuns no Azure Site Recovery durante a replicação e recuperação de máquinas virtuais (VMs) do Azure de uma região para outra. Para obter mais informações sobre configurações com suporte, consulte a [matriz de suporte para replicar máquinas virtuais do Azure](site-recovery-support-matrix-azure-to-azure.md).

## <a name="azure-resource-quota-issues-error-code-150097"></a><a name="azure-resource-quota-issues-error-code-150097"></a>Problemas de cota de recursos do Azure (código de erro 150097)

Certifique-se de que sua assinatura está habilitada para criar VMs do Azure na região de destino que você planeja usar como sua região de recuperação de desastres. Certifique-se também de que sua assinatura tenha cota suficiente para criar VMs dos tamanhos necessários. Por padrão, a Recuperação do Site escolhe um tamanho de VM de destino que é o mesmo que o tamanho da VM de origem. Se o tamanho correspondente não estiver disponível, a Recuperação do Site escolhe automaticamente o tamanho mais próximo disponível.

Se não houver um tamanho que suporte a configuração vm de origem, a seguinte mensagem será exibida:

> "A replicação não poderia ser ativada para a máquina virtual *VmName*."

### <a name="possible-causes"></a>Possíveis causas

- Seu ID de assinatura não está habilitado para criar VMs no local da região de destino.
- Seu ID de assinatura não está habilitado, ou não tem cota suficiente, para criar tamanhos de VM específicos na localização da região alvo.
- Não é encontrado tamanho de VM de destino adequado para corresponder à contagem de interface de rede (NIC) da Fonte VM (NIC) para o ID de assinatura no local da região de destino.

### <a name="fix-the-problem"></a>Corrija o problema

Entre em contato com [o suporte de cobrança do Azure](https://docs.microsoft.com/azure/azure-portal/supportability/resource-manager-core-quotas-request) para permitir que sua assinatura crie VMs dos tamanhos necessários no local de destino. Então, tente novamente a operação fracassada.

Se o local de destino tiver uma restrição de capacidade, desative a replicação para ele. Em seguida, habilite a replicação para um local diferente onde sua assinatura tenha cota suficiente para criar VMs dos tamanhos necessários.

## <a name="trusted-root-certificates-error-code-151066"></a><a name="trusted-root-certificates-error-code-151066"></a>Certificados de raiz confiável (código de erro 151066)

Se nem todos os certificados de raiz confiáveis mais recentes estiverem presentes na VM, o trabalho de recuperação do site "Ativar replicação" pode falhar. A autenticação e a autorização das chamadas de serviço de recuperação de site da VM falham sem esses certificados.

Se o trabalho "Ativar replicação" falhar, a seguinte mensagem será exibida:

> "Falha na configuração de recuperação do site."

### <a name="possible-cause"></a>Possível causa

Os certificados de raiz confiáveis necessários para autorização e autenticação não estão presentes na máquina virtual.

### <a name="fix-the-problem"></a>Corrija o problema

#### <a name="windows"></a>Windows

Para uma VM executando o sistema operacional Windows, instale as últimas atualizações do Windows na VM para que todos os certificados raiz confiáveis estejam presentes na máquina. Siga o típico processo típico de gerenciamento de atualizações do Windows ou de gerenciamento de atualizações de certificados em sua organização para obter os certificados raiz mais recentes e a lista atualizada de revogação de certificados nas VMs.

Se você estiver em um ambiente desconectado, siga o processo de atualização padrão do Windows em sua organização para obter os certificados. Se os certificados necessários não estiverem presentes na VM, as chamadas para o serviço de recuperação de Site falharão por razões de segurança.

Para verificar se o problema estiver resolvido, vá para login.microsoftonline.com em um navegador em sua VM.

Para obter mais informações, consulte [Configurar raízes confiáveis e certificados proibidos](https://technet.microsoft.com/library/dn265983.aspx).

#### <a name="linux"></a>Linux

Siga as orientações fornecidas pelo distribuidor da versão do sistema operacional Linux para obter os mais recentes certificados de raiz confiáveis e a lista de revogação de certificados mais recente na VM.

Como o SUSE Linux usa links simbólicos (ou *simelos)* para manter uma lista de certificados, siga estas etapas:

1. Entre como um usuário raiz.

1. Execute este comando para alterar o diretório:

    **# cd /etc/ssl/certs**

1. Verifique se o certificado symantec raiz CA está presente:

    **# is VeriSign_Class_3_Public_Primary_Certification_Authority_G5.pem**

1. Se o certificado ca raiz Symantec não for encontrado, execute o seguinte comando para baixar o arquivo. Verifique se há erros e siga as ações recomendadas para falhas de rede.

    **Wget https://docs.broadcom.com/docs-and-downloads/content/dam/symantec/docs/other-resources/verisign-class-3-public-primary-certification-authority-g5-en.pem -O VeriSign_Class_3_Public_Primary_Certification_Authority_G5.pem**

1. Verifique se o certificado ca raiz de Baltimore está presente:

    **# ls Baltimore_CyberTrust_Root.pem**

1. Se o certificado CA raiz de Baltimore não for encontrado, execute este comando para baixar o certificado:

    **Wget https://www.digicert.com/CACerts/BaltimoreCyberTrustRoot.crt.pem -O Baltimore_CyberTrust_Root.pem**

1. Verifique se o certificado de DigiCert_Global_Root_CA está presente:

    **Ls DigiCert_Global_Root_CA.pem**

1. Se o DigiCert_Global_Root_CA não for encontrado, execute os seguintes comandos para baixar o certificado:

    **# wgethttp://www.digicert.com/CACerts/DigiCertGlobalRootCA.crt**

    **# openssl x509 -in DigiCertGlobalRootCA.crt -inform der-outform pem -outform DigiCert_Global_Root_CA.pem**

1. Execute o script rehash para atualizar os hashes do assunto do certificado para os certificados recém-baixados:

    **c_rehash**

1. Execute esses comandos para verificar se os hashes do assunto como simelos foram criados para os certificados:

    - Comando:

        **Ls -l | grep Baltimore**

    - Saída:

        `lrwxrwxrwx 1 root root   29 Jan  8 09:48 3ad48a91.0 -> Baltimore_CyberTrust_Root.pem`

        `-rw-r--r-- 1 root root 1303 Jun  5  2014 Baltimore_CyberTrust_Root.pem`

    - Comando:

        **Ls -l | VeriSign_Class_3_Public_Primary_Certification_Authority_G5 grep**

    - Saída:

        `-rw-r--r-- 1 root root 1774 Jun  5  2014 VeriSign_Class_3_Public_Primary_Certification_Authority_G5.pem`

        `lrwxrwxrwx 1 root root   62 Jan  8 09:48 facacbc6.0 -> VeriSign_Class_3_Public_Primary_Certification_Authority_G5.pem`

    - Comando:

        **Ls -l | DigiCert_Global_Root grep**

    - Saída:

        `lrwxrwxrwx 1 root root   27 Jan  8 09:48 399e7759.0 -> DigiCert_Global_Root_CA.pem`

        `-rw-r--r-- 1 root root 1380 Jun  5  2014 DigiCert_Global_Root_CA.pem`

1. Criar uma cópia do arquivo VeriSign_Class_3_Public_Primary_Certification_Authority_G5.pem com nome de arquivo b204d74a.0:

    **# cp VeriSign_Class_3_Public_Primary_Certification_Authority_G5.pem b204d74a.0**

1. Criar uma cópia do arquivo Baltimore_CyberTrust_Root.pem com o nome do arquivo 653b494a.0:

    **# cp Baltimore_CyberTrust_Root.pem 653b494a.0**

1. Criar uma cópia do arquivo DigiCert_Global_Root_CA.pem com o nome do arquivo 3513523f.0:

    **# cp DigiCert_Global_Root_CA.pem 3513523f.0**

1. Verifique se os arquivos estão presentes:

    - Comando:

        **# ls -l 653b494a.0 b204d74a.0 3513523f.0**

    - Saída

        `-rw-r--r-- 1 root root 1774 Jan  8 09:52 3513523f.0`

        `-rw-r--r-- 1 root root 1303 Jan  8 09:52 653b494a.0`

        `-rw-r--r-- 1 root root 1774 Jan  8 09:52 b204d74a.0`

## <a name="outbound-connectivity-for-site-recovery-urls-or-ip-ranges-error-code-151037-or-151072"></a>Conectividade de saída para intervalos de IP ou URLs de recuperação de Site (código de erro 151037 ou 151072)

Para que a replicação de recuperação de site funcione, a conectividade de saída para URLs específicos é necessária a partir da VM. Se a VM estiver atrás de um firewall ou usa regras de grupo de segurança de rede (NSG) para controlar a conectividade de saída, você poderá enfrentar um desses problemas. Observe que, enquanto continuamos a suportar acesso de saída via URLs, usando uma lista de permitir de intervalos IP não é mais suportado.

### <a name="issue-1-failed-to-register-azure-virtual-machine-with-site-recovery-151195-br"></a><a name="issue-1-failed-to-register-azure-virtual-machine-with-site-recovery-151195-br"></a>Problema 1: falha ao registrar a máquina virtual do Azure com o Site Recovery (151195) </br>
- **Possível causa** </br>
  - A conexão não pode ser estabelecida nos pontos finais de recuperação do site devido à falha de resolução do DNS.
  - Isso é visto com mais frequência durante a proteção novamente quando você fizer failover da máquina virtual, mas o servidor DNS não está acessível da região de recuperação de Desastre.

- **Resolução**
   - Se você estiver usando o DNS personalizado, certifique-se de que o servidor DNS está acessível da região de recuperação de desastres. Para verificar se você tem um DNS personalizado vá para a VM > rede de Recuperação de Desastres > servidores DNS. Tente acessar o servidor DNS da máquina virtual. Se não estiver acessível, em seguida, disponibilize-o failover do servidor DNS ou crie a linha de site entre a rede de recuperação de Desastre e DNS.

    ![com-erro](./media/azure-to-azure-troubleshoot-errors/custom_dns.png)


### <a name="issue-2-site-recovery-configuration-failed-151196"></a>Problema 2: falha na configuração do Azure Site Recovery (151196)
- **Possível causa** </br>
  - Não é possível estabelecer a Conexão para pontos de extremidade de IP4 de identidade e autenticação do Office 365.

- **Resolução**
  - O Azure Site Recovery precisa de acesso aos intervalos de IPs do Office 365 para autenticação.
    Se você estiver usando as regras do Azure Network Security Group (NSG) para controlar a conectividade de rede de saída na VM, certifique-se de usar a regra NSG [(AAD) baseada em tag de serviço Azure Active Directory (AAD)](../virtual-network/security-overview.md#service-tags) para permitir o acesso ao AAD. Não suportamos mais as regras de NSG baseadas em endereçoip.


### <a name="issue-3-site-recovery-configuration-failed-151197"></a>Problema 3: falha na configuração do Site Recovery (151197)
- **Possível causa** </br>
  - Não é possível estabelecer conexão com pontos de extremidade de serviço de Recuperação de Site.

- **Resolução**
  - Se você estiver usando as regras do Azure Network Security Group (NSG) para controlar a conectividade de rede de saída na VM, certifique-se de usar tags de serviço. Não temos mais suporte usando uma lista de permitir endereços IP via NSGs para ASR (Azure Site Recovery).


### <a name="issue-4-a2a-replication-failed-when-the-network-traffic-goes-through-on-premise-proxy-server-151072"></a>Problema 4: a replicação de A2A falhou quando o tráfego de rede passou pelo servidor proxy no local (151072)
 - **Possível causa** </br>
   - As configurações de proxy personalizadas são inválidas, e o agente do Serviço de Mobilidade do ASR não detectou automaticamente as configurações de proxy do IE


 - **Resolução**
   1.    O agente do Serviço de Mobilidade detecta as configurações de proxy do IE no Windows e o ambiente /etc/ no Linux.
   2.  Se você preferir definir o proxy somente para o Serviço de Mobilidade do ASR, você poderá fornecer os detalhes do proxy no ProxyInfo.conf localizado em:</br>
       - ``/usr/local/InMage/config/``no ***Linux***
       - ``C:\ProgramData\Microsoft Azure Site Recovery\Config``no ***Windows***
   3.    O ProxyInfo.conf deve ter as configurações de proxy no seguinte formato INI.</br>
                   *[proxy]*</br>
                   *Endereço=http://1.2.3.4*</br>
                   *Port=567*</br>
   4. O agente do Serviço de Mobilidade do ASR suporta apenas ***proxies não autenticados***.


### <a name="fix-the-problem"></a>Corrija o problema

Para adicionar [os URLs necessários](azure-to-azure-about-networking.md#outbound-connectivity-for-urls) a uma lista de permitir, siga as etapas do [documento de orientação de rede](site-recovery-azure-to-azure-networking-guidance.md).


### <a name="more-information"></a>Mais informações

Para especificar [URLs necessários](azure-to-azure-about-networking.md#outbound-connectivity-for-urls) ou [faixas DE IP necessárias,](azure-to-azure-about-networking.md#outbound-connectivity-using-service-tags)siga a orientação em [Sobre a rede no Azure para a replicação do Azure](site-recovery-azure-to-azure-networking-guidance.md).

## <a name="disk-not-found-in-the-machine-error-code-150039"></a>Disco não encontrado na máquina (código de erro 150039)

Um novo disco anexado à máquina virtual deve ser inicializado. Se o disco não for encontrado, a seguinte mensagem será exibida:

> "O disco de dados Azure *DiskName* *DiskURI* com o número da unidade lógica *LUN* *LUNValue* não foi mapeado para um disco correspondente sendo relatado dentro da VM que tem o mesmo valor de LUN.

### <a name="possible-causes"></a>Possíveis causas

- Um novo disco de dados foi anexado à VM, mas não foi inicializado.
- O disco de dados dentro da VM não está relatando corretamente o valor do número da unidade lógica (LUN) no qual o disco foi anexado à VM.

### <a name="fix-the-problem"></a>Corrija o problema

Certifique-se de que os discos de dados sejam inicializados e, em seguida, tente novamente a operação.

- **Windows**: [Anexar e inicializar um novo disco](https://docs.microsoft.com/azure/virtual-machines/windows/attach-managed-disk-portal).

- **Linux**: [Inicialize um novo disco de dados no Linux](https://docs.microsoft.com/azure/virtual-machines/linux/add-disk).

Se o problema persistir, contate o Suporte.

## <a name="one-or-more-disks-are-available-for-protection-error-code-153039"></a>Um ou mais discos estão disponíveis para proteção (código de erro 153039)

### <a name="possible-causes"></a>Possíveis causas

- Um ou mais discos foram adicionados recentemente à máquina virtual após proteção.
- Um ou mais discos foram inicializados após a proteção da máquina virtual.

### <a name="fix-the-problem"></a>Corrija o problema

Para tornar o status de replicação da VM saudável novamente, você pode optar por proteger os discos ou descartar o aviso.

#### <a name="to-protect-the-disks"></a>Para proteger os discos

1. Ir para **Itens replicados Discos** > *de nome* > VM **.**
1. Selecione o disco desprotegido e selecione **Ativar a replicação:**

    ![Habilite a replicação em discos VM](./media/azure-to-azure-troubleshoot-errors/add-disk.png)

#### <a name="to-dismiss-the-warning"></a>Para descartar o aviso

1. Vá para **itens** > replicados nome*VM*.
1. Selecione o aviso na seção **Visão geral** e selecione **OK**.

    ![Descarte o aviso de novo disco](./media/azure-to-azure-troubleshoot-errors/dismiss-warning.png)

## <a name="remove-the-virtual-machine-from-the-vault-completed-with-information-error-code-150225"></a>Remova a máquina virtual do cofre concluída com informações (código de erro 150225)

Quando protege a máquina virtual, o Site Recovery cria alguns links na máquina virtual de origem. Quando você remove a proteção ou desativa a replicação, o Site Recovery remove esses links como parte do trabalho de limpeza. Se a máquina virtual tiver um bloqueio de recursos, o trabalho de limpeza será concluído com as informações. As informações dizem que a máquina virtual foi removida do cofre dos Serviços de Recuperação, mas que alguns dos links obsoletos não poderiam ser limpos na máquina de origem.

Você pode ignorar este aviso se você nunca pretende proteger esta máquina virtual novamente. Mas se você tiver que proteger esta máquina virtual mais tarde, siga as etapas em "Corrigir o problema" para limpar os links.

> [!WARNING]
> Se você não fizer a limpeza:
>
> - Quando você habilitar a replicação por meio do cofre dos Serviços de Recuperação, a máquina virtual não será listada.
> - Se você tentar proteger a VM usando configurações **de máquina** > virtual Recuperação de**desastres,** > **Disaster Recovery**a operação falhará com a mensagem "A replicação não pode ser ativada devido aos links de recursos obsoletos existentes na VM".

### <a name="fix-the-problem"></a>Corrija o problema

> [!NOTE]
> A Recuperação do Site não exclui a máquina virtual de origem ou a afeta de forma alguma enquanto executa essas etapas.

1. Remova o bloqueio do grupo de recursos VM ou VM. Por exemplo, na imagem a seguir, o bloqueio de recursos na VM chamada "MoveDemo" deve ser excluído:

    ![Remover bloqueio da VM](./media/site-recovery-azure-to-azure-troubleshoot/vm-locks.png)

1. Baixe o script para [remover uma configuração de recuperação de site obsoleta](https://github.com/AsrOneSdk/published-scripts/blob/master/Cleanup-Stale-ASR-Config-Azure-VM.ps1).
1. Execute o script, que é chamado de Cleanup-sod-asr-config-Azure-VM.ps1. Forneça o ID de assinatura, o VM Resource Group e o nome VM como parâmetros.
1. Se lhe pedirem credenciais do Azure, forneça-as. Em seguida, verifique se o script é executado sem falhas.

## <a name="replication-cant-be-enabled-because-of-stale-resource-links-on-the-vm-error-code-150226"></a>A replicação não pode ser ativada devido a links de recursos obsoletos na VM (código de erro 150226)

### <a name="possible-cause"></a>Possível causa

A máquina virtual tem uma configuração obsoleta da proteção anterior de recuperação de site.

Uma configuração obsoleta pode ocorrer em uma VM do Azure se você habilitou a replicação para o Azure VM usando a Recuperação do Site e, em seguida:

- Você desabilitou a replicação, mas a VM de origem tinha um bloqueio de recursos.
- Você excluiu o cofre de recuperação de site sem desativar explicitamente a replicação na VM.
- Você excluiu o grupo de recursos que contém o cofre de recuperação do site sem desativar explicitamente a replicação na VM.

### <a name="fix-the-problem"></a>Corrija o problema

> [!NOTE]
> A Recuperação do Site não exclui a máquina virtual de origem ou a afeta de forma alguma enquanto executa essas etapas.

1. Remova o bloqueio do grupo de recursos VM ou VM. Por exemplo, na imagem a seguir, o bloqueio de recursos na VM chamada "MoveDemo" deve ser excluído:

    ![Remover bloqueio da VM](./media/site-recovery-azure-to-azure-troubleshoot/vm-locks.png)

1. Baixe o script para [remover uma configuração de recuperação de site obsoleta](https://github.com/AsrOneSdk/published-scripts/blob/master/Cleanup-Stale-ASR-Config-Azure-VM.ps1).
1. Execute o script, que é chamado de Cleanup-sod-asr-config-Azure-VM.ps1. Forneça o ID de assinatura, o VM Resource Group e o nome VM como parâmetros.
1. Se lhe pedirem credenciais do Azure, forneça-as. Em seguida, verifique se o script é executado sem falhas.

## <a name="unable-to-see-the-azure-vm-or-resource-group-for-the-selection-in-the-enable-replication-job"></a>Não é possível ver o Azure VM ou o grupo de recursos para a seleção no trabalho "Ativar replicação"

### <a name="cause-1-the-resource-group-and-source-virtual-machine-are-in-different-locations"></a>Causa 1: O grupo de recursos e a máquina virtual de origem estão em diferentes locais

Atualmente, a Recuperação do Site exige que o grupo de recursos da região de origem e as máquinas virtuais estejam no mesmo local. Se não forem, você não será capaz de encontrar a máquina virtual ou o grupo de recursos quando tentar aplicar proteção.

Como solução de solução, você pode habilitar a replicação da VM em vez do cofre dos Serviços de Recuperação. Vá para A**Recuperação de Desastres das** **Propriedades** > da **VM** > fonte e habilite a replicação.

### <a name="cause-2-the-resource-group-is-not-part-of-the-selected-subscription"></a>Causa 2: O grupo de recursos não faz parte da assinatura selecionada

Você pode não ser capaz de encontrar o grupo de recursos no momento da proteção se o grupo de recursos não fizer parte da assinatura selecionada. Certifique-se de que o grupo de recursos pertence à assinatura que você está usando.

### <a name="cause-3-stale-configuration"></a>Causa 3: Configuração obsoleta

Você pode não ver a VM que deseja habilitar para replicação se uma configuração de recuperação de site obsoleta tiver sido deixada na VM do Azure. Essa condição pode ocorrer se você habilitou a replicação para o Azure VM usando a Recuperação do Site e, em seguida:

- Você excluiu o cofre de recuperação de site sem desativar explicitamente a replicação na VM.
- Você excluiu o grupo de recursos que contém o cofre de recuperação do site sem desativar explicitamente a replicação na VM.
- Você desabilitou a replicação, mas a VM de origem tinha um bloqueio de recursos.

### <a name="fix-the-problem"></a>Corrija o problema

> [!NOTE]
> Certifique-se de atualizar o módulo "AzureRM.Resources" antes de usar o script mencionado nesta seção.  A Recuperação do Site não exclui a máquina virtual de origem ou a afeta de forma alguma enquanto executa essas etapas.

1. Remova o bloqueio, se houver, do grupo de recursos VM ou VM. Por exemplo, na imagem a seguir, o bloqueio de recursos na VM chamada "MoveDemo" deve ser excluído:

    ![Remover bloqueio da VM](./media/site-recovery-azure-to-azure-troubleshoot/vm-locks.png)

1. Baixe o script para [remover uma configuração de recuperação de site obsoleta](https://github.com/AsrOneSdk/published-scripts/blob/master/Cleanup-Stale-ASR-Config-Azure-VM.ps1).
1. Execute o script, que é chamado de Cleanup-sod-asr-config-Azure-VM.ps1. Forneça o ID de assinatura, o VM Resource Group e o nome VM como parâmetros.
1. Se lhe pedirem credenciais do Azure, forneça-as. Em seguida, verifique se o script é executado sem falhas.

## <a name="unable-to-select-a-virtual-machine-for-protection"></a>Não é possível selecionar uma máquina virtual para proteção

### <a name="cause-1-the-virtual-machine-has-an-extension-installed-in-a-failed-or-unresponsive-state"></a>Causa 1: A máquina virtual tem uma extensão instalada em um estado com falha ou sem resposta

Vá para as > **configurações de configurações** > de**máquinas** **virtuais**e verifique se há extensões em um estado com falha. Desinstale qualquer extensão com falha e tente novamente proteger a máquina virtual.

### <a name="cause-2-the-vms-provisioning-state-is-not-valid"></a>Causa 2: O estado de provisionamento da VM não é válido

Veja as etapas de solução de problemas no [estado de provisionamento da VM não são válidas](#the-vms-provisioning-state-is-not-valid-error-code-150019), mais tarde neste artigo.

## <a name="the-vms-provisioning-state-is-not-valid-error-code-150019"></a>O estado de provisionamento da VM não é válido (código de erro 150019)

Para habilitar a replicação na VM, seu estado de provisionamento deve ser **bem sucedido**. Siga estas etapas para verificar o estado de provisionamento:

1. No portal Azure, selecione o Explorador de **recursos** de **todos os serviços**.
1. Expanda a lista **Assinaturas** e selecione sua assinatura.
1. Expanda a lista **ResourceGroups** e selecione o grupo de recursos da VM.
1. Expanda a lista **recursos** e selecione sua VM.
1. Verifique o **campo provisionamentoEstado** na exibição instância no lado direito.

### <a name="fix-the-problem"></a>Corrija o problema

- Se **provisioningState** estiver com **Falha**, entre em contato com o suporte com detalhes para solucionar o problema.
- Se **o provisionamentoState** estiver **sendo atualizado,** outra extensão poderá ser implantada. Verifique se há operações em andamento na VM, espere que elas terminem e, em seguida, tente novamente o trabalho de recuperação de site com falha "Habilitar replicação".

## <a name="unable-to-select-target-vm-network-selection-tab-is-unavailable"></a>Não é possível selecionar vm de destino (a guia de seleção de rede está indisponível)

### <a name="cause-1-your-vm-is-attached-to-a-network-thats-already-mapped-to-a-target-network"></a>Causa 1: Sua VM está conectada a uma rede que já está mapeada para uma rede de destino

Se a VM de origem fizer parte de uma rede virtual e outra VM da mesma rede virtual já estiver mapeada com uma rede no grupo de recursos de destino, a caixa de lista suspensa de seleção de rede não está disponível (aparece escurecida) por padrão.

![Lista de seleção de rede indisponível](./media/site-recovery-azure-to-azure-troubleshoot/unabletoselectnw.png)

### <a name="cause-2-you-previously-protected-the-vm-by-using-site-recovery-and-then-you-disabled-the-replication"></a>Causa 2: Você protegeu anteriormente a VM usando a Recuperação do Site e, em seguida, desativou a replicação

Desativar a replicação de uma VM não exclui o mapeamento da rede. O mapeamento deve ser excluído do cofre dos Serviços de Recuperação onde a VM foi protegida. Vá para o mapeamento da rede de gerenciamento da > **infra-estrutura** > de recuperação do site do serviço de recuperação de *serviços***de recuperação**.

![Excluir mapeamento de rede](./media/site-recovery-azure-to-azure-troubleshoot/delete_nw_mapping.png)

A rede de destino configurada durante a configuração de recuperação de desastres pode ser alterada após a configuração inicial, após a vm ser protegida:

![Modificar o mapeamento da rede](./media/site-recovery-azure-to-azure-troubleshoot/modify_nw_mapping.png)

Observe que a alteração do mapeamento da rede afeta todas as VMs protegidas que usam esse mesmo mapeamento de rede.

## <a name="com-or-volume-shadow-copy-service-error-error-code-151025"></a>Erro de serviço com+ ou cópia de sombra de volume (código de erro 151025)

Quando esse erro ocorre, a seguinte mensagem aparece:

> "Extensão de recuperação de site não foi instalada"

### <a name="possible-causes"></a>Possíveis causas

- O serviço de aplicação do sistema COM+ está desativado.
- O serviço Volume Shadow Copy está desativado.

### <a name="fix-the-problem"></a>Corrija o problema

Defina os serviços com aplicativo do sistema COM+ e cópia de sombra de volume para o modo de inicialização automático ou manual.

1. Abra o console serviços no Windows.
1. Certifique-se de que os serviços com aplicativo do sistema COM+ e cópia de sombra de volume não estão **definidos** como desativados como seu **tipo de inicialização**.

    ![Verifique o tipo de inicialização dos serviços de aplicativo do sistema COM+ e do Volume Shadow Copy](./media/azure-to-azure-troubleshoot-errors/com-error.png)

## <a name="unsupported-managed-disk-size-error-code-150172"></a>Tamanho do disco gerenciado sem suporte (código de erro 150172)

Quando esse erro ocorre, a seguinte mensagem aparece:

> "A proteção não poderia ser ativada para a máquina virtual, pois ela tem *DiskName* com tamanho *DiskSize*)* que é menor do que o tamanho mínimo suportado de 1024 MB."

### <a name="possible-cause"></a>Possível causa

O disco é menor do que o tamanho suportado de 1024 MB.

### <a name="fix-the-problem"></a>Corrija o problema

Certifique-se de que o tamanho do disco está dentro da faixa de tamanho suportada e, em seguida, tente novamente a operação.

## <a name="protection-was-not-enabled-because-the-grub-configuration-includes-the-device-name-instead-of-the-uuid-error-code-151126"></a><a name="enable-protection-failed-as-device-name-mentioned-in-the-grub-configuration-instead-of-uuid-error-code-151126"></a>A proteção não foi ativada porque a configuração do GRUB inclui o nome do dispositivo em vez do UUID (código de erro 151126)

### <a name="possible-cause"></a>Possível causa

Os arquivos de configuração do Linux GRUB (/boot/grub/menu.lst", /boot/grub/grub.cfg, /boot/grub2/grub.cfg ou /etc/default/grub) podem especificar os nomes reais do dispositivo em vez de valores UUID para os parâmetros *raiz* e *currículo.* A recuperação do site requer UUIDs porque os nomes dos dispositivos podem mudar. Após a reinicialização, uma VM pode não aparecer com o mesmo nome no failover, resultando em problemas.

Os exemplos a seguir são linhas de arquivos GRUB onde nomes de dispositivos (mostrados em negrito) aparecem em vez dos UUIDs necessários:

- Arquivo /boot/grub2/grub.cfg

  > linux   /boot/vmlinuz-3.12.49-11-default **root=/dev/sda2**  ${extra_cmdline} **resume=/dev/sda1** splash=silent quiet showopts

- Arquivo: /boot/grub/menu.lst

  > kernel /boot/vmlinuz-3.0.101-63-default **root=/dev/sda2** **resume=/dev/sda1** splash=silent crashkernel=256M-:128M showopts vga=0x314


### <a name="fix-the-problem"></a>Corrija o problema

Substitua cada nome do dispositivo pelo UUID correspondente:

1. Encontre o UUID do dispositivo executando o nome do ***dispositivo*** **blkid** de comando . Por exemplo: 

    ```
    blkid /dev/sda1
    /dev/sda1: UUID="6f614b44-433b-431b-9ca1-4dd2f6f74f6b" TYPE="swap"
    blkid /dev/sda2
    /dev/sda2: UUID="62927e85-f7ba-40bc-9993-cc1feeb191e4" TYPE="ext3"
   ```

1. Substitua o nome do dispositivo pelo seu UUID, nos formatos **root=UUID**=*UUID UUID* e **resume=UUID**=*UUID UUID*. Por exemplo, após a substituição, a linha de /boot/grub/menu.lst (discutido anteriormente) ficaria assim:

    > kernel /boot/vmlinuz-3.0.101-63-default **root=UUID=62927e85-f7ba-40bc-9993-cc1feeb191e4** **resume=UUID=6f614b44-433b-431b-9ca1-4dd2f6f74f6b** splash=silent crashkernel=256M-:128M showopts vga=0x314

1. Tente novamente a proteção.

## <a name="enable-protection-failed-because-the-device-mentioned-in-the-grub-configuration-doesnt-exist-error-code-151124"></a>Habilitar a proteção falhou porque o dispositivo mencionado na configuração GRUB não existe (código de erro 151124)

### <a name="possible-cause"></a>Possível causa

Os arquivos de configuração do GRUB (/boot/grub/menu.lst, /boot/grub/grub.cfg, /boot/grub2/grub.cfg ou /etc/default/grub) podem conter os parâmetros *rd.lvm.lv* ou *rd_LVM_LV*. Esses parâmetros identificam os dispositivos LVM (Logical Volume Manager, gerenciador de volumes lógicos) que devem ser descobertos na hora da inicialização. Se esses dispositivos LVM não existirem, o sistema protegido em si não será inicializado e ficará preso no processo de inicialização. O mesmo problema também será visto com o Failover VM. Veja alguns exemplos:

- Arquivo: /boot/grub2/grub.cfg em RHEL7:

    > linux16 /vmlinuz-3.10.0-957.el7.x86_64 root=/dev/mapper/rhel_mup-rhel7u6-root ro crashkernel=128M\@64M **rd.lvm.lv=rootvg/root rd.lvm.lv=rootvg/rhgb** quiet a lang=en_US. UTF-8

- Arquivo: /etc/default/grub no RHEL7:

    > GRUB_CMDLINE_LINUX="crashkernel=auto **rd.lvm.lv=rootvg/root rd.lvm.lv=rootvg/swap** rhgb quiet"

- Arquivo: /boot/grub/menu.lst em RHEL6:

    > kernel /vmlinuz-2.6.32-754.el6.x86_64 ro root=UUID=36dd8b45-e90d-40d6-81ac-ad0d0725d69e rd_NO_LUKS LANG=en_US. UTF-8 rd_NO_MD SYSFONT=latarcyrheb-sun16 crashkernel=auto **rd_LVM_LV=rootvg/lv_root** KEYBOARDTYPE=pc KEYTABLE=us **rd_LVM_LV=rootvg/lv_swap** rd_NO_DM rhgb quiet

Em cada exemplo, a parte em negrito mostra que o GRUB tem que detectar dois dispositivos LVM com os nomes "root" e "swap" do grupo de volume "rootvg".

### <a name="fix-the-problem"></a>Corrija o problema

Se o dispositivo LVM não existir, crie-o ou remova os parâmetros correspondentes dos arquivos de configuração DO GRUB. Então, tente novamente ativar a proteção.

## <a name="a-site-recovery-mobility-service-update-finished-with-warnings-error-code-151083"></a>Uma atualização do Serviço de Mobilidade de Recuperação do Site terminou com avisos (código de erro 151083)

O Serviço de Mobilidade de Recuperação do Site tem muitos componentes, um dos quais é chamado de driver de filtro. O driver do filtro é carregado na memória do sistema somente durante a reinicialização do sistema. Sempre que uma atualização do Serviço de Mobilidade inclui alterações do driver de filtro, a máquina é atualizada, mas você ainda vê um aviso de que algumas correções requerem uma reinicialização. O aviso aparece porque as correções do filtro do driver só podem fazer efeito quando o novo driver do filtro estiver carregado, o que acontece apenas durante uma reinicialização.

> [!NOTE]
> Isso é apenas um aviso. A replicação existente continua a funcionar mesmo após a nova atualização do agente. Você pode optar por reiniciar sempre que quiser os benefícios do novo driver de filtro, mas o antigo driver de filtro continua funcionando se você não reiniciar.
>
> Além do driver de filtro, os benefícios de quaisquer outras melhorias e correções na atualização do Serviço de Mobilidade fazem efeito sem exigir uma reinicialização.

## <a name="protection-couldnt-be-enabled-because-the-replica-managed-disk-already-exists-without-expected-tags-in-the-target-resource-group-error-code-150161"></a>A proteção não pôde ser ativada porque o disco gerenciado de réplica já existe, sem tags esperadas, no grupo de recursos de destino (código de erro 150161)

### <a name="possible-cause"></a>Possível causa

Esse problema pode ocorrer se a máquina virtual estiver previamente protegida e, quando a replicação foi desativada, o disco de réplica não foi limpo.

### <a name="fix-the-problem"></a>Corrija o problema

Exclua o disco de réplica identificado na mensagem de erro e tente novamente o trabalho de proteção falha.

## <a name="next-steps"></a>Próximas etapas

[Replicar as máquinas virtuais do Azure](site-recovery-replicate-azure-to-azure.md)
