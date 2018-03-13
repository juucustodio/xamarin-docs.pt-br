---
title: Xamarin.Android Errors Matrix
ms.topic: article
ms.prod: xamarin
ms.assetid: 7EBE4C01-8EFC-4B7E-97BA-D879994F59AB
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 02/16/2018
ms.openlocfilehash: 1c9284f3db1c503b5c88f0d310f916f4c9e3363d
ms.sourcegitcommit: 0fdb243b46cf21be47584900805cadcd077121bf
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/12/2018
---
# <a name="xamarinandroid-errors-matrix"></a>Xamarin.Android Errors Matrix

## <a name="errors-reference"></a>Referência de erros

Este documento fornece informações sobre os vários códigos de erro do Xamarin.

<table>
    <thead>
        <tr>
            <td>Categoria</td>
            <td>Descrição</td>
        </tr>
    </thead>
    <tbody>
        <tr><td>XA0xxx</td><td><code>mandroid</code> Erros</td></tr>
        <tr><td>XA1xxx</td><td>Cópia do arquivo / links simbólicos (projeto relacionado) erros</td></tr>
        <tr><td>XA2xxx</td><td>Erros de Vinculador</td></tr>
        <tr><td>XA3xxx</td><td>Erros AOT</td></tr>
        <tr><td>XA4xxx</td><td>Erros de geração de código</td></tr>
        <tr><td>XA5xxx</td><td>Erros de cadeia de ferramentas e GCC</td></tr>
        <tr><td>XA6xxx</td><td><code>mandroid</code> Erros internos de ferramentas</td></tr>
        <tr><td>XA7xxx</td><td>Reservado</td></tr>
        <tr><td>XA8xxx</td><td>Reservado</td></tr>
        <tr><td>XA9xxx</td><td>Erros de licenciamento</td></tr>
    </tbody>
</table>

## <a name="error-codes"></a>Códigos de erro

### <a name="xa0xxx-errors"></a>Erros de XA0xxx

<table>
    <thead>
        <tr><td>Código do erro</td><td>Descrição</td></tr>
    </thead>
    <tbody>
        <tr><td>XA0000</td><td>Erro inesperado - preencha um relatório de erros em <a href="http://bugzilla.xamarin.com">http://bugzilla.xamarin.com</a></td></tr>
        <tr><td>XA0001</td><td>'-devname foi fornecida sem qualquer ação específicos do dispositivo</td></tr>
        <tr><td>XA0002</td><td>Não foi possível analisar a variável de ambiente '{0}'.</td></tr>
        <tr><td>XA0003</td><td>Aplicativo nome '{0} .exe' conflita com um nome de assembly (. dll) do SDK ou produto.</td></tr>
        <tr><td>XA0004</td><td>Nova lógica de refcounting requer sgen para ser habilitada.</td></tr>
        <tr><td>XA0005</td><td>O diretório de saída '{0}' não existe</td></tr>
        <tr><td>XA0006</td><td>Não há nenhuma plataforma devel no '{0}', use - plataforma = para especificar o SDK em várias plataformas</td></tr>
        <tr><td>XA0007</td><td>O assembly de raiz '{0}' não existe</td></tr>
        <tr><td>XA0008</td><td>Você deve fornecer um assembly de raiz apenas</td></tr>
        <tr><td>XA0009</td><td>Erro ao carregar assemblies: {0}</td></tr>
        <tr><td>XA0010</td><td>Não foi possível analisar os argumentos de linha de comando: {0}</td></tr>
        <tr><td>XA0011</td><td>{0} foi desenvolvido com um tempo de execução mais recente (\\{1 \\}) que oferece suporte a MonoTouch</td></tr>
        <tr><td>XA0012</td><td>Dados incompletos são fornecidos para concluir `{0}`.</td></tr>
        <tr><td>XA0013</td><td>Suporte de criação de perfil requer sgen para ser ativada</td></tr>
        <tr><td>XA0014</td><td>iOS {0} não dá suporte a aplicativos de construção direcionamento ARMv6</td></tr>
        <tr><td>XA0020</td><td>Não foi possível determinar o caminho mandroid.</td></tr>
        <tr><td>XA0100</td><td>EmbeddedNativeLibrary '{0}' é inválido no projeto de aplicativo do Android. Use AndroidNativeLibrary.</td></tr>
    </tbody>
