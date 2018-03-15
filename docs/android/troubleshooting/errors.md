---
title: Xamarin.Android Errors Matrix
ms.topic: article
ms.prod: xamarin
ms.assetid: 7EBE4C01-8EFC-4B7E-97BA-D879994F59AB
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 03/13/2018
ms.openlocfilehash: d113e7747f656eda2bedb88574f4b2027616add8
ms.sourcegitcommit: 8e722d72c5d1384889f70adb26c5675544897b1f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/15/2018
---
# <a name="xamarinandroid-errors-matrix"></a>Xamarin.Android Errors Matrix

## <a name="errors-reference"></a>Referência de erros

Este documento fornece informações sobre os vários códigos de erro do Xamarin.

|Categoria|Descrição|
|--- |--- |
|XA0xxx|mandroid erros|
|XA1xxx|Cópia do arquivo / links simbólicos (projeto relacionado) erros|
|XA2xxx|Erros de Vinculador|
|XA3xxx|Erros AOT|
|XA4xxx|Erros de geração de código|
|XA5xxx|Erros de cadeia de ferramentas e GCC|
|XA6xxx|erros de ferramentas internas de mandroid|
|XA7xxx|Reservado|
|XA8xxx|Reservado|
|XA9xxx|Erros de licenciamento|


## <a name="error-codes"></a>Códigos de erro

### <a name="xa0xxx-errors"></a>Erros de XA0xxx

|Código do erro|Descrição|
|--- |--- |
|XA0000|Erro inesperado - preencha um [relatório de bugs](http://bugzilla.xamarin.com).|
|XA0001|'-devname foi fornecida sem qualquer ação específicos do dispositivo.|
|XA0002|Não foi possível analisar a variável de ambiente '{0}'.|
|XA0003|Aplicativo nome '{0} .exe' conflita com um nome de assembly (. dll) do SDK ou produto.|
|XA0004|Nova lógica de refcounting requer sgen para ser habilitada.|
|XA0005|O diretório de saída '{0}' não existe.|
|XA0006|Não há nenhuma plataforma devel no '{0}', use - plataforma = para especificar o SDK em várias plataformas|
|XA0007|O assembly de raiz '{0}' não existe.|
|XA0008|Você deve fornecer um assembly de raiz apenas.|
|XA0009|Erro ao carregar assemblies: {0}.|
|XA0010|Não foi possível analisar os argumentos de linha de comando: {0}.|
|XA0011|{0} foi desenvolvido com um tempo de execução mais recente (\\{1 \\}) que oferece suporte a MonoTouch.|
|XA0012|Dados incompletos são fornecidos para completar '{0}'.|
|XA0013|Suporte de criação de perfil requer sgen para ser habilitada.|
|XA0014|iOS {0} não dá suporte a aplicativos de construção direcionamento ARMv6.|
|XA0020|Não foi possível determinar o caminho mandroid.|
|XA0100|EmbeddedNativeLibrary '{0}' é inválido no projeto de aplicativo do Android. Use AndroidNativeLibrary.|

### <a name="xa1xxx-errors"></a>Erros de XA1xxx

|Código do erro|Descrição|
|--- |--- |
|XA1001|Não foi possível encontrar um aplicativo no diretório especificado.|
|XA1002|Não foi possível criar links simbólicos, os arquivos foram copiados.|
|XA1003|Não foi possível eliminar o aplicativo '{0}'. Você precisará interromper o aplicativo manualmente.|
|XA1004|Não foi possível obter a lista de aplicativos instalados.|
|XA1005|Não foi possível interromper o aplicativo '{0}' no dispositivo '\\{1 \\}': 2}. Você precisará interromper o aplicativo manualmente.|
|XA1006|Não foi possível instalar o aplicativo '{0}' no dispositivo '\\{1 \\}': 2}.|
|XA1007|Falha ao iniciar o aplicativo '{0}' no dispositivo '\\{1 \\}': 2}. Você ainda pode iniciar o aplicativo manualmente tocando nele.|
|XA1008|Falha ao iniciar o simulador: {0}.|
|XA1009|Não foi possível copiar o assembly '{0}' para '\\{1 \\}': 2}.|
|XA1010|Não foi possível carregar o assembly '{0}': \\{1 \\}.|
|XA1011|Não foi possível adicionar o arquivo de recurso ausente: '{0}'.|
|XA1101|Não foi possível iniciar o aplicativo.|
|XA1102|Não foi possível anexar ao aplicativo (para eliminá-lo): {0}.|
|XA1103|Não foi possível desanexar.|
|XA1104|Falha ao enviar o pacote: {0}.|
|XA1105|Tipo de resposta inesperado.|
|XA1106|Não foi possível obter a lista de aplicativos no dispositivo: tempo limite da solicitação.|
|XA1107|Falha ao iniciar o aplicativo.|
|XA1201|Não foi possível carregar o simulador: {0}.|
|XA1301|Biblioteca nativa '{0}' (\\{1 \\}) foi ignorada, pois ela não corresponde a architecture(s) de compilação atual (2}).|

