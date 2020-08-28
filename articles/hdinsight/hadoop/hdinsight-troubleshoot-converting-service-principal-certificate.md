---
title: Convertendo o conteúdo do certificado em base-64-Azure HDInsight
description: Convertendo o conteúdo do certificado da entidade de serviço em formato de cadeia de caracteres codificada em base 64 no Azure HDInsight
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 07/31/2019
ms.custom: devx-track-csharp
ms.openlocfilehash: 7f4974d9e9a2ff3b63b36e45d406a016078bb290
ms.sourcegitcommit: 419cf179f9597936378ed5098ef77437dbf16295
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/27/2020
ms.locfileid: "89008158"
---
# <a name="converting-service-principal-certificate-contents-to-base-64-encoded-string-format-in-hdinsight"></a>Convertendo o conteúdo do certificado da entidade de serviço em formato de cadeia de caracteres codificada em base 64 no HDInsight

Este artigo descreve as etapas de solução de problemas e as possíveis resoluções para problemas ao interagir com clusters do Azure HDInsight.

## <a name="issue"></a>Problema

Você recebe uma mensagem de erro informando que a entrada não é uma cadeia de caracteres base 64 válida, pois contém um caractere sem base 64, mais de dois caracteres de preenchimento ou um caractere de espaço não-branco entre os caracteres de preenchimento.

## <a name="cause"></a>Causa

Ao usar o PowerShell ou a implantação de modelo do Azure para criar clusters com Data Lake como armazenamento primário ou adicional, o conteúdo do certificado de entidade de serviço fornecido para acessar a conta de armazenamento de Data Lake está no formato base-64. Conversão inadequada de conteúdo de certificado pfx para cadeia de caracteres codificada em base 64 pode levar a esse erro.

## <a name="resolution"></a>Resolução

Depois que você tiver o certificado da entidade de serviço no formato PFX (consulte [aqui](https://github.com/Azure/azure-quickstart-templates/tree/master/201-hdinsight-datalake-store-azure-storage) para obter as etapas de criação da entidade de serviço de exemplo), use o seguinte comando do PowerShell ou trecho de C# para converter o conteúdo do certificado para o formato base-64.

```powershell
$servicePrincipalCertificateBase64 = [System.Convert]::ToBase64String([System.IO.File]::ReadAllBytes(path-to-servicePrincipalCertificatePfxFile))
```

```csharp
using System;
using System.IO;

namespace ConsoleApplication
{
    class Program
    {
        static void Main(string[] args)
        {
            var certContents = File.ReadAllBytes(@"<path to pfx file>");
            string certificateData = Convert.ToBase64String(certContents);
            System.Diagnostics.Debug.WriteLine(certificateData);
        }
    }
}
```

## <a name="next-steps"></a>Próximas etapas

Se você não encontrou seu problema ou não conseguiu resolver seu problema, visite um dos seguintes canais para obter mais suporte:

* Obtenha respostas de especialistas do Azure por meio do [Suporte da Comunidade do Azure](https://azure.microsoft.com/support/community/).

* Conecte-se a [@AzureSupport](https://twitter.com/azuresupport) – a conta oficial do Microsoft Azure para melhorar a experiência do cliente conectando-se à comunidade do Azure para os recursos certos: respostas, suporte e especialistas.

* Se precisar de mais ajuda, poderá enviar uma solicitação de suporte do [portal do Azure](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Selecione **Suporte** na barra de menus ou abra o hub **Ajuda + suporte**. Para obter informações mais detalhadas, confira [Como criar uma solicitação de suporte do Azure](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request). O acesso ao Gerenciamento de assinaturas e ao suporte de cobrança está incluído na sua assinatura do Microsoft Azure, e o suporte técnico é fornecido por meio de um dos [Planos de suporte do Azure](https://azure.microsoft.com/support/plans/).