</table>

### <a name="xa1xxx-errors"></a>Erros de XA1xxx

<table>
    <thead>
        <tr><td>Código do erro</td><td>Descrição</td></tr>
    </thead>
    <tbody>
        <tr><td>XA1001</td><td>Não foi possível encontrar um aplicativo no diretório especificado</td></tr>
        <tr><td>XA1002</td><td>Não foi possível criar links simbólicos, os arquivos foram copiados</td></tr>
        <tr><td>XA1003</td><td>Não foi possível eliminar o aplicativo '{0}'. Você precisará interromper o aplicativo manualmente.</td></tr>
        <tr><td>XA1004</td><td>Não foi possível obter a lista de aplicativos instalados.</td></tr>
        <tr><td>XA1005</td><td>Não foi possível interromper o aplicativo '{0}' no dispositivo '\\{1 \\}': 2}. Você precisará interromper o aplicativo manualmente.</td></tr>
        <tr><td>XA1006</td><td>Não foi possível instalar o aplicativo '{0}' no dispositivo '\\{1 \\}': 2}.</td></tr>
        <tr><td>XA1007</td><td>Falha ao iniciar o aplicativo '{0}' no dispositivo '\\{1 \\}': 2}. Você ainda pode iniciar o aplicativo manualmente tocando nele.</td></tr>
        <tr><td>XA1008</td><td>Falha ao iniciar o simulador: {0}</td></tr>
        <tr><td>XA1009</td><td>Não foi possível copiar o assembly '{0}' para '\\{1 \\}': 2}</td></tr>
        <tr><td>XA1010</td><td>Não foi possível carregar o assembly '{0}': \\{1 \\}</td></tr>
        <tr><td>XA1011</td><td>Não foi possível adicionar o arquivo de recurso ausente: '{0}'</td></tr>
        <tr><td>XA1101</td><td>Não foi possível iniciar o aplicativo</td></tr>
        <tr><td>XA1102</td><td>Não foi possível anexar ao aplicativo (para eliminá-lo): {0}</td></tr>
        <tr><td>XA1103</td><td>Não foi possível desanexar</td></tr>
        <tr><td>XA1104</td><td>Falha ao enviar o pacote: {0}</td></tr>
        <tr><td>XA1105</td><td>Tipo de resposta inesperado</td></tr>
        <tr><td>XA1106</td><td>Não foi possível obter a lista de aplicativos no dispositivo: tempo limite da solicitação.</td></tr>
        <tr><td>XA1107</td><td>Falha ao iniciar o aplicativo</td></tr>
        <tr><td>XA1201</td><td>Não foi possível carregar o simulador: {0}</td></tr>
        <tr><td>XA1301</td><td>Biblioteca nativa `{0}` (\\{1 \\}) foi ignorado porque ele não coincide com o architecture(s) de compilação atual (2})</td></tr>
    </tbody>
</table>

### <a name="xa2xxx-errors"></a>Erros de XA2xxx

<table>
    <thead>
        <tr><td>Código do erro</td><td>Descrição</td></tr>
    </thead>
    <tbody>
        <tr><td>XA2001</td><td>Não foi possível vincular assemblies</td></tr>
        <tr><td>XA2002</td><td>Não é possível resolver a referência: {0}</td></tr>
        <tr><td>XA2003</td><td>Opção '{0}' será ignorada porque a vinculação está desabilitada</td></tr>
        <tr><td>XA2004</td><td>Arquivo de definições de vinculador extra '{0}' não pôde ser localizado.</td></tr>
        <tr><td>XA2005</td><td>Definições de '{0}' não podem ser analisadas.</td></tr>
        <tr><td>XA2006</td><td>Não foi possível resolver a referência ao item de metadados '{0}' (definido em '\\{1 \\}') de '2}'.</td></tr>
    </tbody>
