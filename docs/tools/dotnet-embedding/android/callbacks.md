---
title: Retornos de chamada no Android
ms.prod: xamarin
ms.assetid: F3A7A4E6-41FE-4F12-949C-96090815C5D6
ms.technology: xamarin-cross-platform
author: topgenorth
ms.author: toopge
ms.date: 11/14/2017
ms.openlocfilehash: 72786ac4bceca2635747ebcc844a98b0ce60383f
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/04/2018
---
# <a name="callbacks-on-android"></a>Retornos de chamada no Android

Chamada para Java do c# é uma *arriscado*. Isto significa que há um *padrão* para retornos de chamada do c# para Java; no entanto, é mais complicado do que gostaríamos.

Abordaremos as três opções para fazer chamadas de retorno que fazem mais sentido para Java:

- Classes abstratas
- Interfaces
- Métodos virtuais

## <a name="abstract-classes"></a>Classes abstratas

Essa é a rota mais fácil para retornos de chamada, portanto, recomendamos usar `abstract` se apenas ao tentar obter um retorno de chamada trabalhar de forma mais simples.

Vamos começar com uma classe c#, gostaríamos de Java para implementar:

```csharp
[Register("mono.embeddinator.android.AbstractClass")]
public abstract class AbstractClass : Java.Lang.Object
{
    public AbstractClass() { }

    public AbstractClass(IntPtr handle, JniHandleOwnership transfer) : base(handle, transfer) { }

    [Export("getText")]
    public abstract string GetText();
}
```

Aqui estão os detalhes para que funcione:

- `[Register]` gera um nome de pacote adequado no Java – você receberá um nome de pacote gerado automaticamente sem ele.
- Subclasses `Java.Lang.Object` sinaliza Embeddinator para executar a classe por meio do gerador de Java do xamarin.
- Construtor vazio: é que você deseja usar no código de Java.
- `(IntPtr, JniHandleOwnership)` construtor: é o que usará xamarin para criar o c#-equivalente de objetos Java.
- `[Export]` sinaliza o xamarin para expor o método para Java. Também podemos alterar o nome do método, desde que o mundo de Java desejar usar métodos de letras minúsculas.

Avançar vamos fazer um método c# para testar o cenário:

```csharp
[Register("mono.embeddinator.android.JavaCallbacks")]
public class JavaCallbacks : Java.Lang.Object
{
    [Export("abstractCallback")]
    public static string AbstractCallback(AbstractClass callback)
    {
        return callback.GetText();
    }
}
```
`JavaCallbacks` pode ser qualquer classe para testar isso, desde que seja um `Java.Lang.Object`.

Agora, execute Embeddinator no assembly .NET para gerar um AAR. Consulte o [guia de Introdução](~/tools/dotnet-embedding/get-started/java/android.md) para obter detalhes.

Depois de importar o arquivo AAR Android Studio, vamos escrever um teste de unidade:

```java
@Test
public void abstractCallback() throws Throwable {
    AbstractClass callback = new AbstractClass() {
        @Override
        public String getText() {
            return "Java";
        }
    };

    assertEquals("Java", callback.getText());
    assertEquals("Java", JavaCallbacks.abstractCallback(callback));
}
```
Portanto é:

- Implementado o `AbstractClass` em Java com um tipo anônimo
- Certificar-se de que a instância retorna `"Java"` do Java
- Certificar-se de que a instância retorna `"Java"` do c#
- Adicionado `throws Throwable`, como c# construtores são marcados com `throws`

Se ocorreu essa unidade de teste como-é, ele falhará com um erro, como:

```csharp
System.NotSupportedException: Unable to find Invoker for type 'Android.AbstractClass'. Was it linked away?
```

O que está faltando aqui está uma `Invoker` tipo. Isso é uma subclasse de `AbstractClass` que encaminha c# chamadas para Java. Se um objeto de Java entra no mundo do c# e o tipo equivalente do c# é abstrato, xamarin procura automaticamente um tipo c# com o sufixo `Invoker` para uso em código c#.

