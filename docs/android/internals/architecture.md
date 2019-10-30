---
title: Arquitetura
ms.prod: xamarin
ms.assetid: 7DC22A08-808A-DC0C-B331-2794DD1F9229
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 04/25/2018
ms.openlocfilehash: fe0903eca5c907fc104728ca0ad7c676a45a5180
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/29/2019
ms.locfileid: "73027919"
---
# <a name="architecture"></a>Arquitetura

Os aplicativos Xamarin. Android são executados no ambiente de execução mono.
Esse ambiente de execução é executado lado a lado com a máquina virtual de tempo de execução do Android (arte). Ambos os ambientes de tempo de execução são executados na parte superior do kernel do Linux e expõem várias APIs para o código do usuário que permite aos desenvolvedores acessar o sistema subjacente. O tempo de execução do Mono é escrito na linguagem C.

Você pode usar o [sistema](xref:System), o [System.IO](xref:System.IO), o [System.net](xref:System.Net) e o restante das bibliotecas de classes do .net para acessar os recursos subjacentes do sistema operacional Linux.

No Android, a maioria dos recursos do sistema, como áudio, gráficos, OpenGL e telefonia, não estão disponíveis diretamente para aplicativos nativos, eles são expostos apenas por meio das APIs Java de tempo de execução do Android que residem em um dos namespaces [Java](xref:Java.Lang). * ou [Android ](xref:Android). * Namespaces. A arquitetura é, aproximadamente, assim:

[![diagrama de mono e ART acima do kernel e abaixo do .NET/Java + bindings](architecture-images/architecture1.png)](architecture-images/architecture1.png#lightbox)

Os desenvolvedores do Xamarin. Android acessam os vários recursos no sistema operacional chamando as APIs do .NET que eles conhecem (para acesso de baixo nível) ou usando as classes expostas nos namespaces do Android que fornecem uma ponte para as APIs Java que são expostas pelo o tempo de execução do Android.

Para obter mais informações sobre como as classes do Android se comunicam com as classes de tempo de execução do Android, consulte o documento de [design da API](~/android/internals/api-design.md) .

## <a name="application-packages"></a>Pacotes de aplicativos

Os pacotes de aplicativos Android são contêineres ZIP com uma extensão de arquivo *. apk* . Os pacotes de aplicativos Xamarin. Android têm a mesma estrutura e layout que os pacotes Android normais, com as seguintes adições:

- Os assemblies de aplicativo (contendo IL) são *armazenados* sem compactação na pasta *assemblies* . Durante a inicialização do processo na versão, compila o *. apk* é *mmap ()* Ed no processo e os assemblies são carregados da memória. Isso permite a inicialização de aplicativo mais rápida, pois os assemblies não precisam ser extraídos antes da execução.  
- *Observação:* Informações de localização do assembly, como [assembly. Location](xref:System.Reflection.Assembly.Location) e [assembly. CodeBase](xref:System.Reflection.Assembly.CodeBase) *não pode ser confiável* em builds de versão. Eles não existem como entradas de sistema de arquivos distintas e não têm nenhum local utilizável.

- As bibliotecas nativas que contêm o tempo de execução do mono estão presentes dentro de *. apk* . Um aplicativo Xamarin. Android deve conter bibliotecas nativas para as arquiteturas do Android desejadas/direcionadas, por exemplo, *ARMEABI* , *ARMEABI-v7a* , *x86* . Os aplicativos Xamarin. Android não podem ser executados em uma plataforma a menos que contenham as bibliotecas de tempo de execução apropriadas.

Os aplicativos Xamarin. Android também contêm *wrappers* que podem ser chamados pelo Android para permitir que o Android chame código gerenciado.

## <a name="android-callable-wrappers"></a>Callable Wrappers do Android

- Os **wrappers** que podem ser chamados pelo Android são uma ponte [JNI](https://en.wikipedia.org/wiki/Java_Native_Interface) , que são usados sempre que o tempo de execução do Android precisa para invocar o código gerenciado. Os wrappers callable Android são como os métodos virtuais podem ser substituídos e as interfaces Java podem ser implementadas. Consulte o documento [visão geral da integração de Java](~/android/platform/java-integration/index.md) para obter mais informações.

<a name="Managed_Callable_Wrappers" />

## <a name="managed-callable-wrappers"></a>Wrappers callable gerenciados

Wrappers callable gerenciados são uma ponte JNI, que são usadas sempre que o código gerenciado é necessário para invocar o código do Android e dar suporte à substituição de métodos virtuais e à implementação de interfaces Java. Todos os namespaces [Android](xref:Android). * e relacionados são wrappers callable gerenciados gerados por meio da [associação. jar](~/android/platform/binding-java-library/index.md).
Wrappers callable gerenciados são responsáveis por converter entre tipos gerenciados e Android e invocar os métodos de plataforma Android subjacentes via JNI.

Cada wrapper chamado gerenciado e criado contém uma referência global do Java, que pode ser acessada pela propriedade [Android. Runtime. IJavaObject. Handle](xref:Android.Runtime.IJavaObject.Handle) . As referências globais são usadas para fornecer o mapeamento entre instâncias do Java e instâncias gerenciadas. As referências globais são um recurso limitado: os emuladores permitem que apenas 2000 referências globais existam de cada vez, enquanto a maioria dos hardwares permite que mais de 52.000 referências globais existam de cada vez.

Para controlar quando as referências globais são criadas e destruídas, você pode definir a propriedade do sistema [debug. mono. log](~/android/troubleshooting/index.md) para conter [gref](~/android/troubleshooting/index.md).

As referências globais podem ser liberadas explicitamente chamando [Java. lang. Object. Dispose ()](xref:Java.Lang.Object.Dispose) no wrapper callable gerenciado. Isso removerá o mapeamento entre a instância do Java e a instância gerenciada e permitirá que a instância do Java seja coletada. Se a instância do Java for acessada novamente do código gerenciado, um novo wrapper callable gerenciado será criado para ele.

Deve-se ter cuidado ao descartar wrappers callable gerenciados se a instância puder ser compartilhada inadvertidamente entre threads, uma vez que descartar a instância afetará as referências de quaisquer outros threads. Para segurança máxima, somente `Dispose()` de instâncias que foram alocadas por meio de `new` *ou* de métodos que você *sabe* sempre alocar novas instâncias e não instâncias em cache que podem causar compartilhamento de instância acidental entre threads.

## <a name="managed-callable-wrapper-subclasses"></a>Subclasses de wrapper callable gerenciadas

As subclasses de wrapper callable gerenciadas são onde toda a lógica específica do aplicativo "interessante" pode residir. Isso inclui subclasses de [Android. app. Activity](xref:Android.App.Activity) personalizadas (como o tipo [Atividade1](https://github.com/xamarin/monodroid-samples/blob/master/HelloM4A/Activity1.cs#L13) no modelo de projeto padrão). (Especificamente, essas são todas as subclasses *Java. lang. Object* que *não* contêm um atributo personalizado [RegisterAttribute](xref:Android.Runtime.RegisterAttribute) ou [RegisterAttribute. DoNotGenerateAcw](xref:Android.Runtime.RegisterAttribute.DoNotGenerateAcw) é *false*, que é o padrão.)

Como os wrappers chamáveis gerenciados, as subclasses de wrapper callable Managed também contêm uma referência global, acessível por meio da propriedade [Java. lang. Object. Handle](xref:Java.Lang.Object.Handle) . Assim como ocorre com os wrappers callable gerenciados, as referências globais podem ser liberadas explicitamente chamando [Java. lang. Object. Dispose ()](xref:Java.Lang.Object.Dispose).
Ao contrário dos wrappers chamáveis gerenciados, deve-se *tomar muito cuidado* antes de descartar tais instâncias, já que *Dispose ()* -ing da instância interromperá o mapeamento entre a instância Java (uma instância de um Android Callable Wrapper) e o cópia.

### <a name="java-activation"></a>Ativação do Java

Quando um ACW ( [Android callable wrapper](~/android/platform/java-integration/android-callable-wrappers.md) ) é criado a partir do Java, o Construtor ACW fará com C# que o Construtor correspondente seja invocado. Por exemplo, o ACW para *MainActivity* conterá um construtor padrão que invocará o construtor padrão do *MainActivity*. (Isso é feito por meio da chamada *typemanager. Activate ()* dentro dos construtores ACW.)

Há uma outra assinatura de construtor de consequência: o construtor *(IntPtr, JniHandleOwnership)* . O construtor *(IntPtr, JniHandleOwnership)* é invocado sempre que um objeto Java é exposto a código gerenciado e um wrapper callable gerenciado precisa ser construído para gerenciar o identificador JNI. Isso geralmente é feito automaticamente.

Há dois cenários em que o construtor *(IntPtr, JniHandleOwnership)* deve ser fornecido manualmente em uma subclasse de wrapper resgatável gerenciada:

1. [Android. app. Application](xref:Android.App.Application) é uma subclasse. O *aplicativo* é especial; o construtor *aplicativo* padrão *nunca* será invocado e o [Construtor (IntPtr, JniHandleOwnership) deverá ser fornecido](https://github.com/xamarin/monodroid-samples/blob/f01b5c31/SanityTests/Hello.cs#L105).

2. Invocação de método virtual de um construtor de classe base.

Observe que (2) é uma abstração vazada. No Java, como no C#, as chamadas para métodos virtuais de um construtor sempre invocam a implementação do método mais derivado. Por exemplo, o [Construtor TextView (context, attributeset, int)](xref:Android.Widget.TextView#ctor*) invoca o método virtual [TextView. getDefaultMovementMethod ()](https://developer.android.com/reference/android/widget/TextView.html#getDefaultMovementMethod()), que é associado como a [Propriedade TextView. DefaultMovementMethod](xref:Android.Widget.TextView.DefaultMovementMethod).
Portanto, se um tipo [LogTextBox](https://github.com/xamarin/monodroid-samples/blob/f01b5c31/ApiDemo/Text/LogTextBox.cs) fosse (1) [TextView de subclasse](https://github.com/xamarin/monodroid-samples/blob/f01b5c31/ApiDemo/Text/LogTextBox.cs#L26), (2) [override TextView. DefaultMovementMethod](https://github.com/xamarin/monodroid-samples/blob/f01b5c31/ApiDemo/Text/LogTextBox.cs#L45)e (3) [Ativar uma instância dessa classe via XML,](https://github.com/xamarin/monodroid-samples/blob/f01b5c31/ApiDemo/Resources/layout/log_text_box_1.xml#L29) a propriedade *DefaultMovementMethod* substituída ser chamado antes que o Construtor ACW tenha a oportunidade de ser executado, e isso ocorreria C# antes que o Construtor tivesse a oportunidade de ser executado.

Isso tem suporte instanciando uma instância LogTextBox por meio do construtor [LogTextView (IntPtr, JniHandleOwnership)](https://github.com/xamarin/monodroid-samples/blob/f01b5c31/ApiDemo/Text/LogTextBox.cs#L28) quando a instância ACW LogTextBox entra em código gerenciado pela primeira vez e, em seguida, invocando o [LogTextBox (context, IAttributeSet, int)](https://github.com/xamarin/monodroid-samples/blob/f01b5c31/ApiDemo/Text/LogTextBox.cs#L41) *na mesma instância* quando o Construtor ACW é executado.

Ordem dos eventos:

1. O layout XML é carregado em um [ContentView](https://github.com/xamarin/monodroid-samples/blob/f01b5c31/ApiDemo/Text/LogTextBox1.cs#L41).

2. O Android instancia o grafo do objeto de layout e cria uma instância de *monodroid. apidemo. LogTextBox* , a ACW para *LogTextBox* .

3. O construtor *monodroid. apidemo. LogTextBox* executa o construtor [Android. widget. TextView](https://developer.android.com/reference/android/widget/TextView.html#TextView%28android.content.Context,%20android.util.AttributeSet%29) .

4. O construtor *TextView* invoca *monodroid. apidemo. LogTextBox. getDefaultMovementMethod ()* .

5. *monodroid. apidemo. LogTextBox. getDefaultMovementMethod ()* invoca *LogTextBox. n_getDefaultMovementMethod ()* , que invoca *TextView. n_getDefaultMovementMethod ()* , que invoca [Java. lang. Object. GetObject @no_ _t_4_ TextView&gt; (Handle, JniHandleOwnership. DoNotTransfer)](xref:Java.Lang.Object.GetObject*) .

6. *Java. lang. Object. GetObject&lt;TextView&gt;()* verifica se já existe uma instância correspondente C# para o *identificador* . Se houver, ele será retornado. Nesse cenário, não há, portanto, *Object. GetObject&lt;t&gt;()* deve criar um.

7. *Object. GetObject&lt;t&gt;()* procura o construtor *LogTextBox (IntPtr, JniHandleOwneship)* , invoca-o, cria um mapeamento entre o *identificador* e a instância criada e retorna a instância criada.

8. *TextView. n_GetDefaultMovementMethod ()* invoca o getter da propriedade *LogTextBox. DefaultMovementMethod* .

9. O controle retorna para o construtor *Android. widget. TextView* , que conclui a execução.

10. O construtor *monodroid. apidemo. LogTextBox* é executado, invocando o *tipo. Activate ()* .

11. O construtor *LogTextBox (context, IAttributeSet, int)* é executado *na mesma instância criada em (7)* .

12. Se o Construtor (IntPtr, JniHandleOwnership) não puder ser encontrado, um System. MissingMethodException] (xref: System. MissingMethodException) será gerado.

<a name="Premature_Dispose_Calls" />

### <a name="premature-dispose-calls"></a>Chamadas prematuros de descarte ()

Há um mapeamento entre um identificador de JNI e a instância C# correspondente. Java. lang. Object. Dispose () interrompe esse mapeamento. Se um identificador JNI inserir código gerenciado depois que o mapeamento for interrompido, ele se parece com a ativação de Java e o construtor *(IntPtr, JniHandleOwnership)* será verificado e invocado. Se o construtor não existir, uma exceção será lançada.

Por exemplo, dada a seguinte subclasse de encapsulador callable chamável:

```csharp
class ManagedValue : Java.Lang.Object {

    public string Value {get; private set;}

    public ManagedValue (string value)
    {
        Value = value;
    }

    public override string ToString ()
    {
        return string.Format ("[Managed: Value={0}]", Value);
    }
}
```

Se criarmos uma instância, descartá-la () e fará com que o wrapper callable gerenciado seja recriado:

```csharp
var list = new JavaList<IJavaObject>();
list.Add (new ManagedValue ("value"));
list [0].Dispose ();
Console.WriteLine (list [0].ToString ());
```

O programa irá morrer:

```shell
E/mono    ( 2906): Unhandled Exception: System.NotSupportedException: Unable to activate instance of type Scratch.PrematureDispose.ManagedValue from native handle 4051c8c8 --->
System.MissingMethodException: No constructor found for Scratch.PrematureDispose.ManagedValue::.ctor(System.IntPtr, Android.Runtime.JniHandleOwnership)
E/mono    ( 2906):   at Java.Interop.TypeManager.CreateProxy (System.Type type, IntPtr handle, JniHandleOwnership transfer) [0x00000] in <filename unknown>:0
E/mono    ( 2906):   at Java.Interop.TypeManager.CreateInstance (IntPtr handle, JniHandleOwnership transfer, System.Type targetType) [0x00000] in <filename unknown>:0
E/mono    ( 2906):   --- End of inner exception stack trace ---
E/mono    ( 2906):   at Java.Interop.TypeManager.CreateInstance (IntPtr handle, JniHandleOwnership transfer, System.Type targetType) [0x00000] in <filename unknown>:0
E/mono    ( 2906):   at Java.Lang.Object.GetObject (IntPtr handle, JniHandleOwnership transfer, System.Type type) [0x00000] in <filename unknown>:0
E/mono    ( 2906):   at Java.Lang.Object._GetObject[IJavaObject] (IntPtr handle, JniHandleOwnership transfer) [0x00000
```

Se a subclasse contiver um construtor *(IntPtr, JniHandleOwnership)* , uma *nova* instância do tipo será criada. Como resultado, a instância será exibida para "perder" todos os dados da instância, pois é uma nova instância. (Observe que o valor é NULL.)

```shell
I/mono-stdout( 2993): [Managed: Value=]
```

Apenas *Dispose ()* de subclasses de wrapper callable gerenciados quando você sabe que o objeto Java não será mais usado, ou a subclasse não contém dados de instância e um construtor *(IntPtr, JniHandleOwnership)* foi fornecido.

## <a name="application-startup"></a>Inicialização do aplicativo

Quando uma atividade, um serviço, etc. é iniciado, o Android verifica primeiro se já existe um processo em execução para hospedar a atividade/serviço/etc. Se esse processo não existir, um novo processo será criado, o [AndroidManifest. xml](https://developer.android.com/guide/topics/manifest/manifest-intro.html) será lido e o tipo especificado no atributo [/manifest/application/@android:name](https://developer.android.com/guide/topics/manifest/application-element.html#nm) será carregado e instanciado. Em seguida, todos os tipos especificados pela [/manifest/application/provider/@android:name](https://developer.android.com/guide/topics/manifest/provider-element.html#nm) valores de atributo são instanciados e têm seu método [ContentProvider. attachInfo %28)](xref:Android.Content.ContentProvider.AttachInfo*) invocado. O Xamarin. Android se conecta a isso adicionando um *mono. Myruntimeprovider* *ContentProvider* para AndroidManifest. xml durante o processo de compilação. O *mono. O método monoruntimeprovider. attachInfo ()* é responsável por carregar o tempo de execução do mono no processo.
Qualquer tentativa de usar o mono antes desse ponto falhará. ( *Observação*: é por isso que os tipos de subclasse [Android. app. Application](xref:Android.App.Application) precisam fornecer um [Construtor (IntPtr, JniHandleOwnership)](https://github.com/xamarin/monodroid-samples/blob/a9e8ef23/SanityTests/Hello.cs#L103), pois a instância do aplicativo é criada antes que o mono possa ser inicializado.)

Quando a inicialização do processo for concluída, `AndroidManifest.xml` será consultada para localizar o nome da classe da atividade/serviço/etc. para iniciar. Por exemplo, o [atributo/manifest/application/activity/@android:name](https://developer.android.com/guide/topics/manifest/activity-element.html#nm) é usado para determinar o nome de uma atividade a ser carregada. Para atividades, esse tipo deve herdar [Android. app. Activity](xref:Android.App.Activity).
O tipo especificado é carregado por meio de [Class. forName ()](https://developer.android.com/reference/java/lang/Class.html#forName(java.lang.String)) (que requer que o tipo seja um tipo Java, portanto, os wrappers que podem ser chamados pelo Android) e, em seguida, instanciados. A criação de uma instância de wrapper callable do Android irá disparar a criação de uma C# instância do tipo correspondente. Em seguida, o Android invocará [Activity. OnCreate (Bundle)](https://developer.android.com/reference/android/app/Activity.html#onCreate(android.os.Bundle)) , o que fará com que a [atividade correspondente. OnCreate (pacote)](xref:Android.App.Activity.OnCreate*) seja invocada e você esteja pronto para as corridas.