</table>

### <a name="xa3xxx-errors"></a>Erros de XA3xxx

Esses são erros AOT.

<table>
    <thead>
        <tr><td>Código do erro</td><td>Descrição</td></tr>
    </thead>
    <tbody>
        <tr><td>XA3001</td><td>Foi AOT o assembly '{0}'</td></tr>
        <tr><td>XA3002</td><td>Restrição de AOT: o método '{0}' deve ser estático, pois ela está decorada com [MonoPInvokeCallback].</td></tr>
        <tr><td>XA3003</td><td>Conflitantes – opções de depuração e – llvm. Depuração de software está desabilitado.</td></tr>
    </tbody>
</table>

### <a name="xa4xxx-errors"></a>Erros de XA4xxx

Esses são erros de geração de código.

<table>
    <thead>
        <tr><td>Código do erro</td><td>Descrição</td></tr>
    </thead>
    <tbody>
        <tr><td>XA4001</td><td>O modelo principal não pôde ser expansed para `{0}`.</td></tr>
        <tr><td>XA4101</td><td>O registrador não é possível criar uma assinatura para o tipo `{0}`.</td></tr>
        <tr><td>XA4102</td><td>O registrador encontrado um tipo inválido `{0}` na assinatura para o método `{2}`. Use `{1}` em seu lugar.</td></tr>
        <tr><td>XA4103</td><td>O registrador encontrado um tipo inválido `{0}` na assinatura para o método `{2}`: O tipo implementa INativeObject, mas não tem um construtor que aceita dois (IntPtr, bool) argumentos</td></tr>
        <tr><td>XA4104</td><td>O registrador não é possível empacotar o valor de retorno para o tipo `{0}` na assinatura para o método `{1}`.</td></tr>
        <tr><td>XA4105</td><td>O registrador não pode realizar marshaling para o parâmetro de tipo `{0}` na assinatura para o método `{1}`.</td></tr>
        <tr><td>XA4106</td><td>O registrador não é possível empacotar o valor de retorno para a estrutura `{0}` na assinatura para o método `{1}`.</td></tr>
        <tr><td>XA4107</td><td>O registrador não pode realizar marshaling para o parâmetro de tipo `{0}` na assinatura para o método `{1}`.</td></tr>
        <tr><td>XA4108</td><td>O registrador não é possível obter o tipo de ObjectiveC para tipo gerenciado `{0}`.</td></tr>
        <tr><td>XA4109</td><td>Falha ao compilar o código gerado registrador. Envie um relatório de erros em <a href="http://bugzilla.xamarin.com">http://bugzilla.xamarin.com</a></td></tr>
        <tr><td>XA4110</td><td>O registrador não pode realizar marshaling para o parâmetro de saída do tipo `{0}` na assinatura para o método `{1}`.</td></tr>
        <tr><td>XA4111</td><td>O registrador não é possível criar uma assinatura para o tipo `{0}' in method `\\{1 \\} '.</td></tr>
        <tr><td>XA4200</td><td>Só pode gerar do ACW para tipos de 'claas'.</td></tr>
        <tr><td>XA4201</td><td>Não é possível determinar o nome JNI para tipo de {0}.</td></tr>
        <tr><td>XA4203</td><td>O nome do tipo especificado deve ser totalmente qualificado.</td></tr>
        <tr><td>XA4204</td><td>Não é possível resolver o tipo de interface '{0}'. Você não tem uma referência de assembly?</td></tr>
        <tr><td>XA4205</td><td>[ExportField] só pode ser usado em métodos com parâmetros de 0.</td></tr>
        <tr><td>XA4206</td><td>Exportação] não pode ser usada em um tipo genérico.</td></tr>
        <tr><td>XA4207</td><td>[ExportField] não pode ser usado em um tipo genérico.</td></tr>
        <tr><td>XA4208</td><td>[Java.Interop.ExportFieldAttribute] não pode ser usado em um método de retorno de void.</td></tr>
        <tr><td>XA4209</td><td>Falha ao criar JavaTypeInfo para classe: {0} devido a \\{1 \\}</td></tr>
        <tr><td>XA4210</td><td>Você precisa adicionar uma referência a Mono.Android.Export.dll quando você usar ExportAttribute ou ExportFieldAttribute.</td></tr>
        <tr><td>XA4211</td><td>AndroidManifest.xml //uses-sdk/@android:targetSdkVersion '{0}' é menor do que $(TargetFrameworkVersion) '\\{1 \\}'. Usando a API-\ {1 \} para compilação ACW.</td></tr>
    </tbody>
