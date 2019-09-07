---
title: Dicas de solução de problemas
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: 56137ACA-4811-B312-6860-E16D0FA123F7
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 03/15/2018
ms.openlocfilehash: b750dd4eebb4e181e3a1d3a33c6505bb58b3848b
ms.sourcegitcommit: 57f815bf0024b1afe9754c0e28054fc0a53ce302
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/06/2019
ms.locfileid: "70757079"
---
# <a name="troubleshooting-tips"></a>Dicas de solução de problemas

## <a name="getting-diagnostic-information"></a>Obtendo informações de diagnóstico

O Xamarin. Android tem alguns locais a serem examinados ao rastrear vários bugs.
Elas incluem:

1. Saída do MSBuild de diagnóstico.
2. Logs de implantação de dispositivo.
3. Saída de log de depuração do Android.

<a name="Diagnostic_MSBuild_Output" />

## <a name="diagnostic-msbuild-output"></a>Saída de diagnóstico do MSBuild

O MSBuild de diagnóstico pode conter informações adicionais relacionadas à criação de pacotes e pode conter algumas informações de implantação de pacote.

Para habilitar os resultados de diagnóstico do MSBuild no Visual Studio para Mac:

1. Clique em **ferramentas > opções...**
2. No modo de exibição de árvore à esquerda, selecione **projetos e soluções > compilar e executar**
3. No painel direito, defina o menu suspenso de detalhes de saída de compilação do MSBuild como diagnóstico
4. Clique em **OK**
5. Limpe e recompile o seu pacote.
6. A saída de diagnóstico é visível no painel de saída.

Para habilitar a saída de diagnóstico do MSBuild dentro do Visual Studio para Mac/OS X:

1. Clique em **Visual Studio para Mac > preferências...**
2. No modo de exibição de árvore à esquerda, selecione **projetos > compilação**
3. No painel direito, defina a lista suspensa detalhes do log para diagnóstico
4. Clique em **OK**
5. Reiniciar o Visual Studio para Mac
6. Limpe e recompile o seu pacote.
7. A saída de diagnóstico é visível no painel de erros (**exibir > Pads > erros** ), clicando no botão criar saída.

## <a name="device-deployment-logs"></a>Logs de implantação de dispositivo

Para habilitar o log de implantação de dispositivo no Visual Studio:

1. **Ferramentas > opções...** >
2. No modo de exibição de árvore à esquerda, selecione **Xamarin > configurações do Android**
3. No painel direito, habilite a caixa de seleção [X] **log de depuração de extensão (grava monodroid. log em sua área de trabalho)** .
4. As mensagens de log são gravadas no arquivo monodroid. log em sua área de trabalho.

Visual Studio para Mac sempre grava logs de implantação de dispositivo. Encontrá-los é um pouco mais difícil; um arquivo de log do *AndroidUtils* é criado para cada dia + hora em que uma implantação ocorre, por exemplo: **AndroidTools-2012-10-24_12-35-45.log**.

- No Windows, os arquivos de log são `%LOCALAPPDATA%\XamarinStudio-{VERSION}\Logs`gravados no.
- No OS X, os arquivos de `$HOME/Library/Logs/XamarinStudio-{VERSION}`log são gravados no.

## <a name="android-debug-log-output"></a>Saída de log de depuração do Android

