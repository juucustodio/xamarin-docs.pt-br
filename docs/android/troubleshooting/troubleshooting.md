---
title: Dicas de Solução de Problemas
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: 56137ACA-4811-B312-6860-E16D0FA123F7
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 03/15/2018
ms.openlocfilehash: 140307dbfe55d5445c329ea83eafedd467fe58fa
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/25/2018
ms.locfileid: "50109348"
---
# <a name="troubleshooting-tips"></a>Dicas de Solução de Problemas


## <a name="getting-diagnostic-information"></a>Obtendo informações de diagnóstico

Xamarin. Android tem alguns locais para procurar quando o rastreamento de vários bugs.
Elas incluem:

1.  Saída de diagnóstico MSBuild.
2.  Logs de implantação do dispositivo.
3.  Saída de Log de depuração do Android.


<a name="Diagnostic_MSBuild_Output" />

## <a name="diagnostic-msbuild-output"></a>Resultados de diagnóstico do MSBuild

Diagnóstico MSBuild pode conter informações adicionais relativas à compilação do pacote e pode conter algumas informações de implantação do pacote.

Para habilitar os resultados de diagnóstico do MSBuild no Visual Studio para Mac:

1.  Clique em **Ferramentas > Opções...**
2.  Na exibição de árvore à esquerda, selecione **projetos e soluções > compilar e executar**
3.  No painel direito, defina o menu suspenso de detalhamento MSBuild build saída para diagnóstico
4.  Clique em **OK**
5.  Limpe e recompile o seu pacote.
6.  Saída de diagnóstico está visível no painel de saída.


Para habilitar o diagnóstico saída do MSBuild no Visual Studio para Mac/OS x:

1.  Clique em **Visual Studio para Mac > Preferências...**
2.  Na exibição de árvore à esquerda, selecione **projetos > compilar**
3.  No painel direito, defina o detalhamento do Log lista suspensa para diagnóstico
4.  Clique em **OK**
5.  Reiniciar o Visual Studio para Mac
6.  Limpe e recompile o seu pacote.
7.  Saída de diagnóstico está visível no painel de erros (**exibição > painéis > erros** ), clicando no botão de saída do Build.




## <a name="device-deployment-logs"></a>Logs de implantação do dispositivo

Para habilitar o log de implantação do dispositivo dentro do Visual Studio:

1.  **Ferramentas > Opções...**>
2.  Na exibição de árvore à esquerda, selecione **Xamarin > configurações do Android**
3.  No painel direito, habilitar [X] **log de depuração de extensão (grava monodroid. log na sua área de trabalho)** caixa de seleção.
4.  Mensagens de log são gravadas no arquivo monodroid. log na sua área de trabalho.


O Visual Studio para Mac sempre grava logs de implantação do dispositivo. Localizar-los é um pouco mais difícil; uma *AndroidUtils* arquivo de log é criado para cada dia + a hora em que uma implantação ocorre, por exemplo: **AndroidTools-2012-10-24_12-35-45.log**.

-  No Windows, os arquivos de log são gravados em `%LOCALAPPDATA%\XamarinStudio-{VERSION}\Logs`.
-  Nos X, os arquivos de log são gravados em `$HOME/Library/Logs/XamarinStudio-{VERSION}`.




## <a name="android-debug-log-output"></a>Saída de Log de depuração do Android