</table>

### <a name="xa5xxx-errors"></a>Erros de XA5xxx

<table>
    <thead>
        <tr><td>Código do erro</td><td>Descrição</td></tr>
    </thead>
    <tbody>
        <tr><td>XA5101</td><td>Faltando '{0}' compilador. Instale o Android NDK.</td></tr>
        <tr><td>XA5102</td><td>Falha na conversão do assembly para código nativo. Envie um relatório de erros em <a href="http://bugzilla.xamarin.com">http://bugzilla.xamarin.com</a></td></tr>
        <tr><td>XA5103</td><td>Falha ao compilar o arquivo '{0}'. Envie um relatório de erros em <a href="http://bugzilla.com">http://bugzilla.xamarin.com</a></td></tr>
        <tr><td>XA5201</td><td>Falha ao vincular nativo. Analise os sinalizadores de usuário fornecidos para gcc: {0}</td></tr>
        <tr><td>XA5202</td><td>Falha ao vincular nativo. Examine o log de compilação.</td></tr>
        <tr><td>XA5303</td><td>Nativo vinculação Aviso: {0}</td></tr>
        <tr><td>XA5300</td><td>SDK do Android não encontrado ou não totalmente instalado.</td></tr>
        <tr><td>XA5301</td><td>Ferramenta 'Remover' ausente. Instale o Xcode componente 'Ferramentas de linha de comando'</td></tr>
        <tr><td>XA5302</td><td>Ferramenta de 'dsymutil' ausente. Instale o Xcode componente 'Ferramentas de linha de comando'</td></tr>
        <tr><td>XA5203</td><td>Falha ao gerar os símbolos de depuração (dSYM directory). Examine o log de compilação.</td></tr>
        <tr><td>XA5204</td><td>Falha ao remover o binário final. Examine o log de compilação.</td></tr>
        <tr><td>XA5205</td><td>Ferramenta de 'aapt' ausente. Instale o pacote de ferramentas de compilação do SDK do Android.</td></tr>
        <tr><td>XA5206</td><td>{0}. Diretório de recursos Android \\{1 \\} não existe.</td></tr>
        <tr><td>XA5207</td><td>{0}. \\{1 \\} o arquivo de biblioteca Java não existe.</td></tr>
        <tr><td>XA5208</td><td>Falha no download. Faça o download {0} e colocá-lo para o diretório de \\{1 \\}.</td></tr>
        <tr><td>XA5209</td><td>Falha ao descompactar. Baixe {0} e extraia-o para o diretório de \\{1 \\}.</td></tr>
        <tr><td>XA5210</td><td>{0}. Arquivo de biblioteca nativa \\{1 \\} não existe.</td></tr>
    </tbody>
</table>

### <a name="xa6xxx-errors"></a>Erros de XA6xxx

<table>
    <thead>
        <tr><td>Código do erro</td><td>Descrição</td></tr>
    </thead>
    <tbody>
        <tr><td>XA6001</td><td>Versão de execução de Cecil não dá suporte a remoção de assembly</td></tr>
        <tr><td>XA6002</td><td>Não foi possível remover assembly `{0}`.</td></tr>
        <tr><td>XA6003</td><td>Mensagem UnauthorizedAccessException]</td></tr>
    </tbody>
</table>

### <a name="xa9xxx-errors"></a>Erros de XA9xxx

