---
title: Solução de problemas do Azure Data Box para usar a interface REST| Microsoft Docs
description: Descreve como solucionar problemas vistos na Caixa de Dados do Azure quando a cópia de dados é através da interface REST.
services: databox
author: alkohli
ms.service: databox
ms.subservice: disk
ms.topic: article
ms.date: 04/19/2019
ms.author: alkohli
ms.openlocfilehash: 7fe5afbc4984c430cbf393e4e2b44122bdd43983
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80297136"
---
# <a name="troubleshoot-issues-related-to-azure-data-box-blob-storage"></a>Solucionando problemas relacionados ao armazenamento blob da caixa de dados do Azure

Este artigo detalha informações sobre como solucionar problemas que você pode ver ao usar o armazenamento Data Box Blob através da interface REST em sua Data Box para copiar dados. Esses problemas surgem quando você está usando o armazenamento Blob da Caixa de Dados com outros aplicativos ou bibliotecas de clientes, como o Azure Storage Explorer, o AzCopy ou a biblioteca Azure Storage para Python.

## <a name="errors-seen-in-azure-storage-explorer"></a>Erros vistos no Azure Storage Explorer

Esta seção detalha alguns dos problemas enfrentados ao usar o Azure Storage Explorer com o armazenamento Blob da Caixa de Dados.

|Mensagem de erro  |Ação recomendada |
|---------|---------|
|Incapaz de recuperar recursos de crianças. O valor de um dos cabeçalhos HTTP não está no formato correto.|No menu **Editar,** selecione **'Segment Azure Stack's APIs**. <br>Reiniciar o Azure Storage Explorer.|
|`getaddrinfo ENOTFOUND <accountname>.blob.<serialnumber>.microsoftdatabox.com` |Verifique se o `<accountname>.blob.<serialnumber>.microsoftdatabox.com` nome do ponto final é adicionado ao arquivo hosts neste caminho: <li>`C:\Windows\System32\drivers\etc\hosts`no Windows, ou </li><li> `/etc/hosts`no Linux.</li>|
|Incapaz de recuperar recursos de crianças. <br>Detalhes: certificado auto-assinado |Importe o certificado TLS/SSL para o seu dispositivo no Azure Storage Explorer: <li>Baixe o certificado no portal Azure. Para obter mais informações, acesse [Baixe o certificado](data-box-deploy-copy-data-via-rest.md#download-certificate).</li><li>No menu **Editar,** selecione **Certificados SSL** e selecione **Certificados de Importação**.</li>|

## <a name="errors-seen-in-azcopy-for-windows"></a>Erros vistos no AzCopy para Windows

Esta seção detalha alguns dos problemas enfrentados ao usar o AzCopy para Windows com o armazenamento Blob da Caixa de Dados.

|Mensagem de erro  |Ação recomendada |
|---------|---------|
|O comando AzCopy parece ser pendurado por um minuto antes de exibir este erro: <br>Falhou em enumerar o diretório https://... O nome remoto não pôde ser resolvido`<accountname>.blob.<serialnumber>.microsoftdatabox.com`|Verifique se o `<accountname>.blob.<serialnumber>.microsoftdatabox.com` nome do ponto final `C:\Windows\System32\drivers\etc\hosts`é adicionado ao arquivo hosts em: .|
|O comando AzCopy parece ser pendurado por um minuto antes de exibir este erro: <br>Erro de análise localização da fonte. A conexão subjacente foi fechada: Não foi possível estabelecer relação de confiança para o canal seguro SSL/TLS.|Importe o certificado TLS/SSL para o seu dispositivo no armazenamento de certificados do sistema. Para obter mais informações, acesse [Baixe o certificado](data-box-deploy-copy-data-via-rest.md#download-certificate).|


## <a name="errors-seen-in-azcopy-for-linux"></a>Erros vistos no AzCopy para Linux

Esta seção detalha alguns dos problemas enfrentados ao usar o AzCopy para Linux com o armazenamento Blob da Data Box.

|Mensagem de erro  |Ação recomendada |
|---------|---------|
|O comando AzCopy parece ser pendurado por 20 minutos antes de exibir este erro: <br>Erro de análise `https://<accountname>.blob.<serialnumber>.microsoftdatabox.com/<cntnr>`localização da fonte . Dispositivo ou endereço inexistente|Verifique se o `<accountname>.blob.<serialnumber>.microsoftdatabox.com` nome do ponto final `/etc/hosts`é adicionado ao arquivo hosts em: .|
|O comando AzCopy parece ser pendurado por 20 minutos antes de exibir este erro: <br>Erro de análise localização da fonte... A conexão SSL não pôde ser estabelecida.|Importe o certificado TLS/SSL para o seu dispositivo no armazenamento de certificados do sistema. Para obter mais informações, acesse [Baixe o certificado](data-box-deploy-copy-data-via-rest.md#download-certificate).|

## <a name="errors-seen-in-azure-storage-library-for-python"></a>Erros vistos na biblioteca de armazenamento do Azure para Python

Esta seção apresenta detalhes sobre alguns dos principais problemas enfrentados durante a implantação do Data Box Disk ao usar um cliente Linux para cópia de dados.

|Mensagem de erro  |Ação recomendada |
|---------|---------|
|O valor de um dos cabeçalhos HTTP não está no formato correto. |A versão instalada da Biblioteca de Armazenamento Microsoft Azure para Python não é suportada pela Data Box. Consulte os requisitos de armazenamento do Azure Data Box Blob para versões suportadas.|
|… [SSL: CERTIFICATE_VERIFY_FAILED] ...|Antes de executar o Python, defina a variável de ambiente REQUESTS_CA_BUNDLE para o caminho do arquivo de certificado TLS codificado pela Base64 (veja como [baixar o certificado).](data-box-deploy-copy-data-via-rest.md#download-certificate) <br>Por exemplo: <br>`export REQUESTS_CA_BUNDLE=/tmp/mycert.cer` <br>`python` <br>Alternativamente, adicione o certificado à loja de certificados do sistema e, em seguida, defina essa variável de ambiente para o caminho dessa loja. <br> Por exemplo, no Ubuntu:  <br>`export REQUESTS_CA_BUNDLE=/etc/ssl/certs/ca-certificates.crt` <br>`python`|


## <a name="common-errors"></a>Erros comuns

Esses erros não são específicos para qualquer aplicação.

|Mensagem de erro  |Ação recomendada |
|---------|---------|
|Os tempos de conexão se estoam. |Entre no dispositivo Data Box e verifique se ele está desbloqueado. Sempre que o dispositivo é reiniciado, ele fica trancado até que alguém entre.|

## <a name="next-steps"></a>Próximas etapas

- Conheça os requisitos do [sistema de armazenamento Data Box Blob](data-box-system-requirements-rest.md).