### <a name="xa2xxx-errors"></a>Erros de XA2xxx

|Código do erro|Descrição|
|--- |--- |
|XA2001|Não foi possível vincular assemblies.|
|XA2002|Não é possível resolver a referência: {0}.|
|XA2003|Opção '{0}' será ignorada porque a vinculação está desabilitada.|
|XA2004|Arquivo de definições de vinculador extra '{0}' não pôde ser localizado.|
|XA2005|Definições de '{0}' não podem ser analisadas.|
|XA2006|Não foi possível resolver a referência ao item de metadados '{0}' (definido em '\\{1 \\}') de '2}'.|

### <a name="xa3xxx-errors"></a>Erros de XA3xxx

Esses são erros AOT.

|Código do erro|Descrição|
|--- |--- |
|XA3001|Pode ser AOT o assembly '{0}'.|
|XA3002|Restrição de AOT: o método '{0}' deve ser estático, pois ela está decorada com [MonoPInvokeCallback].|
|XA3003|Conflitantes – opções de depuração e – llvm. Depuração de software está desabilitado.|


### <a name="xa4xxx-errors"></a>Erros de XA4xxx

Esses são erros de geração de código.

|Código do erro|Descrição|
|--- |--- |
|XA4001|O modelo principal não pôde ser expansed para '{0}'.|
|XA4101|O registrador não é possível criar uma assinatura para o tipo '{0}'.|
|XA4102|O registrador encontrado um tipo inválido '{0}' na assinatura para o método '2}'. Use '\\{1 \\}'.|
|XA4103|O registrador encontrado um tipo inválido '{0}' na assinatura para o método '2}': O tipo implementa INativeObject, mas não tem um construtor que aceita dois (IntPtr, bool) argumentos.|
|XA4104|O registrador não é possível empacotar o valor de retorno para o tipo '{0}' na assinatura para o método '\\{1 \\}'.|
|XA4105|O registrador não é possível empacotar o parâmetro do tipo '{0}' na assinatura para o método '\\{1 \\}'.|
|XA4106|O registrador não é possível empacotar o valor de retorno para a estrutura '{0}' na assinatura para o método '\\{1 \\}'.|
|XA4107|O registrador não é possível empacotar o parâmetro do tipo '{0}' na assinatura para o método '\\{1 \\}'.|
|XA4108|O registrador não é possível obter o tipo de ObjectiveC para tipo gerenciado '{0}'.|
|XA4109|Falha ao compilar o código gerado registrador. Envie um [relatório de bugs](http://bugzilla.xamarin.com).|
|XA4110|O registrador não é possível empacotar o parâmetro de saída do tipo '{0}' na assinatura para o método '\\{1 \\}'.|
|XA4111|O registrador não é possível criar uma assinatura para o tipo '{0}' no método '\\{1 \\}'.|
|XA4200|Só pode gerar do ACW para tipos de 'claas'.|
|XA4201|Não é possível determinar o nome JNI para tipo de {0}.|
|XA4203|O nome do tipo especificado deve ser totalmente qualificado.|
|XA4204|Não é possível resolver o tipo de interface '{0}'. Você não tem uma referência de assembly?|
|XA4205|[ExportField] só pode ser usado em métodos com parâmetros de 0.|
|XA4206|Exportação] não pode ser usada em um tipo genérico.|
|XA4207|[ExportField] não pode ser usado em um tipo genérico.|
|XA4208|[Java.Interop.ExportFieldAttribute] não pode ser usado em um método de retorno de void.|
|XA4209|Falha ao criar JavaTypeInfo para classe: {0} devido a \\{1 \\}.|
|XA4210|Você precisa adicionar uma referência a Mono.Android.Export.dll quando você usar ExportAttribute ou ExportFieldAttribute.|
|XA4211|AndroidManifest.xml //uses-sdk/@android:targetSdkVersion '{0}' é menor do que $(TargetFrameworkVersion) '\\{1 \\}'. Usando a API-\ {1 \} para compilação ACW.|