Este códigos de erro são erros de licenciamento e ativação.



#### <a name="xa9000"></a>XA9000

 **Causa:** licença expirou

 **Verificado durante:** de compilação

<table>
    <thead>
        <tr>
            <th>Starter</th>
            <th>Indie</th>
            <th>Business(Trial)</th>
            <th>Negócio</th>
            <th>Enterprise</th>
        </tr>
    </thead>
    <tbody>
        <tr>
            <td>AVISO</td>
            <td>AVISO</td>
            <td>AVISO</td>
            <td>AVISO</td>
            <td>AVISO</td>
        </tr>
    </tbody>
</table>



#### <a name="xa9001"></a>XA9001

 **Causa:** avaliação expirou

 **Verificado durante:** de compilação

<table>
    <thead>
        <tr>
            <th>Starter</th>
            <th>Indie</th>
            <th>Business(Trial)</th>
            <th>Negócio</th>
            <th>Enterprise</th>
        </tr>
    </thead>
    <tbody>
        <tr>
            <td>AVISO</td>
            <td>AVISO</td>
            <td>AVISO</td>
            <td>AVISO</td>
            <td>AVISO</td>
        </tr>
    </tbody>
</table>



#### <a name="xa9002"></a>XA9002

 **Cause:** AndroidJavaSource

 **Verificado durante:** de compilação

<table>
    <thead>
        <tr>
            <th>Starter</th>
            <th>Indie</th>
            <th>Business(Trial)</th>
            <th>Negócio</th>
            <th>Enterprise</th>
        </tr>
    </thead>
    <tbody>
        <tr>
            <td>ERROR</td>
            <td>OK</td>
            <td>OK</td>
            <td>OK</td>
            <td>OK</td>
        </tr>
    </tbody>
</table>

 **Causa:** AndroidJavaLibrary

 **Verificado durante:** de compilação

<table>
    <thead>
        <tr>
            <th>Starter</th>
            <th>Indie</th>
            <th>Business(Trial)</th>
            <th>Negócio</th>
            <th>Enterprise</th>
        </tr>
    </thead>
    <tbody>
        <tr>
            <td>ERROR</td>
            <td>OK</td>
            <td>OK</td>
            <td>OK</td>
            <td>OK</td>
        </tr>
    </tbody>
</table>

 **Se um assembly de associação tem o JAR inseridos, isso é detectado no tempo de pacote, não o tempo de compilação.**

 **Cause:** AndroidNativeLibrary

 **Verificado durante:** de compilação

<table>
    <thead>
        <tr>
            <th>Starter</th>
            <th>Indie</th>
            <th>Business(Trial)</th>
            <th>Negócio</th>
            <th>Enterprise</th>
        </tr>
    </thead>
    <tbody>
        <tr>
            <td>ERROR</td>
            <td>OK</td>
            <td>OK</td>
            <td>OK</td>
            <td>OK</td>
        </tr>
    </tbody>
</table>



#### <a name="xa9003"></a>XA9003

 **Cause:** System.Runtime.Serialization

 **Verificado durante:** pacote

<table>
    <thead>
        <tr>
            <th>Starter</th>
            <th>Indie</th>
            <th>Business(Trial)</th>
            <th>Negócio</th>
            <th>Enterprise</th>
        </tr>
    </thead>
    <tbody>
        <tr>
            <td>ERROR</td>
            <td>ERROR</td>
            <td>OK</td>
            <td>OK</td>
            <td>OK</td>
        </tr>
    </tbody>
</table>

 **Cause:** System.ServiceModel.Web

 **Verificado durante:** pacote

<table>
    <thead>
        <tr>
            <th>Starter</th>
            <th>Indie</th>
            <th>Business(Trial)</th>
            <th>Negócio</th>
            <th>Enterprise</th>
        </tr>
    </thead>
    <tbody>
        <tr>
            <td>ERROR</td>
            <td>ERROR</td>
            <td>OK</td>
            <td>OK</td>
            <td>OK</td>
        </tr>
    </tbody>
