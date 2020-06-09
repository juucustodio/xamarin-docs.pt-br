---
title: Coleta de lixo
ms.prod: xamarin
ms.assetid: 298139E2-194F-4A58-BC2D-1D22231066C4
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 03/15/2018
ms.openlocfilehash: f8a64a68be042268860889357e46b42612795635
ms.sourcegitcommit: 93e6358aac2ade44e8b800f066405b8bc8df2510
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/09/2020
ms.locfileid: "84567951"
---
# <a name="garbage-collection"></a>Coleta de lixo

O Xamarin. Android usa o [coletor de lixo de geração simples](https://www.mono-project.com/docs/advanced/garbage-collector/sgen/)do mono. Este é um coletor de lixo de Mark-and-Sweep com duas gerações e um *espaço de objeto grande*, com dois tipos de coleções: 

- Coleções secundárias (coleta heap Gen0) 
- Coleções principais (coleta Gen1 e heaps de espaço de objeto grande). 

> [!NOTE]
> Na ausência de uma coleção explícita por [GC. As coleções Collect ()](xref:System.GC.Collect) são *sob demanda*, com base nas alocações de heap. *Este não é um sistema de contagem de referência*; *os objetos não serão coletados assim que não houver nenhuma referência pendente*ou quando um escopo for encerrado. O GC será executado quando o heap secundário ficar sem memória para novas alocações. Se não houver nenhuma alocação, ela não será executada.

As coleções secundárias são baratas e frequentes e são usadas para coletar objetos recentemente alocados e inativos. As coleções secundárias são executadas depois de cada MB de objetos alocados. As coleções secundárias podem ser executadas manualmente chamando [GC. Coletar (0)](/dotnet/api/system.gc.collect#System_GC_Collect_System_Int32_) 

As principais coleções são caras e menos frequentes e são usadas para recuperar todos os objetos inativos. As principais coleções são executadas quando a memória é esgotada para o tamanho atual do heap (antes de redimensionar o heap). As coleções principais podem ser executadas manualmente chamando [GC. Colete ()](xref:System.GC.Collect) ou chamando [GC. Coletar (int)](/dotnet/api/system.gc.collect#System_GC_Collect_System_Int32_) com o argumento [GC. MaxGeneration](xref:System.GC.MaxGeneration). 

## <a name="cross-vm-object-collections"></a>Coleções de objetos de VM cruzada

Há três categorias de tipos de objeto.

- **Objetos gerenciados**: tipos que *não* herdam de [Java. lang. Object](xref:Java.Lang.Object) , por exemplo, [System. String](xref:System.String). 
    Eles são coletados normalmente pelo GC. 

- **Objetos Java**: tipos Java que estão presentes na VM de tempo de execução do Android, mas não são expostos à VM mono. Eles são enfadonhos e não serão mais discutidos. Eles são coletados normalmente pela VM de tempo de execução do Android. 

- **Objetos pares**: tipos que implementam [IJavaObject](xref:Android.Runtime.IJavaObject) , por exemplo, todas as subclasses [Java. lang. Object](xref:Java.Lang.Object) e [Java. lang. Throwable](xref:Java.Lang.Throwable) . As instâncias desses tipos têm duas "metades" um *par gerenciado* e um *par nativo*. O par gerenciado é uma instância da classe C#. O par nativo é uma instância de uma classe Java dentro da VM de tempo de execução do Android e a propriedade C# [IJavaObject. Handle](xref:Android.Runtime.IJavaObject.Handle) contém uma referência global JNI para o par nativo. 

Há dois tipos de pares nativos:

- **Pares de estrutura** : tipos Java "normais" que não conhecem nada do Xamarin. Android, por exemplo,   [Android. Content. Context](xref:Android.Content.Context).

- **Pares do usuário** : [Android callable wrappers](~/android/platform/java-integration/working-with-jni.md) que são gerados no momento da compilação para cada subclasse Java. lang. Object presente no aplicativo.

Como há duas VMs em um processo Xamarin. Android, há dois tipos de coletas de lixo:

- Coleções de tempo de execução do Android 
- Coleções mono 

As coleções de tempo de execução do Android operam normalmente, mas com uma limitação: uma referência global JNI é tratada como uma raiz GC. Consequentemente, se houver uma referência global JNI mantendo um objeto de VM de tempo de execução do Android, o objeto *não poderá* ser coletado, mesmo que ele seja elegível para a coleção.

As coleções mono são onde a diversão acontece. Os objetos gerenciados são coletados normalmente. Os objetos pares são coletados executando o seguinte processo:

1. Todos os objetos pares qualificados para a coleção mono têm sua referência global JNI substituída por uma referência global fraca JNI. 

2. Um GC de VM de tempo de execução do Android é invocado. Qualquer instância de par nativo pode ser coletada. 

3. As referências globais fracas JNI criadas em (1) são verificadas. Se a referência fraca tiver sido coletada, o objeto par será coletado. Se a referência fraca *não* tiver sido coletada, a referência fraca será substituída por uma referência global JNI e o objeto par não será coletado. Observação: na API 14 +, isso significa que o valor retornado de `IJavaObject.Handle` pode ser alterado após um GC. 

O resultado final de tudo isso é que uma instância de um objeto par residirá desde que seja referenciada por um código gerenciado (por exemplo, armazenado em uma `static` variável) ou referenciada pelo código Java. Além disso, o tempo de vida de pares nativos será estendido além do que faria de outra forma, pois o ponto nativo não será coleção até que o par nativo e o ponto gerenciado sejam de coleção.

## <a name="object-cycles"></a>Ciclos de objetos

Os objetos pares estão presentes logicamente no tempo de execução do Android e nas VMs mono. Por exemplo, uma instância de par gerenciado de [Android. app. Activity](xref:Android.App.Activity) terá uma instância de Java de pares de estrutura [Android. app. Activity](https://developer.android.com/reference/android/app/Activity.html) correspondente. Todos os objetos que herdam de [Java. lang. Object](xref:Java.Lang.Object) podem ter representações em ambas as VMs. 

Todos os objetos que têm representação em ambas as VMs terão tempos de vida que são estendidos em comparação com os objetos que estão presentes somente em uma única VM (como, por exemplo, um [`System.Collections.Generic.List<int>`](xref:System.Collections.Generic.List%601) ). Chamando [GC. A coleta](xref:System.GC.Collect) não coletará necessariamente esses objetos, já que o CG Xamarin. Android precisa garantir que o objeto não seja referenciado por nenhuma VM antes de coletá-lo. 

Para reduzir o tempo de vida do objeto, o [Java. lang. Object. Dispose ()](xref:Java.Lang.Object.Dispose) deve ser invocado. Isso criará manualmente "servidor" a conexão no objeto entre as duas VMs, liberando a referência global, permitindo assim que os objetos sejam coletados mais rapidamente. 

## <a name="automatic-collections"></a>Coleções automáticas

A partir da [versão 4.1.0](https://github.com/xamarin/release-notes-archive/blob/master/release-notes/android/mono_for_android_4/mono_for_android_4.1.0/index.md), o Xamarin. Android executa automaticamente um GC completo quando um limite de gref é ultrapassado. Esse limite é de 90% da grefs máxima conhecida para a plataforma: 1800 grefs no emulador (2000 Max) e 46800 grefs no hardware (máximo de 52000). *Observação:* O Xamarin. Android conta apenas o grefs criado pelo [Android. Runtime. JNIEnv](xref:Android.Runtime.JNIEnv)e não saberá sobre qualquer outro grefs criado no processo. Isso é *apenas*uma heurística. 

Quando uma coleção automática é executada, uma mensagem semelhante à seguinte será impressa no log de depuração:

```shell
I/monodroid-gc(PID): 46800 outstanding GREFs. Performing a full GC!
```

A ocorrência disso é não determinística e pode acontecer em inopportune vezes (por exemplo, no meio da renderização de gráficos). Se você vir essa mensagem, talvez queira executar uma coleção explícita em outro lugar ou talvez queira tentar [reduzir o tempo de vida de objetos pares](#Helping_the_GC). 

## <a name="gc-bridge-options"></a>Opções de ponte do GC

O Xamarin. Android oferece gerenciamento de memória transparente com o Android e o tempo de execução do Android. Ele é implementado como uma extensão para o coletor de lixo mono chamado *ponte GC*. 

A ponte GC funciona durante uma coleta de lixo mono e descobre quais objetos pares precisam de sua "dinâmica" verificada com o heap de tempo de execução do Android. A ponte GC faz essa determinação seguindo as etapas a seguir (em ordem):

1. Induzir o grafo de referência mono de objetos pares inacessíveis nos objetos Java que eles representam. 

2. Execute um GC do Java.

3. Verifique quais objetos estão realmente inativos. 

Esse processo complicado é o que permite que as subclasses do façam `Java.Lang.Object` referência a todos os objetos livremente; ela remove quaisquer restrições nas quais os objetos Java possam ser associados ao C#. Devido a essa complexidade, o processo de ponte pode ser muito caro e pode causar pausas perceptíveis em um aplicativo. Se o aplicativo estiver experimentando pausas significativas, vale a pena investigar uma das três implementações de ponte de GC a seguir: 

- **Tarjan** – um design completamente novo da ponte GC com base no [algoritmo de Robert Tarjan e na propagação de referência para versões anteriores](https://en.wikipedia.org/wiki/Tarjan's_strongly_connected_components_algorithm).
    Ele tem o melhor desempenho em nossas cargas de trabalho simuladas, mas também tem a maior parcela do código experimental. 

- **Novo** -uma grande revisão do código original, corrigindo duas instâncias do comportamento de quadrática, mas mantendo o algoritmo principal (com base no [algoritmo do Kosaraju](https://en.wikipedia.org/wiki/Kosaraju's_algorithm) para localizar componentes altamente conectados). 

- **Antiga** -a implementação original (considerada a mais estável das três). Essa é a ponte que um aplicativo deve usar se as `GC_BRIDGE` pausas forem aceitáveis. 

A única maneira de descobrir qual ponte do GC funciona melhor é experimentando um aplicativo e analisando a saída. Há duas maneiras de coletar os dados para o parâmetro de comparação: 

- **Habilitar** log – habilite o registro em log (como descrito na seção de [configuração](~/android/internals/garbage-collection.md) ) para cada opção de ponte do GC e, em seguida, Capture e compare as saídas de log de cada configuração. Inspecione as `GC` mensagens para cada opção; em particular, as `GC_BRIDGE` mensagens. Pausa até 150ms para aplicativos não interativos são tolerável, mas pausas acima 60ms para aplicativos muito interativos (como jogos) são um problema. 

- **Habilitar ponte** de contabilidade – a contabilidade de ponte exibirá o custo médio dos objetos apontados por cada objeto envolvido no processo de ponte. A classificação dessas informações por tamanho fornecerá dicas sobre o que está mantendo a maior quantidade de objetos extras. 

A configuração padrão é **Tarjan**. Se você encontrar uma regressão, poderá ser necessário definir essa opção como **antiga**. Além disso, você pode optar por usar a opção **antiga** mais estável se o **Tarjan** não produzir uma melhoria no desempenho.

Para especificar qual `GC_BRIDGE` opção um aplicativo deve usar, passe `bridge-implementation=old` `bridge-implementation=new` ou `bridge-implementation=tarjan` para a `MONO_GC_PARAMS` variável de ambiente. Isso é feito adicionando um novo arquivo ao seu projeto com uma **ação de compilação** de `AndroidEnvironment` . Por exemplo: 

```shell
MONO_GC_PARAMS=bridge-implementation=tarjan
```

Para obter mais informações, consulte [Configuration](#configuration).

<a name="Helping_the_GC"></a>

## <a name="helping-the-gc"></a>Ajudando o GC

Há várias maneiras de ajudar o GC a reduzir o uso de memória e os tempos de coleta.

### <a name="disposing-of-peer-instances"></a>Descartando instâncias de pares

O GC tem uma exibição incompleta do processo e pode não ser executado quando a memória está baixa porque o GC não sabe que a memória está baixa. 

Por exemplo, uma instância de um tipo [Java. lang. Object](xref:Java.Lang.Object) ou tipo derivado tem pelo menos 20 bytes de tamanho (sujeito a alterações sem aviso prévio, etc.). 
[Wrappers callable gerenciados](~/android/internals/architecture.md) não adicionam membros de instância adicionais, portanto, quando você tem uma instância de [Android. Graphics. bitmap](xref:Android.Graphics.Bitmap) que se refere a um blob de 10 MB de memória, o GC do Xamarin. Android não saberá que &ndash; o GC verá um objeto de 20 bytes e não conseguirá determinar se ele está vinculado aos objetos alocados no tempo de execução do 

É frequentemente necessário para ajudar o GC. Infelizmente, *GC. AddMemoryPressure ()* e *GC. *Não há suporte para RemoveMemoryPressure (), portanto, se você *souber* que acabou de liberar um grafo de objeto grande alocado para Java, talvez seja necessário chamar manualmente o [GC. Collect ()](xref:System.GC.Collect) para solicitar um GC para liberar a memória do lado do Java, ou você pode descartar explicitamente as subclasses *Java. lang. Object* , interrompendo o mapeamento entre o wrapper callable gerenciado e a instância Java. Por exemplo, consulte [Bug 1084](https://bugzilla.xamarin.com/show_bug.cgi?id=1084#c6). 

> [!NOTE]
> Você deve ser *extremamente* cuidadoso ao descartar `Java.Lang.Object` instâncias de subclasse.

Para minimizar a possibilidade de corrupção de memória, observe as diretrizes a seguir ao chamar `Dispose()` .

#### <a name="sharing-between-multiple-threads"></a>Compartilhamento entre vários threads

Se a instância do *Java ou gerenciada* pode ser compartilhada entre vários threads, *ela não deve ser `Dispose()` d*, **nunca**. Por exemplo,[`Typeface.Create()`](xref:Android.Graphics.Typeface.Create*) 
pode retornar uma *instância armazenada em cache*. Se vários threads fornecerem os mesmos argumentos, eles obterão a *mesma* instância. Consequentemente, `Dispose()` o ing da `Typeface` instância de um thread pode invalidar outros threads, o que pode resultar em `ArgumentException` s `JNIEnv.CallVoidMethod()` (entre outros) porque a instância foi descartada de outro thread. 

#### <a name="disposing-bound-java-types"></a>Descartando tipos de Java associados

Se a instância for de um tipo de Java associado, a instância poderá ser descartada *desde que a instância* não seja reutilizada do código gerenciado *e* a instância do Java não possa ser compartilhada entre threads (consulte a `Typeface.Create()` discussão anterior). (Tornar essa determinação pode ser difícil.) Na próxima vez em que a instância Java entrar em código gerenciado, um *novo* wrapper será criado para ele. 

Isso é geralmente útil quando se trata de drawables e outras instâncias com recursos pesados:

```csharp
using (var d = Drawable.CreateFromPath ("path/to/filename"))
    imageView.SetImageDrawable (d);
```

O item acima é seguro porque o ponto que o [empate. CreateFromPath ()](xref:Android.Graphics.Drawables.Drawable.CreateFromPath*) retornará a um ponto de estrutura, e *não* a um usuário de mesmo nível. A `Dispose()` chamada no final do `using` bloco interromperá a relação entre as instâncias de desenho [Drawable](xref:Android.Graphics.Drawables.Drawable) gerenciado e de estrutura [Drawable](https://developer.android.com/reference/android/graphics/drawable/Drawable.html) , permitindo que a instância do Java seja coletada assim que o tempo de execução do Android precisar. Isso *não* seria seguro se a instância de pares referenciasse um ponto de usuário; aqui, estamos usando informações "externas" para *saber* que o `Drawable` não pode fazer referência a um ponto de usuário e, portanto, a `Dispose()` chamada é segura. 

#### <a name="disposing-other-types"></a>Descartando outros tipos 

Se a instância se referir a um tipo que não é uma associação de um tipo Java (como um personalizado `Activity` ), **não** chame `Dispose()` a menos que você *saiba* que nenhum código Java chamará os métodos substituídos nessa instância. Falha ao fazer isso resulta em [ `NotSupportedException` s](~/android/internals/architecture.md#Premature_Dispose_Calls). 

Por exemplo, se você tiver um ouvinte de clique personalizado:

```csharp
partial class MyClickListener : Java.Lang.Object, View.IOnClickListener {
    // ...
}
```

Você *não deve* descartar essa instância, pois o Java tentará invocar métodos nele no futuro:

```csharp
// BAD CODE; DO NOT USE
Button b = FindViewById<Button> (Resource.Id.myButton);
using (var listener = new MyClickListener ())
    b.SetOnClickListener (listener);
```

#### <a name="using-explicit-checks-to-avoid-exceptions"></a>Usando verificações explícitas para evitar exceções

Se você tiver implementado um método de sobrecarga [Java. lang. Object. Dispose](xref:Java.Lang.Object.Dispose*) , evite tocar objetos que envolvam JNI. Isso pode criar uma situação de *descarte duplo* que possibilita ao seu código (fatalmente) tentar acessar um objeto Java subjacente que já tenha sido coletado pelo lixo. Isso produz uma exceção semelhante à seguinte: 

```shell
System.ArgumentException: 'jobject' must not be IntPtr.Zero.
Parameter name: jobject
at Android.Runtime.JNIEnv.CallVoidMethod
```

Essa situação geralmente ocorre quando a primeira descartar um objeto faz com que um membro se torne nulo e, em seguida, uma tentativa de acesso subsequente nesse membro NULL faz com que uma exceção seja gerada. Especificamente, o objeto `Handle` (que vincula uma instância gerenciada à sua instância de Java subjacente) é invalidado na primeira Dispose, mas o código gerenciado ainda tenta acessar essa instância Java subjacente, embora não esteja mais disponível (consulte [wrappers callable gerenciados](~/android/internals/architecture.md#Managed_Callable_Wrappers) para obter mais informações sobre o mapeamento entre instâncias de Java e instâncias gerenciadas). 

Uma boa maneira de evitar essa exceção é verificar explicitamente em seu `Dispose` método que o mapeamento entre a instância gerenciada e a instância Java subjacente ainda é válido; ou seja, verifique se o objeto `Handle` é nulo ( `IntPtr.Zero` ) antes de acessar seus membros. Por exemplo, o método a seguir `Dispose` acessa um `childViews` objeto: 

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

Se uma passagem de descarte inicial causar `childViews` um inválido `Handle` , o `for` acesso do loop gerará um `ArgumentException` . Ao adicionar uma `Handle` verificação nula explícita antes do primeiro `childViews` acesso, o seguinte `Dispose` método impede que a exceção ocorra: 

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

Sempre que uma instância de um `Java.Lang.Object` tipo ou subclasse é verificada durante o GC, todo o *gráfico de objeto* ao qual a instância se refere também deve ser verificado. O grafo de objeto é o conjunto de instâncias de objeto ao qual a "instância raiz" se refere, *além* de tudo que é referenciado pelo qual a instância raiz se refere, recursivamente. 

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

Quando `BadActivity` é construído, o grafo do objeto conterá 10004 instâncias (1x `BadActivity` , 1x `strings` , 1x `string[]` mantidas por `strings` , instâncias de cadeia *all* de caracteres 10000x), que precisarão ser verificadas sempre que a `BadActivity` instância for verificada. 

Isso pode ter impactos prejudiciais nos tempos de coleta, resultando em tempos de pausa de GC maiores. 

Você pode ajudar o GC *reduzindo* o tamanho dos grafos de objeto que são modificados por instâncias de pares do usuário. No exemplo acima, isso pode ser feito ao mover `BadActivity.strings` para uma classe separada que não herda de Java. lang. Object: 

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

As coleções secundárias podem ser executadas manualmente chamando [GC. Coletar (0)](xref:System.GC.Collect). As coleções secundárias são baratas (quando comparadas às principais coleções), mas têm um custo fixo significativo, portanto, você não quer dispará-las com muita frequência e deve ter um tempo de pausa de alguns milissegundos. 

Se seu aplicativo tiver um "ciclo de serviço" no qual a mesma coisa é feita repetidamente, pode ser aconselhável executar manualmente uma coleção secundária depois que o ciclo de serviço for encerrado. Os ciclos de serviço de exemplo incluem: 

- O ciclo de renderização de um único quadro de jogo.
- Toda a interação com uma determinada caixa de diálogo de aplicativo (abertura, preenchimento, fechamento) 
- Um grupo de solicitações de rede para atualizar/sincronizar dados de aplicativo.

## <a name="major-collections"></a>Coleções principais

As coleções principais podem ser executadas manualmente chamando [GC. Collect ()](xref:System.GC.Collect) ou `GC.Collect(GC.MaxGeneration)` . 

Eles devem ser executados raramente e podem ter um tempo de pausa de um segundo em um dispositivo em estilo Android ao coletar um heap de 512MB. 

As principais coleções só devem ser invocadas manualmente, se já: 

- No final dos ciclos de imposto longos e quando uma pausa longa não apresentará um problema ao usuário. 

- Em um método do [Android. app. Activity. OnLowMemory ()](xref:Android.App.Activity.OnLowMemory) substituído. 

## <a name="diagnostics"></a>Diagnósticos

Para controlar quando as referências globais são criadas e destruídas, você pode definir a propriedade do sistema [debug. mono. log](~/android/troubleshooting/index.md) para conter [*gref*](~/android/troubleshooting/index.md) e/ou [*GC*](~/android/troubleshooting/index.md). 

## <a name="configuration"></a>Configuração

O coletor de lixo do Xamarin. Android pode ser configurado definindo a `MONO_GC_PARAMS` variável de ambiente. As variáveis de ambiente podem ser definidas com uma ação de compilação de [AndroidEnvironment](~/android/deploy-test/environment.md).

A `MONO_GC_PARAMS` variável de ambiente é uma lista separada por vírgulas dos seguintes parâmetros: 

- `nursery-size` = *tamanho* : define o tamanho do Nursery. O tamanho é especificado em bytes e deve ser uma potência de dois. Os sufixos `k` `m` e `g` podem ser usados para especificar quilobytes-, mega-e gigabytes, respectivamente. O Nursery é a primeira geração (de dois). Um Nursery maior geralmente acelerará o programa, mas, obviamente, usará mais memória. O tamanho padrão de Nursery 512 KB. 

- `soft-heap-limit` = *tamanho* : o consumo máximo de memória gerenciada de destino para o aplicativo. Quando a utilização de memória está abaixo do valor especificado, o GC é otimizado para o tempo de execução (menos coleções). 
    Acima desse limite, o GC é otimizado para uso de memória (mais coleções). 

- `evacuation-threshold` = *limite* : define o limite de evacuação em porcentagem. O valor deve ser um número inteiro no intervalo de 0 a 100. O padrão é 66. Se a fase de varredura da coleção descobrir que a ocupação de um tipo de bloco de heap específico é menor que esse percentual, ele fará uma coleção de cópia para esse tipo de bloco na próxima coleção principal, restaurando assim a ocupação para quase 100%. Um valor de 0 desativa A evacuação. 

- `bridge-implementation` = *implementação de ponte* : isso definirá a opção de ponte GC para ajudar a resolver problemas de desempenho de GC. Há três valores possíveis: *antigo* , *novo* , *Tarjan*.

- `bridge-require-precise-merge`: A ponte Tarjan contém uma otimização que pode, em raras ocasiões, fazer com que um objeto seja coletado um GC depois de se tornar o lixo pela primeira vez. A inclusão dessa opção desabilita essa otimização, tornando os GCs mais previsíveis, mas potencialmente mais lentos.

Por exemplo, para configurar o GC para ter um limite de tamanho de heap de 128MB, adicione um novo arquivo ao seu projeto com uma **ação de compilação** de `AndroidEnvironment` com o conteúdo: 

```shell
MONO_GC_PARAMS=soft-heap-limit=128m
```
