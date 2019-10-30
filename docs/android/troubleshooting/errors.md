---
title: Matriz de erros do Xamarin. Android
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: 7EBE4C01-8EFC-4B7E-97BA-D879994F59AB
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 03/13/2018
ms.openlocfilehash: dd05acdf58fbae85d9d8a82e899ebedc4c945281
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/29/2019
ms.locfileid: "73019614"
---
# <a name="xamarinandroid-errors-matrix"></a>Matriz de erros do Xamarin. Android

## <a name="errors-reference"></a>Referência de erros

Este documento fornece algumas informações sobre os vários códigos de erro do Xamarin.

|Categoria|Descrição|
|--- |--- |
|XA0xxx|Erros de mandroid|
|XA1xxx|Erros de cópia de arquivo/symlinks (relacionados ao projeto)|
|XA2xxx|Erros de Vinculador|
|XA3xxx|Erros de AOT|
|XA4xxx|Erros de geração de código|
|XA5xxx|Erros GCC e ferramentas|
|XA6xxx|erros de ferramentas internas do mandroid|
|XA7xxx|Reservado|
|XA8xxx|Reservado|
|XA9xxx|Erros de licenciamento|

## <a name="error-codes"></a>Códigos de erro

### <a name="xa0xxx-errors"></a>Erros de XA0xxx