O Android gravará muitas mensagens no [log de depuração do Android](~/android/deploy-test/debugging/android-debug-log.md).
O Xamarin. Android usa as propriedades do sistema Android para controlar a geração de mensagens adicionais para o log de depuração do Android. As propriedades do sistema Android podem ser definidas por meio do comando *SetProp* dentro do [Android Debug Bridge (ADB)](https://developer.android.com/guide/developing/tools/adb.html):

```shell
adb shell setprop PROPERTY_NAME PROPERTY_VALUE
```

As propriedades do sistema são lidas durante a inicialização do processo e, portanto, devem ser definidas antes de o aplicativo ser iniciado ou o aplicativo deve ser reiniciado depois que as propriedades do sistema são alteradas.

### <a name="xamarinandroid-system-properties"></a>Propriedades do sistema Xamarin.Android

O Xamarin. Android dá suporte às seguintes propriedades do sistema:

- *debug.mono.debug*: Se uma cadeia de caracteres não vazia, isso é equivalente `*mono-debug*`a.

- *debug.mono.env*: Uma lista separada por pipe ( *|* ' ') de variáveis de ambiente para exportar durante a inicialização do aplicativo, *antes que* o mono tenha sido inicializado. Isso permite definir variáveis de ambiente que controlam o log mono.

  - *Observação*: Como o valor é ' *|* ' separado, o valor deve ter um nível extra de quoting, pois o \`comando do *shell* \` do ADB removerá um conjunto de aspas.

  - *Observação*: Os valores de Propriedade do sistema Android não podem ter mais de 92 caracteres de comprimento.

  - Exemplo:

    ```
    adb shell setprop debug.mono.env "'MONO_LOG_LEVEL=info|MONO_LOG_MASK=asm'"
    ```

- *debug.mono.log*: Uma lista separada por vírgulas (' *,* ') de componentes que devem imprimir mensagens adicionais no log de depuração do Android. Por padrão, nada é definido. Os componentes incluem:

  - *todos*: Imprimir todas as mensagens
  - *gc*: Imprimir mensagens relacionadas ao GC.
  - *gref*: Impressão (fraca, global) referenciar mensagens de alocação e desalocação.
  - *lref*: Imprimir mensagens de alocação e desalocação de referência local.

  *Observação*: eles são *extremamente* detalhados. Não habilite a menos que você realmente precise.

- *debug.mono.trace*: Permite definir a configuração de [rastreamento](http://docs.go-mono.com/?link=man%3amono(1)) `=PROPERTY_VALUE` mono.

## <a name="deleting-bin-and-obj"></a>Excluindo `bin` e`obj`

O Xamarin. Android foi sofrido no passado de uma situação como:

- Você encontra um erro estranho de compilação ou tempo de execução.
- Você `Clean` `bin` `obj` , `Rebuild`ou exclui manualmente seus diretórios e.
- O problema desaparece.

Estamos muito investidos em corrigir problemas como esses devido ao impacto na produtividade do desenvolvedor.

Se um problema, como isso acontece com você:

1. Faça uma observação mental. Qual foi a última ação que o projeto recebeu nesse estado?
1. Salve o log de Build atual. Tente compilar novamente e registre um [log de compilação de diagnóstico](#diagnostic-msbuild-output).
1. Envie um [relatório de bugs][bug].

Antes de excluir `bin` seus `obj` diretórios e, compactá-los e salvá-los para diagnóstico posterior, se necessário. Provavelmente, você pode `Clean` simplesmente seu projeto de aplicativo Xamarin. Android para que as coisas funcionem novamente.

[bug]: https://github.com/xamarin/xamarin-android/wiki/Submitting-Bugs,-Feature-Requests,-and-Pull-Requests

## <a name="xamarinandroid-cannot-resolve-systemvaluetuple"></a>O Xamarin. Android não pode resolver System. ValueTuple

Esse erro ocorre devido a uma incompatibilidade com o Visual Studio.

- **Visual Studio 2017 atualização 1** (a versão 15,1 ou mais antiga) é compatível apenas com o **System. ValueTuple NuGet 4.3.0** (ou mais antigo).

- **Atualização 2 do Visual Studio 2017** (a versão 15,2 ou mais recente) só é compatível com o **System. ValueTuple NuGet 4.3.1** (ou mais recente).

Escolha o NuGet System. ValueTuple correto que corresponde à sua instalação do Visual Studio 2017.

## <a name="gc-messages"></a>Mensagens GC

As mensagens do componente GC podem ser exibidas definindo a propriedade do sistema Debug. mono. log como um valor que contém GC.

As mensagens GC são geradas sempre que o GC é executado e fornece informações sobre a quantidade de trabalho que o GC fez:

```shell
I/monodroid-gc(12331): GC cleanup summary: 81 objects tested - resurrecting 21.
```

Informações adicionais do GC, como informações de tempo, podem ser geradas definindo `MONO_LOG_LEVEL` a `debug`variável de ambiente como:

```shell
adb shell setprop debug.mono.env MONO_LOG_LEVEL=debug
```

Isso resultará em (muitas) mensagens mono adicionais, incluindo estas três conseqüências:

```shell
D/Mono (15723): GC_BRIDGE num-objects 1 num_hash_entries 81226 sccs size 81223 init 0.00ms df1 285.36ms sort 38.56ms dfs2 50.04ms setup-cb 9.95ms free-data 106.54ms user-cb 20.12ms clenanup 0.05ms links 5523436/5523436/5523096/1 dfs passes 1104 6883/11046605
D/Mono (15723): GC_MINOR: (Nursery full) pause 2.01ms, total 287.45ms, bridge 225.60 promoted 0K major 325184K los 1816K
D/Mono ( 2073): GC_MAJOR: (user request) pause 2.17ms, total 2.47ms, bridge 28.77 major 576K/576K los 0K/16K
```

Na mensagem, `num-objects` é o número de objetos de ponte que essa passagem está considerando e `num_hash_entries` é o número de objetos processados durante essa invocação do código de ponte. `GC_BRIDGE`

Nas mensagens `GC_MINOR` e `GC_MAJOR` , `total` é o tempo durante o qual o mundo é pausado (nenhum thread está em execução), enquanto `bridge` é a quantidade de tempo gasto no código de processamento de ponte (que lida com a VM Java). O mundo *não* está em pausa enquanto o processamento da ponte ocorre.

 *Em geral*, quanto maior o valor de `num_hash_entries`, `bridge` mais tempo as coleções terão e quanto maior o `total` tempo gasto na coleta será.

## <a name="global-reference-messages"></a>Mensagens de referência global

Para habilitar o log de loggig de referência global (GREF), a propriedade do sistema *debug. mono. log* deve conter *GREF*, por exemplo:

```shell
adb shell setprop debug.mono.log gref
```

O Xamarin. Android usa referências globais do Android para fornecer mapeamentos entre instâncias do Java e as instâncias gerenciadas associadas, como ao invocar um método Java, uma instância Java precisa ser fornecida ao Java.

Infelizmente, os emuladores do Android só permitem que as referências globais de 2000 existam por vez. O hardware tem um limite muito maior de 52000 referências globais. O limite inferior pode ser problemático ao executar aplicativos no emulador, portanto, saber de *onde* a instância veio pode ser muito útil.

 *Observação*: a contagem de referência global é interna ao Xamarin. Android e não (e não pode) incluir referências globais retiradas por outras bibliotecas nativas carregadas no processo. Use a contagem de referência global como uma estimativa.

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

Há quatro mensagens de consequência:

- Criação de referência global: essas são as linhas que começam com *+ g +* e fornecerão um rastreamento de pilha para o caminho de código de criação.
- Destruição de referência global: essas são as linhas que começam com *-g-* e podem fornecer um rastreamento de pilha para a descartação do caminho de código da referência global. Se o GC estiver descartando o gref, nenhum rastreamento de pilha será fornecido.
- Criação de referência global fraca: essas são as linhas que começam com *+ w +* .
- Destruição de referência global fraca: essas são linhas que começam com *-w-* .

Em todas as mensagens, o valor de *grefc* é a contagem de referências globais que o Xamarin. Android criou, enquanto o valor de *grefwc* é a contagem de referências globais fracas que o xamarin. Android criou. O *valor de Handle ou* *obj-Handle* é o valor de identificador JNI e o caractere após ' */* ' é o tipo de valor de identificador *:/l* para referência local, */g* para referências globais e */w* para referências globais fracas.

Como parte do processo de GC, as referências globais (+ g +) são convertidas em referências globais fracas (causando + w + e-g-), um GC do lado do Java é iniciado e, em seguida, a referência global fraca é verificada para ver se ele foi coletado. Se ele ainda estiver ativo, um novo gref será criado em relação à referência fraca (+ g +,-w-); caso contrário, a referência fraca será destruída (-w).

## <a name="java-instance-is-created-and-wrapped-by-a-mcw"></a>A instância Java é criada e encapsulada por uma MCW

```shell
I/monodroid-gref(27679): +g+ grefc 2211 gwrefc 0 obj-handle 0x4066df10/L -> new-handle 0x4066df10/L from ...
I/monodroid-gref(27679): handle 0x4066df10; key_handle 0x4066df10: Java Type: `android/graphics/drawable/TransitionDrawable`; MCW type: `Android.Graphics.Drawables.TransitionDrawable`
```

## <a name="a-gc-is-being-performed"></a>Um GC está sendo executado...

```shell
I/monodroid-gref(27679): +w+ grefc 1953 gwrefc 259 obj-handle 0x4066df10/G -> new-handle 0xde68f95f/W from take_weak_global_ref_jni
I/monodroid-gref(27679): -g- grefc 1952 gwrefc 259 handle 0x4066df10/G from take_weak_global_ref_jni
```

## <a name="object-is-still-alive-as-handle--null"></a>O objeto ainda está ativo, como identificador! = nulo
## <a name="wref-turned-back-into-a-gref"></a>Wref voltado para um gref

```shell
I/monodroid-gref(27679): *try_take_global obj=0x4976f080 -> wref=0xde68f95f handle=0x4066df10
I/monodroid-gref(27679): +g+ grefc 1930 gwrefc 39 obj-handle 0xde68f95f/W -> new-handle 0x4066df10/G from take_global_ref_jni
I/monodroid-gref(27679): -w- grefc 1930 gwrefc 38 handle 0xde68f95f/W from take_global_ref_jni
```

## <a name="object-is-dead-as-handle--null"></a>O objeto está inativo, como identificador = = nulo
## <a name="wref-is-freed-no-new-gref-created"></a>Wref é liberado, nenhum novo gref criado

```shell
I/monodroid-gref(27679): *try_take_global obj=0x4976f080 -> wref=0xde68f95f handle=0x0
I/monodroid-gref(27679): -w- grefc 1914 gwrefc 296 handle 0xde68f95f/W from take_global_ref_jni
```

Há uma ruga "interessante" aqui: em destinos que executam o Android antes de 4,0, o valor de gref é igual ao endereço do objeto Java na memória do tempo de execução do Android. (Ou seja, o GC é um coletor não movido, conservador, e está distribuindo referências diretas para esses objetos.) Assim, após a + g +, + w +,-g-, + g +,-w-Sequence, o gref resultante terá o mesmo valor que o valor de gref original. Isso torna o grepping por meio de logs bastante simples.

O Android 4,0, no entanto, tem um coletor móvel e não distribui mais referências diretas para objetos de VM de tempo de execução do Android. Consequentemente, após a + g +, + w +,-g-, + g +,-w-Sequence, o valor de gref *será diferente*. Se o objeto sobreviver a vários GCs, ele passará por vários valores de gref, tornando mais difícil determinar de onde uma instância foi alocada de fato.

### <a name="querying-programmatically"></a>Consultando programaticamente

Você pode consultar as contagens GREF e WREF consultando o `JniRuntime` objeto.

`Java.Interop.JniRuntime.CurrentRuntime.GlobalReferenceCount`-Contagem de referência global

`Java.Interop.JniRuntime.CurrentRuntime.WeakGlobalReferenceCount`-Contagem de referência fraca

## <a name="android-debug-logs"></a>Logs de depuração do Android

Os [logs de depuração do Android](~/android/deploy-test/debugging/android-debug-log.md) podem fornecer contexto adicional sobre quaisquer erros de tempo de execução que você esteja vendo.

## <a name="floating-point-performance-is-terrible"></a>O desempenho do ponto flutuante é terrível!

Como alternativa, "meu aplicativo é executado 10 vezes mais rapidamente com a compilação de depuração do que com a versão Build!"

O Xamarin. Android dá suporte a vários dispositivos ABIs: *ARMEABI*, *ARMEABI-v7a*e *x86*. Os dispositivos ABIs podem ser especificados nas **Propriedades do projeto > guia aplicativo > arquiteturas com suporte**.

As compilações de depuração usam um pacote do Android que fornece todos os ABIs e, portanto, usarão a ABI mais rápida para o dispositivo de destino.

As compilações de versão incluirão apenas o ABIs selecionado na guia Propriedades do projeto. Mais de um pode ser selecionado.

*ARMEABI* é a Abi padrão e tem o mais amplo suporte a dispositivos. *No entanto*, o ARMEABI não dá suporte a dispositivos de várias CPUs e ponto flutuante de hardware, Amont outras coisas. Consequentemente, os aplicativos que usam o tempo de execução de liberação do ARMEABI serão vinculados a um único núcleo e usarão uma implementação de flutuação suave. Ambos podem contribuir para um desempenho significativamente mais lento para seu aplicativo.

Se seu aplicativo exigir um desempenho de ponto flutuante razoável (por exemplo, jogos), você deverá habilitar a Abi *ARMEABI-v7a* . Talvez você queira dar suporte apenas ao tempo de execução *ARMEABI-v7a* , embora isso signifique que os dispositivos mais antigos que dão suporte apenas a *ARMEABI* não poderão executar seu aplicativo.

## <a name="could-not-locate-android-sdk"></a>Não foi possível localizar SDK do Android

Há dois downloads disponíveis no Google para o SDK do Android para Windows.
Se você escolher o instalador. exe, ele gravará as chaves do registro que dizem ao Xamarin. Android onde ele foi instalado. Se você escolher o arquivo. zip e descompactá-lo por conta própria, o Xamarin. Android não saberá onde procurar o SDK. Você pode dizer ao Xamarin. Android que o SDK está no Visual Studio acessando **ferramentas > opções > Xamarin > configurações do Android**:

[![Localização de SDK do Android nas configurações do Xamarin Android](troubleshooting-images/01.png)](troubleshooting-images/01.png#lightbox)

## <a name="ide-does-not-display-target-device"></a>O IDE não exibe o dispositivo de destino

Às vezes, você tentará implantar seu aplicativo em um dispositivo, mas o dispositivo que você deseja implantar não será mostrado na caixa de diálogo Selecionar dispositivo. Isso pode acontecer quando a Android Debug Bridge decide entrar em férias.

Para diagnosticar esse problema, localize o [programa ADB](~/android/deploy-test/debugging/android-debug-log.md)e, em seguida, execute:

```shell
adb devices
```

Se o dispositivo não estiver presente, você precisará reiniciar o Android Debug Bridge Server para que seu dispositivo possa ser encontrado:

```shell
adb kill-server
adb start-server
```

O software de sincronização HTC pode impedir que o **ADB Start-Server** funcione corretamente. Se o comando **ADB Start-Server** não imprimir em qual porta ele está iniciando, saia do software de sincronização do HTC e tente reiniciar o servidor ADB.

## <a name="the-specified-task-executable-keytool-could-not-be-run"></a>O executável da tarefa especificado "keytool" não pôde ser executado

Isso significa que o caminho não contém o diretório em que o diretório bin do SDK do Java está localizado. Verifique se você seguiu essas etapas no guia de [instalação](~/android/get-started/installation/index.md) .

## <a name="monodroidexe-or-aresgenexe-exited-with-code-1"></a>monodroid. exe ou aresgen. exe encerrado com o código 1

Para ajudá-lo a depurar esse problema, acesse o Visual Studio e altere o nível de detalhamento do MSBuild, para fazer isso, selecione: **Ferramentas > opções > projeto** e **soluções > criar** e **Executar > Detalhamento da saída de compilação do projeto do MSBuild** e definir esse valor como **normal**.

Recompile e verifique o painel de saída do Visual Studio, que deve conter o erro completo.

## <a name="there-is-not-enough-storage-space-on-the-device-to-deploy-the-package"></a>Não há espaço de armazenamento suficiente no dispositivo para implantar o pacote

Isso ocorre quando você não inicia o emulador de dentro do Visual Studio. Ao iniciar o emulador fora do Visual Studio, você precisa passar as `-partition-size 512` opções, por exemplo,

```shell
emulator -partition-size 512 -avd MonoDroid
```

Certifique-se de usar o nome do simulador correto, ou seja, [o nome usado ao configurar o simulador](~/android/get-started/installation/windows.md#device).

## <a name="install_failed_invalid_apk-when-installing-a-package"></a>Falha\_na\_instalação\_de apk inválido ao instalar um pacote

Os nomes de pacote do Android *devem* conter um ponto (' *.* '). Edite o nome do pacote para que ele contenha um ponto.

- No Visual Studio:
  - Clique com o botão direito do mouse no projeto > Propriedades
  - Clique na guia manifesto do Android à esquerda.
  - Atualize o campo nome do pacote.
    - Se você vir a mensagem &ldquo;nenhum AndroidManifest. xml foi encontrado. Clique para adicionar um. &rdquo;, clique no link e, em seguida, atualize o campo nome do pacote.
- Dentro do Visual Studio para Mac:
  - Clique com o botão direito do mouse no projeto > opções.
  - Navegue até a seção Build/aplicativo Android.
  - Altere o campo nome do pacote para conter um '. '.

## <a name="install_failed_missing_shared_library-when-installing-a-package"></a>Falha\_na\_instalação\_da bibliotecacompartilhada\_ausente ao instalar um pacote

Uma "biblioteca compartilhada" neste contexto *não* é um arquivo*libfoo.so*(biblioteca compartilhada nativa); em vez disso, é uma biblioteca que deve ser instalada separadamente no dispositivo de destino, como o Google Maps.

O pacote do Android especifica quais bibliotecas compartilhadas são `<uses-library/>` necessárias com o elemento. Se uma biblioteca *necessária* não estiver presente no dispositivo de destino (por exemplo `//uses-library/@android:required` , se for *true*, que é o padrão), a instalação do *\_\_pacote falhará com falha de instalação compartilhada ausente\_\_ BIBLIOTECA*.

Para determinar quais bibliotecas compartilhadas são necessárias, exiba o arquivo**AndroidManifest. xml** *gerado*
(por exemplo, **\\obj Debug\\Android\\AndroidManifest. xml**) e procure o `<uses-library/>` elementos. `<uses-library/>`os elementos podem ser adicionados manualmente no arquivo **AndroidManifest.\\XML das propriedades** do seu projeto e por meio do [atributo personalizado UsesLibraryAttribute](xref:Android.App.UsesLibraryAttribute).

Por exemplo, a adição de uma referência de assembly a *mono. Android. GoogleMaps. dll* irá adicionar `<uses-library/>` implicitamente uma para a biblioteca compartilhada do Google Maps.

## <a name="install_failed_update_incompatible-when-installing-a-package"></a>Falha\_na\_instalação\_da atualização incompatível ao instalar um pacote

Os pacotes do Android têm três requisitos:

- Eles devem conter um '. ' (consulte a entrada anterior)
- Eles devem ter um nome de pacote de cadeia de caracteres exclusivo (portanto, a Convenção reversa do TLD é vista em nomes de aplicativos Android, por exemplo, com. Android. Chrome para o aplicativo Chrome)
- Ao atualizar pacotes, o pacote deve ter a mesma chave de assinatura.

Portanto, imagine este cenário:

1. Você cria & implanta seu aplicativo como um aplicativo de depuração
2. Você altera a chave de assinatura, por exemplo, para usar como um aplicativo de versão (ou porque você não gosta da chave de assinatura de depuração fornecida por padrão)
3. Você instala seu aplicativo sem removê-lo primeiro, por exemplo, Debug > Iniciar sem depuração no Visual Studio

Quando isso acontecer, a instalação do pacote falhará com\_um\_erro\_de falha na atualização de instalação incompatível, pois o nome do pacote não foi alterado enquanto a chave de assinatura fazia. O [log de depuração do Android](~/android/deploy-test/debugging/android-debug-log.md) também conterá uma mensagem semelhante a:

```shell
E/PackageManager(  146): Package [PackageName] signatures do not match the previously installed version; ignoring!
```

Para corrigir esse erro, remova completamente o aplicativo do dispositivo antes de reinstalá-lo.

## <a name="install_failed_uid_changed-when-installing-a-package"></a>Instalar\_\_UIDcom\_falha alterado ao instalar um pacote

Quando um pacote do Android é instalado, ele recebe uma *ID de usuário* (UID).
*Às vezes*, por razões desconhecidas no momento, ao instalar o em um aplicativo já instalado, `INSTALL_FAILED_UID_CHANGED`a instalação falhará com:

```shell
ERROR [2015-03-23 11:19:01Z]: ANDROID: Deployment failed
Mono.AndroidTools.InstallFailedException: Failure [INSTALL_FAILED_UID_CHANGED]
   at Mono.AndroidTools.Internal.AdbOutputParsing.CheckInstallSuccess(String output, String packageName)
   at Mono.AndroidTools.AndroidDevice.<>c__DisplayClass2c.<InstallPackage>b__2b(Task`1 t)
   at System.Threading.Tasks.ContinuationTaskFromResultTask`1.InnerInvoke()
   at System.Threading.Tasks.Task.Execute()
```