Xamarin usa esse `Invoker` padrão para projetos de associação de Java entre outras coisas.

Aqui está a nossa implementação do `AbstractClassInvoker`:
```csharp
class AbstractClassInvoker : AbstractClass
{
    IntPtr class_ref, id_gettext;

    public AbstractClassInvoker(IntPtr handle, JniHandleOwnership transfer) : base(handle, transfer)
    {
        IntPtr lref = JNIEnv.GetObjectClass(Handle);
        class_ref = JNIEnv.NewGlobalRef(lref);
        JNIEnv.DeleteLocalRef(lref);
    }

    protected override Type ThresholdType
    {
        get { return typeof(AbstractClassInvoker); }
    }

    protected override IntPtr ThresholdClass
    {
        get { return class_ref; }
    }

    public override string GetText()
    {
        if (id_gettext == IntPtr.Zero)
            id_gettext = JNIEnv.GetMethodID(class_ref, "getText", "()Ljava/lang/String;");
        IntPtr lref = JNIEnv.CallObjectMethod(Handle, id_gettext);
        return GetObject<Java.Lang.String>(lref, JniHandleOwnership.TransferLocalRef)?.ToString();
    }

    protected override void Dispose(bool disposing)
    {
        if (class_ref != IntPtr.Zero)
            JNIEnv.DeleteGlobalRef(class_ref);
        class_ref = IntPtr.Zero;

        base.Dispose(disposing);
    }
}
```

Há um pouco acontecendo aqui, podemos:

- Adicionada uma classe com o sufixo `Invoker` que as subclasses `AbstractClass`
- Adicionado `class_ref` para manter o JNI fazem referência à classe Java que as subclasses nossa classe c#
- Adicionado `id_gettext` para manter a referência JNI para Java `getText` método
- Incluído um `(IntPtr, JniHandleOwnership)` construtor
- Implementado `ThresholdType` e `ThresholdClass` como um requisito para xamarin saber detalhes sobre o `Invoker`
- `GetText` necessário para pesquisar o Java `getText` método com a assinatura apropriada de JNI e chamá-lo
- `Dispose` é necessário apenas para eliminar a referência para `class_ref`

Depois de adicionar essa classe e gerar um novo AAR, nossa unidade teste passa. Como você pode ver esse padrão para retornos de chamada não é *ideal*, mas poderá ser feito.