### <a name="xa5xxx-errors"></a>Erros de XA5xxx

|Código do erro|Descrição|
|--- |--- |
|XA5101|Faltando '{0}' compilador. Instale o Android NDK.|
|XA5102|Falha na conversão do assembly para código nativo. Envie um [relatório de bugs](http://bugzilla.xamarin.com).|
|XA5103|Falha ao compilar o arquivo '{0}'. Envie um [relatório de bugs](http://bugzilla.xamarin.com).|
|XA5201|Falha ao vincular nativo. Analise os sinalizadores de usuário fornecidos para gcc: {0}|
|XA5202|Falha ao vincular nativo. Examine o log de compilação.|
|XA5303|Nativo vinculação Aviso: {0}.|
|XA5300|SDK do Android não encontrado ou não totalmente instalado.|
|XA5301|Ferramenta 'Remover' ausente. Instale o Xcode componente 'Ferramentas de linha de comando'.|
|XA5302|Ferramenta de 'dsymutil' ausente. Instale o Xcode componente 'Ferramentas de linha de comando'.|
|XA5203|Falha ao gerar os símbolos de depuração (dSYM directory). Examine o log de compilação.|
|XA5204|Falha ao remover o binário final. Examine o log de compilação.|
|XA5205|Ferramenta de 'aapt' ausente. Instale o pacote de ferramentas de compilação do SDK do Android.|
|XA5206|{0}. Diretório de recursos Android \\{1 \\} não existe.|
|XA5207|{0}. \\{1 \\} o arquivo de biblioteca Java não existe.|
|XA5208|Falha no download. Faça o download {0} e colocá-lo para o diretório de \\{1 \\}.|
|XA5209|Falha ao descompactar. Baixe {0} e extraia-o para o diretório de \\{1 \\}.|
|XA5210|{0}. Arquivo de biblioteca nativa \\{1 \\} não existe.|

### <a name="xa6xxx-errors"></a>Erros de XA6xxx

|Código do erro|Descrição|
|--- |--- |
|XA6001|Versão de execução de Cecil não dá suporte a colocação de assembly.|
|XA6002|Não foi possível retirar o assembly '{0}'.|
|XA6003|Mensagem UnauthorizedAccessException.|

### <a name="xa9xxx-errors"></a>Erros de XA9xxx

Esses códigos de erro são erros de licenciamento e ativação.


#### <a name="xa9000"></a>XA9000

 **Causa:** licença expirou

 **Verificado durante:** de compilação

|Starter|Indie|Business(Trial)|Negócio|Enterprise|
|--- |--- |--- |--- |--- |
|AVISO|AVISO|AVISO|AVISO|AVISO|


#### <a name="xa9001"></a>XA9001

 **Causa:** avaliação expirou

 **Verificado durante:** de compilação

|Starter|Indie|Business(Trial)|Negócio|Enterprise|
|--- |--- |--- |--- |--- |
|AVISO|AVISO|AVISO|AVISO|AVISO|



#### <a name="xa9002"></a>XA9002

 **Cause:** AndroidJavaSource

 **Verificado durante:** de compilação

|Starter|Indie|Business(Trial)|Negócio|Enterprise|
|--- |--- |--- |--- |--- |
|ERROR|OK|OK|OK|OK|

 **Causa:** AndroidJavaLibrary

 **Verificado durante:** de compilação

|Starter|Indie|Business(Trial)|Negócio|Enterprise|
|--- |--- |--- |--- |--- |
|ERROR|OK|OK|OK|OK|

 **Se um assembly de associação tem o JAR inseridos, isso é detectado no tempo de pacote, não o tempo de compilação.**

 **Cause:** AndroidNativeLibrary

 **Verificado durante:** de compilação

|Starter|Indie|Business(Trial)|Negócio|Enterprise|
|--- |--- |--- |--- |--- |
|ERROR|OK|OK|OK|OK|


#### <a name="xa9003"></a>XA9003

 **Cause:** System.Runtime.Serialization

 **Verificado durante:** pacote

|Starter|Indie|Business(Trial)|Negócio|Enterprise|
|--- |--- |--- |--- |--- |
|ERROR|ERROR|OK|OK|OK|

 **Cause:** System.ServiceModel.Web

 **Verificado durante:** pacote

|Starter|Indie|Business(Trial)|Negócio|Enterprise|
|--- |--- |--- |--- |--- |
|ERROR|ERROR|OK|OK|OK|

 **Cause:** Mono.Data.Tds

 **Verificado durante:** de compilação

|Starter|Indie|Business(Trial)|Negócio|Enterprise|
|--- |--- |--- |--- |--- |
|ERROR|ERROR|OK|OK|OK|

 **Isso é referenciado pela System.Data.dll, que é permitido**

 **Cause:** Mono.Android.Export

 **Verificado durante:** de compilação

|Starter|Indie|Business(Trial)|Negócio|Enterprise|
|--- |--- |--- |--- |--- |
|ERROR|OK|OK|OK|OK|



#### <a name="xa9004"></a>XA9004

 **Causa:** – criação de perfil

 **Verificado durante:** pacote

|Starter|Indie|Business(Trial)|Negócio|Enterprise|
|--- |--- |--- |--- |--- |
|ERROR|ERROR|OK|OK|OK|



#### <a name="xa9005"></a>XA9005

 **Causa:** limite de tamanho (32 kb).

 **Verificado durante:** pacote

|Starter|Indie|Business(Trial)|Negócio|Enterprise|
|--- |--- |--- |--- |--- |
|ERROR|OK|-|-|-|



#### <a name="xa9006"></a>XA9006

 **Causa:** namespace SqlClient.

 **Verificado durante:** pacote

|Starter|Indie|Business(Trial)|Negócio|Enterprise|
|--- |--- |--- |--- |--- |
|ERROR|ERROR|OK|OK|OK|


#### <a name="xa9008"></a>XA9008

 **Causa:** Compilando a partir da linha de comando.

 **Verificado durante:** de compilação

|Starter|Indie|Business(Trial)|Negócio|Enterprise|
|--- |--- |--- |--- |--- |
|ERROR|ERROR|OK|OK|OK|


#### <a name="xa9009"></a>XA9009

 **Causa:** o número de série ausente.

 **Verificado durante:** emaranhados

|Starter|Indie|Business(Trial)|Negócio|Enterprise|
|--- |--- |--- |--- |--- |
|ERROR|ERROR|ERROR|ERROR|ERROR|


#### <a name="xa9010"></a>XA9010

 **Causa:** ProductId inválido.

 **Verificado durante:** de compilação

|Starter|Indie|Business(Trial)|Negócio|Enterprise|
|--- |--- |--- |--- |--- |
|ERROR|ERROR|ERROR|ERROR|ERROR|

Equivalente a XA9018.



#### <a name="xa9011"></a>XA9011

 **Causa:** Falha ao atualizar o arquivo de licença (para o novo formato de arquivo).

 **Verificado durante:** ativação

|Starter|Indie|Business(Trial)|Negócio|Enterprise|
|--- |--- |--- |--- |--- |
|ERROR|ERROR|ERROR|ERROR|ERROR|

#### <a name="xa9012"></a>XA9012

 **Causa:** sem internet

 **Verificado durante:** ativação

|Starter|Indie|Business(Trial)|Negócio|Enterprise|
|--- |--- |--- |--- |--- |
|ERROR|ERROR|ERROR|ERROR|ERROR|


#### <a name="xa9013"></a>XA9013

 **Causa:** erro desconhecido

 **Verificado durante:** ativação

|Starter|Indie|Business(Trial)|Negócio|Enterprise|
|--- |--- |--- |--- |--- |
|ERROR|ERROR|ERROR|ERROR|ERROR|


#### <a name="xa9014"></a>XA9014

 **Causa:** código de ativação inválido

 **Verificado durante:** ativação

|Starter|Indie|Business(Trial)|Negócio|Enterprise|
|--- |--- |--- |--- |--- |
|ERROR|ERROR|ERROR|ERROR|ERROR|


#### <a name="xa9017"></a>XA9017

 **Causa:** servidor de ativação não retorna uma licença válida.

 **Verificado durante:** ativação

|Starter|Indie|Business(Trial)|Negócio|Enterprise|
|--- |--- |--- |--- |--- |
|ERROR|ERROR|ERROR|ERROR|ERROR|


#### <a name="xa9018"></a>XA9018

**Causa:** licença inválida

**Verificado durante:** de compilação

|Starter|Indie|Business(Trial)|Negócio|Enterprise|
|--- |--- |--- |--- |--- |
|-|-|ERROR|-|-|

