---
title: Certificação de máquina virtual – problemas e soluções
description: Este artigo explica mensagens de erro comuns para imagens de VM. Ele também aborda soluções relacionadas
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: troubleshooting
author: iqshahmicrosoft
ms.author: iqshah
ms.date: 06/16/2020
ms.openlocfilehash: 5878ea6a554439c261399706eec708b06ed59b11
ms.sourcegitcommit: 152c522bb5ad64e5c020b466b239cdac040b9377
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/14/2020
ms.locfileid: "88225353"
---
# <a name="issues-and-solutions-during-virtual-machine-certification"></a>Problemas e soluções durante a certificação de máquina virtual 

Quando você publica sua imagem de VM (máquina virtual) no Azure Marketplace, a equipe do Azure a valida para garantir sua inicialização, segurança e compatibilidade do Azure. Se qualquer um dos testes de alta qualidade falhar, a publicação falhará e você receberá uma mensagem de erro que descreve o problema.

Este artigo explica as mensagens de erro comuns durante a publicação de imagens de VM, juntamente com as soluções relacionadas.

> [!NOTE]
> Se você tiver dúvidas ou comentários para aprimoramento, entre em contato com o [suporte do Partner Center](https://partner.microsoft.com/support/v2/?stage=1).

## <a name="approved-base-image"></a>Imagem de base aprovada

Quando você envia uma solicitação para republicar a imagem com atualizações, o caso de teste de verificação de número de peça pode falhar. Se falhar, a imagem não será aprovada.

Essa falha ocorre quando você usa uma imagem base que pertence a outro Publicador e você atualizou a imagem. Nessa situação, você não terá permissão para publicar sua imagem.

Para corrigir esse problema, recupere a imagem do Azure Marketplace e faça alterações nela. Para obter mais informações, consulte os seguintes artigos:

- [Imagens do Linux](../../virtual-machines/linux/endorsed-distros.md?toc=/azure/virtual-machines/linux/toc.json)
- [Imagens do Windows](create-azure-vm-technical-asset.md#create-a-vm-image-using-an-approved-base)

## <a name="vm-extension-failure"></a>Falha na extensão da VM

Verifique se sua imagem dá suporte a extensões de VM.

Para habilitar as extensões de VM, faça o seguinte:

1. Selecione sua VM do Linux.
1. Vá para **configurações de diagnóstico**.
1. Habilite matrizes base atualizando a **conta de armazenamento**.
1. Selecione **Salvar**.

   ![Habilitar o monitoramento no nível do convidado](./media/vm-certification-issues-solutions-1.png)

Para verificar se as extensões de VM estão ativadas corretamente, faça o seguinte:

1. Na VM, selecione a guia **extensões de VM** e verifique o status da **extensão de diagnóstico do Linux**.
    * Se o status for *provisionado com êxito*, o caso de teste de extensões foi aprovado.  
    * Se o status for *falha no provisionamento*, o caso de teste de extensões falhou e você precisa definir o sinalizador de proteção.

      ![Captura de tela mostrando que o provisionamento foi bem-sucedido](./media/vm-certification-issues-solutions-2.png)

      Se a extensão da VM falhar, consulte [usar a extensão de diagnóstico do Linux para monitorar as métricas e os logs](../../virtual-machines/extensions/diagnostics-linux.md) para habilitá-la. Se você não quiser que a extensão de VM seja habilitada, entre em contato com a equipe de suporte e peça para desabilitá-la.

## <a name="vm-provisioning-issue"></a>Problema de provisionamento de VM

Verifique se você seguiu rigorosamente o processo de provisionamento da VM antes de enviar sua oferta. Para exibir o formato JSON para provisionar a VM, consulte [certificação de imagem de máquina virtual do Azure](azure-vm-image-certification.md).

Os problemas de provisionamento podem incluir os seguintes cenários de falha:

|Cenário|Erro|Motivo|Solução|
|---|---|---|---|
|1|VHD (disco rígido virtual) inválido|Se o valor do cookie especificado no rodapé do VHD estiver incorreto, o VHD será considerado inválido.|Recrie a imagem e envie a solicitação.|
|2|Tipo de blob inválido|Falha no provisionamento da VM porque o bloco usado é um tipo de BLOB em vez de um tipo de página.|Recrie a imagem e envie a solicitação.|
|3|Tempo limite de provisionamento ou não generalizado corretamente|Há um problema com a generalização da VM.|Recrie a imagem com generalização e envie a solicitação.|

> [!NOTE]
> Para obter mais informações sobre a generalização da VM, consulte:
> - [Documentação do Linux](create-azure-vm-technical-asset.md#generalize-the-image)
> - [Documentação do Windows](../../virtual-machines/windows/capture-image-resource.md#generalize-the-windows-vm-using-sysprep)

## <a name="software-compliance-for-windows"></a>Conformidade de software para Windows

Se a solicitação de imagem do Windows for rejeitada devido a um problema de conformidade de software, você poderá ter criado uma imagem do Windows com a instância do SQL Server instalada em vez de usar a imagem de base da versão do SQL relevante do Azure Marketplace.

Não crie sua própria imagem do Windows com o SQL Server instalado. Em vez disso, use as imagens base SQL aprovadas (Enterprise/Standard/Web) do Azure Marketplace.

Se você estiver tentando instalar o Visual Studio ou qualquer produto licenciado pelo Office, entre em contato com a equipe de suporte para aprovação prévia.

Para obter mais informações sobre como selecionar uma base aprovada, consulte [criar seus ativos técnicos de máquina virtual do Azure](create-azure-vm-technical-asset.md#create-a-vm-image-using-an-approved-base).

## <a name="tool-kit-test-case-execution-failed"></a>Falha na execução do caso de teste do kit de ferramentas

O Microsoft Certification Toolkit pode ajudá-lo a executar casos de teste e verificar se o VHD ou a imagem é compatível com o ambiente do Azure.

Baixe o [Microsoft certificar Toolkit](azure-vm-image-certification.md).

## <a name="linux-test-cases"></a>Casos de teste do Linux

A tabela a seguir lista os casos de teste do Linux que o kit de ferramentas executará. A validação de teste é indicada na descrição.

|Cenário|Caso de teste|Descrição|
|---|---|---|
|1|Histórico de bash|Os arquivos de histórico do bash devem ser limpos antes de criar a imagem da VM.|
|2|Versão do agente do Linux|O agente Linux do Azure 2.2.41 ou posterior deve estar instalado.|
|3|Parâmetros de kernel necessários|Verifica se os seguintes parâmetros de kernel estão definidos: <br>console = ttyS0<br>earlyprintk = ttyS0<br>atrasoraiz = 300|
|4|Trocar partição no disco do sistema operacional|Verifica se as partições de permuta não foram criadas no disco do sistema operacional.|
|5|Partição raiz no disco do sistema operacional|Crie uma única partição raiz para o disco do sistema operacional.|
|6|Versão do OpenSSL|A versão do OpenSSL deve ser v 0.9.8 ou posterior.|
|7|Versão do Python|O Python versão 2,6 ou posterior é altamente recomendável.|
|8|Intervalo de ativo do cliente|Defina ClientAliveInterval como 180. Na necessidade do aplicativo, ele pode ser definido de 30 a 235. Se você estiver habilitando o SSH para seus usuários finais, esse valor deverá ser definido como explicado.|
|9|Arquitetura do SO|Há suporte somente para sistemas operacionais de 64 bits.|
|10|Atualização automática|Identifica se a atualização automática do agente do Linux está habilitada.|

### <a name="common-errors-found-while-executing-previous-test-cases"></a>Erros comuns encontrados durante a execução de casos de teste anteriores

A tabela a seguir lista os erros comuns encontrados durante a execução dos casos de teste anteriores:
 
|Cenário|Caso de teste|Erro|Solução|
|---|---|---|---|
|1|Caso de teste de versão do agente Linux|A versão mínima do agente do Linux é 2,241 ou posterior. Esse requisito foi obrigatório desde 1º de maio de 2020.|A imagem deve ser atualizada com a versão necessária para [enviar a solicitação](https://support.microsoft.com/help/4049215/extensions-and-virtual-machine-agent-minimum-version-support).|
|2|Caso de teste do histórico de bash|Você verá um erro se o tamanho do histórico de bash em sua imagem enviada tiver mais de 1 kilobyte (KB). O tamanho é restrito a 1 KB para garantir que qualquer informação potencialmente confidencial não seja capturada em seu arquivo de histórico bash.|Para resolver esse problema, monte o VHD em qualquer outra VM de trabalho e faça as alterações desejadas (por exemplo, exclua os arquivos de histórico *. bash* ) para reduzir o tamanho para menor ou igual a 1 KB.|
|3|Caso de teste do parâmetro do kernel necessário|Você receberá esse erro quando o valor do **console** não estiver definido como **ttyS0**. Verifique executando o seguinte comando:<br>`cat /proc/cmdline`|Defina o valor do **console** para **ttyS0**e envie a solicitação novamente.|
|4|Caso de teste de intervalo ClientAlive|Se o resultado do kit de ferramentas fornecer um resultado com falha para esse caso de teste, haverá um valor inadequado para **ClientAliveInterval**.|Defina o valor de **ClientAliveInterval** como menor ou igual a 235 e envie a solicitação novamente.|

### <a name="windows-test-cases"></a>Casos de teste do Windows

A tabela a seguir lista os casos de teste do Windows que o kit de ferramentas executará, juntamente com uma descrição da validação do teste:

|Cenário |Casos de teste|Descrição|
|---|---|---|---|
|1|Arquitetura do SO|O Azure dá suporte apenas a sistemas operacionais de 64 bits.|
|2|Dependência da conta de usuário|A execução do aplicativo não deve ser dependente da conta de administrador.|
|3|Cluster de failover|Ainda não há suporte para o recurso Windows Server failover clustering. O aplicativo não deve depender desse recurso.|
|4|IPV6|O IPv6 ainda não tem suporte no ambiente do Azure. O aplicativo não deve depender desse recurso.|
|5|DHCP|A função de servidor do protocolo de configuração de host dinâmico ainda não tem suporte. O aplicativo não deve depender desse recurso.|
|6|Hyper-V|A função de servidor Hyper-V ainda não tem suporte. O aplicativo não deve depender desse recurso.|
|7|Acesso remoto|A função de servidor acesso remoto (acesso direto) ainda não tem suporte. O aplicativo não deve depender desse recurso.|
|8|Rights Management Services|Serviços Rights Managements. A função de servidor ainda não tem suporte. O aplicativo não deve depender desse recurso.|
|9|Windows Deployment Services|Serviços de implantação do Windows. A função de servidor ainda não tem suporte. O aplicativo não deve depender desse recurso.|
|10|Criptografia de Unidade de Disco BitLocker|Não há suporte para Criptografia de Unidade de Disco BitLocker no disco rígido do sistema operacional, mas ele pode ser usado em discos de dados.|
|11|Internet Storage Name Server|O recurso de servidor de nome de armazenamento da Internet ainda não tem suporte. O aplicativo não deve depender desse recurso.|
|12|Multipath I/O|Multipath I/O. Ainda não há suporte para esse recurso de servidor. O aplicativo não deve depender desse recurso.|
|13|Network Load Balancing|Balanceamento de carga de rede. Ainda não há suporte para esse recurso de servidor. O aplicativo não deve depender desse recurso.|
|14|Protocolo PNRP|Protocolo de resolução de nome de par. Ainda não há suporte para esse recurso de servidor. O aplicativo não deve depender desse recurso.|
|15|Serviços SNMP|O recurso de serviços SNMP (Simple Network Management Protocol) ainda não tem suporte. O aplicativo não deve depender desse recurso.|
|16|Serviço de Cadastramento na Internet do Windows|Serviço de cadastramento na Internet do Windows. Ainda não há suporte para esse recurso de servidor. O aplicativo não deve depender desse recurso.|
|17|Serviço de LAN sem fio|Serviço de LAN sem fio. Ainda não há suporte para esse recurso de servidor. O aplicativo não deve depender desse recurso.|

Se você entrar em qualquer falha com os casos de teste anteriores, consulte a coluna **Descrição** na tabela da solução. Se você precisar de mais informações, entre em contato com a equipe de suporte. 

## <a name="data-disk-size-verification"></a>Verificação do tamanho do disco de dados

Se o tamanho de qualquer solicitação enviada com o disco de dados for maior que 1023 gigabytes (GB), a solicitação não será aprovada. Essa regra se aplica ao Linux e ao Windows.

Envie novamente a solicitação com um tamanho menor ou igual a 1023 GB.

## <a name="os-disk-size-validation"></a>Validação do tamanho do disco do so

Consulte as regras a seguir para obter limitações no tamanho do disco do sistema operacional. Ao enviar qualquer solicitação, verifique se o tamanho do disco do sistema operacional está dentro da limitação do Linux ou do Windows.

|Sistema operacional|Tamanho de VHD recomendado|
|---|---|
|Linux|30 GB a 1023 GB|
|Windows|30 GB a 250 GB|

Como as VMs permitem o acesso ao sistema operacional subjacente, verifique se o tamanho do VHD é suficientemente grande para o VHD. Como os discos não são expansíveis sem tempo de inatividade, use um tamanho de disco de 30 GB a 50 GB.

|Tamanho do VHD|Tamanho real de ocupado|Solução|
|---|---|---|
|>500 tebibytes (TiB)|N/D|Contate a equipe de suporte para obter uma aprovação de exceção.|
|250-500 TiB|Diferença de >200 Gibibytes (GiB) do tamanho do blob|Contate a equipe de suporte para obter uma aprovação de exceção.|

> [!NOTE]
> Tamanhos de disco maiores incorrem em custos maiores e incorrerão em um atraso durante o processo de instalação e replicação. Devido a esse atraso e custo, a equipe de suporte pode buscar justificativa para a aprovação da exceção.

## <a name="wannacry-patch-verification-test-for-windows"></a>Teste de verificação de patch do WannaCry para Windows

Para evitar um ataque potencial relacionado ao vírus WannaCry, verifique se todas as solicitações de imagem do Windows foram atualizadas com o patch mais recente.

Para verificar a versão com patch do Windows Server para os detalhes do sistema operacional e a versão mínima que dará suporte, consulte a tabela a seguir: 

A versão do arquivo de imagem pode ser verificada de `C:\windows\system32\drivers\srv.sys` ou `srv2.sys` .

> [!NOTE]
> O Windows Server 2019 não tem nenhum requisito de versão obrigatório.

|Sistema operacional|Versão|
|---|---|
|Windows servindo 2008 R2|6.1.7601.23689|
|Windows Server 2012|6.2.9200.22099|
|Windows Server 2012 R2|6.3.9600.18604|
|Windows Server 2016|10.0.14393.953|
|Windows Server 2019|NA|

## <a name="sack-vulnerability-patch-verification"></a>Verificação de patch de vulnerabilidade de SACK

Quando você envia uma imagem do Linux, sua solicitação pode ser rejeitada devido a problemas de versão do kernel.

Atualize o kernel com uma versão aprovada e envie a solicitação novamente. Você pode encontrar a versão de kernel aprovada na tabela a seguir. O número de versão deve ser igual ou maior que o número listado aqui.

Se a imagem não estiver instalada com uma das seguintes versões de kernel, atualize-a com os patches corretos. Solicite a aprovação necessária da equipe de suporte depois que a imagem for atualizada com estes patches necessários:

- CVE-2019-11477 
- CVE-2019-11478 
- CVE-2019-11479

|Família de sistema operacional|Versão|Kernel|
|---|---|---|
|Ubuntu|14.04 LTS|4.4.0-151| 
||14.04 LTS|4.15.0-1049-*-Azure|
||16.04 LTS|4.15.0-1049|
||18.04 LTS|4.18.0-1023|
||18.04 LTS|5.0.0-1025|
||18,10|4.18.0-1023|
||19, 4|5.0.0-1010|
||19, 4|5.3.0-1004|
|Sistema operacional RHEL e Cent|6.10|2.6.32-754.15.3|
||7.2|3.10.0-327.79.2|
||7.3|3.10.0-514.66.2|
||7.4|3.10.0-693.50.3|
||7.5|3.10.0-862.34.2|
||7.6|3.10.0-957.21.3|
||7.7|3.10.0-1062.1.1|
||8.0|4.18.0-80.4.2|
||8.1|4.18.0 – 147|
||"7-RAW" (7,6)||
||"7-LVM" (7,6)|3.10.0-957.21.3|
||RHEL-SAP 7,4|TBD|
||RHEL-SAP 7,5|TBD|
|SLES|SLES11SP4 (incluindo SAP)|3.0.101-108.95.2|
||SLES12SP1 para SAP|3.12.74-60.64.115.1|
||SLES12SP2 para SAP|4.4.121-92.114.1|
||SLES12SP3|4.4180-4.31.1 (kernel – Azure)|
||SLES12SP3 para SAP|4.4.180-94.97.1|
||SLES12SP4|4.12.14-6.15.2 (kernel – Azure)|
||SLES12SP4 para SAP|4.12.14-95.19.1|
||SLES15|4.12.14-5.30.1 (kernel – Azure)|
||SLES15 para SAP|4.12.14-5.30.1 (kernel – Azure)|
||SLES15SP1|4.12.14-5.30.1 (kernel – Azure)|
|Oracle|6.10|UEK2 2.6.39-400.312.2<br>UEK3 3.8.13-118.35.2<br>RHCK 2.6.32-754.15.3 
||7.0-7.5|UEK3 3.8.13-118.35.2<br>UEK4 4.1.12-124.28.3<br>RHCK segue RHEL acima|
||7.6|RHCK 3.10.0-957.21.3<br>UEK5 4.14.35-1902.2.0|
|CoreOS 2079.6.0 estável|4.19.43*|
||2135.3.1 beta|4.19.50*|
||2163.2.1 Alfa|4.19.50*|
|Debian|Jessie (segurança)|3.16.68-2|
||Jessie backports|4.9.168-1 + deb9u3|
||Stretch (segurança)|4.9.168-1 + deb9u3|
||Debian GNU/Linux 10 (Buster)|Debian 6.3.0-18 + deb9u1|
||Buster, Sid (alongar portas de ampliação)|4.19.37-5|

## <a name="image-size-should-be-in-multiples-of-megabytes"></a>O tamanho da imagem deve estar em múltiplos de megabytes

Todos os VHDs no Azure devem ter um tamanho virtual alinhado a múltiplos de 1 megabyte (MB). Se o VHD não aderir ao tamanho virtual recomendado, sua solicitação poderá ser rejeitada.

Siga as diretrizes quando estiver convertendo de um disco bruto para VHD e certifique-se de que o tamanho do disco bruto seja um múltiplo de 1 MB. Para obter mais informações, consulte [informações para distribuições não endossadas](../../virtual-machines/linux/create-upload-generic.md).

## <a name="vm-access-denied"></a>Acesso à VM negado

Se você entrar nos problemas de acesso negado enquanto estiver executando os casos de teste na VM, isso pode ser devido a privilégios insuficientes para executar os casos de teste.

Verifique se o acesso adequado está habilitado para a conta na qual os casos de teste automático estão em execução. Se o acesso não estiver habilitado, habilite-o para executar os casos de teste. Se não quiser habilitar o acesso, você poderá compartilhar os resultados do caso de teste automático com a equipe de suporte.

## <a name="download-failure"></a>Falha no download
    
Consulte a tabela a seguir para obter os problemas que surgirem quando você baixar a imagem da VM usando uma URL de assinatura de acesso compartilhado (SAS).

|Cenário|Erro|Motivo|Solução|
|---|---|---|---|
|1|Blob não encontrado|O VHD pode ser excluído ou movido do local especificado.|| 
|2|Blob em uso|O VHD é usado por outro processo interno.|O VHD deve estar em um estado usado quando você o baixa usando uma URL SAS.|
|3|URL SAS inválida|A URL SAS associada para o VHD está incorreta.|Obtenha a URL SAS correta.|
|4|Assinatura inválida|A URL SAS associada para o VHD está incorreta.|Obtenha a URL SAS correta.|
|6|Cabeçalho condicional HTTP|A URL SAS é inválida.|Obtenha a URL SAS correta.|
|7|Nome de VHD inválido|Verifique se há algum caractere especial, como um sinal de porcentagem (%) ou aspas ("), existem no nome do VHD.|Renomeie o arquivo VHD removendo os caracteres especiais.|

## <a name="first-1-mb-partition"></a>Primeira partição de 1 MB

Ao enviar o VHD, verifique se a primeira partição de 1 MB do VHD está vazia. Caso contrário, a solicitação falhará.

## <a name="default-credentials"></a>Credenciais padrão

Sempre certifique-se de que as credenciais padrão não sejam enviadas com o VHD enviado. Adicionar credenciais padrão torna o VHD mais vulnerável a ameaças de segurança. Em vez disso, crie suas próprias credenciais ao enviar o VHD.
  
## <a name="datadisk-mapped-incorrectly"></a>Datadisk mapeado incorretamente

Quando uma solicitação é enviada com vários discos de dados, mas sua ordem não está em sequência, isso é considerado um problema de mapeamento. Por exemplo, se houver três discos de dados, a ordem de numeração deverá ser *0, 1, 2*. Qualquer outra ordem é tratada como um problema de mapeamento.

Envie novamente a solicitação com o sequenciamento adequado de discos de dados.

## <a name="incorrect-os-mapping"></a>Mapeamento de so incorreto

Quando uma imagem é criada, ela pode ser mapeada ou atribuída ao rótulo de so errado. Por exemplo, quando você seleciona **Windows** como parte do nome do sistema operacional enquanto estiver criando a imagem, o disco do sistema operacional deve ser instalado somente com o Windows. O mesmo requisito se aplica ao Linux.

## <a name="vm-not-generalized"></a>VM não generalizada

Se todas as imagens obtidas do Azure Marketplace forem reutilizadas, o VHD do sistema operacional deverá ser generalizado.

* Para o **Linux**, o processo a seguir GENERALIZA uma VM Linux e a implanta novamente como uma VM separada.

  Na janela SSH, digite o seguinte comando: `sudo waagent -deprovision+user`

* Para o **Windows**, você generaliza as imagens do Windows usando o `sysreptool` .

Para obter mais informações sobre essa ferramenta, consulte [visão geral do Sysprep (preparação do sistema)]( https://docs.microsoft.com/windows-hardware/manufacture/desktop/sysprep--system-preparation--overview).

## <a name="datadisk-errors"></a>Erros de disco

Para obter soluções para erros relacionados ao disco de dados, use a seguinte tabela:

|Erro|Motivo|Solução|
|---|---|---|
|`DataDisk- InvalidUrl:`|Esse erro pode ocorrer devido a um número inválido especificado para o LUN (número de unidade lógica) quando a oferta é enviada.|Verifique se a sequência de números de LUN para o disco de dados está no Partner Center.|
|`DataDisk- NotFound:`|Esse erro pode ocorrer devido a um disco de dados não estar localizado em uma URL SAS especificada.|Verifique se o disco de dados está localizado na URL SAS especificada na solicitação.|

## <a name="remote-access-issue"></a>Problema de acesso remoto

Se a opção protocolo RDP (RDP) não estiver habilitada para a imagem do Windows, você receberá esse erro. 

Habilite o acesso RDP para imagens do Windows antes de enviá-las.

## <a name="bash-history-failed"></a>Falha no histórico de bash

Você verá esse erro se o tamanho do histórico de bash em sua imagem enviada tiver mais de 1 kilobyte (KB). O tamanho é restrito a 1 KB para garantir que qualquer informação potencialmente confidencial não seja capturada em seu arquivo de histórico bash.

Abaixo estão as etapas para excluir o "histórico do bash".

Etapa 1. Implante a VM e clique na opção "executar comando" em portal do Azure.
![Executar comando em portal do Azure](./media/vm-certification-issues-solutions-3.png)

Etapa 2. Selecione a primeira opção "RunShellScript" e execute o comando abaixo.

Comando: "Cat/dev/null > ~/. bash_history && History-c" ![ bash história Command on portal do Azure](./media/vm-certification-issues-solutions-4.png)

Etapa 3. Após executar o comando com êxito, reinicie a VM.

Etapa 4. Generalizar a VM, pegar o VHD da imagem e parar a VM.

Etapa 5.     Envie novamente a imagem generalizada.

## <a name="requesting-exceptions-custom-templates-on-vm-images-for-selective-tests"></a>Solicitando exceções (modelos personalizados) em imagens de VM para testes seletivos

Os editores podem entrar em contato para solicitar exceções para alguns testes executados durante a certificação da VM. As exceções são fornecidas em casos extremamente raros quando o Publisher fornece evidências para dar suporte à solicitação.
A equipe de certificação reserva o direito de negar ou aprovar exceções em qualquer momento.

Nas seções a seguir, falaremos sobre os principais cenários em que as exceções são solicitadas e como solicitar uma exceção.

Cenários para exceção

Há três cenários/casos em que os editores geralmente solicitam essas exceções. 

* **Exceção para um ou mais casos de teste:** Os editores podem acessar as exceções de solicitação de [suporte do Publicador do Marketplace](https://aka.ms/marketplacepublishersupport) para casos de teste. 

* **VMs bloqueadas/sem acesso à raiz:** Alguns Publicadores têm cenários em que as VMs precisam ser bloqueadas, pois têm software como firewalls instalados na VM. 
       Nesse caso, os editores podem baixar a [ferramenta de teste certificada](https://aka.ms/AzureCertificationTestTool) aqui e fornecer o relatório no [suporte ao editor do Marketplace](https://aka.ms/marketplacepublishersupport)


* **Modelos personalizados:** Alguns Publicadores publicam imagens de VM que exigem um modelo de ARM personalizado para implantar as VMs. Nesse caso, os editores são solicitados a fornecer os modelos personalizados no [suporte de Publicador do Marketplace](https://aka.ms/marketplacepublishersupport) para que o mesmo possa ser usado pela equipe de certificação para validação. 

### <a name="information-to-provide-for-exception-scenarios"></a>Informações a serem fornecidas para cenários de exceção

Os editores devem entrar em contato com o suporte ao [Editor do Marketplace](https://aka.ms/marketplacepublishersupport) para solicitar exceções para o cenário acima com as informações adicionais a seguir:

   1.   ID do editor – a ID do editor no portal do Partner Center
   2.   ID da oferta/nome – a ID da oferta/nome para o qual a exceção é solicitada 
   3.   ID do plano/SKU – a ID do plano/SKU da oferta da VM para a qual a exceção é solicitada
   4.    Versão – a versão da oferta de VM para a qual a exceção é solicitada
   5.   Tipo de exceção – testes, VM bloqueada, modelos personalizados
   6.   Motivo da solicitação – motivo para essa exceção e informações sobre os testes a serem isentos 
   7.   Anexo-anexe qualquer documento de evidência de importância. Para VMs bloqueadas, anexe o relatório de teste e os modelos personalizados, forneça o modelo ARM personalizado como anexo. Falha ao anexar o relatório para VMs bloqueadas e o modelo ARM personalizado para modelos personalizados resultará em negação de solicitação


## <a name="next-steps"></a>Próximas etapas

Se você tiver dúvidas ou comentários para aprimoramento, entre em contato com o [suporte do Partner Center](https://partner.microsoft.com/support/v2/?stage=1).