Para obter detalhes sobre interoperabilidade de Java, consulte a incrível [xamarin documentação](https://developer.xamarin.com/guides/android/advanced_topics/java_integration_overview/working_with_jni/) sobre este assunto.

## <a name="interfaces"></a>Interfaces

Interfaces são muito semelhante a de classes abstratas, exceto um detalhe: xamarin não gera Java para eles. Isso ocorre porque antes da inserção de .NET, não há muitos cenários em que o Java deve implementar uma interface c#.

Digamos que temos a interface c# a seguir:

```csharp
[Register("mono.embeddinator.android.IJavaCallback")]
public interface IJavaCallback : IJavaObject
{
    [Export("send")]
    void Send(string text);
}
```
`IJavaObject` sinaliza Embeddinator que esta é uma interface de xamarin, mas, caso contrário, isso é exatamente o mesmo que um `abstract` classe.

Desde xamarin atualmente não irá gerar o código Java para esta interface, adicione o Java a seguir ao seu projeto c#:

```java
package mono.embeddinator.android;

public interface IJavaCallback {
    void send(String text);
}
```
Você pode colocar o arquivo em qualquer lugar, mas certifique-se de definir a ação de compilação `AndroidJavaSource`. Isso sinalizará Embeddinator copiá-lo para o diretório apropriado para obter compilados em seu arquivo AAR.

Em seguida, o `Invoker` implementação será exatamente o mesmo:

```csharp
class IJavaCallbackInvoker : Java.Lang.Object, IJavaCallback
{
    IntPtr class_ref, id_send;

    public IJavaCallbackInvoker(IntPtr handle, JniHandleOwnership transfer) : base(handle, transfer)
    {
        IntPtr lref = JNIEnv.GetObjectClass(Handle);
        class_ref = JNIEnv.NewGlobalRef(lref);
        JNIEnv.DeleteLocalRef(lref);
    }

    protected override Type ThresholdType
    {
        get { return typeof(IJavaCallbackInvoker); }
    }

    protected override IntPtr ThresholdClass
    {
        get { return class_ref; }
    }

    public void Send(string text)
    {
        if (id_send == IntPtr.Zero)
            id_send = JNIEnv.GetMethodID(class_ref, "send", "(Ljava/lang/String;)V");
        JNIEnv.CallVoidMethod(Handle, id_send, new JValue(new Java.Lang.String(text)));
    }

    protected override void Dispose(bool disposing)
    {
        if (class_ref != IntPtr.Zero)
            JNIEnv.DeleteGlobalRef(class_ref);
        class_ref = IntPtr.Zero;

        base.Dispose(disposing);
    }
}
```

Depois de gerar um arquivo AAR, no Android Studio poderíamos escrever a seguinte unidade de passagem de teste:

```java
class ConcreteCallback implements IJavaCallback {
    public String text;
    @Override
    public void send(String text) {
        this.text = text;
    }
}

@Test
public void interfaceCallback() {
    ConcreteCallback callback = new ConcreteCallback();
    JavaCallbacks.interfaceCallback(callback, "Java");
    assertEquals("Java", callback.text);
}
```

## <a name="virtual-methods"></a>Métodos virtuais

Substituindo um `virtual` em Java, é possível, mas não uma ótima experiência.

Vamos supor que você tem a seguinte classe c#:

```csharp
[Register("mono.embeddinator.android.VirtualClass")]
public class VirtualClass : Java.Lang.Object
{
    public VirtualClass() { }

    public VirtualClass(IntPtr handle, JniHandleOwnership transfer) : base(handle, transfer) { }

    [Export("getText")]
    public virtual string GetText() { return "C#"; }
}
```

Se você seguiu o `abstract` exemplo de classe acima, ele funcionará, exceto um detalhe: _xamarin não pesquisar o `Invoker`_ .

Para corrigir isso, modifique a classe do c# para ser `abstract`:

```csharp
public abstract class VirtualClass : Java.Lang.Object
```

Isso não é ideal, mas ele obtém esse cenário funcione. Xamarin escolherá o `VirtualClassInvoker` e Java pode usar `@Override` no método.

## <a name="callbacks-in-the-future"></a>Retornos de chamada no futuro

Há algumas coisas possível para melhorar a estes cenários:

1. `throws Throwable` no c# construtores é fixa nisso [PR](https://github.com/xamarin/java.interop/pull/170).
1. Verifique o gerador de Java em xamarin oferecem suporte a interfaces.
    - Isso remove a necessidade de adicionar o arquivo de origem Java com uma ação de compilação de `AndroidJavaSource`.
1. Fazer uma maneira para xamarin carregar um `Invoker` para classes virtuais.
    - Isso elimina a necessidade de marcar a classe em nossa `virtual` exemplo `abstract`.
1. Gerar `Invoker` classes para Embeddinator automaticamente
    - Isso vai ser complicada, mas poderá ser feito. Xamarin já está fazendo algo semelhante a este para projetos de associação de Java.

Há muito trabalho a ser feito aqui, mas esses aprimoramentos para Embeddinator são possíveis.

## <a name="further-reading"></a>Leitura adicional

* [Guia de Introdução no Android](~/tools/dotnet-embedding/get-started/java/android.md)
* [Pesquisa Android preliminar](~/tools/dotnet-embedding/android/index.md)
* [Limitações de Embeddinator](~/tools/dotnet-embedding/limitations.md)
* [Contribuindo para o projeto de código-fonte aberto](https://github.com/mono/Embeddinator-4000/blob/master/docs/Contributing.md)
* [Descrições e códigos de erro](~/tools/dotnet-embedding/errors.md)
