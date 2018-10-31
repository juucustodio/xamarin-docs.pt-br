---
title: Coleta de Lixo
ms.prod: xamarin
ms.assetid: 298139E2-194F-4A58-BC2D-1D22231066C4
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 03/15/2018
ms.openlocfilehash: 814e975f57023424618c5ea403126f36f87467a7
ms.sourcegitcommit: 4859da8772dbe920fdd653180450e5ddfb436718
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/30/2018
ms.locfileid: "50235006"
---
# <a name="garbage-collection"></a>Coleta de Lixo

Xamarin. Android usa do Mono [simples o coletor de lixo](http://www.mono-project.com/docs/advanced/garbage-collector/sgen/). Isso é um coletor de lixo de mark-and-sweep com duas gerações e uma *espaço de objeto grande*, com dois tipos de coleções: 

-   Coleções pequenas (heap de coleta Gen0) 
-   Principais coleções (coleta Gen1 e objeto grande espaço heaps). 

> [!NOTE]
> Na ausência de uma coleção explícita por meio de [GC. Collect ()](xref:System.GC.Collect) coleções são *sob demanda*, com base em alocações de heap. *Isso não é um sistema de contagem de referência*; objetos *não serão coletados, assim como existem referências pendentes*, ou quando um escopo foi encerrado. O GC será executado quando o heap secundário tem memória insuficiente para novas alocações. Se não houver nenhuma alocação, ele não será executado.


Coleções secundárias são baratos e frequente e são usadas para coletar objetos inativos e recentemente alocados. Coleções secundárias são executadas após cada MB alguns dos objetos alocados. Coleções secundárias podem ser executadas manualmente, chamando [GC. Coletar (0)](/dotnet/api/system.gc.collect#System_GC_Collect_System_Int32_) 

Coleções grandes são caros e menos frequentes e são usadas para recuperar todos os objetos inativos. Coleções principais são executadas depois que a memória é esgotada para o tamanho atual do heap (antes de redimensionar o heap). Principais coleções podem ser executadas manualmente, chamando [GC. Coletar ()](xref:System.GC.Collect) ou chamando [GC. Coletar (int)](/dotnet/api/system.gc.collect#System_GC_Collect_System_Int32_) com o argumento [GC. Collect ou](xref:System.GC.MaxGeneration). 



## <a name="cross-vm-object-collections"></a>Coleções de objetos entre VMs

Há três categorias de tipos de objeto.

-   **Objetos gerenciados**: tipos que realizam *não* herdam [Java.Lang.Object](https://developer.xamarin.com/api/type/Java.Lang.Object/) , por exemplo, [System. String](xref:System.String). 
    Esses são normalmente coletadas pelo GC. 

-   **Objetos Java**: tipos de Java que estão presentes no tempo de execução VM Android, mas não exposta à VM Mono. Essas são sem graça e não serão discutidas mais. Esses são coletados normalmente pelo tempo de execução VM Android. 

-   **Objetos de pares**: tipos que implementam [IJavaObject](https://developer.xamarin.com/api/type/Android.Runtime.IJavaObject/) , por exemplo, todos [Java.Lang.Object](https://developer.xamarin.com/api/type/Java.Lang.Object/) e [Throwable](https://developer.xamarin.com/api/type/Java.Lang.Throwable/) subclasses. As instâncias desses tipos têm dois "halfs" um *par gerenciada* e um *par nativo*. O par gerenciado é uma instância do C# classe. O par nativo é uma instância de uma classe de Java no tempo de execução VM, Android e o C# [IJavaObject.Handle](https://developer.xamarin.com/api/property/Android.Runtime.IJavaObject.Handle/) propriedade contém uma referência global de JNI para o par nativo. 


Há dois tipos de colegas nativos:

-   **Pares de estrutura** : os tipos "Normal" Java que não sabem nada do xamarin. Android, por exemplo, [android.content.Context](https://developer.xamarin.com/api/type/Android.Content.Context/).

-   **Pares de usuário** : [Callable Wrappers do Android](~/android/platform/java-integration/working-with-jni.md) que é gerado no momento da compilação para cada subclasse Java.Lang.Object presente dentro do aplicativo.


Como há duas VMs dentro de um processo de xamarin. Android, há dois tipos de coletas de lixo:

-   Coleções de tempo de execução Android 
-   Coleções do mono 

Coleções de tempo de execução Android operam normalmente, mas com uma limitação: uma referência global de JNI é tratada como uma raiz GC. Consequentemente, se houver um JNI global referenciar ocupar um tempo de execução do Android objeto da VM, o objeto *não é possível* ser coletados, mesmo que é qualificado para coleta.

Mono coleções são onde acontece a diversão. Objetos gerenciados são coletados normalmente. Objetos pares são coletados, executando o seguinte processo:

1.  Todos os objetos de ponto a ponto qualificados para coleta Mono têm sua referência global de JNI substituída por uma referência de global fraca JNI. 

2.  Um tempo de execução do Android VM GC é invocado. Qualquer instância de ponto a ponto nativo pode ser coletada. 

3.  As referências de globais fracas da JNI criadas em (1) são verificadas. Se a referência fraca foi coletada, o objeto de ponto a ponto é coletado. Se tiver a referência fraca *não* foram coletados, em seguida, a referência fraca é substituída por uma referência global de JNI e o objeto par não é coletado. Observação: na API 14 +, isso significa que o valor retornado de `IJavaObject.Handle` pode ser alterado após um GC. 

O resultado final de tudo isso é que uma instância de um objeto de ponto a ponto ao vivo enquanto ele é referenciado por qualquer um de código gerenciado (por exemplo, armazenados em um `static` variável) ou referenciado pelo código Java. Além disso, o tempo de vida de seus colegas nativos será estendido além do que eles seriam caso contrário, ao vivo, o par nativo não será coleção até que o par nativo e o par de gerenciado são colecionáveis.


## <a name="object-cycles"></a>Ciclos de objeto

Objetos de mesmo nível estão logicamente presentes dentro do tempo de execução do Android e Mono da VM. Por exemplo, um [Android.App.Activity](https://developer.xamarin.com/api/type/Android.App.Activity/) instância de par gerenciado terá um correspondente [android.app.Activity](http://developer.android.com/reference/android/app/Activity.html) instância de Java do framework ponto a ponto. Todos os objetos que herdam [Java.Lang.Object](https://developer.xamarin.com/api/type/Java.Lang.Object/) é esperado que têm representações em ambas as VMs. 

Todos os objetos que têm representação em ambas as VMs terão vidas úteis que são estendidas em comparação com os objetos que estão presentes apenas em uma única VM (como uma [ `System.Collections.Generic.List<int>` ](xref:System.Collections.Generic.List%601)). Chamar [GC. Coletar](xref:System.GC.Collect) necessariamente não coletará esses objetos, como o GC do xamarin. Android precisa garantir que o objeto não é referenciado por qualquer VM antes de coletar a ele. 

Para encurtar o tempo de vida do objeto [Java.Lang.Object.Dispose()](https://developer.xamarin.com/api/member/Java.Lang.Object.Dispose/) deve ser invocado. Isso será manualmente "servidor" a conexão no objeto entre as duas VMs ao liberar a referência global, permitindo que os objetos a serem coletados com mais rapidez. 


## <a name="automatic-collections"></a>Coleções automática

Começando com [versão 4.1.0](https://developer.xamarin.com/releases/android/mono_for_android_4/mono_for_android_4.1.0), xamarin. Android executa automaticamente um GC completo quando um limite de gref é ultrapassado. Esse limite é de 90% do grefs máximo conhecidos para a plataforma: 1800 grefs no emulador (máximo de 2000) e 46800 grefs no hardware (52000 máximo). *Observação:* xamarin. Android conta apenas o grefs criados pelo [Android.Runtime.JNIEnv](https://developer.xamarin.com/api/type/Android.Runtime.JNIEnv/)e não saberá sobre quaisquer outros grefs criada no processo. Isso é uma heurística *apenas*. 

Quando uma coleta automática é executada, uma mensagem semelhante à seguinte será impresso para o log de depuração:

```shell
I/monodroid-gc(PID): 46800 outstanding GREFs. Performing a full GC!
```

A ocorrência de que isso é não determinística e pode ocorrer em momentos inoportunos (por exemplo, no meio de renderização de gráficos). Se você vir essa mensagem, você talvez queira realizar uma coleção explícita em outro lugar ou você talvez queira tentar [reduzir o tempo de vida de objetos pares](#Helping_the_GC). 

## <a name="gc-bridge-options"></a>Opções de ponte de GC

Xamarin. Android oferece gerenciamento de memória transparente com Android e o tempo de execução do Android. Ele é implementado como uma extensão para o coletor de lixo Mono chamada a *GC ponte*. 

A ponte de GC funciona durante uma coleta de lixo Mono e de figuras out qual par objetos precisa seu verificada com o heap do tempo de execução Android "execução". A ponte de GC faz essa determinação seguindo as etapas a seguir (em ordem):

1.  Induza o grafo de referência mono de objetos pares inacessível para os objetos de Java que eles representam. 

2.  Execute um GC de Java.

3.  Verifique se os objetos que estão realmente inativos. 

Esse processo complicado é o que permite que as subclasses `Java.Lang.Object` para referência livremente qualquer um dos objetos; ele remove quaisquer restrições em quais Java objetos podem ser associados ao C#. Devido a essa complexidade, o processo de ponte pode ser muito caro e pode causar pausas perceptíveis em um aplicativo. Se o aplicativo está apresentando pausas significativas, vale a pena investigar um dos três implementações de ponte de GC a seguir: 

-   **Tarjan** -um design completamente novo da ponte GC com base no [algoritmo de Robert Tarjan e propagação de referência com versões anteriores](http://en.wikipedia.org/wiki/Tarjan's_strongly_connected_components_algorithm).
    Ele tem o melhor desempenho em nossas cargas de trabalho simuladas, mas ele também tem o maior compartilhamento de código experimental. 

-   **Novos** -uma revisão principal do código original, correção de duas instâncias do comportamento quadrática mas mantendo o algoritmo principal (com base no [algoritmo do Kosaraju](http://en.wikipedia.org/wiki/Kosaraju's_algorithm) para encontrar fortemente conectados componentes). 

-   **Antigo** -a implementação original (considerada a mais estável dos três). Isso é a ponte que um aplicativo deve usar se o `GC_BRIDGE` pausas são aceitáveis. 


É a única maneira de descobrir o que funciona a ponte de GC melhor ao testar em um aplicativo e analisar a saída. Há duas maneiras de coletar os dados para parâmetros de comparação: 

-   **Habilitar registro em log** -habilitar o log (conforme descrito em de [configuração](~/android/internals/garbage-collection.md) seção) para cada opção de ponte de GC, em seguida, capturar e comparar as saídas de log de cada configuração. Inspecione o `GC` mensagens para cada opção, em particular, o `GC_BRIDGE` mensagens. Faz uma pausa até 150 ms para aplicativos não interativos toleráveis, mas pausa acima de 60 MS para aplicativos muito interativos (por exemplo, jogos) é um problema. 

-   **Habilitar as estatísticas de ponte** -estatísticas de ponte exibirá o custo médio dos objetos apontados por cada objeto envolvido no processo de ponte. Essas informações de classificação por tamanho fornecerá dicas sobre o que está fazendo a maior quantidade de objetos extras. 


Para especificar quais `GC_BRIDGE` opção de um aplicativo deve usar, passe `bridge-implementation=old`, `bridge-implementation=new` ou `bridge-implementation=tarjan` para o `MONO_GC_PARAMS` variável de ambiente, por exemplo: 

```shell
MONO_GC_PARAMS=bridge-implementation=tarjan
```

A configuração padrão é **Tarjan**. Se você encontrar uma regressão, talvez seja necessário definir essa opção como **antigo**. Além disso, você pode optar por usar o mais estável **antigo** se a opção **Tarjan** não produz uma melhoria no desempenho. 

<a name="Helping_the_GC" />

## <a name="helping-the-gc"></a>Ajudar o GC

Há várias maneiras para ajudar a reduzir os tempos de coleta e uso de memória o GC.



### <a name="disposing-of-peer-instances"></a>Descarte de instâncias de ponto a ponto

O GC tem uma visão incompleta do processo e não pode ser executado quando a memória é baixa porque o GC não sabe que a memória é baixa. 

Por exemplo, uma instância de um [Java.Lang.Object](https://developer.xamarin.com/api/type/Java.Lang.Object/) tipo ou tipo derivado tem pelo menos 20 bytes de tamanho (sujeitas a alterações sem aviso prévio, etc., etc.). 
[Gerenciado Callable Wrappers](~/android/internals/architecture.md) não adicionar membros de instância adicional, portanto, quando você tem uma [Android.Graphics.Bitmap](https://developer.xamarin.com/api/type/Android.Graphics.Bitmap/) instância que se refere a um blob de 10MB de memória, GC do xamarin. Android não saberá que &ndash; o GC verá um objeto de 20 bytes e não será possível determinar o que ele está vinculado ao Android objetos alocados no tempo de execução que está impedindo a 10MB de memória ativa. 

É necessário com frequência ajudar o GC. Infelizmente, *GC. AddMemoryPressure()* e *GC. RemoveMemoryPressure()* não têm suporte, portanto, se você *sabe* que você acaba de liberar um grafo de objetos grandes alocados em Java que talvez você precise chamar manualmente [GC. Collect ()](xref:System.GC.Collect) ao prompt um GC para liberar o lado do Java memória, ou você pode explicitamente descarto *Java.Lang.Object* subclasses, quebrando o mapeamento entre o callable wrapper gerenciado e a instância de Java. Por exemplo, consulte [1084 Bug](http://bugzilla.xamarin.com/show_bug.cgi?id=1084#c6). 


> [!NOTE]
> Você deve ser *extremamente* cuidadoso ao descartar `Java.Lang.Object` instâncias de subclasse.

Para minimizar a possibilidade de corrupção de memória, observe as seguintes diretrizes ao chamar `Dispose()`.


#### <a name="sharing-between-multiple-threads"></a>Compartilhando entre vários Threads

Se o *Java ou gerenciados* instância pode ser compartilhada entre vários threads *não deve ser `Dispose()`1!d*, **nunca**. Por exemplo, [`Typeface.Create()`](https://developer.xamarin.com/api/member/Android.Graphics.Typeface.Create/(System.String%2cAndroid.Graphics.TypefaceStyle)) 
pode retornar um *instância armazenada em cache*. Se vários threads fornecem os mesmos argumentos, eles obterão os *mesmo* instância. Consequentemente, `Dispose()`ing do `Typeface` instância de um thread pode invalidar outros threads, o que podem resultar em `ArgumentException`s de `JNIEnv.CallVoidMethod()` (entre outros) porque a instância foi descartada de outro thread. 


#### <a name="disposing-bound-java-types"></a>Descartando tipos Java acoplado

Se a instância for de um tipo associado do Java, a instância pode ser descartada *, desde* a instância não será reutilizada do código gerenciado *e* a instância de Java não pode ser compartilhada entre threads (consulte anterior `Typeface.Create()` discussão). (Tomar essa decisão pode ser difícil.) Na próxima vez em que a instância de Java entra em código gerenciado, uma *novo* wrapper será criado para ele. 

Isso é frequentemente útil quando se trata de Desenháveis e outras instâncias com uso intenso de recursos:

```csharp
using (var d = Drawable.CreateFromPath ("path/to/filename"))
    imageView.SetImageDrawable (d);
```

As opções acima é seguro porque a ponto a ponto que [Drawable.CreateFromPath()](https://developer.xamarin.com/api/member/Android.Graphics.Drawables.Drawable.CreateFromPath/) retorna fará referência a um par de Framework, *não* um par de usuário. O `Dispose()` chamar no final do `using` bloco interromperá a relação entre o gerenciado [Drawable](https://developer.xamarin.com/api/type/Android.Graphics.Drawables.Drawable/) e o framework [Drawable](http://developer.android.com/reference/android/graphics/drawable/Drawable.html) instâncias, permitindo que a instância de Java para ser coletados assim que o tempo de execução do Android precisa. Isso seria *não* ser seguro se a instância de ponto a ponto chamada de um par de usuário; aqui, estamos usando as informações de "externo" para *sabe* que o `Drawable` não pode se referir a um par de usuário e, portanto, o `Dispose()` chamar é seguro. 


#### <a name="disposing-other-types"></a>Descartando outros tipos 

Se a instância se refere a um tipo que não é uma associação de um tipo de Java (como um personalizado `Activity`), **não faça** chamar `Dispose()` , a menos que você *sabe* nenhum código Java chamará métodos substituídos em que instância. Falha ao fazer isso resulta em [ `NotSupportedException`s](~/android/internals/architecture.md#Premature_Dispose_Calls). 

Por exemplo, se você tiver personalizado clique ouvinte:

```csharp
partial class MyClickListener : Java.Lang.Object, View.IOnClickListener {
    // ...
}
```

Você *não deve* dispose desta instância, como Java tentará chamar métodos no futuro:

```csharp
// BAD CODE; DO NOT USE
Button b = FindViewById<Button> (Resource.Id.myButton);
using (var listener = new MyClickListener ())
    b.SetOnClickListener (listener);
```


#### <a name="using-explicit-checks-to-avoid-exceptions"></a>Usando verificações explícitas para evitar exceções

Se você já implementou um [Java.Lang.Object.Dispose](https://developer.xamarin.com/api/member/Java.Lang.Object.Dispose(System.Boolean)/) sobrecarga de método, evite tocar em objetos que envolvem JNI. Isso pode criar uma *dispose dupla* situação que torna possível para seu código (fatalmente) tentam acessar um objeto Java subjacente que já foi coletado como lixo. Isso gera uma exceção similar à seguinte: 

```shell
System.ArgumentException: 'jobject' must not be IntPtr.Zero.
Parameter name: jobject
at Android.Runtime.JNIEnv.CallVoidMethod
```

Essa situação geralmente ocorre quando o primeiro descarte de um objeto faz com que um membro se torne nulo e, em seguida, uma tentativa de acesso subsequente neste membro nulo causa uma exceção seja lançada. Especificamente, do objeto `Handle` (que é vinculado a uma instância gerenciada para sua instância subjacente do Java) será invalidado a primeira dispose, mas ainda tentará a código gerenciado acessar essa instância de Java subjacente, mesmo que ele não está mais disponível (consulte [ Gerenciado Callable Wrappers](~/android/internals/architecture.md#Managed_Callable_Wrappers) para obter mais informações sobre o mapeamento entre as instâncias de Java e instâncias gerenciadas). 

Uma boa maneira de evitar essa exceção é verificar explicitamente no seu `Dispose` método o mapeamento entre a instância gerenciada e a instância de Java subjacente ainda é válido; que é, verifique se o objeto `Handle` é nulo (`IntPtr.Zero`) antes de acessar seus membros. Por exemplo, a seguinte `Dispose` método acessa uma `childViews` objeto: 

```csharp
class MyClass : Java.Lang.Object, ISomeInterface 
{
    protected override void Dispose (bool disposing)
    {
        base.Dispose (disposing);
        for (int i = 0; i < this.childViews.Count; ++i)
        {
            // ...
        }
    }
}
```

Se um dispose inicial passar causas `childViews` para ter um inválido `Handle`, o `for` acesso de loop lançará um `ArgumentException`. Adicionando um explícito `Handle` nulo seleção antes da primeira `childViews` acessar os seguintes `Dispose` método impede que a exceção ocorra: 

```csharp
class MyClass : Java.Lang.Object, ISomeInterface 
{
    protected override void Dispose (bool disposing)
    {
        base.Dispose (disposing);

        // Check for a null handle:
        if (this.childViews.Handle == IntPtr.Zero)
            return;

        for (int i = 0; i < this.childViews.Count; ++i)
        {
            // ...
        }
    }
}
```


### <a name="reduce-referenced-instances"></a>Reduzir instâncias referenciadas

Sempre que uma instância de um `Java.Lang.Object` subclasse ou tipo é verificada durante o GC, todo o *grafo de objeto* que a instância se refere a também deve ser verificado. O gráfico do objeto é o conjunto de instâncias de objeto que a instância"raiz" se refere, *plus* tudo referenciado pela instância raiz se refere, recursivamente. 

Considere a seguinte classe:

```csharp
class BadActivity : Activity {

    private List<string> strings;

    protected override void OnCreate (Bundle bundle)
    {
        base.OnCreate (bundle);

        strings.Value = new List<string> (
                Enumerable.Range (0, 10000)
                .Select(v => new string ('x', v % 1000)));
    }
}
```

Quando `BadActivity` é construído, o grafo de objeto irá conter 10004 instâncias (1x `BadActivity`, 1X `strings`, 1X `string[]` mantido por `strings`, 10000 x instâncias da cadeia de caracteres), *todos os* de que precisará ser verificado sempre que o `BadActivity` instância é verificada. 

Isso pode ter impacto negativo em seus tempos de coleção, aumentando a tempos de pausa do GC. 

Você pode ajudar o GC por *reduzindo* o tamanho dos gráficos de objetos que estão vinculados por instâncias de mesmo nível de usuário. No exemplo acima, isso pode ser feito movendo `BadActivity.strings` em uma classe separada que não herda de Java.Lang.Object: 

```csharp
class HiddenReference<T> {

    static Dictionary<int, T> table = new Dictionary<int, T> ();
    static int idgen = 0;

    int id;

    public HiddenReference ()
    {
        lock (table) {
            id = idgen ++;
        }
    }

    ~HiddenReference ()
    {
        lock (table) {
            table.Remove (id);
        }
    }

    public T Value {
        get { lock (table) { return table [id]; } }
        set { lock (table) { table [id] = value; } }
    }
}

class BetterActivity : Activity {

    HiddenReference<List<string>> strings = new HiddenReference<List<string>>();

    protected override void OnCreate (Bundle bundle)
    {
        base.OnCreate (bundle);

        strings.Value = new List<string> (
                Enumerable.Range (0, 10000)
                .Select(v => new string ('x', v % 1000)));
    }
}
```


## <a name="minor-collections"></a>Coleções pequenas

Coleções secundárias podem ser executadas manualmente, chamando [GC. Collect(0)](xref:System.GC.Collect). Coleções secundárias são baratas (quando comparado às coleções principais), mas têm um impacto significativo fixo custo, portanto, você não deseja dispará-los com muita frequência e deve ter um tempo de pausa de alguns milissegundos. 

Se seu aplicativo tem um "ciclo de imposto" em que a mesma coisa é feita repetidamente, pode ser aconselhável executar manualmente uma coleção de pequena depois que o ciclo de serviço foi encerrada. Ciclos de serviço de exemplo incluem: 

-  O ciclo de renderização de um único quadro de jogo.
-  Toda interação com uma caixa de diálogo de determinado aplicativo (Abrir, preenchimento, fechamento) 
-  Um grupo de solicitações de rede/sincronização de atualização de dados do aplicativo.



## <a name="major-collections"></a>Principais coleções

Principais coleções podem ser executadas manualmente, chamando [GC. Collect ()](xref:System.GC.Collect) ou `GC.Collect(GC.MaxGeneration)`. 

Eles devem ser executados raramente e podem ter um tempo de pausa de um segundo em um dispositivo Android estilo durante a coleta de um heap de 512MB. 

Principais coleções devem apenas ser invocadas manualmente, ou nunca: 

-   No final do imposto longo ciclos e quando uma longa pausa não apresentam um problema para o usuário. 

-   Dentro de um substituída [Android.App.Activity.OnLowMemory()](https://developer.xamarin.com/api/member/Android.App.Activity.OnLowMemory/) método. 



## <a name="diagnostics"></a>Diagnóstico

Para controlar quando as referências de globais são criadas e destruídas, você pode definir as [debug.mono.log](~/android/troubleshooting/index.md) propriedade do sistema para conter [ *gref* ](~/android/troubleshooting/index.md) e/ou [ *gc*](~/android/troubleshooting/index.md). 



## <a name="configuration"></a>Configuração

O coletor de lixo do xamarin. Android pode ser configurado definindo o `MONO_GC_PARAMS` variável de ambiente. Variáveis de ambiente podem ser definidas com uma ação de Build [AndroidEnvironment](~/android/deploy-test/environment.md).

O `MONO_GC_PARAMS` variável de ambiente é uma lista separada por vírgulas dos seguintes parâmetros: 

-   `nursery-size` = *tamanho* : define o tamanho do berçário. O tamanho é especificado em bytes e deve ser uma potência de dois. Os sufixos `k` , `m` e `g` pode ser usado para especificar quilo, megabytes e gigabytes, respectivamente. O berçário é a primeira geração (dois). Um maior berçário geralmente vai acelerar o programa, mas obviamente usarão mais memória. O berçário padrão 512 kb de tamanho. 

-   `soft-heap-limit` = *tamanho* : O máximo de destino gerenciados consumo de memória para o aplicativo. Quando o uso de memória está abaixo do valor especificado, o GC é otimizado para o tempo de execução (menos coleções). 
    Acima desse limite, o GC é otimizado para o uso de memória (mais coleções). 

-   `evacuation-threshold` = *limite* : define o limite de evacuação em porcentagem. O valor deve ser um inteiro no intervalo de 0 a 100. O padrão é 66. Se a fase de limpeza da coleção de encontrar o que a ocupação de um tipo de bloco de heap específico é menor que esse percentual, ele fará uma cópia coleção para esse tipo de bloco na próxima coleção principais, restaurando assim ocupação para perto de 100 por cento. Um valor 0 desativa a evacuação. 

-   `bridge-implementation` = *implementação de uma ponte* : isso definirá a opção de ponte de GC para ajudar o GC de solucionar problemas de desempenho. Há três valores possíveis: *antigo* , *novos* , *tarjan*.

-   `bridge-require-precise-merge`: O Tarjan ponte contém uma otimização que pode, em raras ocasiões, fazer um objeto seja coletado um GC depois que ele primeiro se torna lixo. Incluindo essa opção desabilita essa otimização, tornando os GCs mais previsível, mas potencialmente mais lento.

Por exemplo, para configurar o GC para ter um limite de tamanho de heap de 128MB, adicione um novo arquivo ao seu projeto com um **ação de Build** de `AndroidEnvironment` com o conteúdo: 

```shell
MONO_GC_PARAMS=soft-heap-limit=128m
```
