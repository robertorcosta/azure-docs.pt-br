---
title: Certificação de máquina virtual – problemas e soluções
description: Este artigo explica mensagens de erro comuns para imagens de VM. Ele também aborda soluções relacionadas
author: v-miegge
ms.author: v-krmall
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: troubleshooting
ms.date: 06/16/2020
ms.openlocfilehash: 7bd3f1a5b242ee5196e92456cb3fc8c97f8f5b27
ms.sourcegitcommit: 845a55e6c391c79d2c1585ac1625ea7dc953ea89
ms.contentlocale: pt-BR
ms.lasthandoff: 07/05/2020
ms.locfileid: "85958524"
---
# <a name="virtual-machine-certification---issues-and-solutions"></a>Certificação de máquina virtual – problemas e soluções

Ao publicar suas imagens de máquina virtual (VM) no Azure Marketplace, a equipe do Azure valida a imagem da VM para garantir a inicialização, a segurança e a compatibilidade do Azure. Se qualquer um dos testes de alta qualidade falhar, a publicação falhará com uma mensagem que contém o erro.

Este artigo explica mensagens de erro comuns para imagens de VM. Ele também aborda soluções relacionadas:

> [!NOTE]
> Se você tiver dúvidas ou comentários para aprimoramento, entre em contato com o [suporte do Partner Center](https://partner.microsoft.com/support/v2/?stage=1).

## <a name="approved-base-image"></a>Imagem de base aprovada

Quando você envia uma solicitação para republicar a imagem com atualizações, o caso de teste de verificação de número de peça pode falhar. Nessa instância, a imagem não será aprovada.

Essa falha ocorrerá quando você usou uma imagem base que pertence a outro Publicador e atualizou a imagem. Nessa situação, você não terá permissão para publicar sua imagem.

Para corrigir esse problema, recupere a imagem mais recente do Azure Marketplace e faça alterações nessa imagem. Consulte o seguinte para exibir as imagens base aprovadas nas quais você pode pesquisar sua imagem:

- [Linux-imagens](../../virtual-machines/linux/endorsed-distros.md?toc=/azure/virtual-machines/linux/toc.json)
- [Windows-imagens](create-azure-vm-technical-asset.md#create-a-vm-image-using-an-approved-base))

## <a name="vm-extension-failure"></a>Falha na extensão da VM

Para verificar se a imagem dá suporte à extensão de VM ou não, siga estas etapas:

Habilitar extensões de VM:

1. Selecione a VM Linux.
2. Vá para **configurações de diagnóstico**.
3. Habilite matrizes base atualizando a **conta de armazenamento**.
4. Selecione **Salvar**.

   ![Habilitar o monitoramento no nível do convidado](./media/vm-certification-issues-solutions-1.png)

Verifique se as extensões de VM estão ativadas corretamente:

5. Vá para a guia **extensões de VM** da VM e verifique a **extensão de diagnóstico do Linux**.
6. Se o status for **Provisionando com êxito** , o caso de teste de extensões foi aprovado.
7. Se o status for **Provisionando falha** , o caso de teste de extensões falhou e você precisa definir o sinalizador de proteção.

   ![Provisionamento bem-sucedido](./media/vm-certification-issues-solutions-2.png)

   Se a extensão da VM falhar, vá para [usar a extensão de diagnóstico do Linux para monitorar as métricas e os logs](../../virtual-machines/extensions/diagnostics-linux.md) para habilitá-lo. Se você não quiser que a extensão de VM seja habilitada, entre em contato com a equipe de suporte e peça para desabilitar a extensão.

## <a name="virtual-machine-provisioning-issue"></a>Problema de provisionamento de máquina virtual

Verifique se o processo de provisionamento é rigorosamente seguido para a VM antes de enviar sua oferta. Para exibir o formato JSON para provisionar a VM, vá para [certificação de imagem de VM (máquina virtual) do Azure](azure-vm-image-certification.md).

Os problemas de provisionamento podem incluir os seguintes cenários de falha:

|S.NO|erro|reason|solução|
|---|---|---|---|
|1|VHD (disco rígido virtual) inválido|Se o valor do cookie especificado no rodapé do VHD não estiver correto, o VHD será considerado inválido.|Recrie a imagem e envie a solicitação.|
|2|Tipo de blob inválido|Falha no provisionamento da VM, pois o bloco usado é um tipo de BLOB em vez de um tipo de página.|Recrie a imagem e envie a solicitação.|
|3|Tempo limite de provisionamento ou não generalizado corretamente|Há um problema com a generalização da VM.|Recrie a imagem com generalização e envie a solicitação.|

> [!NOTE]
> Siga estes links para obter a documentação relacionada à generalização da VM:
> - [Linux](create-azure-vm-technical-asset.md#generalize-the-image))
> - [Windows](../../virtual-machines/windows/capture-image-resource.md#generalize-the-windows-vm-using-sysprep))

## <a name="software-compliance-for-windows"></a>Conformidade de software para Windows

Se sua solicitação de imagem do Windows for rejeitada devido à conformidade do software, você poderá ter criado uma imagem do Windows com o SQL Server instalado, em vez de usar a imagem de base da versão do SQL relevante do Azure Marketplace.

Não crie sua própria imagem do Windows com o SQL Server instalado. Em vez disso, use as imagens base SQL aprovadas (Enterprise/Standard/Web) do Azure Marketplace.

Se você estiver tentando instalar o Visual Studio ou qualquer produto licenciado do Office, entre em contato com a equipe de suporte para aprovação prévia.

Para obter mais informações, visite [criar seus ativos técnicos da máquina virtual do Azure](create-azure-vm-technical-asset.md#create-a-vm-image-using-an-approved-base)) para selecionar uma base aprovada.

## <a name="tool-kit-test-case-execution-failed"></a>Falha na execução do caso de teste do kit de ferramentas

O Microsoft Certification Toolkit o ajudará a executar casos de teste, verifique se o VHD/imagem é compatível com o ambiente do Azure.

Baixe o [Microsoft certificar Toolkit](azure-vm-image-certification.md).

## <a name="linux-test-cases"></a>Casos de teste do Linux

A seguir, os casos de teste do Linux que o kit de ferramentas executará. A validação de teste é indicada na descrição.

|S.Não|casos de teste|descrição|
|---|---|---|
|1|Histórico de bash|Os arquivos de histórico do bash devem ser limpos antes da criação da imagem da VM.|
|2|Versão do agente do Linux|O agente Linux do Azure 2.2.41 e posterior deve ser instalado.|
|3|Parâmetros de kernel necessários|Verifica se os seguintes parâmetros de kernel estão definidos: <br>console = ttyS0<br>earlyprintk = ttyS0<br>atrasoraiz = 300|
|4|Trocar partição no disco do sistema operacional|Verifica se as partições de permuta não foram criadas no disco do sistema operacional.|
|5|Partição raiz no disco do sistema operacional|Crie uma única partição raiz para o disco do sistema operacional.|
|6|Versão do OpenSSL|A versão do OpenSSL deve ser maior ou igual a v 0.9.8.|
|7|Versão do Python|O Python versão 2.6 + é altamente recomendável.|
|8|Intervalo de ativo do cliente|Defina ClientAliveInterval como 180. Na necessidade do aplicativo, ele pode ser definido entre 30 e 235. Se você estiver habilitando o SSH para seus usuários finais, esse valor deverá ser definido como explicado.|
|9|Arquitetura do SO|Há suporte somente para sistemas operacionais de 64 bits.|
|10|Atualização automática|Identifica se a atualização automática do agente do Linux está habilitada.|

### <a name="common-errors-found-while-executing-the-previous-test-cases"></a>Erros comuns encontrados ao executar os casos de teste anteriores

Erros comuns encontrados ao executar os casos de teste anteriores.
 
|S.NO|caso de teste|erro|solução|
|---|---|---|---|
|1|Caso de teste de versão do agente Linux|A versão mínima do agente do Linux é 2,241 ou superior. Esse requisito foi obrigatório desde 1º de maio de 2020|A imagem deve ser atualizada com a versão necessária para [enviar a solicitação](https://support.microsoft.com/help/4049215/extensions-and-virtual-machine-agent-minimum-version-support).|
|2|Caso de teste do histórico de bash|Você verá um erro se o tamanho do histórico de bash em sua imagem enviada for maior que 1 KB. O tamanho é restrito a 1 KB para garantir que qualquer informação potencialmente confidencial não seja capturada em seu arquivo de histórico bash.|Para resolver esse problema, monte o VHD em qualquer outra VM de trabalho e faça as alterações (por exemplo, exclua os `.bash` arquivos de histórico) que você deseja reduzir o tamanho para menor ou igual a 1 KB.|
|3|Caso de teste do parâmetro do kernel necessário|Você receberá esse erro quando o valor do **console** não estiver definido como **ttyS0**. Verifique executando o comando:<br>`cat /proc/cmdline`|Defina o valor do **console** como **ttyS0** e envie a solicitação novamente.|
|4|Caso de teste de intervalo ClientAlive|Se o resultado do kit de ferramentas fornecer um resultado com falha para esse caso de teste, haverá um valor inadequado para **ClientAliveInterval**.|Defina o valor de **ClientAliveInterval** como menor ou igual a 235 e envie a solicitação novamente.|

### <a name="windows-test-cases"></a>Casos de teste do Windows

A seguir, os casos de teste do Windows que o kit de ferramentas executará. A validação de teste é indicada na descrição.

|S.Não|casos de teste|descrição|
|---|---|---|---|
|1|Arquitetura do SO|O Azure só dá suporte a sistemas operacionais de 64 bits.|
|2|Dependência da conta de usuário|A execução do aplicativo não deve depender da conta de administrador.|
|3|Cluster de failover|O recurso Windows Server failover clustering ainda não tem suporte. o aplicativo não deve depender desse recurso.|
|4|IPV6|O IPv6 ainda não tem suporte no ambiente do Azure. O aplicativo não deve depender desse recurso.|
|5|DHCP|A função de servidor do protocolo de configuração de host dinâmico ainda não tem suporte. O aplicativo não deve depender desse recurso.|
|6|Hyper-V|A função de servidor Hyper-V ainda não tem suporte. O aplicativo não deve depender desse recurso.|
|7|Acesso remoto|A função de servidor acesso remoto (acesso direto) ainda não tem suporte. O aplicativo não deve depender desse recurso.|
|8|Rights Management Services|Serviços Rights Managements. A função de servidor ainda não tem suporte. O aplicativo não deve depender desse recurso.|
|9|Windows Deployment Services|Serviços de implantação do Windows. A função de servidor ainda não tem suporte. O aplicativo não deve depender desse recurso.|
|10|Criptografia de Unidade de Disco BitLocker|Não há suporte para Criptografia de Unidade de Disco BitLocker no disco rígido do sistema operacional, mas pode ser usado em discos de dados.|
|11|Internet Storage Name Server|O recurso de servidor de nome de armazenamento da Internet ainda não tem suporte. O aplicativo não deve depender desse recurso.|
|12|Multipath I/O|Multipath I/O. Ainda não há suporte para esse recurso de servidor. O aplicativo não deve depender desse recurso.|
|13|Network Load Balancing|Balanceamento de carga de rede. Ainda não há suporte para esse recurso de servidor. O aplicativo não deve depender desse recurso.|
|14|Protocolo PNRP|Protocolo de resolução de nome de par. Ainda não há suporte para esse recurso de servidor. O aplicativo não deve depender desse recurso.|
|15|Serviços SNMP|O recurso de serviços SNMP ainda não tem suporte. O aplicativo não deve depender desse recurso.|
|16|Serviço de Cadastramento na Internet do Windows|Serviço de cadastramento na Internet do Windows. Ainda não há suporte para esse recurso de servidor. O aplicativo não deve depender desse recurso.|
|17|Serviço de LAN sem fio|Serviço de LAN sem fio. Ainda não há suporte para esse recurso de servidor. O aplicativo não deve depender desse recurso.|

Se você entrar em qualquer falha com os casos de teste acima, consulte a coluna **Descrição** na tabela anterior para a solução. Se você precisar de mais informações, entre em contato com a equipe de suporte. 

## <a name="data-disk-size-verification"></a>Verificação do tamanho do disco de dados

Se o tamanho de qualquer solicitação enviada com o disco de dados for maior que 1023 GB, essa solicitação não será aprovada. Essa regra se aplica ao Linux & Windows.

Envie novamente a solicitação com um tamanho menor ou igual a 1023 GB.

## <a name="os-disk-size-validation"></a>Validação do tamanho do disco do so

Consulte as regras a seguir para obter limitações no tamanho do disco do sistema operacional. Ao enviar qualquer solicitação, verifique se o tamanho do disco do sistema operacional está dentro da limitação do Linux ou do Windows.

|Sistema operacional|tamanho de VHD recomendado|
|---|---|
|Linux|30 GB a 1023 GB|
|Windows|30 GB a 250 GB|

Como as VMs permitem o acesso ao sistema operacional subjacente, verifique se o tamanho do VHD é suficientemente grande para o VHD. Como os discos não são expansíveis sem tempo de inatividade, use um tamanho de disco entre 30 e 50 GB.

|Tamanho do VHD|tamanho real de ocupado|solução|
|---|---|---|
|>500 TiB|N/D|Contate a equipe de suporte para obter uma aprovação de exceção.|
|250-500 TiB|>200 GiB diferente do tamanho do blob|Contate a equipe de suporte para obter uma aprovação de exceção.|

> [!NOTE]
> Tamanhos de disco maiores incorrem em custos maiores e incorrerão em um atraso durante as etapas de provisionamento e de replicação. Devido a esse atraso e custo, a equipe de suporte pode buscar justificativa para a aprovação da exceção.

## <a name="wannacry-patch-verification-test-for-windows"></a>Teste de verificação de patch do WannaCry para Windows

Para evitar um ataque potencial relacionado ao vírus WannaCry, certifique-se de que todas as solicitações de imagem do Windows sejam atualizadas com o patch mais recente.

Para verificar a versão corrigida do Windows Server, consulte a tabela a seguir para obter os detalhes do sistema operacional e a versão mínima que dará suporte. 

A versão do arquivo de imagem pode ser verificada de `C:\windows\system32\drivers\srv.sys` ou `srv2.sys` .

> [!NOTE]
> WindowsServer2019 não tem nenhum requisito de versão obrigatório.

|Sistema operacional|version|
|---|---|
|WindowsServer2008R2|6.1.7601.23689|
|WindowsServer2012|6.2.9200.22099|
|WindowsServer2012R2|6.3.9600.18604|
|WindowsServer2016|10.0.14393.953|
|WindowsServer2019|NA|

## <a name="sack-vulnerability-patch-verification"></a>Verificação de patch de vulnerabilidade de SACK

Quando você envia uma imagem do Linux, sua solicitação pode ser rejeitada devido a problemas de versão do kernel.

Atualize o kernel com uma versão aprovada e reenvie a solicitação. Você pode encontrar a versão de kernel aprovada na tabela a seguir. O número de versão deve ser igual ou maior que aquele listado abaixo.

Se a imagem não estiver instalada com uma das versões de kernel a seguir, atualize sua imagem com os patches corretos. Você pode encontrar mais informações nos links a seguir. Solicite a aprovação necessária da equipe de suporte depois que a imagem for atualizada com estes patches necessários:

- CVE-2019-11477 
- CVE-2019-11478 
- CVE-2019-11479

|Família de sistema operacional|version|kernel|
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
||7,5|3.10.0-862.34.2|
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

Todos os VHDs no Azure devem ter um tamanho virtual alinhado a múltiplos de 1 MB. Se o VHD não aderir ao tamanho virtual recomendado, sua solicitação poderá ser rejeitada.

Siga as diretrizes quando estiver convertendo de um disco bruto para VHD e deve garantir que o tamanho do disco bruto seja um múltiplo de 1 MB. Para obter mais informações, consulte [informações para distribuições não endossadas](../../virtual-machines/linux/create-upload-generic.md)

## <a name="vm-access-denied"></a>Acesso à VM negado

Se você entrar nos problemas de acesso negado ao executar os casos de teste na VM, isso pode ser devido a privilégios insuficientes para executar os casos de teste.

Verifique se o acesso adequado está habilitado para a conta na qual os casos de teste automático estão em execução. Se não forem, habilite o acesso para executar os casos de teste. Se não quiser habilitar o acesso, você poderá compartilhar os resultados do caso de teste automático com a equipe de suporte.

## <a name="download-failure"></a>Falha no download
    
Consulte a tabela a seguir para quaisquer problemas ao baixar a imagem da VM usando a URL SAS.

|S.NO|erro|reason|solução|
|---|---|---|---|
|1|BLOB não encontrado|O VHD pode ser excluído ou movido do local especificado|| 
|2|Blob em uso|O VHD é usado por outro processo interno|O VHD deve estar em um estado usado ao baixar usando a URL SAS.|
|3|URL SAS inválida|A URL SAS associada para esse VHD está incorreta.|Obtenha a URL SAS correta.|
|4|Invalid Signature|A URL SAS associada para esse VHD está incorreta.|Obtenha a URL SAS correta.|
|6|Cabeçalho condicional HTTP|URL SAS inválida.|Obtenha a URL SAS correta.|
|7|Nome de VHD inválido|Verifique se algum caractere especial, como **%** ou **""** , existe no nome do VHD|Renomear o arquivo VHD removendo caracteres especiais|

## <a name="first-1-mb-partition"></a>Primeira partição de 1 MB

Ao enviar o VHD, verifique se a primeira partição de 1 MB do VHD está vazia. Caso contrário, a solicitação falhará.

## <a name="default-credentials"></a>Credenciais padrão

Sempre certifique-se de que as credenciais padrão não sejam enviadas com o VHD enviado. Adicionar credenciais padrão torna o VHD mais vulnerável a ameaças de segurança. Em vez disso, crie suas próprias credenciais ao enviar o VHD.
  
## <a name="datadisk-mapped-incorrectly"></a>Datadisk mapeado incorretamente

Quando uma solicitação é enviada com vários discos de dados, mas sua ordem não está em sequência, isso é considerado um problema de mapeamento. Por exemplo, se houver três discos de dados, a ordem de numeração deverá ser **0, 1, 2**. Qualquer outro pedido será tratado como um problema de mapeamento.

Envie novamente a solicitação com o sequenciamento adequado de discos de dados.

## <a name="incorrect-os-mapping"></a>Mapeamento de so incorreto

Quando uma imagem é criada, ela pode ser mapeada ou atribuída ao rótulo de so errado. Por exemplo, quando o **Windows** é selecionado como parte do nome do sistema operacional durante a criação da imagem, o disco do sistema operacional só deve ser instalado com o Windows. O mesmo se aplica ao Linux.

## <a name="vm-not-generalized"></a>VM não generalizada

Se todas as imagens extraídas do Azure Marketplace forem reutilizadas, o VHD do sistema operacional deverá ser generalizado.

Linux:

O processo a seguir generalizará uma VM do Linux e a reimplantará como uma VM separada.

Na janela SSH, digite o seguinte comando:`sudo waagent -deprovision+user`

Windows:

As imagens do Windows são generalizadas com o `sysreptool` .

Você pode encontrar mais informações sobre essa ferramenta na [visão geral do Sysprep (preparação do sistema)]( https://docs.microsoft.com/windows-hardware/manufacture/desktop/sysprep--system-preparation--overview)

## <a name="datadisk-error"></a>Erro de disco

Consulte a tabela a seguir para obter soluções para erros relacionados ao disco de dados.

|Erro|reason|solução|
|---|---|---|
|`DataDisk- InvalidUrl:`|Esse erro pode ser devido a um número inválido especificado para LUN ao enviar a oferta.|Verifique se a sequência de números de LUN para o disco de dados está no Partner Center.|
|`DataDisk- NotFound:`|Esse erro pode ser devido a um disco de dados não estar localizado em uma URL SAS especificada|Verifique se o disco de dados está localizado na URL SAS especificada na solicitação.|

## <a name="remote-access-issue"></a>Problema de acesso remoto

Se a opção RDP não estiver habilitada para a imagem do Windows, você receberá esse erro. 

Habilite o acesso RDP para imagens do Windows antes de enviar.

## <a name="next-steps"></a>Próximas etapas

Se você tiver dúvidas ou comentários para aprimoramento, entre em contato com o [suporte do Partner Center](https://partner.microsoft.com/support/v2/?stage=1).