</table>

 **Cause:** Mono.Data.Tds

 **Verificado durante:** de compilação

<table>
    <thead>
        <tr>
            <th>Starter</th>
            <th>Indie</th>
            <th>Business(Trial)</th>
            <th>Negócio</th>
            <th>Enterprise</th>
        </tr>
    </thead>
    <tbody>
        <tr>
            <td>ERROR</td>
            <td>ERROR</td>
            <td>OK</td>
            <td>OK</td>
            <td>OK</td>
        </tr>
    </tbody>
</table>

 **Isso é referenciado pela System.Data.dll, que é permitido**

 **Cause:** Mono.Android.Export

 **Verificado durante:** de compilação

<table>
    <thead>
        <tr>
            <th>Starter</th>
            <th>Indie</th>
            <th>Business(Trial)</th>
            <th>Negócio</th>
            <th>Enterprise</th>
        </tr>
    </thead>
    <tbody>
        <tr>
            <td>ERROR</td>
            <td>OK</td>
            <td>OK</td>
            <td>OK</td>
            <td>OK</td>
        </tr>
    </tbody>
</table>



#### <a name="xa9004"></a>XA9004

 **Causa:** – criação de perfil

 **Verificado durante:** pacote

<table>
    <thead>
        <tr>
            <th>Starter</th>
            <th>Indie</th>
            <th>Business(Trial)</th>
            <th>Negócio</th>
            <th>Enterprise</th>
        </tr>
    </thead>
    <tbody>
        <tr>
            <td>ERROR</td>
            <td>ERROR</td>
            <td>OK</td>
            <td>OK</td>
            <td>OK</td>
        </tr>
    </tbody>
</table>



#### <a name="xa9005"></a>XA9005

 **Causa:** limite de tamanho (32 kb)

 **Verificado durante:** pacote

<table>
    <thead>
        <tr>
            <th>Starter</th>
            <th>Indie</th>
            <th>Business(Trial)</th>
            <th>Negócio</th>
            <th>Enterprise</th>
        </tr>
    </thead>
    <tbody>
        <tr>
            <td>ERROR</td>
            <td>OK</td>
            <td>-</td>
            <td>-</td>
            <td>-</td>
        </tr>
    </tbody>
</table>



#### <a name="xa9006"></a>XA9006

 **Causa:** namespace SqlClient

 **Verificado durante:** pacote

<table>
    <thead>
        <tr>
            <th>Starter</th>
            <th>Indie</th>
            <th>Business(Trial)</th>
            <th>Negócio</th>
            <th>Enterprise</th>
        </tr>
    </thead>
    <tbody>
        <tr>
            <td>ERROR</td>
            <td>ERROR</td>
            <td>OK</td>
            <td>OK</td>
            <td>OK</td>
        </tr>
    </tbody>
</table>



#### <a name="xa9008"></a>XA9008

 **Causa:** Compilando a partir da linha de comando

 **Verificado durante:** de compilação

<table>
    <thead>
        <tr>
            <th>Starter</th>
            <th>Indie</th>
            <th>Business(Trial)</th>
            <th>Negócio</th>
            <th>Enterprise</th>
        </tr>
    </thead>
    <tbody>
        <tr>
            <td>ERROR</td>
            <td>ERROR</td>
            <td>OK</td>
            <td>OK</td>
            <td>OK</td>
        </tr>
    </tbody>
</table>



#### <a name="xa9009"></a>XA9009

 **Causa:** o número de série ausente

 **Verificado durante:** emaranhados

<table>
    <thead>
        <tr>
            <th>Starter</th>
            <th>Indie</th>
            <th>Business(Trial)</th>
            <th>Negócio</th>
            <th>Enterprise</th>
        </tr>
    </thead>
    <tbody>
        <tr>
            <td>ERROR</td>
            <td>ERROR</td>
            <td>ERROR</td>
            <td>ERROR</td>
            <td>ERROR</td>
        </tr>
    </tbody>
</table>



#### <a name="xa9010"></a>XA9010

 **Causa:** ProductId inválido

 **Verificado durante:** de compilação