Para contornar esse problema, *desinstale completamente* o pacote do Android, seja instalando o aplicativo da GUI do destino do Android ou `adb`usando:

```shell
$ adb uninstall @PACKAGE_NAME@
```

Não **usar** , pois isso preservará os dados do aplicativo e, portanto, preservará a UID conflitante no dispositivo de destino. `adb uninstall -k`

## <a name="release-apps-fail-to-launch-on-device"></a>Os aplicativos de liberação falham ao iniciar no dispositivo

A saída do log de depuração do Android conterá uma mensagem semelhante a:

```shell
D/AndroidRuntime( 1710): Shutting down VM
W/dalvikvm( 1710): threadid=1: thread exiting with uncaught exception (group=0xb412f180)
E/AndroidRuntime( 1710): FATAL EXCEPTION: main
E/AndroidRuntime( 1710): java.lang.UnsatisfiedLinkError: Couldn't load monodroid: findLibrary returned null
E/AndroidRuntime( 1710):        at java.lang.Runtime.loadLibrary(Runtime.java:365)
```

Nesse caso, há duas causas possíveis para isso:

1. O. apk não fornece uma ABI à qual o dispositivo de destino dá suporte.
    Por exemplo, o. apk contém apenas binários ARMEABI-v7a e o dispositivo de destino só dá suporte a ARMEABI.

