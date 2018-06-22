---
title: Coleta de Lixo
ms.prod: xamarin
ms.assetid: 298139E2-194F-4A58-BC2D-1D22231066C4
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 03/15/2018
ms.openlocfilehash: 49bb340edcad0c5ce39a2d9db6da72d488a114b1
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/04/2018
ms.locfileid: "30772962"
---
# <a name="garbage-collection"></a>Coleta de Lixo

Xamarin usa da Mono [coletor de lixo geracional simples](http://www.mono-project.com/docs/advanced/garbage-collector/sgen/). Este é um coletor de lixo de varredura marca com duas gerações e um *espaço de objeto grande*, com dois tipos de coleções: 

-   Coleções secundárias (coleta Gen0 heap) 
-   Coleções principais (objeto grande e coleta Gen1 espaço heaps). 

> [!NOTE]
> Na ausência de uma coleção explícita por meio de [GC. Collect ()](xref:System.GC.Collect) coleções são *sob demanda*, com base em alocações de heap. *Isso não é um sistema de contagem de referência*; objetos *não serão coletados como existem referências pendentes*, ou quando um escopo foi encerrado. O GC será executado quando a pilha secundária ficou sem memória para alocações de novo. Se não houver nenhuma alocação, ele não será executado.


Coleções secundárias são baratas e frequentes e são usadas para coletar objetos recentemente alocados e inativos. Coleções secundárias são executadas após cada MB alguns dos objetos alocados. Coleções secundárias podem ser executadas chamando [GC. Coletar (0)](/dotnet/api/system.gc.collect#System_GC_Collect_System_Int32_) 

Coleções principais são menos frequentes e caro e são usadas para recuperar todos os objetos inativos. Coleções principais são executadas quando a memória é esgotada para o tamanho do heap atual (antes de redimensionar o heap). Coleções principais podem ser executadas chamando [GC. Coletar ()](xref:System.GC.Collect) ou chamando [GC. Coletar (int)](/dotnet/api/system.gc.collect#System_GC_Collect_System_Int32_) com o argumento [GC. Collect ou](xref:System.GC.MaxGeneration). 



## <a name="cross-vm-object-collections"></a>VM de várias coleções de objetos

Há três categorias de tipos de objeto.

-   **Objetos gerenciados**: tipos que *não* herdam [Java.Lang.Object](https://developer.xamarin.com/api/type/Java.Lang.Object/) , por exemplo, [System. String](xref:System.String). 
    Esses são normalmente coletados pelo GC. 

-   **Objetos Java**: tipos de Java que estão presentes no tempo de execução Android VM, mas não exposta à VM Mono. Essas são entediante e não serão discutidas mais. Esses são normalmente coletados pelo tempo de execução Android VM. 

-   **Objetos de mesmo nível**: tipos que implementam [IJavaObject](https://developer.xamarin.com/api/type/Android.Runtime.IJavaObject/) , por exemplo, todos os [Java.Lang.Object](https://developer.xamarin.com/api/type/Java.Lang.Object/) e [Throwable](https://developer.xamarin.com/api/type/Java.Lang.Throwable/) subclasses. Instâncias desses tipos tem dois "halfs" um *par gerenciada* e um *par nativo*. O par gerenciado é uma instância da classe c#. O par nativo é uma instância de uma classe Java no tempo de execução Android VM e c# [IJavaObject.Handle](https://developer.xamarin.com/api/property/Android.Runtime.IJavaObject.Handle/) propriedade contém uma referência JNI global para o par nativo. 


Há dois tipos de pares nativo:

-   **Pares de Framework** : tipos de Java "Normal" que não sabem nada de xamarin, por exemplo, [android.content.Context](https://developer.xamarin.com/api/type/Android.Content.Context/).

-   **Pares de usuário** : [Android Callable Wrappers](~/android/platform/java-integration/working-with-jni.md) que é gerado no momento da compilação para cada subclasse Java.Lang.Object presente no aplicativo.


Como existem duas VMs dentro de um processo de xamarin, há dois tipos de coletas de lixo:

-   Coleções de tempo de execução Android 
-   Coleções mono 

Coleções de tempo de execução Android operam normalmente, mas com uma limitação: uma referência JNI global é tratada como uma raiz GC. Consequentemente, se houver um JNI global referência mantiver um tempo de execução Android objeto da VM, o objeto *não é possível* coletadas, mesmo se o caso contrário é qualificada para a coleta.

Coleções mono são onde acontece a fun. Objetos gerenciados são coletados normalmente. Objetos pares são coletados, executando o seguinte processo:

1.  Todos os objetos de ponto a ponto qualificados para a coleta Mono têm sua referência global JNI substituída por uma referência de global JNI fraca. 

2.  Um tempo de execução Android que VM GC é invocado. Qualquer instância de ponto a ponto nativo pode ser coletada. 

3.  As JNI referências fracas e globais criadas em (1) são verificadas. Se a referência fraca foi coletada, o objeto de ponto a ponto é coletado. Se a referência fraca tem *não* foi coletado, em seguida, a referência fraca é substituída por uma referência JNI global e o objeto de ponto a ponto não é coletado. Observação: no API 14+, isso significa que o valor retornado de `IJavaObject.Handle` podem ser alterados após um GC. 

O resultado final de tudo isso é que uma instância de um objeto de ponto a ponto operará como ela é referenciada por qualquer um de código gerenciado (por exemplo, armazenados em um `static` variável) ou referenciados pelo código de Java. Além disso, o tempo de vida de pares nativo será estendido além do que seriam em caso contrário ao vivo, o par nativo não será coleção até que o par nativo e gerenciado par coleção.


## <a name="object-cycles"></a>Ciclos de objeto

Objetos pares são logicamente presentes no tempo de execução do Android e Mono da VM. Por exemplo, um [Android.App.Activity](https://developer.xamarin.com/api/type/Android.App.Activity/) instância par gerenciada terá correspondente [android.app.Activity](http://developer.android.com/reference/android/app/Activity.html) instância de Java do framework ponto a ponto. Todos os objetos que herdam de [Java.Lang.Object](https://developer.xamarin.com/api/type/Java.Lang.Object/) podem ser esperadas ter representações em ambas as VMs. 

Todos os objetos que têm representação em ambas as VMs terão tempos de vida que são estendidos em comparação aos objetos que existem somente em uma única VM (como um [ `System.Collections.Generic.List<int>` ](xref:System.Collections.Generic.List%601)). Chamando [GC. Coletar](xref:System.GC.Collect) necessariamente não coletará esses objetos, como o GC xamarin precisa garantir que o objeto não é referenciado por qualquer VM antes de coletá-lo. 

Para reduzir o tempo de vida do objeto, [Java.Lang.Object.Dispose()](https://developer.xamarin.com/api/member/Java.Lang.Object.Dispose/) deve ser chamado. Isso manualmente "cortará" a conexão no objeto entre as duas VMs liberando referência global, permitindo que os objetos a serem coletadas mais rapidamente. 


## <a name="automatic-collections"></a>Coleções automática

Começando com [versão 4.1.0](https://developer.xamarin.com/releases/android/mono_for_android_4/mono_for_android_4.1.0), xamarin executa um GC total automaticamente quando um limite de gref é cruzado. Esse limite é de 90% do grefs máximo conhecidos para a plataforma: 1800 grefs no emulador (máximo de 2000) e 46800 grefs no hardware (52000 máximo). *Observação:* xamarin conta apenas o grefs criado por [Android.Runtime.JNIEnv](https://developer.xamarin.com/api/type/Android.Runtime.JNIEnv/)e não saberá sobre qualquer outro grefs criado no processo. Isso é uma heurística *somente*. 

Quando uma coleta automática é executada, uma mensagem semelhante à seguinte será impressa no log de depuração:

```shell
I/monodroid-gc(PID): 46800 outstanding GREFs. Performing a full GC!
```

A ocorrência desse é não determinística e pode ocorrer em momentos inoportunos (por exemplo, no meio de processamento gráfico). Se você vir essa mensagem, você pode desejar executar uma coleção explícita em outro lugar ou convém tentar [reduzir o tempo de vida de objetos pares](#Helping_the_GC). 

## <a name="gc-bridge-options"></a>Opções de ponte de GC

Xamarin oferece gerenciamento de memória transparente com Android e o tempo de execução do Android. Ele é implementado como uma extensão para o coletor de lixo Mono chamado o *ponte GC*. 

A ponte de GC funciona durante uma coleta de lixo Mono e valores de limite que ponto a ponto objetos precisa seu verificada com o heap de tempo de execução Android "execução". A ponte de GC faz essa determinação seguindo as etapas a seguir (em ordem):

1.  Induza o gráfico de referência mono de objetos pares inacessível para os objetos de Java que eles representam. 

2.  Execute um GC Java.

3.  Verifique se os objetos que estão realmente inativos. 

Esse processo complicado é o que permite que as subclasses de `Java.Lang.Object` para referência livremente quaisquer objetos; remove quaisquer restrições em qual Java objetos podem ser associados a c#. Devido a essa complexidade, o processo de ponte pode ser muito caro e pode causar notável pausa em um aplicativo. Se o aplicativo estiver apresentando pausa significativa, vale a pena investigar um dos três implementações de ponte de GC a seguir: 

-   **Tarjan** -um completamente novo design da ponte GC com base em [algoritmo de Robert Tarjan e referência com versões anteriores a propagação](http://en.wikipedia.org/wiki/Tarjan's_strongly_connected_components_algorithm).
    Ele tem o melhor desempenho em nosso simuladas cargas de trabalho, mas ele também tem o maior compartilhamento de código experimental. 

-   **Novo** -uma revisão principal do código original, correção de duas instâncias do comportamento quadrática e mantendo o algoritmo principal (com base em [algoritmo do Kosaraju](http://en.wikipedia.org/wiki/Kosaraju's_algorithm) Localizando fortemente conectados componentes). 

-   **Antigo** -a implementação original (considerada mais estável dos três). Isso é a ponte que um aplicativo deve usar se o `GC_BRIDGE` pausa é aceitável. 


A única maneira de descobrir qual funciona a ponte de GC melhor é testar em um aplicativo e analisar a saída. Há duas maneiras para coletar os dados para analisar o desempenho: 

-   **Habilitar o log** -habilitar o log (conforme descrito no [configuração](~/android/internals/garbage-collection.md) seção) para cada opção de ponte de GC, em seguida, capturar e comparar as saídas de log de cada configuração. Inspecione o `GC` mensagens para cada opção; em particular, o `GC_BRIDGE` mensagens. Faz uma pausa até 150 ms para aplicativos não interativos são toleráveis, mas pausa acima seja, 60 MS para aplicativos muito interativos (por exemplo, jogos) é um problema. 

-   **Habilitar estatísticas de ponte** -estatísticas de ponte exibirá o custo médio dos objetos apontada por cada objeto envolvido no processo de ponte. Essas informações de classificação por tamanho fornecem dicas sobre o que está mantendo a maior quantidade de objetos extras. 


Para especificar qual `GC_BRIDGE` opção um aplicativo deve usar, transmitir `bridge-implementation=old`, `bridge-implementation=new` ou `bridge-implementation=tarjan` para o `MONO_GC_PARAMS` variável de ambiente, por exemplo: 

```shell
MONO_GC_PARAMS=bridge-implementation=tarjan
```

A configuração padrão é **Tarjan**. Se você encontrar uma regressão, talvez seja necessário definir essa opção como **antigo**. Além disso, você pode optar por usar mais estável **antigo** opção se **Tarjan** não produz uma melhoria no desempenho. 

<a name="Helping_the_GC" />

## <a name="helping-the-gc"></a>Ajudando o GC

Há várias maneiras para ajudar o GC para reduzir o tempo de coleta e uso de memória.



### <a name="disposing-of-peer-instances"></a>Descarte de instâncias de ponto a ponto

O GC tem uma visão incompleta do processo e não pode ser executado quando a memória é baixa porque o GC não sabe que a memória é insuficiente. 

Por exemplo, uma instância de um [Java.Lang.Object](https://developer.xamarin.com/api/type/Java.Lang.Object/) tipo ou tipo derivado é pelo menos 20 bytes de tamanho (sujeito a alterações sem aviso prévio, etc., etc.). 
[Gerenciado Callable Wrappers](~/android/internals/architecture.md) não adicionar membros de instância adicional, para quando você tiver um [Android.Graphics.Bitmap](https://developer.xamarin.com/api/type/Android.Graphics.Bitmap/) instância que se refere a um blob de 10MB de memória, GC do xamarin não saberão que &ndash; o GC verá um objeto de 20 bytes e não poderá determinar que ele está vinculado ao Android objetos alocados no tempo de execução que está impedindo a 10MB de memória ativa. 

Frequentemente, é necessário ajudar o GC. Infelizmente, *GC. AddMemoryPressure()* e *GC. RemoveMemoryPressure()* não têm suporte, portanto, se você *saber* que você acaba de liberar um gráfico de objeto grande alocada Java pode ser necessário chamar manualmente [GC. Collect ()](xref:System.GC.Collect) prompt um GC para liberar o lado Java memória, ou você pode explicitamente descartar *Java.Lang.Object* subclasses, interrompendo o mapeamento entre callable wrapper gerenciado e a instância de Java. Por exemplo, consulte [1084 Bug](http://bugzilla.xamarin.com/show_bug.cgi?id=1084#c6). 


> [!NOTE]
> Você deve ser *extremamente* cuidado ao descartar `Java.Lang.Object` instâncias de subclasse.

Para minimizar a possibilidade de corrupção de memória, observe as seguintes diretrizes ao chamar `Dispose()`.


#### <a name="sharing-between-multiple-threads"></a>Compartilhamento entre vários Threads

Se o *Java ou gerenciados* instância pode ser compartilhada entre vários threads, *não deve ser `Dispose()`d*, **nunca**. Por exemplo, [ `Typeface.Create()` ](https://developer.xamarin.com/api/member/Android.Graphics.Typeface.Create/(System.String%2cAndroid.Graphics.TypefaceStyle)) pode retornar um *instância armazenada em cache*. Se vários threads fornecem os mesmos argumentos, obterá o *mesmo* instância. Consequentemente, `Dispose()`ndo do `Typeface` instância de um thread pode invalidar outros threads, o que podem resultar em `ArgumentException`s `JNIEnv.CallVoidMethod()` (entre outros) porque a instância foi descartada de outro thread. 


#### <a name="disposing-bound-java-types"></a>Descartar os tipos de limite Java

Se a instância for de um tipo de Java associado, a instância pode ser descartada de *enquanto* a instância não ser reutilizada de código gerenciado *e* da instância Java não pode ser compartilhada entre threads (consulte anterior `Typeface.Create()` discussão). (Fazer essa determinação pode ser difícil.) Na próxima vez em que a instância de Java entra em código gerenciado, uma *novo* wrapper será criado para ele. 

Isso é frequentemente útil quando se trata de Drawables e outras instâncias intenso de recursos:

```csharp
using (var d = Drawable.CreateFromPath ("path/to/filename"))
    imageView.SetImageDrawable (d);
```

Acima é segura porque a ponto a ponto que [Drawable.CreateFromPath()](https://developer.xamarin.com/api/member/Android.Graphics.Drawables.Drawable.CreateFromPath/) retorna fará referência a um par de estrutura, *não* um par de usuário. O `Dispose()` chamar no final o `using` bloco quebrará a relação entre o gerenciado [Drawable](https://developer.xamarin.com/api/type/Android.Graphics.Drawables.Drawable/) e do framework [Drawable](http://developer.android.com/reference/android/graphics/drawable/Drawable.html) instâncias, permitindo que a instância de Java ser coletados assim que precisa de tempo de execução do Android. Isso seria *não* seja seguro se a instância de ponto a ponto chamado um par de usuário; aqui, estamos usando informações "externas" *saber* que o `Drawable` não pode se referir a um par de usuário e, portanto, o `Dispose()` chamar é seguro. 


#### <a name="disposing-other-types"></a>Descartar os outros tipos 

Se a instância se refere a um tipo que não é uma associação de um tipo de Java (como um personalizado `Activity`), **não** chamar `Dispose()` , a menos que você *saber* nenhum código Java chamará métodos substituídos em que instância. Falha ao fazer isso resulta em [ `NotSupportedException`s](~/android/internals/architecture.md#Premature_Dispose_Calls). 

Por exemplo, se você tiver um personalizado clique ouvinte:

```csharp
partial class MyClickListener : Java.Lang.Object, View.IOnClickListener {
    // ...
}
```

Você *não devem* dispose desta instância, como Java tentará chamar métodos no futuro:

```csharp
// BAD CODE; DO NOT USE
Button b = FindViewById<Button> (Resource.Id.myButton);
using (var listener = new MyClickListener ())
    b.SetOnClickListener (listener);
```


#### <a name="using-explicit-checks-to-avoid-exceptions"></a>Usando verificações explícitas para evitar exceções

Se você já implementou um [Java.Lang.Object.Dispose](https://developer.xamarin.com/api/member/Java.Lang.Object.Dispose(System.Boolean)/) sobrecarregar o método, evite tocar objetos que envolvem JNI. Isso pode criar um *duplo dispose* situação que torna possível que seu código (fatalmente) tenta acessar um objeto de Java subjacente que já foi coletado como lixo. Isso gera uma exceção similar à seguinte: 

```shell
System.ArgumentException: 'jobject' must not be IntPtr.Zero.
Parameter name: jobject
at Android.Runtime.JNIEnv.CallVoidMethod
```

Essa situação geralmente ocorre quando o primeiro descarte de um objeto faz com que um membro se torne nulo e, em seguida, uma tentativa de acesso subsequente neste membro nulo faz com que uma exceção seja lançada. Do especificamente, o objeto `Handle` (que é vinculado a uma instância gerenciada para sua instância de Java subjacente) é invalidado primeiro descarte, mas ainda tentará a código gerenciado acessar esta instância subjacente do Java, mesmo que ele não está mais disponível (consulte [ Gerenciado Callable Wrappers](~/android/internals/architecture.md#Managed_Callable_Wrappers) para obter mais informações sobre o mapeamento entre as instâncias de Java e instâncias gerenciadas). 

Uma boa maneira de evitar essa exceção é verificar explicitamente em seu `Dispose` método que o mapeamento entre a instância gerenciada e a instância de Java subjacente ainda é válido; que é, verifique se o objeto `Handle` é nulo (`IntPtr.Zero`) antes de acessar seus membros. Por exemplo, a seguinte `Dispose` método acessa uma `childViews` objeto: 

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

Se um dispose inicial passar causas `childViews` ter inválido `Handle`, o `for` acesso loop lançará um `ArgumentException`. Adicionando uma explícita `Handle` null verificação antes da primeira `childViews` acessar o seguinte `Dispose` método impede que a exceção ocorra: 

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

Sempre que uma instância de um `Java.Lang.Object` tipo ou a subclasse é verificada durante o GC, todo o *gráfico de objeto* que a instância se refere a também deve ser verificado. O gráfico de objeto é o conjunto de instâncias de objeto que a instância"raiz" refere-se ao, *mais* tudo referenciado pela instância raiz se refere, recursivamente. 

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

Quando `BadActivity` é construída, o gráfico de objeto irá conter 10004 instâncias (1 x `BadActivity`, 1 x `strings`, 1 x `string[]` mantido por `strings`, 10000 x instâncias de cadeia de caracteres), *todos os* de que precisa ser verificado sempre que o `BadActivity` instância é verificada. 

Isso pode ter impacto negativo em suas horas de coleta, resultando em melhor GC pausar vezes. 

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


## <a name="minor-collections"></a>Coleções secundárias

Coleções secundárias podem ser executadas chamando [GC. Collect(0)](xref:System.GC.Collect). Coleções secundárias são baratas (quando comparado às coleções principais), mas tem um impacto significativo fixa custo, portanto você não deseja dispará-los com muita frequência e deve ter um tempo de pausa de alguns milissegundos. 

Se seu aplicativo tiver um "ciclo de imposto" em que a mesma coisa é feita várias vezes, talvez seja aconselhável realizar manualmente uma coleção secundária após o ciclo de serviço foi encerrada. Ciclos de serviço de exemplo incluem: 

-  O ciclo de processamento de uma única estrutura de jogo.
-  Toda interação com uma caixa de diálogo de determinado aplicativo (Abrir, preenchimento, fechar) 
-  Um grupo de solicitações de rede para sincronização/atualização de dados do aplicativo.



## <a name="major-collections"></a>Coleções principais

Coleções principais podem ser executadas chamando [GC. Collect ()](xref:System.GC.Collect) ou `GC.Collect(GC.MaxGeneration)`. 

Eles devem ser executados raramente e podem ter um tempo de pausa de um segundo em um dispositivo Android estilo durante a coleta de um heap de 512MB. 

Coleções principais devem apenas ser invocadas manualmente, se alguma vez: 

-   No final do imposto longo ciclos e quando um longo pausar não representar um problema para o usuário. 

-   Dentro de uma substituição [Android.App.Activity.OnLowMemory()](https://developer.xamarin.com/api/member/Android.App.Activity.OnLowMemory/) método. 



## <a name="diagnostics"></a>Diagnóstico

Para controlar quando referências globais são criadas e destruídas, você pode definir o [debug.mono.log](~/android/troubleshooting/index.md) propriedade do sistema para conter [ *gref* ](~/android/troubleshooting/index.md) e/ou [ *gc*](~/android/troubleshooting/index.md). 



## <a name="configuration"></a>Configuração

O coletor de lixo do xamarin pode ser configurado definindo o `MONO_GC_PARAMS` variável de ambiente. Variáveis de ambiente podem ser definidas com uma ação de compilação de [AndroidEnvironment](~/android/deploy-test/environment.md).

O `MONO_GC_PARAMS` variável de ambiente é uma lista separada por vírgulas dos parâmetros a seguir: 

-   `nursery-size` = *tamanho* : define o tamanho do infantis. O tamanho é especificado em bytes e deve ser uma potência de dois. Os sufixos `k` , `m` e `g` pode ser usado para especificar quilo, megabytes e gigabytes, respectivamente. O infantis é a primeira geração (de dois). Um maior infantis geralmente agiliza o programa mas obviamente usará mais memória. O infantis padrão 512 kb de tamanho. 

-   `soft-heap-limit` = *tamanho* : O número máximo de destino gerenciados consumo de memória para o aplicativo. Quando o uso de memória está abaixo do valor especificado, o GC é otimizado para o tempo de execução (menos coleções). 
    Acima desse limite, o GC é otimizado para o uso de memória (coleções mais). 

-   `evacuation-threshold` = *limite* : define o limite de evacuação em porcentagem. O valor deve ser um número inteiro no intervalo de 0 a 100. O padrão é 66. Se a fase de varredura da coleção localizar se a ocupação de um tipo de bloco de heap específico é menor que essa porcentagem, ele fará uma cópia coleção para esse tipo de bloco na coleção de principais Avançar, restaurando assim ocupação para perto de 100%. Um valor 0 desativa evacuação. 

-   `bridge-implementation` = *implementação de ponte* : isso definirá a opção de GC ponte para ajudar a resolver GC problemas de desempenho. Há três valores possíveis: *antigo* , *novo* , *tarjan*.

-   `bridge-require-precise-merge`: O Tarjan ponte contém uma otimização que pode, em raras ocasiões, fazer com que um objeto a ser coletado um GC depois que ele fica primeiro lixo. Incluindo esta opção desabilita essa otimização, tornando os GCs mais previsível mas potencialmente mais lento.

Por exemplo, para configurar o GC para ter um limite de tamanho de heap de 128MB, adicione um novo arquivo ao seu projeto com um **ação de compilação** de `AndroidEnvironment` com o conteúdo: 

```shell
MONO_GC_PARAMS=soft-heap-limit=128m
```