<table>
    <thead>
        <tr>
            <th>Starter</th>
            <th>Indie</th>
            <th>Business(Trial)</th>
            <th>Negócio</th>
            <th>Enterprise</th>
        </tr>
    </thead>
    <tbody>
        <tr>
            <td>ERROR</td>
            <td>ERROR</td>
            <td>ERROR</td>
            <td>ERROR</td>
            <td>ERROR</td>
        </tr>
    </tbody>
</table>

Equivalente a XA9018



#### <a name="xa9011"></a>XA9011

 **Causa:** Falha ao atualizar o arquivo de licença (para o novo formato de arquivo)

 **Verificado durante:** ativação

<table>
    <thead>
        <tr>
            <th>Starter</th>
            <th>Indie</th>
            <th>Business(Trial)</th>
            <th>Negócio</th>
            <th>Enterprise</th>
        </tr>
    </thead>
    <tbody>
        <tr>
            <td>ERROR</td>
            <td>ERROR</td>
            <td>ERROR</td>
            <td>ERROR</td>
            <td>ERROR</td>
        </tr>
    </tbody>
</table>



#### <a name="xa9012"></a>XA9012

 **Causa:** sem internet

 **Verificado durante:** ativação

<table>
    <thead>
        <tr>
            <th>Starter</th>
            <th>Indie</th>
            <th>Business(Trial)</th>
            <th>Negócio</th>
            <th>Enterprise</th>
        </tr>
    </thead>
    <tbody>
        <tr>
            <td>ERROR</td>
            <td>ERROR</td>
            <td>ERROR</td>
            <td>ERROR</td>
            <td>ERROR</td>
        </tr>
    </tbody>
</table>



#### <a name="xa9013"></a>XA9013

 **Causa:** erro desconhecido

 **Verificado durante:** ativação

<table>
    <thead>
        <tr>
            <th>Starter</th>
            <th>Indie</th>
            <th>Business(Trial)</th>
            <th>Negócio</th>
            <th>Enterprise</th>
        </tr>
    </thead>
    <tbody>
        <tr>
            <td>ERROR</td>
            <td>ERROR</td>
            <td>ERROR</td>
            <td>ERROR</td>
            <td>ERROR</td>
        </tr>
    </tbody>
</table>



#### <a name="xa9014"></a>XA9014

 **Causa:** código de ativação inválido

 **Verificado durante:** ativação

<table>
    <thead>
        <tr>
            <th>Starter</th>
            <th>Indie</th>
            <th>Business(Trial)</th>
            <th>Negócio</th>
            <th>Enterprise</th>
        </tr>
    </thead>
    <tbody>
        <tr>
            <td>ERROR</td>
            <td>ERROR</td>
            <td>ERROR</td>
            <td>ERROR</td>
            <td>ERROR</td>
        </tr>
    </tbody>
</table>



#### <a name="xa9017"></a>XA9017

 **Causa:** servidor de ativação não retorna uma licença válida

 **Verificado durante:** ativação

<table>
    <thead>
        <tr>
            <th>Starter</th>
            <th>Indie</th>
            <th>Business(Trial)</th>
            <th>Negócio</th>
            <th>Enterprise</th>
        </tr>
    </thead>
    <tbody>
        <tr>
            <td>ERROR</td>
            <td>ERROR</td>
            <td>ERROR</td>
            <td>ERROR</td>
            <td>ERROR</td>
        </tr>
    </tbody>
</table>


#### <a name="xa9018"></a>XA9018

**Causa:** licença inválida

**Verificado durante:** de compilação

<table>
    <thead>
        <tr>
            <th>Starter</th>
            <th>Indie</th>
            <th>Business(Trial)</th>
            <th>Negócio</th>
            <th>Enterprise</th>
        </tr>
    </thead>
    <tbody>
        <tr>
            <td>-</td>
            <td>-</td>
            <td>ERROR</td>
            <td>-</td>
            <td>-</td>
        </tr>
    </tbody>
</table>