2. Um [bug do Android](http://code.google.com/p/android/issues/detail?id=21670). Se esse for o caso, desinstale o aplicativo, cruze seus dedos e reinstale o aplicativo.

Para corrigir (1), edite as opções/Propriedades do projeto e [Adicione suporte para a Abi necessária à lista de abis com suporte](~/android/app-fundamentals/cpu-architectures.md). Para determinar qual ABI você precisa adicionar, execute o seguinte comando ADB em seu dispositivo de destino:

```shell
adb shell getprop ro.product.cpu.abi
adb shell getprop ro.product.cpu.abi2
```

A saída conterá o ABIs primário (e o secundário opcional).

```shell
$ adb shell getprop | grep ro.product.cpu
[ro.product.cpu.abi2]: [armeabi]
[ro.product.cpu.abi]: [armeabi-v7a]
```

## <a name="the-outpath-property-is-not-set-for-project-ldquomyappcsprojrdquo"></a>A propriedade outPath não está definida para o &ldquo;projeto MyApp. csproj&rdquo;

Isso geralmente significa que você tem um computador HP e a plataforma &ldquo;&rdquo; de variável de ambiente foi definida como algo como MCD ou HPD. Isso entra em conflito com a propriedade da plataforma MSBuild que geralmente &ldquo;é definida&rdquo; como qualquer&rdquo;CPU ou &ldquo;x86. Você precisará remover essa variável de ambiente do seu computador antes que o MSBuild possa funcionar:

- Painel de controle > sistema > variáveis de ambiente de > avançadas

Reinicie o Visual Studio ou Visual Studio para Mac e tente recompilar. Agora, as coisas devem funcionar conforme o esperado.

## <a name="javalangclasscastexception-monoandroidruntimejavaobject-cannot-be-cast-to"></a>Java. lang. ClassCastException: Mono. Android. Runtime. Javaobject não pode ser convertido em...

O Xamarin. Android 4. x não empacota corretamente tipos genéricos aninhados corretamente. Por exemplo, considere o seguinte código\# C usando [SimpleExpandableListAdapter](xref:Android.Widget.SimpleExpandableListAdapter):

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

O problema é que o Xamarin. Android realiza marshaling incorretamente de tipos genéricos aninhados. O `List<IDictionary<string, object>>` está sendo empacotado para um [Java. lang. ArrrayList](xref:Java.Util.ArrayList), mas o `ArrayList` `Dictionary<string, object>` está contendo `mono.android.runtime.JavaObject` instâncias (que fazem referência às instâncias) em vez de algo que implementa [Java. util. map](xref:Java.Util.IMap), resultando na seguinte exceção:

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

A solução alternativa é usar os [tipos de coleção Java](~/android/internals/api-design.md) fornecidos em vez `System.Collections.Generic` dos tipos para &ldquo;os&rdquo; tipos internos. Isso resultará em tipos Java apropriados ao realizar o marshaling das instâncias. (O código a seguir é mais complicado do que o necessário para reduzir os tempos de vida do gref. Ele pode ser simplificado para alterar o código original por `s/List/JavaList/g` meio `s/Dictionary/JavaDictionary/g` de e se os tempos de vida de gref não se preocupam.)

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

## <a name="unexpected-nullreferenceexceptions"></a>NullReferenceExceptions inesperadas

Ocasionalmente o [logdedepuraçãodoAndroid](~/android/deploy-test/debugging/android-debug-log.md) mencionaráNullReferenceExceptionsque &ldquo;nãopodemacontecer,&rdquo; ouqueserãoprovenientesdocódigodetempodeexecuçãodomonoparaAndroidlogoantesdoaplicativosetornarem :

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

Isso pode acontecer quando o tempo de execução do Android decide anular o processo, o que pode ocorrer por vários motivos, incluindo atingir o limite de GREF do destino &ldquo;ou&rdquo; fazer algo errado com JNI.

Para ver se esse é o caso, verifique o log de depuração do Android em busca de uma mensagem do processo semelhante a:

```shell
E/dalvikvm(  123): VM aborting
```

## <a name="abort-due-to-global-reference-exhaustion"></a>Anular devido a esgotamento de referência global

A camada JNI do tempo de execução do Android só dá suporte a um número limitado de referências de objeto JNI para ser válida em um determinado momento. Quando esse limite é excedido, as coisas são interrompidas.

O limite de GREF (*referência global*) é de 2000 referências no emulador e ~ 52000 referências em hardware.

Você sabe que está começando a criar muitas GREFs ao ver mensagens como essa no log de depuração do Android:

```shell
D/dalvikvm(  602): GREF has increased to 1801
```

Quando você atingir o limite de GREF, uma mensagem como a seguinte será impressa:

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

No exemplo acima (que, incidentalmente, vem do [bug 685215](https://bugzilla.novell.com/show_bug.cgi?id=685215)), o problema é que muitas instâncias de Android. Graphics. Point estão sendo criadas; consulte [o \#comentário 2](https://bugzilla.novell.com/show_bug.cgi?id=685215#c2) para obter uma lista de correções para esse bug específico.

Normalmente, uma solução útil é descobrir qual tipo tem muitas instâncias alocadas &ndash; para Android. Graphics. Point no despejo &ndash; acima, em seguida, encontrar onde elas são criadas no código-fonte e descartá-las adequadamente (para que suas O tempo de vida do objeto Java é reduzido). Isso nem sempre é apropriado (\#o 685215 é multithread e, portanto, a solução trivial evita a chamada Dispose), mas é a primeira coisa a ser considerada.

Você pode habilitar o [log do GREF](~/android/troubleshooting/index.md) para ver quando GREFs são criados e quantas existem.

## <a name="abort-due-to-jni-type-mismatch"></a>Anular devido a incompatibilidade de tipo JNI

Se você distribuir manualmente o código JNI, é possível que os tipos não correspondam corretamente, por exemplo, se você tentar `java.lang.Runnable.run` invocar em um tipo que `java.lang.Runnable`não implementa. Quando isso ocorrer, haverá uma mensagem semelhante a esta no log de depuração do Android:

```shell
W/dalvikvm( 123): JNI WARNING: can't call Ljava/Type;;.method on instance of Lanother/java/Type;
W/dalvikvm( 123):              in Lmono/java/lang/RunnableImplementor;.n_run:()V (CallVoidMethodA)
...
E/dalvikvm( 123): VM aborting
```

## <a name="dynamic-code-support"></a>Suporte a código dinâmico

### <a name="dynamic-code-does-not-compile"></a>O código dinâmico não compila

Para usar o\# C Dynamic em seu aplicativo ou biblioteca, você precisa adicionar System. Core. dll, Microsoft. CSharp. dll e mono. CSharp. dll ao seu projeto.

### <a name="in-release-build-missingmethodexception-occurs-for-dynamic-code-at-run-time"></a>Na compilação da versão, MissingMethodException ocorre para o código dinâmico em tempo de execução.

- É provável que seu projeto de aplicativo não tenha referências a System. Core. dll, Microsoft. CSharp. dll ou mono. CSharp. dll. Verifique se esses assemblies são referenciados.

  - Tenha em mente que o código dinâmico sempre custa. Se você precisar de um código eficiente, considere não usar código dinâmico.

- Na primeira visualização, esses assemblies foram excluídos, a menos que os tipos em cada assembly sejam usados explicitamente pelo código do aplicativo. Consulte o seguinte para obter uma solução alternativa:[http://lists.ximian.com/pipermail/mo...il/009798.html](http://lists.ximian.com/pipermail/monodroid/2012-April/009798.html)

## <a name="projects-built-with-aotllvm-crash-on-x86-devices"></a>Projetos criados com falha AOT + LLVM em dispositivos x86

Ao implantar um aplicativo criado com o [AOT + LLVM](~/android/deploy-test/release-prep/index.md) em dispositivos baseados em x86, você poderá ver uma mensagem de erro de exceção semelhante à seguinte:

```shell
Assertion: should not be reached at /Users/.../external/mono/mono/mini/tramp-x86.c:124
Fatal signal 6 (SIGABRT), code -6 in tid 4051 (amarin.bug56111)
```

Esse é um problema conhecido conforme relatado em [56111](https://bugzilla.xamarin.com/show_bug.cgi?id=56111). A solução alternativa é desabilitar o LLVM.