|Código do erro|Descrição|
|--- |--- |
|XA0000|Erro inesperado-preencha um [relatório de bug](https://github.com/xamarin/xamarin-android/issues/new).|
|XA0001|'-DEVNAME foi fornecido sem nenhuma ação específica do dispositivo.|
|XA0002|Não foi possível analisar a variável de ambiente '{0}'.|
|XA0003|O nome do aplicativo '{0}. exe ' está em conflito com um nome de SDK ou de assembly de produto (. dll).|
|XA0004|A nova lógica refcounting requer que o SGen esteja habilitado também.|
|XA0005|O diretório de saída '{0}' não existe.|
|XA0006|Não há plataforma desenvolvedor em '{0}', use--Platform = PLAT para especificar o SDK|
|XA0007|O assembly raiz '{0}' não existe.|
|XA0008|Você deve fornecer apenas um assembly raiz.|
|XA0009|Erro ao carregar assemblies: {0}.|
|XA0010|Não foi possível analisar os argumentos de linha de comando: {0}.|
|XA0011|{0} foi criado em relação a um tempo de execução mais recente ({1}) do que o MonoTouch dá suporte.|
|XA0012|Dados incompletos são fornecidos para concluir '{0}'.|
|XA0013|O suporte à criação de perfil requer que o SGen esteja habilitado também.|
|XA0014|o iOS {0} não dá suporte à criação de aplicativos destinados a ARMv6.|
|XA0020|Não foi possível determinar o caminho de mandroid.|
|XA0100|EmbeddedNativeLibrary '{0}' é inválido no projeto de aplicativo Android. Em vez disso, use AndroidNativeLibrary.|

### <a name="xa1xxx-errors"></a>Erros de XA1xxx

|Código do erro|Descrição|
|--- |--- |
|XA1001|Não foi possível encontrar um aplicativo no diretório especificado.|
|XA1002|Não foi possível criar symlinks, os arquivos foram copiados.|
|XA1003|Não foi possível eliminar o aplicativo '{0}'. Talvez seja necessário encerrar o aplicativo manualmente.|
|XA1004|Não foi possível obter a lista de aplicativos instalados.|
|XA1005|Não foi possível eliminar o aplicativo '{0}' no dispositivo '{1}': {2}. Talvez seja necessário encerrar o aplicativo manualmente.|
|XA1006|Não foi possível instalar o aplicativo '{0}' no dispositivo '{1}': {2}.|
|XA1007|Falha ao iniciar o aplicativo '{0}' no dispositivo '{1}': {2}. Você ainda pode iniciar o aplicativo manualmente tocando nele.|
|XA1008|Falha ao iniciar o simulador: {0}.|
|XA1009|Não foi possível copiar o assembly '{0}' para '{1}': {2}.|
|XA1010|Não foi possível carregar o assembly '{0}': {1}.|
|XA1011|Não foi possível adicionar o arquivo de recurso ausente: '{0}'.|
|XA1101|Não foi possível iniciar o aplicativo.|
|XA1102|Não foi possível anexar ao aplicativo (para Kill-lo): {0}.|
|XA1103|Não foi possível desanexar.|
|XA1104|Falha ao enviar o pacote: {0}.|
|XA1105|Tipo de resposta inesperado.|
|XA1106|Não foi possível obter a lista de aplicativos no dispositivo: o tempo limite da solicitação foi atingido.|
|XA1107|Falha ao iniciar o aplicativo.|
|XA1201|Não foi possível carregar o simulador: {0}.|
|XA1301|A biblioteca nativa '{0}' ({1}) foi ignorada porque não corresponde às arquiteturas de compilação atuais ({2}).|

### <a name="xa2xxx-errors"></a>Erros de XA2xxx

|Código do erro|Descrição|
|--- |--- |
|XA2001|Não foi possível vincular assemblies.|
|XA2002|Não é possível resolver a referência: {0}.|
|XA2003|A opção '{0}' será ignorada porque a vinculação está desabilitada.|
|XA2004|Não foi possível localizar o arquivo de definições do vinculador extra '{0}'.|
|XA2005|Não foi possível analisar as definições de '{0}'.|
|XA2006|Não foi possível resolver a referência ao item de metadados '{0}' (definido em '{1}') de '{2}'.|

### <a name="xa3xxx-errors"></a>Erros de XA3xxx

Esses são erros de AOT.

|Código do erro|Descrição|
|--- |--- |
|XA3001|Não foi possível AOT o assembly '{0}'.|
|XA3002|Restrição de AOT: o método '{0}' deve ser estático porque está decorado com [MonoPInvokeCallback].|
|XA3003|Opções conflitantes--Debug e--LLVM. A depuração reversível está desabilitada.|

### <a name="xa4xxx-errors"></a>Erros de XA4xxx

Esses são erros de geração de código.

|Código do erro|Descrição|
|--- |--- |
|XA4001|Não foi possível paminhar o modelo principal para '{0}'.|
|XA4101|O registrador não pode criar uma assinatura para o tipo '{0}'.|
|XA4102|O registrador encontrou um tipo inválido '{0}' na assinatura para o método '{2}'. Em vez disso, use '{1}'.|
|XA4103|O registrador encontrou um tipo inválido '{0}' na assinatura para o método '{2}': o tipo implementa INativeObject, mas não tem um construtor que aceite dois argumentos (IntPtr, bool).|
|XA4104|O registrador não pode realizar marshaling do valor de retorno para o tipo '{0}' na assinatura para o método '{1}'.|
|XA4105|O registrador não pode realizar marshaling do parâmetro do tipo '{0}' na assinatura para o método '{1}'.|
|XA4106|O registrador não pode realizar marshaling do valor de retorno para a estrutura '{0}' na assinatura para o método '{1}'.|
|XA4107|O registrador não pode realizar marshaling do parâmetro do tipo '{0}' na assinatura para o método '{1}'.|
|XA4108|O registrador não pode obter o tipo ObjectiveC para o tipo gerenciado '{0}'.|
|XA4109|Falha ao compilar o código de registrador gerado. Registre um [relatório de bug](https://bugzilla.xamarin.com).|
|XA4110|O registrador não pode realizar marshaling do parâmetro out do tipo '{0}' na assinatura para o método '{1}'.|
|XA4111|O registrador não pode criar uma assinatura para o tipo '{0}' no método '{1}'.|
|XA4200|Só é possível gerar ACW para tipos ' CLAAS '.|
|XA4201|Não é possível determinar o nome do JNI para o tipo {0}.|
|XA4203|O nome do tipo especificado deve ser totalmente qualificado.|
|XA4204|Não é possível resolver o tipo de interface '{0}'. Você não tem uma referência de assembly?|
|XA4205|[Exportfield] só pode ser usado em métodos com 0 parâmetros.|
|XA4206|[Export] não pode ser usado em um tipo genérico.|
|XA4207|[Exportfield] não pode ser usado em um tipo genérico.|
|XA4208|[Java. Interop. ExportFieldAttribute] não pode ser usado em um método que retorna void.|
|XA4209|Falha ao criar JavaTypeInfo para a classe: {0} devido a {1}.|
|XA4210|Você precisa adicionar uma referência a mono. Android. Export. dll ao usar ExportAttribute ou ExportFieldAttribute.|
|XA4211|AndroidManifest. xml //uses-sdk/@android:targetSdkVersion '{0}' é menor que $ (TargetFrameworkVersion) '{1}'. Usando{1} de API para compilação ACW.|

### <a name="xa5xxx-errors"></a>Erros de XA5xxx

|Código do erro|Descrição|
|--- |--- |
|XA5101|Compilador '{0}' ausente. Instale o Android NDK.|
|XA5102|Falha na conversão do assembly em código nativo. Registre um [relatório de bug](https://bugzilla.xamarin.com).|
|XA5103|Falha ao compilar o arquivo '{0}'. Registre um [relatório de bug](https://bugzilla.xamarin.com).|
|XA5201|Falha na vinculação nativa. Examine os sinalizadores de usuário fornecidos para o gcc: {0}|
|XA5202|Falha na vinculação nativa. Examine o log de compilação.|
|XA5303|Aviso de vinculação nativa: {0}.|
|XA5300|SDK do Android não encontrado ou não está totalmente instalado.|
|XA5301|Ferramenta ' strip ' ausente. Instale o componente ' ferramentas de linha de comando ' do Xcode.|
|XA5302|Ferramenta ' dsymutil ' ausente. Instale o componente ' ferramentas de linha de comando ' do Xcode.|
|XA5203|Falha ao gerar os símbolos de depuração (diretório dSYM). Examine o log de compilação.|
|XA5204|Falha ao remover o binário final. Examine o log de compilação.|
|XA5205|Ferramenta ' AAPT ' ausente. Instale o SDK do Android pacote de ferramentas de Build.|
|XA5206|{0} O diretório de recursos do Android {1} não existe.|
|XA5207|{0} O {1} do arquivo da biblioteca Java não existe.|
|XA5208|Falha no download. Baixe {0} e coloque-o no diretório {1}.|
|XA5209|Falha ao descompactar. Baixe {0} e extraia-o para o diretório {1}.|
|XA5210|{0} O arquivo de biblioteca nativa {1} não existe.|

### <a name="xa6xxx-errors"></a>Erros de XA6xxx

|Código do erro|Descrição|
|--- |--- |
|XA6001|A execução da versão do Cecil não dá suporte à remoção de assembly.|
|XA6002|Não foi possível remover o assembly '{0}'.|
|XA6003|Mensagem UnauthorizedAccessException.|

### <a name="xa9xxx-errors"></a>Erros de XA9xxx

Esses códigos de erro são erros de licenciamento e de ativação.

#### <a name="xa9000"></a>XA9000

 **Causa:** Licença expirada

 **Verificado durante:** Integrado

|Starter|Indie|Negócios (avaliação)|Negócio|Enterprise|
|--- |--- |--- |--- |--- |
|WARNING|WARNING|WARNING|WARNING|WARNING|

#### <a name="xa9001"></a>XA9001

 **Causa:** Avaliação expirada

 **Verificado durante:** Integrado

|Starter|Indie|Negócios (avaliação)|Negócio|Enterprise|
|--- |--- |--- |--- |--- |
|WARNING|WARNING|WARNING|WARNING|WARNING|

#### <a name="xa9002"></a>XA9002

 **Causa:** AndroidJavaSource

 **Verificado durante:** Integrado

|Starter|Indie|Negócios (avaliação)|Negócio|Enterprise|
|--- |--- |--- |--- |--- |
|ERROR|OK|OK|OK|OK|

 **Causa:** AndroidJavaLibrary

 **Verificado durante:** Integrado

|Starter|Indie|Negócios (avaliação)|Negócio|Enterprise|
|--- |--- |--- |--- |--- |
|ERROR|OK|OK|OK|OK|

 **Se um assembly de associação tiver o. jar incorporado, isso será detectado na hora do pacote, não no tempo de compilação.**

 **Causa:** AndroidNativeLibrary

 **Verificado durante:** Integrado

|Starter|Indie|Negócios (avaliação)|Negócio|Enterprise|
|--- |--- |--- |--- |--- |
|ERROR|OK|OK|OK|OK|

#### <a name="xa9003"></a>XA9003

 **Causa:** System. Runtime. Serialization

 **Verificado durante:** Agrupa

|Starter|Indie|Negócios (avaliação)|Negócio|Enterprise|
|--- |--- |--- |--- |--- |
|ERROR|ERROR|OK|OK|OK|

 **Causa:** System. ServiceModel. Web

 **Verificado durante:** Agrupa

|Starter|Indie|Negócios (avaliação)|Negócio|Enterprise|
|--- |--- |--- |--- |--- |
|ERROR|ERROR|OK|OK|OK|

 **Causa:** Mono. Data. TDS

 **Verificado durante:** Integrado

|Starter|Indie|Negócios (avaliação)|Negócio|Enterprise|
|--- |--- |--- |--- |--- |
|ERROR|ERROR|OK|OK|OK|

 **Isso é referenciado por System. Data. dll, que é permitido**

 **Causa:** Mono. Android. Export

 **Verificado durante:** Integrado

|Starter|Indie|Negócios (avaliação)|Negócio|Enterprise|
|--- |--- |--- |--- |--- |
|ERROR|OK|OK|OK|OK|

#### <a name="xa9004"></a>XA9004

 **Causa:** --criação de perfil

 **Verificado durante:** Agrupa

|Starter|Indie|Negócios (avaliação)|Negócio|Enterprise|
|--- |--- |--- |--- |--- |
|ERROR|ERROR|OK|OK|OK|

#### <a name="xa9005"></a>XA9005

 **Causa:** Limite de tamanho (32 KB).

 **Verificado durante:** Agrupa

|Starter|Indie|Negócios (avaliação)|Negócio|Enterprise|
|--- |--- |--- |--- |--- |
|ERROR|OK|-|-|-|

#### <a name="xa9006"></a>XA9006

 **Causa:** Namespace System. Data. SqlClient.

 **Verificado durante:** Agrupa

|Starter|Indie|Negócios (avaliação)|Negócio|Enterprise|
|--- |--- |--- |--- |--- |
|ERROR|ERROR|OK|OK|OK|

#### <a name="xa9008"></a>XA9008

 **Causa:** Criando a partir da linha de comando.

 **Verificado durante:** Integrado

|Starter|Indie|Negócios (avaliação)|Negócio|Enterprise|
|--- |--- |--- |--- |--- |
|ERROR|ERROR|OK|OK|OK|

#### <a name="xa9009"></a>XA9009

 **Causa:** Número de série ausente.

 **Verificado durante:** Códigos

|Starter|Indie|Negócios (avaliação)|Negócio|Enterprise|
|--- |--- |--- |--- |--- |
|ERROR|ERROR|ERROR|ERROR|ERROR|

#### <a name="xa9010"></a>XA9010

 **Causa:** ProductId inválido.

 **Verificado durante:** Integrado

|Starter|Indie|Negócios (avaliação)|Negócio|Enterprise|
|--- |--- |--- |--- |--- |
|ERROR|ERROR|ERROR|ERROR|ERROR|

Equivalente a XA9018.

#### <a name="xa9011"></a>XA9011

 **Causa:** Falha ao atualizar o arquivo de licença (para o novo formato de arquivo).

 **Verificado durante:** Ativá

|Starter|Indie|Negócios (avaliação)|Negócio|Enterprise|
|--- |--- |--- |--- |--- |
|ERROR|ERROR|ERROR|ERROR|ERROR|

#### <a name="xa9012"></a>XA9012

 **Causa:** Sem Internet

 **Verificado durante:** Ativá

|Starter|Indie|Negócios (avaliação)|Negócio|Enterprise|
|--- |--- |--- |--- |--- |
|ERROR|ERROR|ERROR|ERROR|ERROR|

#### <a name="xa9013"></a>XA9013

 **Causa:** Erro desconhecido

 **Verificado durante:** Ativá

|Starter|Indie|Negócios (avaliação)|Negócio|Enterprise|
|--- |--- |--- |--- |--- |
|ERROR|ERROR|ERROR|ERROR|ERROR|

#### <a name="xa9014"></a>XA9014

 **Causa:** Código de ativação inválido

 **Verificado durante:** Ativá

|Starter|Indie|Negócios (avaliação)|Negócio|Enterprise|
|--- |--- |--- |--- |--- |
|ERROR|ERROR|ERROR|ERROR|ERROR|

#### <a name="xa9017"></a>XA9017

 **Causa:** O servidor de ativação não retorna uma licença válida.

 **Verificado durante:** Ativá

|Starter|Indie|Negócios (avaliação)|Negócio|Enterprise|
|--- |--- |--- |--- |--- |
|ERROR|ERROR|ERROR|ERROR|ERROR|

#### <a name="xa9018"></a>XA9018

**Causa:** Licença inválida

**Verificado durante:** Integrado

|Starter|Indie|Negócios (avaliação)|Negócio|Enterprise|
|--- |--- |--- |--- |--- |
|-|-|ERROR|-|-|