Android irá escrever muitas mensagens para o [Log de depuração do Android](~/android/deploy-test/debugging/android-debug-log.md).
Xamarin. Android usa propriedades do sistema Android para controlar a geração de mensagens adicionais para o Log de depuração do Android. Propriedades do sistema Android podem ser definidas por meio de *setprop* comando o [Android Debug Bridge (adb)](http://developer.android.com/guide/developing/tools/adb.html):

```shell
adb shell setprop PROPERTY_NAME PROPERTY_VALUE
```

Propriedades do sistema são lidos durante a inicialização do processo e, portanto, devem ser configurado antes do aplicativo é iniciado ou o aplicativo deve ser reiniciado depois que as propriedades do sistema são alteradas.



### <a name="xamarinandroid-system-properties"></a>Propriedades do sistema Xamarin.Android

Xamarin. Android suporta as seguintes propriedades de sistema:

-   *Debug.mono.Debug*: se uma cadeia de caracteres não vazia, isso é equivalente a `*mono-debug*`.

-   *Debug.mono.env*: um separados por pipe ('*|*') lista de variáveis de ambiente para exportar durante a inicialização do aplicativo *antes de* mono foi inicializado. Isso permite definir variáveis de ambiente controle mono registro em log.

    - *Observação*: como o valor é '*|*'-separados, o valor deve ter um nível extra de delimitação, como o \` *shell do adb* \` comando removerá um conjunto de aspas.

    - *Observação*: valores de propriedade do sistema Android podem não ter mais de 92 caracteres de comprimento.

    - Exemplo:

            adb shell setprop debug.mono.env "'MONO_LOG_LEVEL=info|MONO_LOG_MASK=asm'"

-   *Debug.mono.log*: um separados por vírgula ('*,*') lista de componentes que deve imprimir mensagens adicionais para o Log de depuração do Android. Por padrão, nada é definido. Os componentes incluem:

    -   *todos os*: imprimir todas as mensagens
    -   *GC*: mensagens relacionadas a GC de impressão.
    -   *gref*: imprimir mensagens de alocação e desalocação de referência (fraca, global).
    -   *lref*: imprimir mensagens de alocação e desalocação de referência local.

    *Observação*: essas são *extremamente* detalhado. Não habilite a menos que você realmente precisa.

-   *Debug.mono.Trace*: permite configurar o [mono – rastreamento](http://docs.go-mono.com/?link=man%3amono(1)) `=PROPERTY_VALUE` configuração.



## <a name="xamarinandroid-cannot-resolve-systemvaluetuple"></a>Xamarin. Android não é possível resolver a System. valuetuple

Esse erro ocorre devido a uma incompatibilidade com o Visual Studio.

- **Visual Studio 2017 atualização 1** (versão 15.1 ou anterior) só é compatível com o **valuetuple NuGet 4.3.0** (ou anterior).

- **Visual Studio 2017 atualização 2** (versão 15.2 ou mais recente) só é compatível com o **valuetuple NuGet 4.3.1** (ou mais recente).

Escolha o NuGet de System. valuetuple correta que corresponde à sua instalação do Visual Studio 2017.


## <a name="gc-messages"></a>Mensagens de GC

Mensagens de componente de GC podem ser exibidas, definindo a propriedade do sistema debug.mono.log como um valor que contém o gc.

Mensagens de GC são geradas sempre que o GC é executado e fornece informações sobre a quantidade trabalhar o GC foi:

```shell
I/monodroid-gc(12331): GC cleanup summary: 81 objects tested - resurrecting 21.
```

Informações adicionais de GC, como informações de tempo podem ser geradas definindo a `MONO_LOG_LEVEL` variável de ambiente para `debug`:

```shell
adb shell setprop debug.mono.env MONO_LOG_LEVEL=debug
```

Isso resultará em adicionais mensagens Mono (muitos), incluindo esses três da consequência:

```shell
D/Mono (15723): GC_BRIDGE num-objects 1 num_hash_entries 81226 sccs size 81223 init 0.00ms df1 285.36ms sort 38.56ms dfs2 50.04ms setup-cb 9.95ms free-data 106.54ms user-cb 20.12ms clenanup 0.05ms links 5523436/5523436/5523096/1 dfs passes 1104 6883/11046605
D/Mono (15723): GC_MINOR: (Nursery full) pause 2.01ms, total 287.45ms, bridge 225.60 promoted 0K major 325184K los 1816K
D/Mono ( 2073): GC_MAJOR: (user request) pause 2.17ms, total 2.47ms, bridge 28.77 major 576K/576K los 0K/16K
```

No `GC_BRIDGE` mensagem `num-objects` é o número de objetos da ponte está considerando essa passagem, e `num_hash_entries` é o número de objetos processados durante essa invocação do código de ponte.

No `GC_MINOR` e `GC_MAJOR` mensagens, `total` é a quantidade de tempo enquanto o mundo está em pausa (não há threads estão executando), enquanto `bridge` é a quantidade de tempo gasto na ponte de processamento de código (que lida com a máquina virtual Java). O mundo está *não* pausado enquanto ocorre o processamento da ponte.

 *Em geral*, quanto maior o valor de `num_hash_entries`, o mais tempo que o `bridge` levará coleções e maior o `total` será o tempo gasto na coleta.



## <a name="global-reference-messages"></a>Mensagens de referência global

Para habilitar a referência Global loggig (GREF) registro em log, o *debug.mono.log* propriedade do sistema deve conter *gref*, por exemplo:

```shell
adb shell setprop debug.mono.log gref
```

Xamarin. Android usa referências do Android globais para fornecer os mapeamentos entre as instâncias de Java e as instâncias gerenciadas associadas, como ao invocar o método Java que uma instância do Java deve ser fornecido ao Java.

Infelizmente, emuladores de Android somente permitem referências de globais de 2000 a existir de cada vez. Hardware tem um limite mais alto de referências de globais 52000. O limite inferior pode ser problemático quando a execução de aplicativos no emulador, portanto, saber *onde* provém a instância do pode ser muito útil.

 *Observação*: a contagem de referência global é interna ao xamarin. Android e não considera (e não pode) incluem referências de globais retiradas por outras bibliotecas nativas carregadas no processo. Use a contagem de referência global como uma estimativa.

```shell
I/monodroid-gref(12405): +g+ grefc 108 gwrefc 0 obj-handle 0x40517468/L -> new-handle 0x40517468/L from    at Java.Lang.Object.RegisterInstance(IJavaObject instance, IntPtr value, JniHandleOwnership transfer)
I/monodroid-gref(12405):    at Java.Lang.Object.SetHandle(IntPtr value, JniHandleOwnership transfer)
I/monodroid-gref(12405):    at Java.Lang.Object..ctor(IntPtr handle, JniHandleOwnership transfer)
I/monodroid-gref(12405):    at Java.Lang.Thread+RunnableImplementor..ctor(System.Action handler, Boolean removable)
I/monodroid-gref(12405):    at Java.Lang.Thread+RunnableImplementor..ctor(System.Action handler)
I/monodroid-gref(12405):    at Android.App.Activity.RunOnUiThread(System.Action action)
I/monodroid-gref(12405):    at Mono.Samples.Hello.HelloActivity.UseLotsOfMemory(Android.Widget.TextView textview)
I/monodroid-gref(12405):    at Mono.Samples.Hello.HelloActivity.<OnCreate>m__3(System.Object o)
I/monodroid-gref(12405): handle 0x40517468; key_handle 0x40517468: Java Type: `mono/java/lang/RunnableImplementor`; MCW type: `Java.Lang.Thread+RunnableImplementor`
I/monodroid-gref(12405): Disposing handle 0x40517468
I/monodroid-gref(12405): -g- grefc 107 gwrefc 0 handle 0x40517468/L from    at Java.Lang.Object.Dispose(System.Object instance, IntPtr handle, IntPtr key_handle, JObjectRefType handle_type)
I/monodroid-gref(12405):    at Java.Lang.Object.Dispose()
I/monodroid-gref(12405):    at Java.Lang.Thread+RunnableImplementor.Run()
I/monodroid-gref(12405):    at Java.Lang.IRunnableInvoker.n_Run(IntPtr jnienv, IntPtr native__this)
I/monodroid-gref(12405):    at System.Object.c200fe6f-ac33-441b-a3a0-47659e3f6750(IntPtr , IntPtr )
I/monodroid-gref(27679): +w+ grefc 1916 gwrefc 296 obj-handle 0x406b2b98/G -> new-handle 0xde68f4bf/W from take_weak_global_ref_jni
I/monodroid-gref(27679): -w- grefc 1915 gwrefc 294 handle 0xde691aaf/W from take_global_ref_jni
```

Há quatro mensagens da consequência:

-  Criação de referência global: essas são as linhas que começam com *+ g +* e fornecerá um rastreamento de pilha para o caminho do código de criação.
-  Destruição de referência global: essas são as linhas que começam com *- g-* e pode fornecer um rastreamento de pilha para o caminho do código descarte da referência global. Se o GC está descartando o gref, nenhum rastreamento de pilha será fornecido.
-  Criação de referência fraca global: essas são as linhas que começam com *+ w +* .
-  Destruição de referência fraca global: essas são as linhas que começam com *-w-* .


Em todas as mensagens, o *grefc* valor é a contagem de referências de globais que criou o xamarin. Android, embora o *grefwc* valor é a contagem de referências fracas e globais que criou o xamarin. Android. O *manipular* ou *identificador obj* valor é o valor do identificador JNI e o caractere após o ' */*' é o tipo de valor do identificador: */L* para obter uma referência local, */G* para referências de globais, e */W* para referências fracas e globais.

Como parte do processo de GC, referências de globais (+ + g) são convertidas em referências fracas e globais (fazendo com que a propriedades + w + e - g-), um GC do lado do Java é iniciado e, em seguida, a referência fraca de global é verificada para ver se ele foi coletado. Se ele ainda estiver ativo, um novo gref é criado em torno de ref fraca (+ g +, -w-), caso contrário, a referência fraca é destruída (-w).

## <a name="java-instance-is-created-and-wrapped-by-a-mcw"></a>Instância de Java é criada e encapsulada por uma MCW

```shell
I/monodroid-gref(27679): +g+ grefc 2211 gwrefc 0 obj-handle 0x4066df10/L -> new-handle 0x4066df10/L from ...
I/monodroid-gref(27679): handle 0x4066df10; key_handle 0x4066df10: Java Type: `android/graphics/drawable/TransitionDrawable`; MCW type: `Android.Graphics.Drawables.TransitionDrawable`
```

## <a name="a-gc-is-being-performed"></a>Um GC está sendo executado...

```shell
I/monodroid-gref(27679): +w+ grefc 1953 gwrefc 259 obj-handle 0x4066df10/G -> new-handle 0xde68f95f/W from take_weak_global_ref_jni
I/monodroid-gref(27679): -g- grefc 1952 gwrefc 259 handle 0x4066df10/G from take_weak_global_ref_jni
```

## <a name="object-is-still-alive-as-handle--null"></a>Objeto ainda está ativo, como identificador! = null
## <a name="wref-turned-back-into-a-gref"></a>wref reativados novamente em um gref

```shell
I/monodroid-gref(27679): *try_take_global obj=0x4976f080 -> wref=0xde68f95f handle=0x4066df10
I/monodroid-gref(27679): +g+ grefc 1930 gwrefc 39 obj-handle 0xde68f95f/W -> new-handle 0x4066df10/G from take_global_ref_jni
I/monodroid-gref(27679): -w- grefc 1930 gwrefc 38 handle 0xde68f95f/W from take_global_ref_jni
```

## <a name="object-is-dead-as-handle--null"></a>Objeto está inativo, como o identificador de = = null
## <a name="wref-is-freed-no-new-gref-created"></a>wref é liberada, nenhum novo gref criado

```shell
I/monodroid-gref(27679): *try_take_global obj=0x4976f080 -> wref=0xde68f95f handle=0x0
I/monodroid-gref(27679): -w- grefc 1914 gwrefc 296 handle 0xde68f95f/W from take_global_ref_jni
```

Há um probleminha "interessante": em destinos que executam o Android anteriores à 4.0, o valor de gref é igual ao endereço do objeto na memória do Android do tempo de execução. (Ou seja, o GC é não-mover, conservador, coletor e ele é enviar referências diretas a esses objetos.) Assim, após um + g +, + w +, - g-, + g +, - w-sequência, a gref resultante terá o mesmo valor que o valor de gref original. Isso torna bastante simples grepping por meio de logs.

Android 4.0, no entanto, tem um coletor de movimentação e não é mais prático referências diretas ao tempo de execução do Android objetos VM. Consequentemente, após um + g +, + w +, - g-, + g +, - w-sequência, o valor de gref *serão diferentes*. Se o objeto sobrevive a vários GCs, ele vai por vários valores de gref, tornando mais difícil determinar onde uma instância, na verdade, foi alocada do.

### <a name="querying-programmatically"></a>Consulta de forma programática

Você pode consultar as contagens de GREF e WREF consultando o `JniRuntime` objeto.

`Java.Interop.JniRuntime.CurrentRuntime.GlobalReferenceCount` -Contagem de referência global

`Java.Interop.JniRuntime.CurrentRuntime.WeakGlobalReferenceCount` -Contagem de referência fraca



## <a name="offline-activation"></a>Ativação offline

Se você não é possível ativar o xamarin. Android no Windows ou não é possível instalar a versão completa do xamarin. Android no Mac OS X, confira a [ativação Offline](~/android/get-started/installation/index.md) página.



## <a name="cant-upgrade-to-indiebusiness-from-trial-account"></a>Não é possível atualizar para o negócio/Indie da conta de avaliação

Se você adquiriu recentemente o xamarin. Android e iniciados anteriormente uma versão de avaliação do xamarin. Android, você precisará concluir as seguintes etapas para obter essa alteração de licença selecionada pelo Visual Studio para Mac ou Visual Studio.

-  Feche o Visual Studio para Mac/Visual Studio
-  Remover todos os arquivos de ~/Library/MonoAndroid no Mac ou %PROGRAMDATA%\Mono para Android\License\ para Windows
-  Abra novamente o Visual Studio para Mac/Visual Studio e compilar um projeto xamarin. Android


Isso deve ajudá-lo em execução. Se você continuar a ter problemas, você talvez queira experimentar uma [ativação Offline](~/android/get-started/installation/index.md) para concluir a ativação de sua estação de trabalho.



## <a name="receiving-activation-incomplete-error-message"></a>Recebendo ' mensagem de erro incompleta de ativação

Esse problema pode ocorrer ao usar o xamarin. Android para Visual Studio. Para resolver esse problema, envie os logs do seguinte local para *contact@xamarin.com*.

-  Local do log: **% LocalAppData %\\Xamarin\\Logs**




## <a name="receiving-error-retrieving-update-information-error-message"></a>Recebendo a mensagem de erro 'Erro ao recuperar informações de atualização'

De tempos em tempos, uma atualização falhará com este erro a seguir, que normalmente ocorre durante a verificação de atualizações:

A maioria das vezes, esse erro pode ser resolvido simplesmente ao registro em log fora de sua conta do Xamarin e, em seguida, registro em log novamente.

Para fazer isso, localize sua plataforma preferida abaixo e siga as etapas:

**No Mac:**
1. Abra o Visual Studio para Mac
2. Selecione o Visual Studio para Mac > conta...
3. Clique em fazer logoff
4. Clique em Log no
5. Insira suas credenciais
6. Verifique se há atualizações

**No PC usando o Visual Studio:**
1. Abrir o Visual Studio
2. Selecione Ferramentas > conta do Xamarin
3. Clique em fazer logoff
4. Clique em Log no
5. Insira suas credenciais
6. Verifique se há atualizações

Se essa mensagem de erro continuará aparecendo, envie um e-mail **contact@xamarin.com**.




## <a name="android-debug-logs"></a>Logs de depuração do Android

O [Logs de depuração do Android](~/android/deploy-test/debugging/android-debug-log.md) podem fornecer contexto adicional sobre os erros de tempo de execução que você está vendo.



## <a name="floating-point-performance-is-terrible"></a>Desempenho de ponto flutuante é terrível!

Como alternativa, "meu aplicativo é executado 10 vezes mais rápido com o build de depuração que com a compilação de lançamento!"

Xamarin. Android dá suporte a vários dispositivos ABIs: *armeabi*, *armeabi-v7a*, e *x86*. ABIs de dispositivo podem ser especificados dentro **propriedades do projeto > guia Application > arquiteturas com suporte**.

Compilações de depuração usam um pacote Android que fornece todas as ABIs e, portanto, usará a ABI mais rápida para o dispositivo de destino.

Builds de versão incluirá apenas as ABIs selecionados na guia Propriedades do projeto. Mais de um pode ser selecionado.

*armeabi* é o padrão ABI, e tem o mais amplo suporte de dispositivo. *No entanto*, armeabi não dá suporte a dispositivos com várias CPUs e hardware de ponto flutuante, amont outras coisas. Consequentemente, os aplicativos que usam o tempo de execução de versão do armeabi serão vinculados a um único núcleo e usará uma implementação de flutuação suave. Ambos podem contribuir para um desempenho significativamente mais lento para seu aplicativo.

Se seu aplicativo requer um desempenho razoável de ponto flutuante (por exemplo, jogos), você deve habilitar o *armeabi-v7a* ABI. Talvez você queira só há suporte para o *armeabi-v7a* tempo de execução, embora isso significa que dispositivos mais antigos que só há suporte para *armeabi* não será possível executar seu aplicativo.



## <a name="could-not-locate-android-sdk"></a>Não foi possível localizar o SDK do Android

Há 2 downloads disponíveis do Google para o SDK do Android para Windows.
Se você escolher o instalador .exe, ele gravará chaves do registro que dizem ao xamarin. Android em que ele foi instalado. Se você escolher o arquivo. zip e descompacte-o por conta própria, o xamarin. Android não sabe onde procurar o SDK. Você pode dizer que o xamarin. Android em que o SDK está no Visual Studio acessando **Ferramentas > Opções > Xamarin > configurações do Android**:

[![Local do SDK do Android nas configurações do Xamarin Android](troubleshooting-images/01.png)](troubleshooting-images/01.png#lightbox)



## <a name="ide-does-not-display-target-device"></a>IDE não mostrará o dispositivo de destino

Às vezes, você tenta implantar seu aplicativo em um dispositivo, mas o dispositivo que você deseja implantar a não é mostrado na caixa de diálogo Selecionar dispositivo. Isso pode acontecer quando o Android Debug Bridge decide sair de férias.

Para diagnosticar esse problema, localize o [adb programa](~/android/deploy-test/debugging/android-debug-log.md), em seguida, execute:

```shell
adb devices
```

Se seu dispositivo não estiver presente, você precisará reiniciar o servidor de Android Debug Bridge para que seu dispositivo pode ser encontrado:

```shell
adb kill-server
adb start-server
```

Pode impedir que o software de sincronização do HTC **start-servidor adb** funcione corretamente. Se o **start-servidor adb** comando não imprime qual porta está sendo iniciado, o software de sincronização de HTC de saída e tente reiniciar o servidor adb.


## <a name="the-specified-task-executable-keytool-could-not-be-run"></a>Não foi possível executar o executável da tarefa especificada "keytool"

Isso significa que o caminho não contém o diretório onde o diretório bin de Java do SDK está localizado. Verifique se você seguiu as etapas da [instalação](~/android/get-started/installation/index.md) guia.


## <a name="monodroidexe-or-aresgenexe-exited-with-code-1"></a>monodroid.exe ou aresgen.exe foi encerrado com o código 1

Para ajudá-lo a depurar esse problema, vá para o Visual Studio e alterar o nível de detalhamento do MSBuild, para fazer isso, selecione: **Ferramentas > Opções > projeto** e **soluções > compilar** e **Executar > Compilar detalhamento da saída de projeto MSBuild** e defina esse valor como **Normal**.

Recompile e verifique o painel de saída do Visual Studio, que deve conter o erro completo.

## <a name="there-is-not-enough-storage-space-on-the-device-to-deploy-the-package"></a>Não há espaço de armazenamento suficiente no dispositivo para implantar o pacote

Isso ocorre quando você não inicia o emulador do Visual Studio. Ao iniciar o emulador de fora do Visual Studio, você precisará passar o `-partition-size 512` opções, por exemplo,

```shell
emulator -partition-size 512 -avd MonoDroid
```

Verifique se você usar o nome correto do simulador, ou seja, [o nome usado ao configurar o simulador](~/android/get-started/installation/windows.md#device).


## <a name="installfailedinvalidapk-when-installing-a-package"></a>Instale\_FAILED\_inválido\_ao instalar um pacote APK

Nomes de pacote do Android *devem* contêm um ponto ('*.*'). Edite o nome do pacote para que ele contém um ponto.

-   Dentro do Visual Studio:
    -   Clique com botão direito no seu projeto > Propriedades
    -   Clique na guia manifesto do Android à esquerda.
    -   Atualize o campo de nome do pacote.
        -   Se você vir a mensagem &ldquo;Androidmanifest não encontrado. Clique para adicionar um. &rdquo;, clique no link e, em seguida, atualize o campo de nome do pacote.
-   Dentro do Visual Studio para Mac:
    -   Clique com botão direito no seu projeto > Opções.
    -   Navegue até a compilação / seção aplicativo Android.
    -   Altere o campo de nome de pacote para conter um '.'.




## <a name="installfailedmissingsharedlibrary-when-installing-a-package"></a>Instale\_FAILED\_ausente\_compartilhado\_ao instalar um pacote de biblioteca

É uma "biblioteca compartilhada" neste contexto *não* uma biblioteca compartilhada nativa (*libfoo.so*) de arquivos; em vez disso, é uma biblioteca que deve ser instalada separadamente no dispositivo de destino, como o Google Maps.

O pacote Android Especifica quais bibliotecas compartilhadas são necessárias com o `<uses-library/>` elemento. Se um *necessária* biblioteca não está presente no dispositivo de destino (por exemplo, `//uses-library/@android:required` é *true*, que é o padrão), em seguida, a instalação do pacote falhará com *instalar\_ Falha na\_ausente\_SHARED\_biblioteca*.

Para determinar quais bibliotecas compartilhadas são necessárias, veja a *gerado*
**androidmanifest. XML** arquivo (por exemplo, **obj\\Debug\\android \\Androidmanifest. XML**) e procure o `<uses-library/>` elementos. `<uses-library/>` elementos podem ser adicionados manualmente no seu projeto **propriedades\\androidmanifest. XML** arquivo e por meio de [UsesLibraryAttribute de atributo personalizado](https://developer.xamarin.com/api/type/Android.App.UsesLibraryAttribute/).

Por exemplo, adicionando uma referência de assembly para *Mono.Android.GoogleMaps.dll* adicionará implicitamente um `<uses-library/>` para a biblioteca compartilhada do Google Maps.



## <a name="installfailedupdateincompatible-when-installing-a-package"></a>Instale\_FAILED\_atualização\_INCOMPATÍVEL ao instalar um pacote

Pacotes Android têm três requisitos:

-   Eles devem conter um '.' (consulte a entrada anterior)
-   Eles devem ter um nome de pacote de cadeia de caracteres exclusiva (portanto, a convenção de ordem inversa tld Vista em nomes de aplicativo do Android, por exemplo, com.android.chrome para o aplicativo Chrome)
-   Ao atualizar pacotes, o pacote deve ter a mesma chave de assinatura.

Portanto, considere este cenário:

1.  Compilar e implantar seu aplicativo como um aplicativo de depuração
2.  Altere a chave de assinatura, por exemplo, para usar como um aplicativo de versão (ou porque você não gosta do padrão fornecido pelo depurar chave de assinatura)
3.  Instalar o aplicativo sem removê-lo pela primeira vez, por exemplo, Depurar > Iniciar sem depuração no Visual Studio


Quando isso acontece, a instalação do pacote falhará com uma instalação\_FAILED\_atualização\_erro INCOMPATÍVEL, porque o nome do pacote não foi alterado durante o processo de assinatura chave fez. O [Log de depuração do Android](~/android/deploy-test/debugging/android-debug-log.md) também conterá uma mensagem semelhante a:

```shell
E/PackageManager(  146): Package [PackageName] signatures do not match the previously installed version; ignoring!
```

Para corrigir esse erro, remova completamente o aplicativo do dispositivo antes de instalar novamente.


## <a name="installfaileduidchanged-when-installing-a-package"></a>Instale\_FAILED\_UID\_ALTERADA ao instalar um pacote

Quando um pacote Android é instalado, ele é atribuído um *id de usuário* (UID).
*Às vezes*, por motivos desconhecidos no momento, durante a instalação em um aplicativo já instalado, a instalação falhará com `INSTALL_FAILED_UID_CHANGED`:

```shell
ERROR [2015-03-23 11:19:01Z]: ANDROID: Deployment failed
Mono.AndroidTools.InstallFailedException: Failure [INSTALL_FAILED_UID_CHANGED]
   at Mono.AndroidTools.Internal.AdbOutputParsing.CheckInstallSuccess(String output, String packageName)
   at Mono.AndroidTools.AndroidDevice.<>c__DisplayClass2c.<InstallPackage>b__2b(Task`1 t)
   at System.Threading.Tasks.ContinuationTaskFromResultTask`1.InnerInvoke()
   at System.Threading.Tasks.Task.Execute()
```

Para contornar esse problema *desinstalar totalmente* o pacote Android, instalando o aplicativo de GUI do destino do Android, ou usando `adb`:

```shell
$ adb uninstall @PACKAGE_NAME@
```

**NÃO USE** `adb uninstall -k`, assim como isso *preservar* dados de aplicativo e, portanto, preservar o UID conflitante no dispositivo de destino.



## <a name="release-apps-fail-to-launch-on-device"></a>Aplicativos de versão não iniciado no dispositivo

A saída de Log de depuração do Android conterá uma mensagem semelhante a:

```shell
D/AndroidRuntime( 1710): Shutting down VM
W/dalvikvm( 1710): threadid=1: thread exiting with uncaught exception (group=0xb412f180)
E/AndroidRuntime( 1710): FATAL EXCEPTION: main
E/AndroidRuntime( 1710): java.lang.UnsatisfiedLinkError: Couldn't load monodroid: findLibrary returned null
E/AndroidRuntime( 1710):        at java.lang.Runtime.loadLibrary(Runtime.java:365)
```

Nesse caso, há duas causas possíveis:

1.  O. Apk não fornece uma ABI com suporte pelo dispositivo de destino.
    Por exemplo, o. Apk contém apenas os binários do armeabi-v7a, e o dispositivo de destino dá suporte apenas a armeabi.

2.  Uma [bug Android](http://code.google.com/p/android/issues/detail?id=21670). Se esse for o caso, desinstale o aplicativo, cruzar os dedos e reinstalar o aplicativo.

Para corrigir (1), edite as opções/propriedades do projeto e [adicionar suporte para a ABI necessário à lista de ABIs com suporte](~/android/app-fundamentals/cpu-architectures.md). Para determinar qual ABI que você precisa adicionar, execute o seguinte comando do adb em relação a seu dispositivo de destino:

```shell
adb shell getprop ro.product.cpu.abi
adb shell getprop ro.product.cpu.abi2
```

A saída conterá o primário (e opcional secundário) ABIs.

```shell
$ adb shell getprop | grep ro.product.cpu
[ro.product.cpu.abi2]: [armeabi]
[ro.product.cpu.abi]: [armeabi-v7a]
```

## <a name="the-outpath-property-is-not-set-for-project-ldquomyappcsprojrdquo"></a>A propriedade OutPath não está definida para o projeto &ldquo;MyApp. csproj&rdquo;

Isso geralmente significa que você tenha um computador HP e a variável de ambiente &ldquo;plataforma&rdquo; foi definido para algo como MCD ou HPD. Isso está em conflito com a propriedade da plataforma do MSBuild que geralmente é definida como &ldquo;qualquer CPU&rdquo; ou &ldquo;x86&rdquo;. Você precisará remover essa variável de ambiente do seu computador antes que o MSBuild possa funcionar:

-   Painel de controle > sistema > Avançado > variáveis de ambiente

Reinicie o Visual Studio ou o Visual Studio para Mac e tente recompilar. As coisas agora devem funcionar conforme o esperado.

## <a name="javalangclasscastexception-monoandroidruntimejavaobject-cannot-be-cast-to"></a>java.lang.ClassCastException: mono.android.runtime.JavaObject não pode ser convertido em...

Xamarin. Android 4.x não corretamente realizar marshaling de tipos genéricos aninhados corretamente. Por exemplo, considere o seguinte C\# o código usando [SimpleExpandableListAdapter](https://developer.xamarin.com/api/type/Android.Widget.SimpleExpandableListAdapter/):


```csharp
// BAD CODE; DO NOT USE
var groupData = new List<IDictionary<string, object>> () {
        new Dictionary<string, object> {
                { "NAME", "Group 1" },
                { "IS_EVEN", "This group is odd" },
        },
};
var childData = new List<IList<IDictionary<string, object>>> () {
        new List<IDictionary<string, object>> {
                new Dictionary<string, object> {
                        { "NAME", "Child 1" },
                        { "IS_EVEN", "This group is odd" },
                },
        },
};
mAdapter = new SimpleExpandableListAdapter (
        this,
        groupData,
        Android.Resource.Layout.SimpleExpandableListItem1,
        new string[] { "NAME", "IS_EVEN" },
        new int[] { Android.Resource.Id.Text1, Android.Resource.Id.Text2 },
        childData,
        Android.Resource.Layout.SimpleExpandableListItem2,
        new string[] { "NAME", "IS_EVEN" },
        new int[] { Android.Resource.Id.Text1, Android.Resource.Id.Text2 }
);
```


O problema é que xamarin. Android incorretamente realiza marshaling de tipos genéricos aninhados. O `List<IDictionary<string, object>>` marshaling está sendo realizado para um [java.lang.ArrrayList](https://developer.xamarin.com/api/type/Java.Util.ArrayList/), mas o `ArrayList` é que contém `mono.android.runtime.JavaObject` instâncias (qual fazer referência a `Dictionary<string, object>` instâncias) em vez de algo que implementa [java.util.Map](https://developer.xamarin.com/api/type/Java.Util.IMap/), resultando no seguinte exceção:

```shell
E/AndroidRuntime( 2991): FATAL EXCEPTION: main
E/AndroidRuntime( 2991): java.lang.ClassCastException: mono.android.runtime.JavaObject cannot be cast to java.util.Map
E/AndroidRuntime( 2991):        at android.widget.SimpleExpandableListAdapter.getGroupView(SimpleExpandableListAdapter.java:278)
E/AndroidRuntime( 2991):        at android.widget.ExpandableListConnector.getView(ExpandableListConnector.java:446)
E/AndroidRuntime( 2991):        at android.widget.AbsListView.obtainView(AbsListView.java:2271)
E/AndroidRuntime( 2991):        at android.widget.ListView.makeAndAddView(ListView.java:1769)
E/AndroidRuntime( 2991):        at android.widget.ListView.fillDown(ListView.java:672)
E/AndroidRuntime( 2991):        at android.widget.ListView.fillFromTop(ListView.java:733)
E/AndroidRuntime( 2991):        at android.widget.ListView.layoutChildren(ListView.java:1622)
```

A solução alternativa é usar o fornecido [tipos de coleção de Java](~/android/internals/api-design.md) em vez da `System.Collections.Generic` tipos para o &ldquo;interna&rdquo; tipos. Isso resultará em tipos de Java apropriados quando as instâncias de marshaling. (O código a seguir é mais complicado do que o necessário para reduzir os tempos de vida de gref. Ele pode ser simplificado para alterar o código original por meio `s/List/JavaList/g` e `s/Dictionary/JavaDictionary/g` se tempos de vida de gref não forem uma preocupação.)

```csharp
// insert good code here
using (var groupData = new JavaList<IDictionary<string, object>> ()) {
    using (var groupEntry = new JavaDictionary<string, object> ()) {
        groupEntry.Add ("NAME", "Group 1");
        groupEntry.Add ("IS_EVEN", "This group is odd");
        groupData.Add (groupEntry);
    }
    using (var childData = new JavaList<IList<IDictionary<string, object>>> ()) {
        using (var childEntry = new JavaList<IDictionary<string, object>> ())
        using (var childEntryDict = new JavaDictionary<string, object> ()) {
            childEntryDict.Add ("NAME", "Child 1");
            childEntryDict.Add ("IS_EVEN", "This child is odd.");
            childEntry.Add (childEntryDict);
            childData.Add (childEntry);
        }
        mAdapter = new SimpleExpandableListAdapter (
            this,
            groupData,
            Android.Resource.Layout.SimpleExpandableListItem1,
            new string[] { "NAME", "IS_EVEN" },
            new int[] { Android.Resource.Id.Text1, Android.Resource.Id.Text2 },
            childData,
            Android.Resource.Layout.SimpleExpandableListItem2,
            new string[] { "NAME", "IS_EVEN" },
            new int[] { Android.Resource.Id.Text1, Android.Resource.Id.Text2 }
        );
    }
}
```

[Isso será corrigido em uma versão futura](https://bugzilla.xamarin.com/show_bug.cgi?id=5401).


## <a name="unexpected-nullreferenceexceptions"></a>NullReferenceExceptions inesperados

Ocasionalmente, o [Log de depuração do Android](~/android/deploy-test/debugging/android-debug-log.md) mencionará NullReferenceExceptions que &ldquo;não pode ocorrer,&rdquo; ou provenientes de Mono para Android em tempo de execução código logo antes de se tornar inativa do aplicativo:

```shell
E/mono(15202): Unhandled Exception: System.NullReferenceException: Object reference not set to an instance of an object
E/mono(15202):   at Java.Lang.Object.GetObject (IntPtr handle, System.Type type, Boolean owned)
E/mono(15202):   at Java.Lang.Object._GetObject[IOnTouchListener] (IntPtr handle, Boolean owned)
E/mono(15202):   at Java.Lang.Object.GetObject[IOnTouchListener] (IntPtr handle, Boolean owned)
E/mono(15202):   at Android.Views.View+IOnTouchListenerAdapter.n_OnTouch_Landroid_view_View_Landroid_view_MotionEvent_(IntPtr jnienv, IntPtr native__this, IntPtr native_v, IntPtr native_e)
E/mono(15202):   at (wrapper dynamic-method) object:b039cbb0-15e9-4f47-87ce-442060701362 (intptr,intptr,intptr,intptr)
```

ou

```shell
E/mono    ( 4176): Unhandled Exception:
E/mono    ( 4176): System.NullReferenceException: Object reference not set to an instance of an object
E/mono    ( 4176): at Android.Runtime.JNIEnv.NewString (string)
E/mono    ( 4176): at Android.Util.Log.Info (string,string)
```

Isso pode acontecer quando o tempo de execução do Android opta por anular o processo, que pode ocorrer por vários motivos, incluindo atingindo o limite GREF do destino ou fazendo algo &ldquo;errado&rdquo; com JNI.

Para ver se esse for o caso, verifique o Log de depuração Android para uma mensagem a partir de seu processo semelhante a:

```shell
E/dalvikvm(  123): VM aborting
```


## <a name="abort-due-to-global-reference-exhaustion"></a>Anular devido ao esgotamento de referência Global

Camada JNI do Android do tempo de execução só dá suporte a um número limitado de referências de objeto JNI para ser válido em qualquer ponto no tempo. Quando esse limite for excedido, quebra de coisas.

O GREF (*referência global*) limite de 2000 referências no emulador e ~ 52000 referencia no hardware.

Você sabe que você comece a criar muitos GREFs quando você vê mensagens como esse no Log de depuração do Android:

```shell
D/dalvikvm(  602): GREF has increased to 1801
```

Quando você atingir o limite GREF, é impressa uma mensagem, como o seguinte:

```shell
D/dalvikvm(  602): GREF has increased to 2001
W/dalvikvm(  602): Last 10 entries in JNI global reference table:
W/dalvikvm(  602):  1991: 0x4057eff8 cls=Landroid/graphics/Point; (20 bytes)
W/dalvikvm(  602):  1992: 0x4057f010 cls=Landroid/graphics/Point; (28 bytes)
W/dalvikvm(  602):  1993: 0x40698e70 cls=Landroid/graphics/Point; (20 bytes)
W/dalvikvm(  602):  1994: 0x40698e88 cls=Landroid/graphics/Point; (20 bytes)
W/dalvikvm(  602):  1995: 0x40698ea0 cls=Landroid/graphics/Point; (28 bytes)
W/dalvikvm(  602):  1996: 0x406981f0 cls=Landroid/graphics/Point; (20 bytes)
W/dalvikvm(  602):  1997: 0x40698208 cls=Landroid/graphics/Point; (20 bytes)
W/dalvikvm(  602):  1998: 0x40698220 cls=Landroid/graphics/Point; (28 bytes)
W/dalvikvm(  602):  1999: 0x406956a8 cls=Landroid/graphics/Point; (20 bytes)
W/dalvikvm(  602):  2000: 0x406956c0 cls=Landroid/graphics/Point; (20 bytes)
W/dalvikvm(  602): JNI global reference table summary (2001 entries):
W/dalvikvm(  602):    51 of Ljava/lang/Class; 164B (41 unique)
W/dalvikvm(  602):    46 of Ljava/lang/Class; 188B (17 unique)
W/dalvikvm(  602):     6 of Ljava/lang/Class; 212B (6 unique)
W/dalvikvm(  602):    11 of Ljava/lang/Class; 236B (7 unique)
W/dalvikvm(  602):     3 of Ljava/lang/Class; 260B (3 unique)
W/dalvikvm(  602):     4 of Ljava/lang/Class; 284B (2 unique)
W/dalvikvm(  602):     8 of Ljava/lang/Class; 308B (6 unique)
W/dalvikvm(  602):     1 of Ljava/lang/Class; 316B
W/dalvikvm(  602):     4 of Ljava/lang/Class; 332B (3 unique)
W/dalvikvm(  602):     1 of Ljava/lang/Class; 356B
W/dalvikvm(  602):     2 of Ljava/lang/Class; 380B (1 unique)
W/dalvikvm(  602):     1 of Ljava/lang/Class; 428B
W/dalvikvm(  602):     1 of Ljava/lang/Class; 452B
W/dalvikvm(  602):     1 of Ljava/lang/Class; 476B
W/dalvikvm(  602):     2 of Ljava/lang/Class; 500B (1 unique)
W/dalvikvm(  602):     1 of Ljava/lang/Class; 548B
W/dalvikvm(  602):     1 of Ljava/lang/Class; 572B
W/dalvikvm(  602):     2 of Ljava/lang/Class; 596B (2 unique)
W/dalvikvm(  602):     1 of Ljava/lang/Class; 692B
W/dalvikvm(  602):     1 of Ljava/lang/Class; 956B
W/dalvikvm(  602):     1 of Ljava/lang/Class; 1004B
W/dalvikvm(  602):     1 of Ljava/lang/Class; 1148B
W/dalvikvm(  602):     2 of Ljava/lang/Class; 1172B (1 unique)
W/dalvikvm(  602):     1 of Ljava/lang/Class; 1316B
W/dalvikvm(  602):     1 of Ljava/lang/Class; 3428B
W/dalvikvm(  602):     1 of Ljava/lang/Class; 3452B
W/dalvikvm(  602):     1 of Ljava/lang/String; 28B
W/dalvikvm(  602):     2 of Ldalvik/system/VMRuntime; 12B (1 unique)
W/dalvikvm(  602):    10 of Ljava/lang/ref/WeakReference; 28B (10 unique)
W/dalvikvm(  602):     1 of Ldalvik/system/PathClassLoader; 44B
W/dalvikvm(  602):  1553 of Landroid/graphics/Point; 20B (1553 unique)
W/dalvikvm(  602):   261 of Landroid/graphics/Point; 28B (261 unique)
W/dalvikvm(  602):     1 of Landroid/view/MotionEvent; 100B
W/dalvikvm(  602):     1 of Landroid/app/ActivityThread$ApplicationThread; 28B
W/dalvikvm(  602):     1 of Landroid/content/ContentProvider$Transport; 28B
W/dalvikvm(  602):     1 of Landroid/view/Surface$CompatibleCanvas; 44B
W/dalvikvm(  602):     1 of Landroid/view/inputmethod/InputMethodManager$ControlledInputConnectionWrapper; 36B
W/dalvikvm(  602):     1 of Landroid/view/ViewRoot$1; 12B
W/dalvikvm(  602):     1 of Landroid/view/ViewRoot$W; 28B
W/dalvikvm(  602):     1 of Landroid/view/inputmethod/InputMethodManager$1; 28B
W/dalvikvm(  602):     1 of Landroid/view/accessibility/AccessibilityManager$1; 28B
W/dalvikvm(  602):     1 of Landroid/widget/LinearLayout$LayoutParams; 44B
W/dalvikvm(  602):     1 of Landroid/widget/LinearLayout; 332B
W/dalvikvm(  602):     2 of Lorg/apache/harmony/xnet/provider/jsse/TrustManagerImpl; 28B (1 unique)
W/dalvikvm(  602):     1 of Landroid/view/SurfaceView$MyWindow; 36B
W/dalvikvm(  602):     1 of Ltouchtest/RenderThread; 92B
W/dalvikvm(  602):     1 of Landroid/view/SurfaceView$3; 12B
W/dalvikvm(  602):     1 of Ltouchtest/DrawingView; 412B
W/dalvikvm(  602):     1 of Ltouchtest/Activity1; 180B
W/dalvikvm(  602): Memory held directly by tracked refs is 75624 bytes
E/dalvikvm(  602): Excessive JNI global references (2001)
E/dalvikvm(  602): VM aborting
```


No exemplo acima (que, por acaso, é proveniente [bug 685215](https://bugzilla.novell.com/show_bug.cgi?id=685215)) o problema é que muitos Android.Graphics.Point instâncias estão sendo criadas; consulte [comentário \#2](https://bugzilla.novell.com/show_bug.cgi?id=685215#c2) para obter uma lista de correções para o Esse bug específico.

Normalmente, uma solução útil é encontrar o tipo tem muitas instâncias alocada &ndash; Android.Graphics.Point no despejo acima &ndash; , em seguida, localizar onde eles são criados em seu código-fonte e descarte-os adequadamente (para que seus Tempo de vida Java-Object é abreviado). Isso nem sempre é apropriado (\#685215 é multithreaded, portanto, a solução trivial evita a chamada de Dispose), mas é a primeira coisa a considerar.

Você pode habilitar [GREF log](~/android/troubleshooting/index.md) para ver quando GREFs são criados e quantos existem.


## <a name="abort-due-to-jni-type-mismatch"></a>Anulamento devido à incompatibilidade de tipo JNI

Se você mão-roll código JNI, é possível que os tipos não correspondem corretamente, por exemplo, se você tentar invocar `java.lang.Runnable.run` em um tipo que não implementa `java.lang.Runnable`. Quando isso ocorrer, haverá uma mensagem semelhante a esta no Log de depuração do Android:

```shell
W/dalvikvm( 123): JNI WARNING: can't call Ljava/Type;;.method on instance of Lanother/java/Type;
W/dalvikvm( 123):              in Lmono/java/lang/RunnableImplementor;.n_run:()V (CallVoidMethodA)
...
E/dalvikvm( 123): VM aborting
```

## <a name="dynamic-code-support"></a>Suporte de código dinâmico

### <a name="dynamic-code-does-not-compile"></a>Não compila código dinâmico

Usar C\# dinâmico em seu aplicativo ou biblioteca, você precisa adicionar a dll, Microsoft.CSharp.dll e Mono.CSharp.dll ao seu projeto.

### <a name="in-release-build-missingmethodexception-occurs-for-dynamic-code-at-run-time"></a>Na versão de compilação, MissingMethodException ocorre para código dinâmico no tempo de execução.

-   É provável que seu projeto de aplicativo não tem referências a dll, Microsoft.CSharp.dll ou Mono.CSharp.dll. Verifique se que esses assemblies são referenciados.

    -   Tenha em mente que código dinâmico sempre os custos. Se você precisar de um código eficiente, considere o uso não código dinâmico.

-   Na primeira visualização, esses assemblies foram excluídos, a menos que explicitamente, tipos de cada conjunto são usados pelo código do aplicativo. Consulte o seguinte para uma solução alternativa: [http://lists.ximian.com/pipermail/mo...il/009798.html](http://lists.ximian.com/pipermail/monodroid/2012-April/009798.html)


## <a name="projects-built-with-aotllvm-crash-on-x86-devices"></a>Projetos criados com a falha AOT + LLVM em x86 dispositivos

Ao implantar um aplicativo criado com [AOT + LLVM](~/android/deploy-test/release-prep/index.md) em dispositivos baseados em x86, você poderá ver uma mensagem de erro de exceção semelhante à seguinte:

```shell
Assertion: should not be reached at /Users/.../external/mono/mono/mini/tramp-x86.c:124
Fatal signal 6 (SIGABRT), code -6 in tid 4051 (amarin.bug56111)
```

Isso é um problema conhecido como relatado [56111](https://bugzilla.xamarin.com/show_bug.cgi?id=56111). A solução alternativa é desabilitar LLVM.
